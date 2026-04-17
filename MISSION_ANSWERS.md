# Day 12 Lab - Trả lời nhiệm vụ

## Part 1: Localhost vs Production

### Exercise 1.1: Các anti-pattern tìm thấy
1. Hardcode thông tin bí mật trong code (`OPENAI_API_KEY`, `DATABASE_URL`) dễ bị lộ khi push lên Git.
2. Không có quản lý cấu hình: `DEBUG`, `MAX_TOKENS` bị đóng đinh trong code.
3. Dùng `print()` để log và còn log cả secret ra console.
4. Không có endpoint health check để platform theo dõi và tự restart.
5. Cố định host/port (`localhost:8000`) và `reload=True`, không phù hợp deploy cloud/container.

### Exercise 1.3: Bảng so sánh
| Feature | Develop | Production | Vì sao quan trọng? |
|---------|---------|------------|--------------------|
| Config | Hardcode trong code | Biến môi trường (`.env`) | Bảo mật secret và dễ thay đổi cấu hình theo môi trường mà không sửa code. |
| Health check | Không có | `/health` và `/ready` | Giúp cloud phát hiện lỗi và chỉ route traffic khi app sẵn sàng. |
| Logging | `print()` | Logging JSON có cấu trúc | Dễ lọc/phân tích log và tránh lộ secret. |
| Shutdown | Đột ngột | Graceful shutdown | Hoàn thành request đang chạy và cleanup trước khi thoát. |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image là nền tảng (OS + runtime) để container chạy ứng dụng.
2. `WORKDIR` là thư mục làm việc mặc định bên trong container.
3. Copy `requirements.txt` trước để tận dụng Docker layer cache, tránh phải cài lại dependencies khi code thay đổi.
4. `CMD` là lệnh mặc định có thể bị ghi đè; `ENTRYPOINT` là lệnh “cứng”, tham số sau `docker run` sẽ được thêm làm đối số.

### Exercise 2.2: Build & run (develop)
- Kết quả gọi API `/ask`: "Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận."
- Kích thước image (docker images): Disk Usage ~ 1.66 GB, Content Size ~ 424 MB.

### Exercise 2.3: Multi-stage build
1. Stage 1 (builder) dùng `python:3.11-slim`, cài build tools (`gcc`, `libpq-dev`) và cài dependencies vào `/root/.local` bằng `pip install --user`.
2. Stage 2 (runtime) tạo `appuser`, copy packages từ builder sang `/home/appuser/.local`, copy source + utils, thiết lập `PATH`, `HEALTHCHECK` và chạy `uvicorn`.
3. Image nhỏ hơn vì bỏ lại build tools/cache ở stage 1 và chỉ giữ runtime + source ở stage 2.
	- Develop: Disk Usage ~ 1.66 GB, Content Size ~ 424 MB.
	- Production: Disk Usage ~ 236 MB, Content Size ~ 56.6 MB.

### Exercise 2.4: Docker Compose stack
1. Các service và nhiệm vụ:
	- `agent`: AI agent FastAPI, xử lý logic chính và gọi các dịch vụ phụ trợ.
	- `redis`: cache cho session và rate limiting.
	- `qdrant`: vector database phục vụ RAG.
	- `nginx`: reverse proxy/load balancer, nhận request từ bên ngoài.
2. Luồng giao tiếp:
	- Client -> `nginx` (cổng 80/443).
	- `nginx` proxy vào `agent` trong mạng nội bộ.
	- `agent` dùng `redis` (session/rate limit) và `qdrant` (vector search).
	- Kết quả trả ngược về `nginx` rồi trả cho client.
3. Architecture diagram (ASCII):

```
Internet
   |
   v
+---------+         internal network: internal
| Client  |---------------------------------------------+
+---------+                                             |
   |                                                   |
   v                                                   |
+---------+     :80/:443                               |
|  Nginx  |-------------------------------------------+ |
+---------+                                           | |
   |                                                   | |
   v                                                   | |
+---------+     :8000                                  | |
|  Agent  |--------------------------------------+      | |
| FastAPI |                                      |      | |
+---------+                                      |      | |
   |   |                                         |      | |
   |   +--------------+                          |      | |
   |                  |                          |      | |
   v                  v                          |      | |
+-------+         +--------+                     |      | |
| Redis |         | Qdrant |                     |      | |
| cache |         | vector |                     |      | |
+-------+         +--------+                     |      | |
   |                  |                          |      | |
   v                  v                          |      | |
redis_data       qdrant_data                     |      | |
  (volume)         (volume)                      |      | |
                                                     | |
Note: Agent khong expose port ra ngoai, chi Nginx nhan request tu internet.
```

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://agent-railway-production.up.railway.app
- Health check: `ok` (GET /health)
- Ask endpoint: "Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthro…" (POST /ask).
