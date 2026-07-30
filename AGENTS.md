# [AGENTS.md](http://AGENTS.md)

> **File chứa quy tắc bắt buộc cho AI agent làm việc trong repo này.**
>
> Agent phải đọc hết file này **trước khi** đọc code hay sửa bất cứ thứ gì. Khi các quy tắc mâu thuẫn: **an toàn > đúng > gọn > nhanh**.

---

# PHẦN A — CÁCH AGENT LÀM VIỆC

## A1. Bảy điều tuyệt đối không làm

Vi phạm bất kỳ điều nào là lỗi nghiêm trọng, kể cả khi được yêu cầu "làm nhanh cho xong".

1. **Không bịa.** Không nêu tên hàm, đường dẫn, field, config, số liệu, hay hành vi thư viện mà chưa mở ra xem. Chưa kiểm chứng thì nói thẳng là chưa kiểm chứng.
2. **Không tuyên bố "xong" khi chưa chạy.** Chưa chạy được test/lệnh kiểm tra thì báo "đã viết, chưa verify", kèm lý do.
3. **Không commit secret.** API key, token, password, connection string, private key — kể cả trong file test, comment, hay `.env.example` (ở đó phải là giá trị giả).
4. **Không log PII / dữ liệu nhạy cảm.** Không log token, thông tin định danh cá nhân, nội dung do người dùng cuối tạo ra. Cần debug thì log độ dài + hash rút gọn.
5. **Không nuốt lỗi.** Cấm `except:` trần, `except Exception: pass`, `catch (e) {}`. Chỉ bắt lỗi đã biết rõ loại và xử lý có chủ đích.
6. **Không xoá/ghi đè thứ chưa đọc.** Trước khi ghi đè file, xoá thư mục, sửa migration, hay chạy lệnh phá huỷ (`rm -rf`, `git reset --hard`, `DROP`, `TRUNCATE`) — phải đọc nội dung hiện tại và hỏi trước.
7. **Không tự ý mở rộng phạm vi.** Không refactor thứ không được yêu cầu, không "tiện tay" đổi tên biến, không nâng version thư viện, không đổi format toàn file. Thấy vấn đề khác thì **báo**, đừng tự sửa.

Ngoài ra: chỉ `git commit` / `git push` / mở PR / gọi API bên ngoài **khi được yêu cầu rõ ràng**.

## A2. Quy trình bắt buộc cho mọi task

```
Hiểu  →  Khảo sát  →  Kế hoạch  →  Thay đổi nhỏ nhất  →  Kiểm chứng  →  Báo cáo trung thực
```

**Hiểu** — Diễn đạt lại yêu cầu bằng một câu trước khi làm. Hai cách hiểu dẫn tới hai kết quả khác nhau đáng kể → hỏi (A4).

**Khảo sát trước khi viết** — bắt buộc:

- Trước khi **sửa** file: đọc file đó.
- Trước khi **tạo** file/hàm/util mới: tìm xem repo đã có thứ tương tự chưa. Trùng lặp logic là lỗi.
- Trước khi **thêm thư viện**: kiểm tra dependency hiện có làm được không. Thêm dependency là quyết định cần lý do, không phải phản xạ.
- Trước khi **đặt tên**: nhìn cách repo đang đặt tên và bắt chước.

**Thay đổi nhỏ nhất** — Diff càng nhỏ càng tốt. Không đụng dòng không liên quan. Giữ nguyên style xung quanh (độ dài dòng, mật độ comment, cách import) kể cả khi bạn thích kiểu khác.

**Kiểm chứng** — Chạy lệnh kiểm tra ở C1. Không chạy được thì nói rõ đã kiểm tra tới đâu, bằng cách nào.

## A3. Chống bịa

- Khi nói về code, **trích dẫn vị trí**: `path/to/file.py:42`. Không trích dẫn được nghĩa là chưa đọc.
- Không đoán hành vi thư viện/API từ trí nhớ. Không chắc thì đọc source trong `site-packages`/`node_modules`, tra tài liệu chính thức, hoặc thử bằng một lệnh nhỏ.
- Không đưa số liệu (số dòng, thời gian chạy, coverage, chi phí) mà không đo. Đã đo thì nói đo bằng cách nào.
- Kết quả từ agent khác, log cũ, tài liệu trong repo đều **có thể sai** — đối chiếu code thật trước khi tin.
- Ước lượng phải được gọi là ước lượng.



## A4. Khi nào hỏi, khi nào tự quyết

**Tự quyết** (rồi nêu giả định trong báo cáo): đặt tên, cấu trúc file, thứ tự làm, chi tiết triển khai không đổi hành vi bên ngoài, những chỗ đã có quy ước hiển nhiên trong repo.

**Phải hỏi trước khi làm:**

- Yêu cầu có ≥ 2 cách hiểu dẫn tới sản phẩm khác hẳn nhau.
- Thao tác khó hoàn tác: xoá dữ liệu, migration phá vỡ tương thích, đổi contract API đang có người dùng, đụng vào production.
- Yêu cầu mâu thuẫn với A1.
- Thiếu thông tin mà đoán sai thì toàn bộ công sức thành vô ích.

**Cách hỏi đúng:** làm hết phần **không** phụ thuộc câu trả lời trước, rồi hỏi một câu cụ thể kèm phương án đề xuất — đừng dừng lại với hai bàn tay trắng.

## A5. Báo cáo trung thực

- Test fail thì nói fail, kèm output. Không diễn giải thành "về cơ bản đã chạy".
- Bỏ qua phần nào của yêu cầu thì **liệt kê ra**, kèm lý do. Thu hẹp phạm vi là quyết định của người dùng, không phải của agent.
- Phần chưa verify thì ghi rõ "chưa verify", đừng gộp với phần đã chạy.
- Không tự khen, không kể lể quá trình. Nói: đã đổi gì, ở đâu, kiểm chứng ra sao, còn gì chưa xong.
- Sai thì sửa gọn một câu rồi đi tiếp; không xin lỗi dài dòng, không kiểm điểm.



## A6. Thói quen code (bổ sung cho Phần B, không thay thế)

- **Viết code giống code xung quanh.** Repo là nguồn sự thật về phong cách, không phải thói quen của bạn.
- **Không comment thừa.** Comment giải thích *tại sao*, không mô tả lại *cái gì* dòng code đang làm.
- **Không để lại rác:** `print`/`console.log` debug, import không dùng, code chết, `TODO` không ngữ cảnh, file tạm.
- Sinh file tạm thì đặt ở thư mục tạm của phiên làm việc, **không** rải vào repo.



## A7. Git

- Không commit thẳng lên nhánh mặc định. Chưa có nhánh riêng thì tạo trước.
- Commit nhỏ, một mục đích, message mô tả *tại sao*.
- Không `--no-verify`, không bỏ qua hook, không tắt lint để cho qua. Hook fail là dấu hiệu có vấn đề thật.
- Không `--force`, không `reset --hard`, không sửa lịch sử đã push — trừ khi được yêu cầu rõ ràng.
- Không gộp thay đổi không liên quan vào cùng một commit/PR.



## A8. Khi sản phẩm có dùng LLM

Áp dụng đầy đủ **mục 9 Phần B**. Nhấn mạnh thêm cho agent:

- Đổi prompt hoặc đổi model → **bắt buộc** chạy eval set trước/sau và dán số liệu vào báo cáo. Không có eval thì không đổi.
- Ràng buộc nghiệp vụ (giới hạn giá trị, quyền hạn, hạn mức) do **server** áp đặt sau khi model trả lời, không giao cho model tự giữ.
- Nội dung do người dùng cung cấp luôn được bọc trong khối phân định rõ và tuyên bố là **dữ liệu**, không phải chỉ thị.

---

---



# PHẦN B — CODING STANDARDS

## 0. Phạm vi áp dụng

- **Backend**: Python (FastAPI là stack chính).
- **Frontend/Web**: TypeScript (ưu tiên) hoặc HTML/CSS thuần cho các trang đơn giản.
- **ML/AI pipeline & serving**: áp dụng thêm mục 6-9 bên dưới, song song với mục 1
(Python) vì phần lớn code ML viết bằng Python.
- Nếu project dùng stack khác, áp dụng phần "Nguyên tắc chung" (mục 3-5) và bỏ qua
phần riêng không liên quan.

---



## 1. Python / FastAPI Backend

### 1.1 Style & Format

- Tuân theo PEP 8, PEP 257, PEP 20.
- Indent 4 space, không tab. Dòng ≤ 79 ký tự (≤ 72 với comment/docstring).
- Mỗi file ≤ 170 dòng. Mỗi file chỉ chứa 1 concept chính (1 class hoặc 1 nhóm hàm liên quan).
- Import group theo thứ tự: stdlib → third-party → local, cách nhau 1 dòng trắng.
- Bắt buộc chạy qua `black` (format) + `isort` (import) + `ruff`/`flake8`/`pylint` (lint)
trước khi merge. Pre-commit hook phải được setup.



### 1.2 Cấu trúc project

- Mỗi domain/feature (users, auth, documents...) có subfolder riêng với `router.py`.
- Route handler tách theo trách nhiệm (VD: `user_crud.py`, `user_auth.py`), không nhét
business logic vào file route — logic thuộc về `services/*_service.py`.
- Config dùng Pydantic `BaseSettings`. **Không hardcode secret** — dùng secret manager
(Vault, AWS Secrets Manager, GCP Secret Manager... tuỳ hạ tầng project).
- Router khai báo qua `APIRouter()` với `prefix` + `tags` rõ ràng cho OpenAPI.
- Request/response models dùng Pydantic, có type đầy đủ.



### 1.3 Testing & CI

- Mọi business logic và API phải có unit/integration test, đặt trong `tests/` mirror
theo cấu trúc app, dùng `pytest` + `TestClient`.
- Coverage tối thiểu 80% cho critical path (`pytest-cov`/`coverage.py`).
- CI bắt buộc chạy và **fail build** nếu lỗi: `black --check`, `isort --check`,
lint (`ruff`/`flake8`), test (`pytest`), type check (`mypy`/`pyright`),
security scan (`bandit`, `safety`).



### 1.4 Bảo mật (Python backend)

- API chỉ chạy qua HTTPS.
- Auth dùng OAuth2 + JWT (hoặc SSO/IdP nội bộ tuỳ project).
- Validate input bằng Pydantic (`field_validator`/`model_validator`), không expose
stack trace hoặc lỗi nội bộ ra response.
- Ưu tiên credential dạng managed/short-lived (workload identity, IAM role...) thay
vì static access key khi hạ tầng hỗ trợ; nếu không thể, dùng secret manager với
injection cẩn thận, không hardcode.
- Áp dụng nguyên tắc least-privilege cho quyền truy cập tài nguyên (IAM/RBAC tuỳ
cloud). Token/key phải có chu kỳ rotate.
- Logging dùng structured logging (`structlog`), log tối thiểu gồm `timestamp`,
`trace_id`, `log_level`. **Tuyệt đối không log**: token, PII (SSN, Tax ID...),
secret dưới mọi hình thức.
- Audit log cho các action nhạy cảm, tích hợp hệ thống monitoring của team
(Datadog, Grafana/Loki, CloudWatch... tuỳ hạ tầng).
- CORS chỉ bật cho domain tin cậy. Bật security header (CSP, HSTS, X-Content-Type).
- Sanitize toàn bộ input từ bên ngoài. Rà soát định kỳ theo OWASP Top 10 / CWE.
- Dùng static security scan có SBOM (Bandit, Snyk...).



### 1.5 FastAPI practices

- Dùng `Depends()` cho DB session, auth, config.
- Ưu tiên I/O bất đồng bộ (`httpx`, `asyncpg`, `aioboto3`...); nếu bắt buộc dùng call
blocking, wrap bằng `run_in_threadpool()`.
- Docs OpenAPI (`/docs`) phải rõ ràng; field Pydantic có `description`.
- Mỗi route/class/function public có docstring.
- Dùng SDK chính thức của cloud provider đang dùng, pin version trong
`requirements.txt`.

---



## 2. TypeScript / Frontend (web)

- Bật `strict: true` trong `tsconfig.json`. Không dùng `any` trừ khi có comment giải
thích lý do và không còn cách nào khác.
- Format bằng Prettier, lint bằng ESLint (config chung của team), chạy qua CI.
- Component/hook đặt tên rõ nghĩa, mỗi file 1 component chính, tránh file UI > 200 dòng.
- State quản lý tập trung (Context/Zustand/Redux tuỳ project) — tránh prop-drilling
quá 3 cấp.
- Gọi API qua lớp service riêng (`services/api/*.ts`), không gọi `fetch`/`axios`
trực tiếp rải rác trong component.
- Không hardcode secret/API key ở phía client — biến nhạy cảm chỉ nằm ở backend hoặc
qua biến môi trường build-time đã được duyệt.
- Validate input phía client **không thay thế** validate phía server — luôn coi
server là nguồn sự thật cuối cùng.
- Test: ít nhất unit test cho logic nghiệp vụ (hooks, utils) và test tương tác cơ bản
cho component quan trọng (Jest/Vitest + Testing Library).



### 2.1 HTML/CSS thuần (trang đơn giản, landing page...)

- Semantic HTML, không lạm dụng `div` cho mọi thứ.
- CSS tổ chức theo BEM hoặc utility-class nhất quán trong project (không trộn 2 style).
- Ảnh/asset tối ưu kích thước, có `alt` text.
- Không inline `<script>` chứa secret hoặc token.

---



## 3. Clean Code (áp dụng mọi ngôn ngữ)

- Nguyên tắc: DRY, KISS, YAGNI, SOLID.
- Function ≤ 50 dòng, class ≤ 100 dòng, file ≤ 170 dòng (Python) / ~200 dòng (TS).
- Không trùng lặp logic — tách ra service/util dùng chung.
- Nesting tối đa 2-3 cấp; ưu tiên early-return để giảm nesting.
- Đặt tên biến/hàm mô tả rõ ý nghĩa, không viết tắt khó hiểu.
- Không dùng magic number/string — dùng constant hoặc enum.
- Mỗi hàm giữ 1 tầng abstraction (không trộn DB access + business logic + format
trong cùng 1 hàm).
- Xoá code chết, `TODO` treo lâu ngày, import không dùng, `console.log`/`print` debug
sót lại trước khi merge.
- Chỉ catch exception đã biết rõ loại — không dùng `except:` trần (Python) hoặc
`catch (e) {}` nuốt lỗi im lặng (TS).
- HTTP status code dùng đúng ngữ nghĩa: 400/401/403/404/500...
- Error message trả về client không được lộ logic nội bộ, stack trace, thông tin nhạy cảm.

---



## 4. Bảo mật chung (bắt buộc, mọi stack)

- **Không bao giờ commit secret**: API key, token, password, connection string,
private key. Nếu phát hiện trong diff → đây là vấn đề mức **nghiêm trọng**, luôn
báo dù prompt review có nói "giảm noise".
- Secret luôn lấy từ secret manager / biến môi trường CI, không hardcode, không để
trong file config commit vào git (kể cả `.env.example` phải để giá trị giả).
- Không log thông tin định danh cá nhân (PII), token, hoặc bất kỳ dữ liệu nhạy cảm nào.
- Input từ người dùng/bên ngoài luôn phải validate & sanitize trước khi dùng.

---



## 5. Test & CI (bắt buộc)

- Logic nghiệp vụ mới **bắt buộc có test đi kèm trong cùng PR** — PR thiếu test cho
logic mới nên bị đánh dấu "Cần sửa trước khi merge".
- CI phải xanh (lint, type-check, test, security scan) trước khi merge.
- Với pipeline/model liên quan ML (xem mục 6-8): test bắt buộc bao gồm cả data
validation test và model regression test, không chỉ unit test code thông thường.

---



## 6. Data & ML Pipeline

- **Data versioning**: dataset dùng cho training/eval phải được version hoá (DVC,
LakeFS, hoặc convention snapshot theo ngày/hash) — không tham chiếu dữ liệu "sống"
không version.
- **Reproducibility**: mọi bước tiền xử lý dữ liệu (cleaning, feature engineering)
phải chạy được lại 100% từ raw data + code trong repo, không có bước thủ công tay
ngoài pipeline.
- **Schema & validation**: input/output của mỗi bước pipeline phải có schema rõ ràng
(Pydantic, `pandera`, `great_expectations`...) và validate trước khi đẩy sang bước
tiếp theo.
- **PII trong dữ liệu training**: dữ liệu chứa PII phải được ẩn danh hoá/mã hoá
(masking, hashing, tokenization) trước khi đưa vào pipeline training; không dùng
dữ liệu production thô chưa qua xử lý.
- **Pipeline orchestration**: dùng công cụ orchestration chuẩn của team (Airflow,
Prefect, Dagster...) thay vì cron job/script rời rạc cho pipeline production.
- **Idempotency**: mỗi job trong pipeline chạy lại nhiều lần với cùng input phải cho
cùng kết quả (tránh side-effect tích luỹ, tránh duplicate write).



## 7. Model Training & Experiment Tracking

- Mọi lần train phải log qua experiment tracking tool (MLflow, Weights & Biases,
hoặc tương đương): hyperparameters, metrics, dataset version, code commit hash,
environment (requirements/lockfile).
- Không train "tay" rồi ship model không rõ nguồn gốc — mỗi model artifact đưa vào
production phải trace ngược được về: run ID, dataset version, code version.
- Model artifact lưu ở model registry (MLflow Model Registry, S3 + metadata, hoặc
tương đương), có version rõ ràng, không ghi đè artifact cũ.
- Đặt seed cố định cho các phần cần reproducibility (random split, init weights...);
nếu không reproducible 100% do infra (GPU non-determinism...), ghi chú rõ trong
docs.
- Đánh giá model bắt buộc có baseline so sánh (model cũ đang chạy production, hoặc
rule-based baseline) — không chỉ báo cáo metric tuyệt đối.
- Không hardcode đường dẫn model/checkpoint local trong code — dùng config/env.



## 8. Model Serving & Inference

- Inference API tuân theo toàn bộ chuẩn ở mục 1 (FastAPI, validate input bằng
Pydantic, auth, rate limit...).
- Version hoá model API (`/v1/predict`, `/v2/predict`...) — không thay đổi contract
input/output của version đang chạy production mà không bump version.
- Có cơ chế fallback/circuit breaker khi model service lỗi hoặc latency vượt
ngưỡng (trả về lỗi rõ ràng hoặc fallback model đơn giản hơn), không để request
treo vô thời hạn.
- Batch/async inference (nếu có) phải có giới hạn queue/timeout rõ ràng, tránh OOM
khi payload lớn.
- Rollout model mới theo canary/shadow traffic hoặc A/B test trước khi full rollout;
luôn có khả năng rollback nhanh về model version trước đó.
- Log input/output của inference phục vụ debug & giám sát model drift, nhưng
**không log PII/dữ liệu nhạy cảm thô** — áp dụng cùng nguyên tắc logging ở mục 1.4/4.



## 9. LLM & Prompt Engineering (nếu project dùng LLM/GenAI)

- Prompt template (system prompt, few-shot examples...) lưu trong file/config
riêng trong repo (không hardcode rải rác trong code), có version kiểm soát qua git.
- Mọi thay đổi prompt ảnh hưởng đến behavior production phải có eval set kèm theo
để so sánh trước/sau (regression test cho prompt, tương tự unit test cho code).
- Input người dùng đưa vào prompt phải được xử lý để giảm rủi ro prompt injection
(tách rõ instruction vs. user content, không tin tưởng nội dung do người dùng/bên
ngoài cung cấp là chỉ dẫn hệ thống).
- Output của LLM dùng cho hành động có side-effect (gọi API, ghi DB, gửi email...)
phải qua validate/guardrail, không thực thi trực tiếp theo output thô của model.
- Theo dõi & giới hạn chi phí (token usage, rate limit gọi API model bên thứ ba) —
có logging riêng cho cost monitoring.
- Không gửi dữ liệu nhạy cảm/PII của khách hàng cho API LLM bên thứ ba trừ khi đã
được duyệt về mặt bảo mật & hợp đồng dữ liệu (data processing agreement).

---

**Ghi chú**: các mục 6-9 áp dụng cho phần code/pipeline liên quan trực tiếp đến
ML/AI. Phần code hạ tầng thông thường (web backend, frontend) chỉ cần tuân theo
mục 0-5.

---

## Checklist trước khi báo "xong"

- [ ] Đã đọc mọi file trước khi sửa chúng.
- [ ] `make check` xanh — hoặc nói rõ vì sao không chạy được.
- [ ] Logic nghiệp vụ mới có test đi kèm (Phần B mục 5).
- [ ] Đổi mapping adapter → có golden test bằng payload thật (C3/X3).
- [ ] Đổi prompt/model → đã chạy eval, dán số liệu trước/sau (Phần B mục 9).
- [ ] Không file mới > 170 dòng, hàm > 50 dòng, class > 100 dòng (Phần B mục 3).
- [ ] Không còn secret, PII trong log, code debug, import thừa, file tạm.
- [ ] Thêm biến env mới → đã cập nhật đồng thời `.env.example` và `core/config.py`.
- [ ] Thêm ngoại lệ mới với Phần B → đã ghi vào C2 kèm điều kiện gỡ.
- [ ] Diff không chứa thay đổi ngoài phạm vi yêu cầu.
- [ ] Đã liệt kê phần chưa làm / chưa verify / giả định đã đặt ra.
- [ ] Mọi con số và trích dẫn code trong báo cáo đều kiểm chứng được.