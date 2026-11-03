# Đặc tả LibMini dự kiến

Quy tắc LibMini dưới đây do nhóm đề xuất để tạo cơ sở kiểm thử. Hướng dẫn của thầy gợi ý đề tài thư viện nhưng không quy định hạn mức hay mức phí cụ thể. Tất cả B01-B08 phải được thống nhất, phê duyệt và ghi phiên bản trước khi dùng làm đáp án kiểm thử chính thức.

Phiên bản LB-SPEC-0.1. Trạng thái: chờ nhóm và giảng viên duyệt. Áp dụng cho nguyên mẫu cục bộ, không mô tả thư viện thật.

## B01 Tài khoản

Hai vai trò: độc giả và thủ thư. Tên đăng nhập 6-20 ký tự ASCII, bắt đầu bằng chữ, còn lại chữ/số/gạch dưới; phân biệt hoa thường. Mật khẩu 8-20 ký tự, có hoa, thường, số. Phiên hết hạn sau 30 phút không hoạt động.

## B02 Sách và tra cứu

Phân biệt đầu sách book_id và bản sách copy_id. Tìm theo mã chính xác hoặc tên chứa chuỗi, không phân biệt hoa thường, hỗ trợ tiếng Việt có dấu. Chưa cam kết tìm không dấu.

## B03 Điều kiện mượn

Mỗi yêu cầu mượn đúng 1 bản. Thẻ còn hiệu lực, không có phiếu quá hạn, đang giữ dưới 3 bản và bản sách đang sẵn sàng. Phải kiểm tra nguyên tử cả giới hạn độc giả và trạng thái bản sách.

## B04 Hạn trả và ngày

Ngày đến hạn = ngày mượn + 14 ngày. Tính theo ngày lịch Asia/Ho_Chi_Minh; trả trong ngày đến hạn không bị phạt. Không trừ cuối tuần/ngày lễ.

## B05 Gia hạn

Tối đa 1 lần/phiếu, cộng 7 ngày vào hạn trả cũ. Chỉ khi phiếu đang mượn, chưa quá hạn, thẻ còn hiệu lực và đầu sách không có cờ đặt trước. Cờ đặt trước do dữ liệu thử cấu hình; không xây dựng quy trình đặt chỗ.

## B06 Trả sách

Thủ thư xác nhận trả; phiếu chuyển đã trả và bản sách sẵn sàng trong một giao dịch. Gửi trả lặp không tăng tồn kho hay cộng phí lần nữa. Chưa xử lý mất/hỏng sách.

## B07 Phí quá hạn

Ngày quá hạn = max(0, ngày trả - ngày đến hạn). Phí = min(ngày quá hạn × 2.000, 100.000) đồng cho mỗi phiếu/bản. Hàm tính phí chỉ nhận số ngày nguyên không âm; dữ liệu sai phải bị từ chối. Không thanh toán trực tuyến.

## B08 Phân quyền

Độc giả chỉ xem phiếu và phí của mình, chỉ gia hạn phiếu của mình. Thủ thư lập phiếu mượn, xác nhận trả và xem các phiếu theo nhiệm vụ. Kiểm tra quyền ở máy chủ; không thêm vai trò quản trị vào phạm vi đợt này.

## Yêu cầu chức năng để truy vết

| Mã | Yêu cầu con |
| --- | --- |
| LM01.1 | Đăng nhập bắt buộc, kiểm tra định dạng tài khoản và chính sách mật khẩu B01. |
| LM01.2 | Chỉ xác thực tài khoản hợp lệ; lỗi thông tin không tiết lộ tài khoản tồn tại. |
| LM01.3 | Phiên hết hạn sau 30 phút nhàn rỗi; thao tác hợp lệ làm mới mốc hoạt động. |
| LM01.4 | Quyền độc giả/thủ thư và sở hữu phiếu được kiểm tra tại máy chủ theo B08. |
| LM02.1 | Tìm theo mã/tên đúng B02, mã không tồn tại trả tập rỗng. |
| LM02.2 | Kết quả hiển thị mã, tên, tác giả, tổng bản và số bản sẵn sàng; chi tiết có copy_id/trạng thái. |
| LM03.1 | Chỉ thủ thư lập phiếu cho độc giả có thẻ còn hiệu lực. |
| LM03.2 | Từ chối nếu độc giả có phiếu quá hạn hoặc đang giữ đủ 3 bản. |
| LM03.3 | Chỉ mượn bản sẵn sàng, mỗi yêu cầu đúng 1 copy_id; không mượn trùng bản. |
| LM03.4 | Lập phiếu và đổi trạng thái bản sách nguyên tử; hạn trả theo B04. |
| LM03.5 | Hai yêu cầu tranh một bản chỉ 1 thành công; hai bản khác nhau cũng không làm độc giả vượt 3. |
| LM04.1 | Chỉ gia hạn phiếu đang mượn, chưa quá hạn, thẻ hợp lệ và không có cờ đặt trước. |
| LM04.2 | Tối đa 1 lần gia hạn; hạn mới = hạn cũ +7 ngày; từ chối không thay đổi dữ liệu. |
| LM05.1 | Chỉ thủ thư xác nhận trả phiếu hợp lệ, ghi ngày trả và chuyển trạng thái. |
| LM05.2 | Phiếu, bản sách và phí được cập nhật trong một giao dịch; lỗi giữa chừng phải rollback. |
| LM05.3 | Gửi lại thao tác trả không tăng tồn kho hoặc ghi phí lần nữa. |
| LM06.1 | Ngày quá hạn và phí được tính theo B04/B07; đúng hạn phí 0. |
| LM06.2 | Hàm phí từ chối ngày âm, số lẻ, không phải số; chặn trần tại 100.000 đồng. |
| LM06.3 | Hiển thị phí và dữ liệu phiếu đúng; độc giả chỉ xem phiếu mình theo LM01.4. |

## Điểm cần chốt khi duyệt

- Có chấp nhận toàn bộ B01-B08 không? Nếu thay hạn mức, kỳ hạn hoặc mức phí, cập nhật các ca và hình ma trận có liên quan.
- Thông báo lỗi chuẩn và thứ tự thông báo khi mượn/gia hạn vi phạm nhiều điều kiện cần được ghi trong bản đặc tả được duyệt.
- Xác định hành vi phản hồi khi gửi lại phiếu mượn/trả: mã lỗi hay trả bản ghi cũ; kết quả nghiệp vụ phải không bị ghi lặp.
- Giá trị cờ đặt trước chỉ là dữ liệu điều kiện gia hạn; không đưa chức năng đặt chỗ vào phạm vi nếu chưa duyệt.

## Dữ liệu seed và cách kiểm tra

Dùng tài khoản giả lập reader_a, reader_b, librarian_a và thẻ hết hạn; ít nhất 2 đầu sách có nhiều bản; phiếu giữ 2/3 bản, đúng hạn/quá hạn, đã gia hạn. Không ghi mật khẩu thật vào GitHub. Dùng reset/CSDL tạm và đồng hồ giả lập cho từng ca. Chưa có kết quả thực thi hoặc xác nhận đạt các yêu cầu này.