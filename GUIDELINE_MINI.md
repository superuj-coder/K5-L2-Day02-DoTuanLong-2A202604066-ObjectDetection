# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Các phương tiện ô tô chở người hoặc phương tiện cá nhân có kiểu dáng xe con, bao gồm cả xe bán tải theo quy ước của bài tập. | Sedan, SUV, taxi, hatchback, pickup. Pickup được xếp vào car, không phải truck. |
| `truck` | Các phương tiện được thiết kế chủ yếu để vận chuyển hàng hóa, thường có kích thước lớn và khoang chở hàng riêng biệt. | Xe tải thùng, xe tải ben, xe đầu kéo, xe container. Phân biệt với van dựa trên cấu trúc và mục đích chở hàng. |
| `bus` | Các phương tiện được thiết kế chủ yếu để vận chuyển hành khách với sức chứa lớn hơn xe con hoặc xe van. | Xe buýt công cộng, xe khách, xe buýt trường học, minibus. |
| `van` | Các phương tiện có thân xe dạng hộp, kích thước trung bình, thường có khoang hành khách hoặc khoang chở hàng liền với cabin. | Xe van chở hàng, xe van chở khách, minivan. Phân biệt với truck dựa trên cấu trúc thân xe và khoang chở hàng. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Vẽ bounding box bao quanh toàn bộ phần phương tiện có thể nhìn thấy, bao gồm thân xe và các bộ phận thuộc về xe. Box phải sát với biên ngoài của phương tiện, hạn chế tối đa phần nền hoặc mặt đường bị đưa vào. Không cố ý mở rộng box ra ngoài vật thể.

- Vật thể bị che khuất: Vẫn gán nhãn nếu có thể nhận diện phương tiện và xác định tương đối rõ lớp của nó. Bounding box chỉ bao quanh phần phương tiện thực tế nhìn thấy, không ước lượng hoặc vẽ thêm phần bị vật thể khác che khuất.

- Vật thể bị cắt bởi rìa ảnh: Vẫn gán nhãn nếu phần phương tiện còn nhìn thấy đủ để nhận diện và phân loại. Bounding box kết thúc tại rìa ảnh, không mở rộng ra ngoài phạm vi hình ảnh.

- Vật thể rất nhỏ/mơ hồ: Chỉ gán nhãn khi có đủ đặc điểm để xác định đây là phương tiện và phân biệt được lớp. Không đoán lớp khi hình ảnh quá mờ, quá nhỏ hoặc bị che khuất nghiêm trọng. Ưu tiên tính chính xác và nhất quán thay vì gán nhãn thiếu căn cứ.

- Nguyên tắc một đối tượng — một box: Mỗi phương tiện độc lập được gán một bounding box riêng. Không gộp nhiều phương tiện vào cùng một box, kể cả khi chúng nằm sát nhau.

- Thứ tự ưu tiên phân loại: Dựa vào hình dáng tổng thể, cấu trúc thân xe và mục đích sử dụng đặc trưng theo schema. Không phân loại chỉ dựa trên màu sắc hoặc góc chụp.

- Định dạng nhãn: Mỗi đối tượng được biểu diễn bằng một dòng theo định dạng YOLO: class_id x_center y_center width height. Tọa độ được chuẩn hóa trong khoảng [0, 1]. Class ID lần lượt là car = 0, truck = 1, bus = 2, van = 3.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_014.png` | xe kéo | van | xe kéo tuy ko có khoang chở hàng nhưng kích thước tương tự xe van |
| `drive_026.jpg` | ko rõ xe gì | xe tải | có độ lớn khá giống xe tải |
| `drive_044.jpg` | xe con bị mờ và bị che bởi bụi cây | xe con | vẫn có hình dạng của xe con |
