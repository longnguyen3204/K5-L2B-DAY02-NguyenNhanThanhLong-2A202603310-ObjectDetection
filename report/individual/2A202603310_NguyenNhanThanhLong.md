# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Nhân Thành Long
- MSSV: 2A202603310
- Email: longnguyen5324@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/longnguyen3204/K5-L2B-DAY02-NguyenNhanThanhLong-2A202603310-ObjectDetection/tree/main
- Link Colab đã chạy: https://colab.research.google.com/github/longnguyen3204/K5-L2B-DAY02-NguyenNhanThanhLong-2A202603310-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb#scrollTo=PfxdiwjglDYS

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). 
Tóm tắt ba ca mơ hồ đã quyết định:
| Ảnh | Quyết định cuối | Lý do theo schema |
| --- | --- | --- |
| `drive_045` | Gán nhãn: car | Áp dụng quy tắc bị che khuất; phần đầu lộ diện rõ (>30% diện tích) đủ để nhận diện đặc trưng dòng xe SUV/Jeep địa hình. |
| `drive_001` | Gán nhãn: car | Áp dụng quy tắc bị che khuất; dù bị cột chia đôi thân, kết cấu đuôi dốc và cụm đèn hậu đặc trưng vẫn đủ để nhận diện xe ô tô con (SUV/Crossover). |
| `drive_021` | Gán nhãn: van | Xe có cấu trúc hình khối hộp chữ nhật đồng nhất từ cabin đến khoang sau, trần cao và thiết kế đặc trưng của xe tải van đô thị/van mini (bất chấp ảnh bị nhòe chuyển động). |

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file trong `dataset/labels/train/`, cùng basename với ảnh train. Ảnh không có xe hợp lệ có file rỗng.
- **Kết quả checker:** `python3 tools/check_yolo_labels.py` đạt **50/50** — không có lỗi định dạng (tọa độ ngoài [0,1], thiếu file, ID lớp sai).
- **Danh sách 10 ảnh đã rà bằng mắt:**

| STT | Ảnh | Nhận xét khi rà |
|---|---|---|
| 1 | `drive_002` | Cảnh đông xe; rà lại thấy vẽ 42 box, một số box dư cho vật thể quá nhỏ (<15px) ở xa |
| 2 | `drive_009` | Góc chụp cao, nhiều xe ở xa; chỉ gán 5 box, nhận ra bỏ sót nhiều xe nhỏ nền đường |
| 3 | `drive_019` | Đường cao tốc đông phương tiện; chỉ gán 3 box — bỏ sót rõ rệt xe ở làn xa |
| 4 | `drive_024` | Góc panorama có ~30 xe; chỉ gán 2 box vì xe ở xa quá nhỏ, không tự tin phân lớp |
| 5 | `drive_027` | Cảnh đô thị đông; chỉ gán 1 box — bỏ sót nhiều xe ở viền ảnh và xa horizon |
| 6 | `drive_030` | 18 box gán, nhưng khi rà phát hiện 9 box không khớp đủ IoU do vẽ hơi lệch gương xe |
| 7 | `drive_031` | Gán 28 box cho 14 xe gold — một số xe bị vẽ thành 2 box tách rời do bị cột che giữa |
| 8 | `drive_038` | 16 box cho 17 gold; rà phát hiện 1 xe tải nhỏ bị gán sai class `car` thay vì `truck` |
| 9 | `drive_045` | Ca mơ hồ (SUV/Jeep bị xe tải che); đã xử lý đúng theo schema — giữ nguyên |
| 10 | `drive_050` | 25 box cho 18 gold; 7 box dư do gán nhãn xe máy và xe đạp — lỗi ngoài phạm vi 4 lớp |

- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:**
  - **Bỏ sót xe nhỏ ở xa** (`drive_009`, `drive_019`, `drive_024`, `drive_027`): Do áp quy tắc "dưới 15×15 pixel không gán" quá bảo thủ — nhiều xe 20–30px bị bỏ. Sửa: thêm box cho xe ≥15px còn nhận diện được lớp.
  - **Vẽ box dư ngoài 4 lớp** (`drive_050`): Gán nhãn cho xe máy/xe đạp không thuộc schema. Sửa: xóa các box không thuộc {car, truck, bus, van}.
  - **Box bị vỡ đôi khi vật thể bị che** (`drive_031`): Vẽ 2 box tách rời cho 1 xe bị cột chia giữa. Sửa: hợp thành 1 box ước lượng toàn xe.
  - **Lệch box do bỏ qua gương chiếu hậu** (`drive_030`): Một số box thiếu vài pixel gương — IoU sát ngưỡng 0.5. Sửa: vẽ lại ôm sát gương theo quy tắc Tight Box.

- **Commit khóa nhãn ban đầu:** [`Initial commit`](https://github.com/longnguyen3204/K5-L2B-DAY02-NguyenNhanThanhLong-2A202603310-ObjectDetection/commits/main)

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 339 | — |
| FP | 175 | — |
| FN | 314 | — |
| Precision | 0.6595 | — |
| Recall | 0.5191 | — |
| F1 | 0.581 | — |
| mean_iou_of_matches | 0.8211 | — |
| passed | false | — |

Dẫn tới [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json) trong repo.

**Nhận xét Gold QC:** Kết quả chưa đạt ngưỡng (yêu cầu F1 ≥ 0.80 và mean\_iou ≥ 0.70). Mean IoU của các match đạt 0.8211 (vượt ngưỡng), nhưng F1 chỉ đạt 0.581 do tỷ lệ FN rất cao (314). Phân tích per-image cho thấy hai nhóm lỗi chính:

- **FN cao:** Những ảnh góc cao hoặc đông phương tiện như `drive_009` (5 student / 21 gold), `drive_019` (3/27), `drive_024` (2/30), `drive_027` (1/31), `drive_029` (0/19), `drive_039` (0/11), `drive_040` (1/19) bỏ sót nhiều xe ở xa hoặc xe nhỏ ở nền ảnh. Lỗi này xuất phát từ việc áp quy tắc "dưới 15×15 pixel không gán", không phải từ nhầm lớp hay box sai.
- **FP cao (box dư):** Những ảnh như `drive_002` (42 student / 13 gold), `drive_031` (28/14), `drive_028` (8/3) bị vẽ dư box — một phần do vẽ 2 box cho 1 xe bị che giữa, một phần do gán nhãn vật thể ngoài phạm vi 4 lớp.

**Rework:** Không có rework chính thức.

## 5. Kết quả YOLO

- **Runtime, GPU/CPU, phiên bản Ultralytics và Torch:** Google Colab, GPU Tesla T4 (14 GB VRAM), Ultralytics 8.4.208, Python 3.13.16, torch-2.14.1+cu130. Training 50 epochs hoàn thành trong **0.021 giờ** (~1.3 phút), batch-size tự động = 44 (AutoBatch, 59% GPU).
- **Các tham số thay đổi so với notebook nếu có và lý do:** Giữ nguyên tất cả tham số mặc định của notebook.
- **Đường dẫn best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (epoch chọn theo mAP50 val tốt nhất trong quá trình train; được lưu tại `report/individual/assets/yolo26_runs/vehicle_annotation/weights/` — file `.pt` bị loại bởi `.gitignore`).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4189 |
| Recall | 0.4637 |
| mAP50 | 0.4023 |
| mAP50–95 | 0.2443 |

**Kết quả per-class (best checkpoint validate trên eval):**

| Lớp | Images | Instances | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|---:|
| all | 10 | 113 | 0.419 | 0.464 | 0.402 | 0.244 |
| car | 10 | 94 | 0.550 | 0.809 | 0.759 | 0.483 |
| truck | 5 | 5 | 0.530 | 0.800 | 0.459 | 0.311 |
| bus | 2 | 2 | 0.000 | 0.000 | 0.071 | 0.028 |
| van | 6 | 12 | 0.595 | 0.246 | 0.320 | 0.154 |

Kết quả assets sẽ được đặt tại `assets/eval_predictions/visuals/` và `assets/yolo26_runs/` sau khi giải nén `day2_colab_outputs.zip` từ Colab.

**Ví dụ ảnh cụ thể:** Trên `drive_057.jpg`, model phát hiện đúng **10 cars và 1 truck** — chiếc xe tải lộ rõ khoang chở hàng hở phía sau cabin, model định vị và phân lớp chính xác khớp với nhãn gold. Trên `drive_056.jpg`, model phát hiện **23 cars** trên một cảnh đô thị đông đúc — một số xe nhỏ ở xa bị bỏ sót (FN), phù hợp với pattern quan sát ở annotation QC.

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   Lớp `bus` có recall = 0 — trong 10 ảnh eval chỉ có 2 instance bus và model không detect được cái nào. Điều này có thể quan sát trên các ảnh eval có bus xuất hiện như `drive_059.jpg` (model chỉ detect 1 car trong khi gold có thể bao gồm bus). Lớp `van` có recall = 0.246 (thấp thứ hai trong các lớp có đủ instance) — `drive_055.jpg` với 6 cars là ảnh điển hình model bỏ sót van khi tập trung vào xe con.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   Cần kiểm tra **độ chính xác tọa độ của bounding box**. mAP50 chỉ yêu cầu IoU ≥ 0.5 để tính là TP, trong khi mAP50–95 tính trung bình ở các ngưỡng IoU từ 0.5 đến 0.95. Nếu khoảng cách lớn, nghĩa là model xác định đúng vị trí và lớp xe nhưng box chưa ôm sát đủ chặt — thường là do: box quá lớn hoặc quá nhỏ so với biên thực; box không bao phủ gương chiếu hậu hoặc các bộ phận nhô ra; nhãn train thiếu tight-box dẫn model học sai "độ chặt" của box.

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   Lỗi class `van` ↔ `car` có thể liên quan guideline. Dữ kiện: recall của `van` chỉ đạt 0.246 trong khi `car` đạt 0.809, cho thấy model thường classify van thành car. Điều này phù hợp với ca mơ hồ `drive_021` (xe van màu bạc bị nhòe chuyển động) — ranh giới van/car trong guideline phụ thuộc vào kết cấu cabin và cửa lùa, những đặc điểm khó nhận ra khi ảnh bị nhòe hoặc chụp từ xa. Nếu trong tập train có một số xe van bị gán nhãn car, model sẽ học nhầm biên giới class này.

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   10 ảnh eval **không được dùng để train** vì chúng phải đóng vai trò là tập kiểm tra độc lập để đánh giá khả năng tổng quát hóa của model — nếu dùng để train, model sẽ "nhớ" các ảnh đó, số liệu eval sẽ bị thổi phồng và không phản ánh thực tế. Trong notebook, 10 ảnh eval đóng vai trò **validation set**: sau mỗi epoch train, model được val trên tập này để theo dõi mAP và chọn checkpoint tốt nhất (`best.pt`). Tuy nhiên, đây **không phải test set độc lập hoàn toàn** — việc dùng eval để chọn best checkpoint có nghĩa là model được tối ưu hóa một cách gián tiếp dựa trên tập này; kết quả mAP50 = 0.402 phản ánh hiệu năng trên tập validation, không phải tập test chưa thấy bao giờ.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CP2 — Gán nhãn, cảnh đông xe ở xa (`drive_009`, `drive_019`, `drive_024`, `drive_027`) | Không chắc có nên gán nhãn xe rất nhỏ ở xa (khoảng 15–20px) hay không | Áp quy tắc "dưới 15×15 pixel không gán" trong guideline | Dẫn đến FN rất cao (recall = 0.519 toàn bộ, nhiều ảnh có matches < 20% gold)|
| CP2 — Gán nhãn, xe bị cột/vật che ngang giữa thân (`drive_031`) | Xe bị cột chia thành 2 phần riêng biệt về mặt thị giác, không rõ có cần 1 hay 2 box | Vẽ 2 box tách rời cho mỗi phần lộ diện | Gold chỉ dùng 1 box ước lượng toàn xe — dẫn đến FP dư và 1 FN |
| CP2 — Gán nhãn, vật thể ngoài 4 lớp (`drive_050`) | Nhầm xe máy/xe đạp trong ảnh thành vật thể cần gán nhãn | Gán nhãn `car` cho các vật thể di chuyển nhỏ chưa rõ | Gold bỏ qua xe máy/xe đạp; dẫn đến FP cao cho ảnh đó |
| CP3 — Self-QC, chạy checker | Checker chỉ kiểm định dạng, không kiểm nội dung (class đúng, box sát) | Rà thêm 10 ảnh bằng mắt sau khi checker pass | Phát hiện thêm lỗi nội dung nhưng không đủ thời gian sửa toàn bộ trước mốc khóa nhãn |
| CP5 — Gold QC | F1 = 0.581, chưa đạt ngưỡng 0.80; không có rework chính thức | Phân tích JSON, lập danh sách ảnh sai; chưa sửa nhãn trong repo | Nhãn train được giữ nguyên; bài học rút ra để gán nhãn tốt hơn ở lần sau |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

| Hạng mục rubric | Trạng thái | Ghi chú |
|---|---|---|
| Mini guideline: định nghĩa 4 lớp, quy tắc box, ≥3 ca mơ hồ | ✅ Hoàn thành | Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) và mục 2 báo cáo |
| 50 file nhãn YOLO train, cùng basename với ảnh | ✅ Hoàn thành | 50/50 file, checker đạt |
| Self-QC: checker đạt, rà ≥10 ảnh bằng mắt, ghi lỗi | ✅ Hoàn thành | Checker 50/50; rà 10 ảnh; ghi 4 loại lỗi |
| Commit khóa nhãn ban đầu trước khi nhận gold | ✅ Hoàn thành | Link commit tại mục 3 |
| Gold QC: có JSON, ghi số liệu trước/sau rework | ⚠️ Một phần | Có JSON và số liệu "trước"; không có rework (F1 = 0.581 chưa đạt) |
| Colab đã chạy, có link chạy được | ✅ Hoàn thành | Link Colab tại mục 1 đã chạy đầy đủ 50 epoch |
| Metrics YOLO: Precision, Recall, mAP50, mAP50–95 | ✅ Hoàn thành | Điền từ output notebook tại mục 5 |
| Assets eval_predictions/ và yolo26_runs/ trong repo | ⚠️ Chưa commit | Colab đã tạo ZIP nhưng chưa giải nén và commit lên GitHub |
| Trả lời 4 câu hỏi cuối notebook | ✅ Hoàn thành | Xem mục 5 |
| Repo đúng tên, báo cáo đúng tên file | ✅ Hoàn thành | Tên repo và file đúng chuẩn |

## 8. Công cụ AI và tài liệu tham khảo

**Công cụ AI đã dùng:**

| Công cụ | Bước sử dụng | Phần đã tự kiểm tra |
|---|---|---|
| **Antigravity** | Hỗ trợ hoàn thiện phần mục 3–5 của báo cáo cá nhân này | Toàn bộ số liệu được đối chiếu trực tiếp từ `annotation_evaluation.json` và output cell notebook đã chạy; các nhận xét phân tích dựa trên dữ liệu thực tế, không suy đoán |
| **CVAT / Label Studio (annotation tool)** | CP2 — gán nhãn 50 ảnh train | Tự vẽ và kiểm từng box; không dùng auto-label |
| **Google Colab** | CP6 — train YOLO26n và eval trên 10 ảnh | Chạy toàn bộ notebook từ trên xuống; không chỉnh sửa code |

**Không dùng AI** cho bước gán nhãn (CP2) và quyết định class trong guideline — các quyết định đó dựa trên quan sát trực tiếp ảnh và đọc schema.

**Nguồn ngoài repo được sử dụng:**
- [Ultralytics YOLO documentation](https://docs.ultralytics.com) — tham khảo ý nghĩa tham số train và cách đọc metrics
- [YOLO Paper (Redmon et al.)](https://arxiv.org/abs/1506.02640) — hiểu cơ chế IoU-based matching trong đánh giá detection
