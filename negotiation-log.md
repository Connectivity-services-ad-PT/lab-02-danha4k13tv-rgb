# BIÊN BẢN ĐÀM PHÁN HỢP ĐỒNG API

**Provider:** A7 – Notification Service
**Consumer:** A6 – Core Business Service
**Phiên bản:** v1.0
**Ngày:** [1/6/2026]

---

## Issue #1

**Đề xuất bởi:** Consumer

**Endpoint:** POST /events/alert.created

**Vấn đề:**
Consumer chưa rõ trường `severity` có bắt buộc hay không và các giá trị hợp lệ là gì.

**Đề xuất:**
Quy định `severity` là trường bắt buộc với các giá trị chuẩn:

* LOW
* MEDIUM
* HIGH
* CRITICAL

**Kết quả:**
Accepted (Chấp nhận)

**Lý do:**
Mức độ nghiêm trọng của cảnh báo giúp Notification Service xác định mức ưu tiên và lựa chọn phương thức gửi phù hợp.

**Tác động:**
Consumer phải kiểm tra và cung cấp giá trị `severity` hợp lệ trước khi gửi sự kiện.

---

## Issue #2

**Đề xuất bởi:** Consumer

**Endpoint:** POST /events/alert.created

**Vấn đề:**
Khi xảy ra lỗi mạng hoặc timeout, việc gửi lại yêu cầu có thể làm phát sinh sự kiện trùng lặp.

**Đề xuất:**
Sử dụng `eventId` làm khóa idempotency để đảm bảo cùng một sự kiện chỉ được xử lý một lần.

**Kết quả:**
Accepted (Chấp nhận)

**Lý do:**
Cho phép Consumer gửi lại yêu cầu một cách an toàn mà không tạo ra các bản ghi hoặc thông báo trùng lặp.

**Tác động:**
Provider phải kiểm tra sự tồn tại của `eventId` trước khi xử lý sự kiện.

---

## Issue #3

**Đề xuất bởi:** Provider

**Endpoint:** POST /events/alert.created

**Vấn đề:**
Consumer có thể không gửi trường `channels` trong một số trường hợp.

**Đề xuất:**
Nếu không có trường `channels`, Notification Service sẽ tự động lựa chọn kênh gửi mặc định dựa trên chính sách cấu hình của hệ thống.

**Kết quả:**
Modified (Chấp nhận có điều chỉnh)

**Lý do:**
Đảm bảo tính linh hoạt cho Consumer nhưng vẫn duy trì khả năng gửi thông báo hiệu quả.

**Tác động:**
Provider cần xây dựng cơ chế lựa chọn kênh mặc định và ghi nhận kênh đã sử dụng trong kết quả xử lý.

---

## Issue #4

**Đề xuất bởi:** Consumer

**Endpoint:** GET /events/{id}

**Vấn đề:**
Chưa có quy định thống nhất về trạng thái của sự kiện khi thực hiện tra cứu.

**Đề xuất:**
Chuẩn hóa các trạng thái xử lý như sau:

* PENDING
* PROCESSING
* SUCCESS
* FAILED

**Kết quả:**
Accepted (Chấp nhận)

**Lý do:**
Giúp Consumer dễ dàng theo dõi tiến trình xử lý của sự kiện.

**Tác động:**
Provider phải áp dụng thống nhất các giá trị trạng thái đã thỏa thuận.

---

## Issue #5

**Đề xuất bởi:** Provider

**Endpoint:** POST /events/alert.created

**Vấn đề:**
Chưa xác định rõ mã lỗi chuẩn khi dữ liệu gửi lên không hợp lệ hoặc vi phạm quy tắc nghiệp vụ.

**Đề xuất:**

* Trả về HTTP 400 Bad Request đối với lỗi sai cấu trúc hoặc không đúng schema.
* Trả về HTTP 422 Unprocessable Entity đối với lỗi nghiệp vụ.

**Kết quả:**
Accepted (Chấp nhận)

**Lý do:**
Giúp phân biệt rõ lỗi dữ liệu đầu vào và lỗi nghiệp vụ, hỗ trợ xử lý lỗi chính xác hơn.

**Tác động:**
Consumer cần triển khai xử lý riêng cho từng loại lỗi.

---

## Issue #6

**Đề xuất bởi:** Consumer

**Endpoint:** POST /events/alert.created

**Vấn đề:**
Chưa xác định rõ Notification Service xử lý theo cơ chế đồng bộ hay bất đồng bộ.

**Đề xuất:**
API chỉ xác nhận đã tiếp nhận yêu cầu, sau đó xử lý bất đồng bộ thông qua hàng đợi (Queue). Consumer có thể tra cứu trạng thái xử lý bằng endpoint:

GET /events/{id}

**Kết quả:**
Accepted (Chấp nhận)

**Lý do:**
Phù hợp với kiến trúc hướng sự kiện (Event-Driven Architecture) và giảm thời gian chờ của Consumer.

**Tác động:**
Consumer không được giả định rằng thông báo đã được gửi thành công ngay sau khi nhận phản hồi từ API mà phải kiểm tra trạng thái xử lý khi cần thiết.

---

# Chốt hợp đồng v1.0

Provider sign-off: Notification Team  
Consumer sign-off: Core Business Team  
Witness (GV/TA): ____________________  
Date: 20/05/2026

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
| Missing example response | Chưa kịp bổ sung đầy đủ example | Bổ sung ở phiên v1.1 |
| Enum description chưa chi tiết | Không ảnh hưởng validate | Hoàn thiện documentation |