# Reliability Checklist – team-gate

## Contract

- [x] Contract có ít nhất một response `2xx` cho mỗi operation
- [x] Contract có ít nhất một response `4xx` cho mỗi operation (trừ `/health`)
- [x] Error response dùng cấu trúc `ProblemDetails` (`type`, `title`, `status`)
- [x] `ProblemDetails.status` có `minimum: 400` và `maximum: 599`
- [x] Query parameter `limit` có `minimum: 1` và `maximum: 100` và `default: 10`
- [x] Enum field `direction` ràng buộc rõ: `IN`, `OUT`
- [x] Enum field `status` (AccessLog) ràng buộc rõ: `ALLOW`, `DENY`
- [x] Enum field `status` (GateStatus) ràng buộc rõ: `OPEN`, `CLOSED`, `MAINTENANCE`
- [x] Enum field `cardStatus` ràng buộc rõ: `ACTIVE`, `BLOCKED`, `EXPIRED`
- [x] `AccessDecision` dùng `discriminator` trên `decisionType`

## Postman Collection

- [x] Không hardcode `baseUrl` trong collection – dùng `{{baseUrl}}`
- [x] Không hardcode `authToken` trong collection – dùng `{{authToken}}`
- [x] Không hardcode `coreMockUrl` trong collection – dùng `{{coreMockUrl}}`
- [x] Collection có đủ 6 folder bắt buộc
- [x] Mỗi request có ít nhất một `pm.test`
- [x] Happy path test (folder 01) đã kiểm tra status code và response body
- [x] Auth test (folder 02) dùng request thiếu/sai token thật – không dùng `Prefer: code=401`
- [x] Negative test (folder 03) kiểm tra response body ProblemDetails, không chỉ status code
- [x] Boundary test (folder 04) kiểm tra response từ server, không chỉ request body đã gửi
- [x] Consumer-side smoke test (folder 05) gọi `coreMockUrl` (service phụ thuộc), không gọi lại chính mình
- [x] Local-only test (folder 06) guard bằng `if (pm.environment.get('env') === 'local')`

## Environment

- [x] Environment `mock` có đủ các biến: `env`, `baseUrl`, `authToken`, `teamName`, `coreMockUrl`
- [x] Environment `local` có đủ các biến: `env`, `baseUrl`, `authToken`, `teamName`, `coreMockUrl`
- [x] `baseUrl` khác nhau giữa mock và local

## Newman & CI

- [ ] Collection chạy được bằng `npm run test:mock` không có lỗi
- [ ] Collection chạy được bằng `npm run test:local` (hoặc ghi chú rõ phần chưa hoàn thiện)
- [ ] Report XML và HTML được sinh ra trong thư mục `reports/`
- [ ] Contract lint pass (Spectral hoặc Redocly)
- [ ] GitHub Actions workflow chạy: lint → mock → Newman → upload report

## Test-case Matrix

- [x] Matrix có đủ cột: folder, endpoint, method, input, expected_status, test_type
- [x] Mỗi request trong collection map được với ít nhất một dòng trong matrix
- [x] Tất cả 6 loại test đều có trong matrix

## Consumer-Provider Handshake

- [x] Đã xác định provider và consumer
- [x] Đã liệt kê đủ endpoint được dùng
- [x] Đã thoả thuận schema tối thiểu
- [x] Đã ghi rõ điều kiện handshake rules
- [ ] Cả hai bên đã ký xác nhận