# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đỗ Tuấn Long
- MSSV: 2A202604066
- Email: itslongforwork@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/superuj-coder/K5-L2-Day02-DoTuanLong-2A202604066-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/10MX2kvwGtSwi9ZnTPptJyju7ip6fjIfL

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file .txt, tương ứng 50 ảnh train.
- Kết quả checker và lỗi đã xử lý: đã chạy tools/check_yolo_labels.py; kết quả đạt.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: đã kiểm tra 10 ảnh số 3, 4, 7, 8, 9, 15, 16, 17, 18, 19
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: quá trình rà soát và Gold QC cho thấy cần chú ý tính đầy đủ của vật thể, phân loại class và box. Các lỗi cụ thể được ghi ở phần rework bên dưới.
- Commit khóa nhãn ban đầu: 466bbaa260e4c8d539948f5efc8f9b37f22d6133

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 299 | 480  |
| FP | 191 | 3 |
| FN | 354 | 173 |
| Precision | 0.6102 | 0.9938 |
| Recall | 0.4579 | 0.7351 |
| F1 | 0.5232 | 0.8451 |
| mean_iou_of_matches | 0.7591 | 0.9887 |
| passed | false | true |

Đường dẫn tới [output annotations](../../outputs/annotation_evaluation.json).
Kết quả ban đầu chưa đạt ngưỡng F1 tối thiểu 0.80. Sau rework, F1 đạt 0.8451 và mean IoU đạt 0.9887, đáp ứng hai ngưỡng trong rubric.

Các ảnh đã rework
- drive_003.jpg: đổi class từ car thành van.
- drive_004.jpg: đổi class từ car thành truck.
- drive_007.jpg: đổi class từ van thành truck.
- drive_008.jpg: đổi class từ truck thành bus.
- drive_009.jpg: bổ sung một xe truck bị khuất phía sau xe bus.
- drive_015.jpg: bổ sung hai xe car bị thiếu.
- drive_016.jpg: bổ sung năm xe car bị thiếu.
- drive_017.jpg: bổ sung một xe car bị khuất.
- drive_018.jpg: bổ sung một xe truck.
- drive_019.jpg: bổ sung một xe bus bị khuất.

Các thay đổi gồm sửa class và bổ sung đối tượng bị bỏ sót hoặc bị che khuất. Không suy luận rằng toàn bộ FP/FN đều đến từ một nguyên nhân cụ thể; các quyết định rework ở trên dựa trên việc kiểm tra từng ảnh và đối chiếu guideline.

## 5. Kết quả YOLO

- Runtime Google Colab, CPU, phiên bản Ultralytics 8.4.145 và Torch 2.11.0+cpu
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi; giữ nguyên cấu hình notebook.
- Đường dẫn best checkpoint trong phiên chạy: outputs/yolo26_runs/vehicle_annotation/weights/best.pt trong môi trường Colab. Checkpoint không được đưa lên repo theo quy định nộp bài.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5257 |
| Recall | 0.4348 |
| mAP50 | 0.4778 |
| mAP50–95 | 0.2956 |

[assets/eval_predictions](../../report/individual/assets/eval_predictions) và [assets/yolo26_runs](../../report/individual/assets/yolo26_runs)
Ví dụ tại [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg), ảnh có nhiều phương tiện và xe buýt. Model có một số dự đoán car trên các vùng xe buýt ở phần dưới ảnh, trong khi một xe buýt khác ở phía trên bên phải được dự đoán là bus. Đây là ví dụ cho thấy model chưa phân biệt ổn định class trong cùng một ảnh.
Confusion matrix trong assets/yolo26_runs/vehicle_eval/ cho thấy ở lượt đánh giá này, các lớp truck và bus không có true positive trên ma trận; do đó recall của cả hai lớp bằng 0. Không nên chỉ dựa vào một ảnh đơn lẻ để kết luận về toàn bộ lớp, nhưng drive_053.jpg là một ví dụ trực quan về nhầm lẫn class liên quan đến xe buýt.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
Theo confusion matrix, truck và bus cùng có recall bằng 0, là hai lớp thấp nhất trong lượt đánh giá này. Ảnh drive_053.jpg minh họa việc một số vùng xe buýt bị dự đoán thành car, dù model cũng nhận diện đúng bus ở một vị trí khác trong ảnh.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
Model có thể định vị tương đối đúng để đạt IoU ở ngưỡng 0.50, nhưng box chưa đủ khớp khi đánh giá ở các ngưỡng IoU cao hơn. Cần kiểm tra box quá rộng/quá hẹp, lệch khỏi vật thể, hoặc chưa ôm sát biên phần phương tiện nhìn thấy.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
Có dấu hiệu nhầm lẫn giữa các class có hình dáng gần nhau. Các thay đổi trong rework gồm car → van ở drive_003, car → truck ở drive_004, van → truck ở drive_007 và truck → bus ở drive_008. Các trường hợp này cho thấy cần áp dụng nhất quán định nghĩa class: car bao gồm pickup theo quy ước bài; truck là xe chở hàng thường có khoang hàng riêng; van có khoang hàng/hành khách liền cabin; bus được thiết kế để chở nhiều hành khách. Cần phân loại dựa vào cấu trúc và công năng, không chỉ màu sắc, góc chụp hoặc kích thước.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
Không dùng eval để train nhằm giữ dữ liệu đánh giá độc lập với dữ liệu huấn luyện, tránh rò rỉ dữ liệu và kết quả đánh giá lạc quan giả tạo. Trong notebook, 10 ảnh này được giữ ở vai trò validation/evaluation để theo dõi và đo Precision, Recall, mAP50, mAP50–95 của model sau huấn luyện; chúng không được đưa vào tập train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Trong quá trình thực hiện | Không gặp lỗi kỹ thuật đáng kể | Thực hiện theo các bước trong repo và notebook; dùng ChatGPT để rà soát và lập kế hoạch hoàn thiện | Hoàn thành các bước thực hiện; tiếp tục chốt báo cáo và đẩy bản final lên GitHub |

## 7. Tự đánh giá theo rubric

- Hoàn thành và định dạng YOLO (10 điểm): có 50 file nhãn train; checker đã chạy và kết quả đạt.
- Đầy đủ vật thể (20 điểm): đã self-QC 10 ảnh đầu và rework các ảnh Gold QC có đối tượng bị thiếu; kết quả FN giảm từ 354 xuống 173. Vẫn còn FN sau rework nên cần lưu ý đây là kết quả đánh giá, không khẳng định mọi đối tượng đều đã được gán đủ.
- Độ bám sát và hình học box (20 điểm): mean IoU sau rework là 0.9887, vượt ngưỡng 0.70.
Phân lớp nhất quán (15 điểm): đã ghi nhận và sửa một số trường hợp nhầm class; cần tiếp tục tuân thủ các định nghĩa class trong guideline.
- Mini guideline và quyết định mơ hồ (10 điểm): có định nghĩa bốn class, quy tắc box và ba trường hợp mơ hồ kèm quyết định/lý do; một số trường hợp cần giảng viên xác nhận thêm.
- Gold-set annotation QC (15 điểm): đã có đánh giá trước/sau rework; F1 tăng từ 0.5232 lên 0.8451 và mean IoU sau sửa là 0.9887.
- Pipeline YOLO và phân tích (10 điểm): notebook đã chạy trên CPU; có metrics và ảnh eval, đã phân tích lỗi class. Model có kết quả còn hạn chế, đặc biệt recall của truck và bus bằng 0 theo confusion matrix.

## 8. Công cụ AI và tài liệu tham khảo

- ChatGPT: hỗ trợ rà soát yêu cầu bài tập, giải thích các bước cần thực hiện, lập kế hoạch hoàn thiện báo cáo. Người làm bài đã đối chiếu nội dung với file, kết quả chạy và ảnh đầu ra thực tế.
- CVAT: sử dụng để gán nhãn và chỉnh sửa bounding box cho dữ liệu train.
- Google Colab và Ultralytics YOLO26: sử dụng để chạy notebook huấn luyện và đánh giá model trên CPU.

Tài liệu tham khảo
- Repository bài tập và các file hướng dẫn: K5-L2-Day02-DoTuanLong-2A202604066-ObjectDetection.
- [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).
- [RUBRIC.md](../../RUBRIC.md).
- [SUBMISSION.md](../../SUBMISSION.md).
- Notebook day2_annotation_to_yolo.ipynb.