# Reliability Checklist – team-gate

## Contract

- [x] Contract có ít nhất một response `2xx` cho mỗi operation
- [x] Contract có ít nhất một response `4xx` cho mỗi operation
- [x] Error response dùng cấu trúc `ProblemDetails` (`type`, `title`, `status`)
- [x] `ProblemDetails.status` có `minimum: 400` và `maximum: 599`
- [x] Query parameter `limit` có `minimum: 1` và `maximum: 100` và `default: 10`
- [x] Enum field `direction` ràng buộc rõ: `IN`, `OUT`
- [x] Enum field `status` (AccessLog) ràng buộc rõ: `ALLOW`, `DENY`
- [x] Enum field `status` (GateStatus) ràng buộc rõ: `OPEN`, `CLOSED`, `MAINTENANCE`
- [x] Enum field `cardStatus` ràng buộc rõ: `ACTIVE`, `BLOCKED`, `EXPIRED`
- [x] `AccessDecision` dùng `discriminator` trên `decisionType`

## Contract Lint

- [x] Contract lint pass: `Woohoo! Your API description is valid`
- [x] 1 warning còn lại: `no-server-example.com` – không thể tránh vì môi trường lab dùng Prism mock tại `localhost:4010`
- [x] Lint report lưu tại `reports/contract-lint-report.txt`

## Postman Collection

- [x] Không hardcode `baseUrl` – dùng `{{baseUrl}}`
- [x] Không hardcode `authToken` – dùng `{{authToken}}`
- [x] Không hardcode `coreMockUrl` – dùng `{{coreMockUrl}}`
- [x] Collection có đủ 6 folder bắt buộc
- [x] Mỗi request có ít nhất một `pm.test`
- [x] Happy path test (folder 01) kiểm tra status code và response body
- [x] Auth test (folder 02) dùng request thiếu/sai token thật – không dùng `Prefer: code=401`
- [x] Negative test (folder 03) kiểm tra response body ProblemDetails
- [x] Boundary test (folder 04) kiểm tra response từ server
- [x] Consumer-side smoke test (folder 05) gọi `{{coreMockUrl}}`
- [x] Local-only test (folder 06) guard bằng `if (pm.environment.get('env') === 'local')`

## Environment

- [x] Environment `mock`: `env=mock`, `baseUrl=localhost:4010`, `authToken=lab-token`, `coreMockUrl=localhost:4010`
- [x] Environment `local`: `env=local`, `baseUrl=localhost:8000`, `authToken=local-dev-token`, `coreMockUrl=localhost:4010`

## Newman & CI

- [x] `npm run test:mock`: **30 requests, 54 assertions, 0 failures, 0 errors**
- [x] `npm run test:local`: fail do service thật chưa implement – xem ghi chú bên dưới
- [x] Report XML và HTML trong thư mục `reports/`
- [x] Contract lint pass với Redocly CLI (1 warning localhost – chấp nhận)
- [x] GitHub Actions workflow tại `.github/workflows/newman.yml`

### Ghi chú về local environment

`npm run test:local` thất bại với lỗi `ECONNREFUSED 127.0.0.1:8000` vì service thật
chưa được implement trong phạm vi Lab 03. Collection và environment local đã được
cấu hình đúng – toàn bộ test logic có guard `[local]` sẽ tự động kích hoạt khi
service thật chạy tại `localhost:8000`.

## Test-case Matrix

- [x] Matrix có đủ cột: folder, endpoint, method, input, expected_status, test_type
- [x] Mỗi request trong collection map được với ít nhất một dòng trong matrix

## Consumer-Provider Handshake

- [x] Đã xác định provider (team-gate) và consumer (team-core)
- [x] Đã liệt kê đủ endpoint được dùng
- [x] Đã thoả thuận schema tối thiểu
- [x] Đã ghi rõ điều kiện handshake rules