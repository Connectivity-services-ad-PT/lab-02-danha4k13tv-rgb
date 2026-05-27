# Phân tích yêu cầu — vai Consumer

- Cặp đàm phán: Pair 04 — Core Business → Notification
- Product: A7
- Consumer service: Core Business Service
- Provider service: Notification Service
- Người viết: Dương Công Hảo
- Ngày: 20/05/2026

---

## 1. Resource Consumer cần nhận/gửi

| Resource | Consumer dùng để làm gì? | Field bắt buộc với Consumer | Field có thể tùy chọn |
|---|---|---|---|
| `AlertEvent` | Gửi event cảnh báo để Notification xử lý và gửi thông báo | eventId, alertId, severity, message, timestamp, correlationId | channels |
| `NotificationResult` | Nhận trạng thái xử lý hoặc tra cứu kết quả gửi thông báo | status, processedAt | deliveryDetails |

---

## 2. API Consumer cần gọi

| Method | Path | Lúc nào gọi? | Kỳ vọng response |
|---|---|---|---|
| POST | `/events/alert.created` | Khi có cảnh báo mới cần gửi thông báo | Event được queue và chấp nhận xử lý |
| GET | `/events/{id}` | Khi cần tra cứu trạng thái xử lý của event | Trả về trạng thái xử lý event |

---

## 3. Error case Consumer cần xử lý

Tối thiểu 5 case.

| Status | Consumer hiểu là gì? | Consumer sẽ xử lý thế nào? |
|---:|---|---|
| 400 | Request sai schema | Sửa payload/log lỗi |
| 401 | Thiếu token | Refresh/cấu hình token |
| 403 | Không đủ quyền | Báo lỗi quyền truy cập |
| 404 | Không tìm thấy resource | Hiển thị trạng thái không tồn tại |
| 409 | Xung đột nghiệp vụ | Retry hoặc yêu cầu thao tác lại |
| 422 | Vi phạm rule nghiệp vụ | Hiển thị lý do cụ thể |

---

## 4. Giả định bổ sung

- Giả định 1: Notification xử lý event bất đồng bộ và không trả kết quả gửi ngay lập tức.
- Giả định 2: eventId được dùng để tránh xử lý trùng khi retry.
- Giả định 3: Notification có thể tự chọn channel nếu Consumer không truyền danh sách channel.

---

## 5. Câu hỏi cho Provider

1. Notification có bắt buộc severity để định tuyến hay có thể tự suy luận?
2. Notification có retry downstream delivery nội bộ hay phụ thuộc queue?
3. Nếu event bị trùng eventId thì Notification trả trạng thái gì?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Provider đổi kiểu dữ liệu | Consumer parse lỗi | Chốt type/format/pattern |
| Provider thiếu mã lỗi | Consumer khó xử lý lỗi | Chuẩn hóa Problem Details |