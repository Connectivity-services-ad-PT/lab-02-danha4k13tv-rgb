# Biên bản đàm phán hợp đồng API

- Cặp đàm phán: Pair 04 — Core Business ↔ Notification
- Product: A7
- Provider: Notification Service
- Consumer: Core Business Service
- Phiên: v1.0
- Ngày: 20/05/2026

---

## Issue #1

- Raised by: Consumer
- Endpoint: POST /events/alert.created
- Concern: Consumer chưa rõ `severity` có bắt buộc hay không.
- Proposal: Quy định `severity` là field bắt buộc với các giá trị chuẩn (LOW, MEDIUM, HIGH, CRITICAL).
- Resolution: Accepted
- Rationale: Severity giúp Notification định tuyến và chọn mức ưu tiên gửi.
- Impact: Consumer phải validate dữ liệu trước khi gửi.

---

## Issue #2

- Raised by: Consumer
- Endpoint: POST /events/alert.created
- Concern: Nếu retry request có thể tạo event trùng.
- Proposal: Sử dụng `eventId` làm idempotency key để chống xử lý trùng.
- Resolution: Accepted
- Rationale: Đảm bảo retry an toàn khi network lỗi.
- Impact: Provider phải kiểm tra eventId trước khi xử lý.

---

## Issue #3

- Raised by: Provider
- Endpoint: POST /events/alert.created
- Concern: Consumer không phải lúc nào cũng gửi `channels`.
- Proposal: Nếu thiếu `channels`, Provider tự chọn kênh mặc định.
- Resolution: Modified
- Rationale: Cho phép linh hoạt nhưng vẫn phải ghi rõ channel được chọn trong kết quả xử lý.
- Impact: Provider cần bổ sung logic chọn channel mặc định.

---

## Issue #4

- Raised by: Consumer
- Endpoint: GET /events/{id}
- Concern: Chưa rõ event trả về trạng thái gì khi tra cứu.
- Proposal: Chuẩn hóa status gồm PENDING, PROCESSING, SUCCESS, FAILED.
- Resolution: Accepted
- Rationale: Consumer dễ theo dõi tiến trình xử lý event.
- Impact: Provider phải thống nhất enum status.

---

## Issue #5

- Raised by: Provider
- Endpoint: POST /events/alert.created
- Concern: Payload sai schema nhưng chưa rõ mã lỗi chuẩn.
- Proposal: Dùng HTTP 400 cho schema lỗi và 422 cho business rule lỗi.
- Resolution: Accepted
- Rationale: Tách biệt lỗi cú pháp và lỗi nghiệp vụ.
- Impact: Consumer xử lý lỗi chính xác hơn.

---

## Issue #6

- Raised by: Consumer
- Endpoint: POST /events/alert.created
- Concern: Không rõ Notification xử lý đồng bộ hay bất đồng bộ.
- Proposal: API chỉ trả accepted, xử lý bất đồng bộ qua queue; Consumer tra cứu bằng GET /events/{id}.
- Resolution: Accepted
- Rationale: Phù hợp với kiến trúc event-driven.
- Impact: Consumer không chờ kết quả gửi ngay lập tức.

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