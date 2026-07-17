# Lộ trình học tập 24 tháng — hướng CTO / Principal Engineer

> Chủ nhân: quan.pnm · Bắt đầu: 2026-08 · Kết thúc dự kiến: 2028-08
> Ngân sách thời gian: **10–12h/tuần** (2h/ngày làm việc + 1 buổi cuối tuần)
> Nguyên tắc: spike **trading systems** làm căn cước · độ rộng có chủ đích · **mỗi quý ra 1 artifact** công khai được (ADR, blog, talk, tool, side project)

## Bốn giai đoạn

| Giai đoạn | Thời gian | Chủ đề | Mục tiêu |
|---|---|---|---|
| 1. Móng kỹ thuật | Tháng 1–6 | DB internals, OS/Network, Observability, Writing | Sâu hơn 95% dev ở phần nền |
| 2. Đỉnh nhọn + Phân tán | Tháng 7–12 | Distributed systems, Trading architecture, Perf, Risk/Quant | Trở thành chuyên gia trading systems "có tên" |
| 3. Độ rộng chiến lược | Tháng 13–18 | Cloud/Infra, AI engineering, Security, Domain breadth | Nhìn được toàn cảnh như một CTO |
| 4. Rẽ nhánh & thu hoạch | Tháng 19–24 | CTO track HOẶC Principal track | Chuyển vai trò / đàm phán vị trí mới |

---

## Giai đoạn 1 — Móng kỹ thuật (Tháng 1–6)

### Quý 1 (Tháng 1–3): Database + Writing
- [ ] **CMU 15-445** — 2 lecture/tuần (video + notes), làm project 1–2 nếu kịp
- [ ] **Brent Ozar / SQLskills** — map lý thuyết vào SQL Server (dbG3SB)
- [ ] **Google Technical Writing One + Two** (tuần 1–2, xong sớm)
- [ ] Thói quen: mọi ADR/PR description viết **tiếng Anh**, nhờ AI review theo vòng
- [ ] Đọc nền: *Use The Index, Luke* (rải trong quý)

**Artifact Q1:** 1 bài phân tích nội bộ tiếng Anh: giải thích 1 vụ deadlock/slow query thật ở OMS bằng MVCC/locking + cách fix.

### Quý 2 (Tháng 4–6): OS/Network + Observability
- [ ] **OSTEP** phần Concurrency + Persistence (bỏ qua phần đã biết)
- [ ] **HPBN** (hpbn.co) — TCP/TLS; lab: Wireshark một phiên FIX với HNXSim
- [ ] **TLS 1.3 illustrated** — nối vào JWE/JWS bank integration
- [ ] **OpenTelemetry**: chạy otel-demo → instrument 1 flow thật OMS→Middleware→STP
- [ ] **SRE Workbook** chương SLO/alerting; định nghĩa SLO cho 1 service
- [ ] Gil Tene "How NOT to Measure Latency" + dựng cách đo p99 đúng

**Artifact Q2:** Distributed trace E2E place-order xuyên 3 service + dashboard SLO — demo cho team (đây là artifact "được nhìn thấy" đầu tiên).

---

## Giai đoạn 2 — Đỉnh nhọn Trading + Hệ phân tán (Tháng 7–12)

### Quý 3 (Tháng 7–9): Distributed Systems
- [ ] **MIT 6.824**: video + **lab Raft bằng Go** (ưu tiên số 1 của quý)
- [ ] **DDIA** (đọc kỹ phần II–III nếu chưa)
- [ ] **Patterns of Distributed Systems** (Fowler site) — đọc dạng catalog
- [ ] **Jepsen analyses** — 3–4 bài; đối chiếu NATS JetStream docs
- [ ] Kleppmann lecture series (YouTube) nếu cần bản nhẹ trước 6.824

**Artifact Q3:** ADR tiếng Anh: phân tích trade-off kiến trúc queue/replication hiện tại của BondOMS (NATS, dual-DB) theo khung lý thuyết vừa học + đề xuất cải tiến.

### Quý 4 (Tháng 10–12): Trading Architecture + Performance + Risk
- [ ] **LMAX article + Disruptor paper** + mổ source **exchange-core**
- [ ] **Dave Cheney High Performance Go Workshop** + pprof profile 1 flow OMS thật
- [ ] **Napkin Math** + Bakhvalov perf-book (chương chọn lọc)
- [ ] **Columbia FERM course 1** (Coursera audit) — nền risk/định giá
- [ ] **CME SPAN + quy chế ký quỹ VSDC** — đối chiếu công thức COMS margin
- [ ] Duy trì: Databento blog, Mechanical Sympathy

**Artifact Q4:** Bài viết tiếng Anh *"VSDC margin methodology — from SPAN to code"* (luyện writing + risk + tài liệu onboarding) HOẶC benchmark report matching-engine mini tự viết bằng Go so với exchange-core.

**Checkpoint 12 tháng:** đủ trình apply **Staff Engineer** fintech VN. Cập nhật CV, đi phỏng vấn thử 1–2 nơi để đo thị trường (kể cả không định chuyển).

---

## Giai đoạn 3 — Độ rộng chiến lược (Tháng 13–18)

### Quý 5 (Tháng 13–15): Cloud/Infra + Security
- [ ] **AZ-900** (MS Learn, sandbox free) → nắm khái niệm chung 3 cloud
- [ ] **Terraform tutorials** + dựng 1 stack nhỏ bằng code (VM + Azure SQL)
- [ ] **K8s tutorials** (killercoda) + đọc *Kubernetes The Hard Way* (hiểu, không cần thuộc)
- [ ] **AWS Well-Architected** + **12factor** — ngôn ngữ kiến trúc chung
- [ ] **OWASP API Top 10 + WSTG checklist** chạy qua 1 endpoint gateway thật
- [ ] **PortSwigger Academy**: series race condition + business logic (2–3 lab/tuần)
- [ ] Threat-model 1 flow thật (STRIDE — gợi ý: luồng NHTT remittance)

**Artifact Q5:** Threat model + security review report tiếng Anh cho 1 flow production; hoặc IaC hóa môi trường dev của 1 service.

### Quý 6 (Tháng 16–18): AI Engineering + Domain breadth
- [ ] **Anthropic: Building Effective Agents + cookbook** — hệ thống hóa cách dùng AI trong SDLC
- [ ] **Build 1 MCP server thật** (gợi ý: G3SB read-only cho team) — vừa học protocol vừa ra tool
- [ ] **Chip Huyền blog + Applied LLMs** — AI engineering, evals
- [ ] **DuckDB + dbt fundamentals** — tự động hóa 1 việc reconciliation thật
- [ ] Domain breadth (đọc nhẹ, 1 chủ đề/tháng): Shopify Engineering (e-commerce), DCSA + Flexport (logistics), Grab/Gojek engineering (superapp/payment)
- [ ] **StaffEng** đọc hết các story + talk "Being Glue"

**Artifact Q6:** MCP server chạy thật cho team + 1 talk nội bộ "AI trong quy trình dev của team mình". Đây là artifact "CTO-shaped": vừa kỹ thuật vừa nhân năng suất người khác.

**Checkpoint 18 tháng — CHỌN NHÁNH:** nhìn lại 6 artifact + phản hồi thị trường ở tháng 12 → chọn CTO track hay Principal track cho 6 tháng cuối.

---

## Giai đoạn 4 — Rẽ nhánh (Tháng 19–24)

### Nhánh A — CTO track (startup/scale-up, hoặc nội bộ)
- [ ] **The Manager's Path** (Fournier) — đọc hết, không chỉ 2 chương đầu
- [ ] **LeadDev talks**: hiring, team topologies, roadmap — 1 talk/tuần
- [ ] **Team Topologies** (sách) — thiết kế tổ chức theo luồng giá trị
- [ ] Học đọc **tài chính doanh nghiệp cơ bản**: runway, unit economics, budget engineering (nguồn: khóa "Finance for Non-Finance" bất kỳ + Matt Levine để thấm)
- [ ] Product sense: *Shape Up* (đọc lại với con mắt người quyết định), *Inspired* (Marty Cagan)
- [ ] Network: gặp 5–10 founder/CTO VN (coffee chat) — thị trường CTO đi qua quan hệ, không qua job board
- [ ] Side project hoặc nhận 1 dự án fractional nhỏ để có "CTO reps" thật

**Artifact:** 1 bản "engineering strategy" hoàn chỉnh (có thể viết cho chính team HSC): tech radar, hiring plan, quy trình, KPI — chính là bài test việc CTO làm.

### Nhánh B — Principal track (fintech lớn / Singapore / remote)
- [ ] **NeetCode 150** (6–8 tuần, nghiêm túc như dự án) + **Hello Interview system design**
- [ ] Mock interview tiếng Anh (pramp/interviewing.io hoặc bạn bè SG)
- [ ] Đào sâu spike thêm 1 nấc: **CMU 15-721** (advanced DB) hoặc **deterministic simulation** (nâng cấp HNXSim với fault injection) — chọn 1
- [ ] Viết 2–3 bài blog công khai tiếng Anh về trading systems (nguyên liệu đã có từ artifact Q1–Q4)
- [ ] Apply có chiến lược: TCBS/SSI/MoMo (VN top), Grab/Sea/prop firms (SG), crypto exchange (remote)

**Artifact:** blog kỹ thuật công khai + CV/portfolio hoàn chỉnh + ít nhất 3 vòng phỏng vấn thật.

---

## Nhịp hằng tuần (cả 24 tháng)

| Ngày | Việc | Thời lượng |
|---|---|---|
| T2–T6 | 1 lecture/chương/lab theo quý | 1.5–2h/ngày (chọn 4 ngày) |
| T7 hoặc CN | Lab lớn / viết artifact | 3–4h |
| Rải rác | Newsletter: Pragmatic Engineer, Money Stuff, ByteByteGo | lúc cà phê |
| Mỗi tuần | 1 ADR/PR/note bằng tiếng Anh | trong giờ làm việc |

## Quy tắc điều chỉnh

1. **Trượt tiến độ là bình thường** — cắt độ rộng (Q5–Q6), không bao giờ cắt spike (Q3–Q4) và writing.
2. Mỗi cuối quý: review 30 phút — artifact xong chưa, cái gì bỏ, cái gì dời.
3. Công việc HSC có dự án lớn trùng chủ đề nào → **hoán đổi quý** để học ngay cái đang dùng (học có bối cảnh thật nhanh gấp đôi).
4. Không thu thập tài nguyên mới khi chưa xong tài nguyên của quý — danh sách này đã đủ.

## Những gì CHỦ ĐỘNG không học sâu (để khả thi trong 24 tháng)

- Game engine (Unity/C++): chỉ đọc Gaffer On Games phần netcode — phục vụ real-time, không làm game.
- Blockchain: chỉ MIT Blockchain and Money + BIS tokenization papers — đủ để nói chuyện chiến lược, không viết smart contract.
- ML/Data science hàn lâm: chỉ AI engineering ứng dụng (dùng model, không train model).
- Frontend: giữ mức đọc-hiểu.

> Các mảng trên có thể mở lại sau tháng 24 tùy nhánh đã chọn.
