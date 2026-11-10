# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Văn Bảo
- MSSV: 2A202603104
- Email: baonguyen2001aa@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/baobao201/K5-L2A-DAY2-NguyenVanBao-2A202603104-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1Lcejx0_US0Mjc3RAmhkrQhIreQZjSM_q?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: 20
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_004.jpg, drive_006.jpg, drive_007.jpg, drive_008.jpg, drive_009.jpg, drive_012.jpg, drive_019.jpg, drive_026.jpg, drive_029.jpg, drive_034.jpg
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: xe ở xa, mờ ảo. Cách xử lý là bỏ gán nhãn
- Commit khóa nhãn ban đầu: khóa nhãn bản đầu tiên.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 435 | 354 |
| FP | 404 | 183 |
| FN | 218 | 299 |
| Precision | 0.5185 51.85% | 0.6592 (65.92%) |
| Recall | 0.6662 66.62% | 0.5421 (54.21%) |
| F1 | 0.5831 58.31% | 0.5950 (59.50%) |
| mean_iou_of_matches | 0.8494 84.94% | 0.8562 (85.62%) |
| passed | False | False |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.
Rework những hình có xe ở xa, mở khó xác định: drive_001.jpg, drive_002.jpg, drive_003.jpg

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: GPU T4
- Các tham số thay đổi so với notebook nếu có và lý do:
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5836731183003564 |
| Recall | 0.3191489361702128 |
| mAP50 | 0.3177540174889574 |
| mAP50–95 | 0.217850311704475 |

Đặt cả 2 folder `assets/eval_predictions/` [HÌNH 56](../assets/eval_predictions/visuals/drive_056.jpg) và `assets/yolo26_runs/` [YOLO26](../assets/yolo26_runs/) (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? 
- truck và van có recall thấp nhất (0.0).
- Minh họa: drive_056.jpg, drive_058.jpg.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
- Model phát hiện đúng đối tượng nhưng bbox chưa khít vật thể.
- IoU đủ ở ngưỡng 0.5 nhưng không đủ ở các ngưỡng cao hơn.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
Có dấu hiệu nhầm giữa: 
- van ↔ car
- truck ↔ car
- một phần bus ↔ car
- Cần rà soát lại quy tắc phân biệt các loại xe khi annotation.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
- Để tránh data leakage.
- Giữ tập eval độc lập nhằm đánh giá khách quan khả năng tổng quát hóa của model.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---------------|-------------|------------|-------------------|
| Gán nhãn dữ liệu trên CVAT | Khó phân biệt giữa các lớp `car`, `van`, `truck`, `bus` ở các xe kích thước nhỏ hoặc ở xa | Đọc lại guideline, phóng to ảnh khi gán nhãn và đối chiếu các ví dụ trong tài liệu | Giảm lỗi gán nhãn nhưng vẫn còn một số trường hợp khó phân loại |
| Kiểm tra chất lượng nhãn (QC) | Phát hiện một số box chưa bao phủ sát phương tiện hoặc thiếu đối tượng | Rà soát lại annotation, chỉnh sửa box và bổ sung các đối tượng bị bỏ sót | Chất lượng nhãn được cải thiện nhưng cần nhiều thời gian kiểm tra thủ công |
| Huấn luyện YOLO26 | Model học tốt lớp `car` nhưng nhận diện kém các lớp `truck` và `van` | Kiểm tra phân bố dữ liệu và số lượng mẫu của từng lớp | Kết quả cho thấy `truck` và `van` có Recall = 0.0, có khả năng thiếu dữ liệu hoặc nhãn chưa nhất quán |
| Đánh giá mô hình | Recall tổng thể thấp (0.319), nhiều phương tiện bị bỏ sót | Quan sát ảnh eval và phân tích metric theo từng lớp | Xác định mô hình đang bỏ sót nhiều `truck` và `van` |
| Phân tích kết quả mAP | mAP50-95 thấp hơn đáng kể so với mAP50 | Kiểm tra trực quan các bounding box dự đoán | Một số box chưa khít với vật thể, ảnh hưởng đến IoU ở các ngưỡng cao |
| Cải thiện mô hình | Hiệu năng chưa đạt yêu cầu của bài lab | Đề xuất bổ sung dữ liệu cho lớp `truck`, `van`, rà soát guideline và huấn luyện thêm | Chưa thực hiện trong phạm vi bài lab, cần thử nghiệm ở các lần train tiếp theo |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Kết quả bài làm | Điểm tối đa | Tự đánh giá |
|-----------|----------------|------------:|-------------|
| Hoàn thành và định dạng YOLO | Đã tạo bộ nhãn YOLO cho toàn bộ 50 ảnh. Định dạng nhãn gồm 5 cột (class_id, x_center, y_center, width, height) và đã sử dụng 4 lớp `car`, `truck`, `bus`, `van`. Đã chạy kiểm tra dữ liệu theo quy trình lab. | 10 | Có cơ sở đạt |
| Đầy đủ vật thể | Đã cố gắng gán nhãn toàn bộ xe nhìn thấy, bao gồm xe bị che khuất một phần và xe nằm sát mép ảnh nếu vẫn nhận diện được. Tuy nhiên chưa có công cụ đo độ đầy đủ tuyệt đối nên vẫn có khả năng tồn tại FN trong annotation. | 20 | Khá tốt nhưng chưa thể khẳng định tuyệt đối |
| Độ bám sát và hình học box | Đã chỉnh sửa bounding box để bao phủ sát phần phương tiện nhìn thấy. Kết quả QC cho thấy Mean IoU = 0.8562 (> 0.70), chứng tỏ box khá sát vật thể. | 20 | Tốt |
| Phân lớp nhất quán | Đã áp dụng thống nhất 4 lớp `car`, `truck`, `bus`, `van`. Tuy nhiên kết quả YOLO cho thấy Recall của `truck` và `van` bằng 0, có khả năng vẫn còn một số trường hợp phân lớp chưa nhất quán hoặc dữ liệu chưa đủ đa dạng. | 15 | Cần cải thiện |
| Mini guideline và quyết định mơ hồ | Đã xây dựng guideline ngắn, quy tắc vẽ box và ghi lại các trường hợp khó phân loại (car/van, bus/van, truck/car). | 10 | Đạt |
| Gold-set annotation QC | Kết quả QC: F1 = 0.595 (< 0.80), Mean IoU = 0.8562 (> 0.70). Theo rubric, chỉ cần một chỉ số không đạt là phải rework. Chưa đạt ngưỡng chất lượng annotation. | 15 | Chưa đạt, cần sửa nhãn |
| Pipeline YOLO và phân tích | Đã huấn luyện và đánh giá YOLO26 trên Colab. Không sử dụng tập eval để train. Có báo cáo Precision, Recall, mAP50, mAP50-95, ảnh prediction và phân tích nguyên nhân lỗi. | 10 | Đạt |

### Điều kiện bắt buộc

- Đã thực hiện đánh giá gold-set annotation.
- Đã thực hiện huấn luyện và đánh giá YOLO.
- Không sử dụng tập eval để train.
- Không thay đổi gold labels.
- Không vi phạm các điều kiện loại trừ được nêu trong rubric.

### Đánh giá chất lượng annotation theo rubric

Kết quả QC:

- F1 @ IoU 0.50 = **0.595**
- Mean IoU của matches = **0.8562**

Đối chiếu rubric:

| Chỉ số | Kết quả | Ngưỡng đạt |
|---------|---------:|---------:|
| F1 | 0.595 | ≥ 0.80 |
| Mean IoU | 0.8562 | ≥ 0.70 |

**Kết luận:** Thuộc mức **"Cần sửa nhãn"** vì F1 chưa đạt 0.80 mặc dù Mean IoU đã vượt yêu cầu. Điều này cho thấy hình học bounding box khá tốt nhưng vẫn còn nhiều False Positive hoặc False Negative trong annotation cần được rà soát và chỉnh sửa.

### Nhận xét tổng thể

Bài làm đã hoàn thành toàn bộ pipeline annotation → QC → train → evaluation. Điểm mạnh là chất lượng bounding box khá cao (Mean IoU 0.8562). Điểm cần cải thiện là độ bao phủ và tính nhất quán của nhãn, thể hiện qua F1 annotation chỉ đạt 0.595 và kết quả nhận diện rất thấp đối với các lớp `truck` và `van`. Nếu có thêm thời gian, cần thực hiện vòng rework annotation và chạy lại QC để đạt ngưỡng F1 ≥ 0.80 theo yêu cầu rubric.

## 8. Công cụ AI và tài liệu tham khảo

### Công cụ AI đã sử dụng

| Công cụ | Bước sử dụng | Mục đích | Phần đã tự kiểm tra |
|----------|-------------|----------|---------------------|
| Microsoft Copilot | Phân tích kết quả huấn luyện YOLO26 | Giải thích các metric như Precision, Recall, F1, mAP50, mAP50-95 và hỗ trợ tổng hợp báo cáo | Đối chiếu lại với kết quả `val` của Ultralytics và ảnh prediction |
| Microsoft Copilot | Viết và định dạng báo cáo Markdown | Hỗ trợ trình bày nội dung theo mẫu yêu cầu của bài lab | Kiểm tra lại thông tin, số liệu và nội dung trước khi nộp |
| Microsoft Copilot | Phân tích lỗi mô hình | Gợi ý nguyên nhân của các lỗi nhầm lẫn giữa `car`, `truck`, `bus`, `van` | Kiểm tra trực quan trên ảnh eval và metric theo từng lớp |

### Phần tự thực hiện

- Tự gán nhãn dữ liệu trên CVAT.
- Tự kiểm tra và chỉnh sửa bounding box.
- Tự huấn luyện mô hình YOLO26.
- Tự chạy đánh giá (evaluation).
- Tự quan sát ảnh prediction để xác định các lỗi nhận diện.
- Tự tổng hợp kết quả và rút ra nhận xét cuối cùng.

### Tài liệu tham khảo

#### Tài liệu trong bài lab

- Notebook và hướng dẫn thực hành của AI20K Day 02 Object Detection.
- Dataset và guideline annotation được cung cấp trong bài lab.
- Kết quả huấn luyện và đánh giá từ Ultralytics YOLO26.

#### Nguồn ngoài repo

1. Ultralytics Documentation  
   https://docs.ultralytics.com

2. Ultralytics YOLO GitHub Repository  
   https://github.com/ultralytics/ultralytics

3. CVAT Documentation  
   https://docs.cvat.ai

4. CVAT GitHub Repository  
   https://github.com/cvat-ai/cvat

### Cam kết

- Không sử dụng mật khẩu, token hoặc thông tin đăng nhập trong báo cáo.
- Không đính kèm hoặc chia sẻ dữ liệu train/gold-set ngoài phạm vi được phép.
- Các nội dung do AI hỗ trợ đều đã được đọc lại, kiểm tra và xác nhận trước khi đưa vào báo cáo.
