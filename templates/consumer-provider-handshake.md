# Consumer-Provider Handshake – team-gate

## 1. Thông tin nhóm

| | Provider | Consumer |
|---|---|---|
| **Tên nhóm** | Nhóm 5 (Access Gate) | Nhóm 14 (Core Business) |
| **Service** | Smart Campus Access Gate | Smart Campus Core Business |
| **Contract file** | `contracts/team-gate.openapi.yaml` | – |

---

## 2. Danh sách endpoint được dùng

| Endpoint | Method | Mô tả | Consumer sử dụng |
|---|---|---|---|
| `/access/check` | POST | Gửi cardId + gateId để xin quyết định mở cổng | Core Business |
| `/access/logs/recent` | GET | Core Business lấy log audit gần đây | Core Business |
| `/access/logs/{logId}` | GET | Core Business lấy chi tiết log theo id | Core Business |
| `/gates/{gateId}/status` | GET | Truy vấn trạng thái gate | Core Business |
| `/cards/{cardId}` | GET | Lấy thông tin thẻ | Core Business |
| `/policies/access/{policyId}` | GET | Lấy policy truy cập | Core Business |
| `/decisions/{decisionId}` | GET | Lấy lịch sử quyết định | Core Business |
| `/health` | GET | Health check | Core Business / CI pipeline |

---

## 3. Schema thoả thuận

### AccessCheckRequest
```json
{
  "cardId": "string (required)",
  "gateId": "string (required)",
  "timestamp": "string ISO-8601 (optional)"
}
```

### AccessDecision (ALLOW)
```json
{
  "decisionType": "ALLOW",
  "decisionId": "uuid",
  "allow": true,
  "policyId": "string (optional)",
  "expiresAt": "string | null"
}
```

### AccessDecision (DENY)
```json
{
  "decisionType": "DENY",
  "decisionId": "uuid",
  "allow": false,
  "reasonCode": "string"
}
```

### Error (ProblemDetails)
```json
{
  "type": "uri string",
  "title": "string",
  "status": "integer 400-599",
  "detail": "string | null",
  "instance": "string | null"
}
```

---

## 4. Điều kiện thoả thuận (handshake rules)

- Provider **phải** trả `decisionType` là `ALLOW` hoặc `DENY` – không có giá trị nào khác.
- Provider **phải** trả lỗi dạng `application/problem+json` cho tất cả response `4xx` và `5xx`.
- Consumer **phải** gửi `Authorization: Bearer <token>` cho tất cả request trừ `/health`.
- Consumer **phải** xử lý được cả hai nhánh của `AccessDecision` (discriminator `decisionType`).
- Khi provider trả `404`, consumer không được crash – phải xử lý gracefully.

---

## 5. Mock server

| Môi trường | URL | Chạy bằng |
|---|---|---|
| Mock (Prism) | `http://localhost:4010` | `npm run mock:gate` |
| Local service | `http://localhost:8000` | service thật |

---

## 6. Xác nhận

| | Ký tên / Ngày |
|---|---|
| Provider (team-gate) đã review contract | ________ |
| Consumer (team-core) đã review contract | ________ |
| Consumer-side smoke test đã chạy pass | ________ |