# Phân tích yêu cầu — vai Provider

- Cặp đàm phán: Pair 04 — Core Business → Notification
- Product: A7
- Provider service: Notification Service
- Consumer service: Core Business Service
- Người viết: Nguyễn Thanh Danh
- Ngày: 20/05/2026

---

## 1. Resource chính

| Resource | Mô tả | Thuộc tính bắt buộc | Thuộc tính tùy chọn |
|---|---|---|---|
| `AlertEvent` | Event cảnh báo do Core Business gửi để Notification xử lý | eventId, alertId, severity, message, timestamp, correlationId | channels |
| `NotificationResult` | Kết quả xử lý gửi thông báo nội bộ | status, processedAt | deliveryDetails |

---

## 2. Action/API dự kiến

| Method | Path | Mục đích | Consumer gọi khi nào? |
|---|---|---|---|
| POST | `/events/alert.created` | Gửi event khi alert mới được tạo | Khi hệ thống phát hiện cảnh báo mới |
| GET | `/events/{id}` | Kiểm tra trạng thái event đã xử lý | Khi cần tra cứu trạng thái xử lý |

---

## 3. Error case

Tối thiểu 5 case.

| Status | Tình huống | Response body dự kiến |
|---:|---|---|
| 400 | Payload sai định dạng | `Problem` |
| 401 | Thiếu Bearer token | `Problem` |
| 403 | Token hợp lệ nhưng không có quyền | `Problem` |
| 404 | Resource không tồn tại | `Problem` |
| 409 | Xung đột nghiệp vụ | `Problem` |
| 422 | Dữ liệu đúng JSON nhưng vi phạm nghiệp vụ | `Problem` |

---

## 4. Giả định bổ sung

Ghi rõ những điểm user story chưa nói nhưng Provider cần giả định.

- Giả định 1: severity là bắt buộc để Notification xác định mức ưu tiên gửi.
- Giả định 2: eventId được dùng làm idempotency key để tránh xử lý trùng.
- Giả định 3: Notification tự quyết định routing channel nếu Consumer không chỉ định.

---

## 5. Câu hỏi cho Consumer

1. Consumer có luôn gửi severity hay có thể để Notification tự suy luận?
2. Consumer có cần chỉ định channel (email/Telegram/app) hay Notification tự chọn?
3. Khi gửi lỗi downstream, Consumer muốn retry từ queue hay Notification tự retry?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Tên field không thống nhất | Consumer parse lỗi | Chốt naming trong `openapi.yaml` |
| Payload lớn | Timeout/mock lỗi | Thống nhất content-type và size limit |