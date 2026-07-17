# Study Map — Tổng hợp toàn bộ nguồn học (session 2026-07-17)

> Tổng hợp từ chuỗi trao đổi về nguồn học tập theo chủ đề, định hướng nghề nghiệp CTO/Principal.
> Lộ trình hành động chi tiết: xem [study-roadmap-cto-principal-24-thang.md](study-roadmap-cto-principal-24-thang.md)

---

## 1. Giáo trình AI nền tảng (download free, chính chủ)

| Giáo trình | Tác giả | Link |
|---|---|---|
| Deep Learning | Goodfellow, Bengio, Courville | https://www.deeplearningbook.org |
| Dive into Deep Learning (D2L) | Zhang, Lipton, Li, Smola | https://d2l.ai — code PyTorch/TF chạy được |
| Understanding Deep Learning | Simon Prince | https://udlbook.github.io/udlbook/ — mới, hình đẹp |
| The Little Book of Deep Learning | François Fleuret | https://fleuret.org/public/lbdl.pdf |
| Pattern Recognition and ML (PRML) | Christopher Bishop | Microsoft công khai PDF free |
| Probabilistic Machine Learning (2 tập) | Kevin Murphy | https://probml.github.io/pml-book/ |
| Mathematics for Machine Learning | Deisenroth, Faisal, Ong | https://mml-book.github.io |
| Reinforcement Learning: An Introduction | Sutton & Barto | http://incompleteideas.net/book/the-book.html |
| Speech and Language Processing (ed.3) | Jurafsky & Martin | https://web.stanford.edu/~jurafsky/slp3/ — có LLM |
| The Elements of Statistical Learning | Hastie, Tibshirani, Friedman | https://hastie.su.domains/ElemStatLearn/ |
| An Introduction to Statistical Learning | ISL | https://www.statlearning.com — bản Python và R |
| Build a LLM (From Scratch) — repo | Sebastian Raschka | https://github.com/rasbt/LLMs-from-scratch |
| Hands-On Large Language Models — repo | | https://github.com/HandsOnLLM/Hands-On-Large-Language-Models |
| CS229 / CS231n / CS224n notes | Stanford | search tên course — notes + slides free |

**Lộ trình gợi ý:** Mathematics for ML (lướt) → ISL hoặc D2L (thực hành) → Understanding DL (sâu) → SLP3 + LLMs-from-scratch (nếu mục tiêu LLM).

## 2. Sử dụng AI — prompt engineering, agents, applied

| Tài liệu | Link |
|---|---|
| Anthropic Prompt Eng Docs + Interactive Tutorial | https://docs.anthropic.com + https://github.com/anthropics/prompt-eng-interactive-tutorial |
| OpenAI Cookbook | https://github.com/openai/openai-cookbook |
| Prompt Engineering Guide (DAIR.AI) | https://www.promptingguide.ai |
| Google Prompt Engineering whitepaper (Boonstra) | search trên Kaggle, PDF ~68 trang |
| ChatGPT Prompt Eng for Developers (Ng) | https://www.deeplearning.ai/short-courses/ |
| Anthropic: Building Effective Agents | https://www.anthropic.com/research/building-effective-agents |
| Anthropic Cookbook | https://github.com/anthropics/anthropic-cookbook |
| Google Agents whitepaper | Kaggle, PDF free |
| LLM Engineer's Handbook — repo | https://github.com/PacktPublishing/LLM-Engineers-Handbook |
| Microsoft: Generative AI for Beginners | https://github.com/microsoft/generative-ai-for-beginners — có bản tiếng Việt |
| Hugging Face Courses (LLM, Agents) | https://huggingface.co/learn |
| Claude Code docs + Best Practices | https://docs.claude.com/en/docs/claude-code + anthropic.com engineering blog |
| Simon Willison's blog | https://simonwillison.net |

## 3. Nguồn skill AI (Claude Code / chuẩn SKILL.md)

- **anthropics/skills** — https://github.com/anthropics/skills (chính chủ, chuẩn nhất)
- **Claude Code Plugin Marketplace** — lệnh `/plugin` trong CLI
- **skills.sh** — https://skills.sh ("npm của skills")
- **obra/superpowers** — https://github.com/obra/superpowers (bộ skill cộng đồng chất lượng nhất)
- **claude-code-templates** — https://github.com/davila7/claude-code-templates + https://aitmpl.com
- Lưu ý: skill cá nhân → `~/.claude/skills/`; skill dùng chung project → `ai-context/skills/`. Đọc kỹ SKILL.md + scripts trước khi cài (rủi ro prompt injection). Chất lượng > số lượng.

## 4. Hướng tìm hiểu thêm về AI

- **AI Engineering**: blog Chip Huyền (https://huyenchip.com/blog), Applied LLMs (https://applied-llms.org), chủ đề **evals**
- **MCP**: https://modelcontextprotocol.io — bước tiếp theo: viết MCP server riêng (vd: G3SB read-only)
- **LLM local**: Ollama (https://ollama.com), LM Studio — hiểu token/context/quantization bằng tay
- **AI Security**: OWASP Top 10 for LLM Apps, Simon Willison về prompt injection
- **Newsletter**: The Batch (deeplearning.ai), Latent Space, Anthropic engineering blog
- **Thực hành**: Kaggle micro-courses; side project gắn pain point thật ở HSC

## 5. Tài chính — Ngân hàng — Chứng khoán

### Nền tảng
- Khan Academy Finance & Capital Markets · Investopedia (tra thuật ngữ) · CFA Investment Foundations (free sau đăng ký) · MIT OCW 15.401

### Fixed income (sát mảng Bond/TPRL)
- Fabozzi Handbook (lecture notes free nhiều) · ICMA (https://www.icmagroup.org) — repo, bond conventions

### Hạ tầng thị trường & settlement
- **BIS CPMI** (https://www.bis.org/cpmi/) — đọc **PFMI**, DvP model 1/2/3
- **SWIFT Standards MT** — spec MT5xx (MT544/546), lộ trình ISO 20022
- **FIX Trading Community** (https://www.fixtrading.org) — spec FIX 4.4/5.0
- DTCC / Euroclear learning pages

### Ngân hàng
- BIS (Basel III) · Coursera "Economics of Money and Banking" (Mehrling, Columbia) · Khan Academy Banking

### Việt Nam
- UBCKNN (ssc.gov.vn) — Luật Chứng khoán 2019 + nghị định/thông tư
- VSDC (vsd.vn) — quy chế đăng ký/lưu ký/bù trừ thanh toán
- HNX (hnx.vn), HOSE (hsx.vn) — quy chế giao dịch, tài liệu TPDN
- Thư viện pháp luật / LuatVietnam

**Lộ trình:** Khan Academy bonds+banking → BIS PFMI/DvP → quy chế VSDC + thông tư TPDN → glossary riêng qua Investopedia. Học "market plumbing", không cần học định giá như dân tài chính.

## 6. Blockchain

- **Bitcoin Whitepaper** — https://bitcoin.org/bitcoin.pdf (đọc đầu tiên)
- **Mastering Bitcoin / Mastering Ethereum** — full free trên GitHub (bitcoinbook, ethereumbook)
- **MIT 15.S12 Blockchain and Money** (Gensler) — https://ocw.mit.edu — góc tài chính × quy định, **hợp nhất với profile**
- Princeton Bitcoin course (Coursera audit + PDF draft)
- **Go**: go-ethereum docs (https://geth.ethereum.org/docs), Cosmos SDK (https://docs.cosmos.network), Jeiwan "Building Blockchain in Go"
- **Solidity**: docs.soliditylang.org · CryptoZombies · **Cyfrin Updraft** (https://updraft.cyfrin.io) · Ethernaut (security wargame)
- **Blockchain × TradFi**: BIS papers (tokenisation, unified ledger, mBridge), CBDC (BIS+IMF), DeFi (Campbell Harvey Coursera, finematics.com)
- **VN**: Luật Công nghiệp công nghệ số 2025 (hiệu lực 1/1/2026), sandbox tài sản mã hóa NQ 05/2025

**Lộ trình:** whitepaper + MIT course → blockchain in Go → rẽ Cosmos SDK (infra) hoặc Cyfrin (smart contract) → BIS tokenization. Học với lăng kính "so với VSD/G3" (atomic settlement vs reconciliation).

## 7. Thương mại điện tử

- **Google microservices-demo (Online Boutique)** — https://github.com/GoogleCloudPlatform/microservices-demo — ~11 services Go/gRPC/K8s
- System Design Primer · ByteByteGo blog · **Shopify Engineering** (https://shopify.engineering — flash sale, idempotency, inventory) · Amazon Builders' Library
- **Codebase mổ xẻ**: Medusa (Node/TS), Saleor, Spree, evershop — vẽ lại ERD order–payment–inventory
- **Payment**: Stripe docs (chuẩn mực) · VNPAY/MoMo/ZaloPay/SePay docs
- **UX/Conversion**: **Baymard Institute** (https://baymard.com/blog) · web.dev Commerce · NNGroup
- **VN**: báo cáo EBI (VECOM), Sách trắng TMĐT (idea.gov.vn), NĐ 52/2013 + 85/2021, Shopee/Lazada University

**Insight:** OMS/settlement kinh nghiệm chuyển 1-1 sang TMĐT (order state machine, hold inventory ≈ hold cash, reconciliation gateway ≈ settlement). Phần mới: catalog/search, cart/pricing/promotion, UX.

## 8. Logistics & Shipping line

- **Domain**: UNCTAD Review of Maritime Transport · Maersk/DHL glossary · Flexport Engineering · Incoterms 2020 · MIT Supply Chain MicroMasters (audit free)
- **Chuẩn dữ liệu**: EDIFACT (IFTMIN, CODECO, BAPLIE) · **DCSA** (https://dcsa.org — OpenAPI public, "FIX của ngành container") · GS1/SSCC
- **Kỹ thuật**: Google OR-Tools (VRP routing) · PostGIS, OSRM/Valhalla, H3 · Karrio (https://github.com/karrioapi/karrio) · OpenBoxes, Odoo Inventory
- **VN**: docs API GHN, GHTK, ViettelPost, Ahamove

## 9. Superapp & Thanh toán điện tử

- **Superapp**: Grab engineering (engineering.grab.com) + Gojek blog (blog.gojek.io) — quý nhất, bối cảnh ĐNA · WeChat/Alipay mini-program · **Zalo Mini App** (mini.zalo.me)
- **Payment systems**: Glenbrook (Payments Systems in the US + blog/podcast) · BIS CPMI fast payments · Stripe/Adyen engineering
- **Chuẩn**: **ISO 8583** (thẻ/ATM/POS), ISO 20022 · **EMVCo** (EMV QR — nền VietQR, 3DS, tokenization) · PCI DSS
- **VN**: NAPAS + spec VietQR (vietqr.net) · NĐ 52/2024 TTKDTM · TT 64/2024 Open Banking · docs MoMo/ZaloPay/VNPAY
- **Ledger**: **Modern Treasury journal** (moderntreasury.com/journal) — building a ledger, double-entry, idempotency — trùng khớp nghề settlement

## 10. Game 2D / 3D

- **Lý thuyết**: **Game Programming Patterns** (https://gameprogrammingpatterns.com — free full) · Red Blob Games (pathfinding/grid) · Book of Shaders · 3D math primers
- **2D**: **Godot** (docs.godotengine.org) + GDQuest · **Ebitengine** (https://ebitengine.org — Go) · Phaser (web) · 20 Games Challenge
- **3D**: Unity Learn (learn.unity.com) · Unreal (dev.epicgames.com) · Godot 4 · LearnOpenGL (https://learnopengl.com — hiểu rendering từ gốc)
- **Web**: Three.js + Discover Three.js · Babylon.js · MDN Game Dev · js13kGames
- **Multiplayer (sân nhà)**: **Gaffer On Games** (https://gafferongames.com — netcode kinh điển) · Nakama (Go game server) · Colyseus (Node) · Valve Source Multiplayer Networking
- **Assets**: Kenney.nl (CC0) · OpenGameArt · itch.io · Blender (donut tutorial)

**Insight:** OMS ≈ real-time state sync — netcode là bài toán anh em (tick rate ≈ matching cycle, client prediction ≈ optimistic UI). Nhánh multiplayer + game .io nhỏ là project tổng hợp lý tưởng.

## 11. System Architecture — Monolith/Microservices — Trading Systems

### Kiến trúc nền
- **DDIA** (Kleppmann) — cuốn quan trọng nhất, ed.2 2025 · System Design Primer · martinfowler.com · Azure Architecture Center (pattern catalog) · Amazon Builders' Library

### Monolith vs Microservices
- **microservices.io** (Richardson — saga, outbox, strangler) · Fowler "MonolithFirst" + "Microservice Premium" · Prime Video về monolith (2023) · **Shopify Modular Monolith** · Sam Newman talks (GOTO/QCon)

### Trading systems
- **LMAX Architecture** (martinfowler.com/articles/lmax.html) + **Disruptor** (https://lmax-exchange.github.io/disruptor/)
- **Mechanical Sympathy** (Martin Thompson) · Carl Cook "When a Microsecond Is an Eternity" (CppCon)
- **exchange-core** (https://github.com/exchange-core/exchange-core) — matching engine để mổ xẻ
- Trading and Exchanges (Larry Harris — microstructure) · **Databento blog** · Jane Street talks
- Spec public: Nasdaq ITCH/OUCH, CME iLink/MDP3 — đối chiếu FIX 4.4 HNX
- Event Sourcing/CQRS (Fowler, Greg Young) · Kafka Definitive Guide (Confluent PDF free) · NATS JetStream docs

### Học qua case
- aosabook.org · High Scalability · InfoQ/QCon talks

**Lộ trình:** DDIA → LMAX + exchange-core (so với BondOMS) → microservices.io + Modular Monolith → duy trì Databento/Mechanical Sympathy/QCon.

## 12. System Performance & System Security

### Performance
- **Brendan Gregg** (brendangregg.com — USE method, flame graphs) + Systems Performance 2nd
- **perf-book Bakhvalov** (https://github.com/dendibakh/perf-book — PDF free)
- **Napkin Math** (github.com/sirupsen/napkin-math) + Latency Numbers
- **Go**: pprof/trace + **Dave Cheney High Performance Go Workshop** · Node: V8, clinic.js
- **Use The Index, Luke** (https://use-the-index-luke.com)
- **k6** (open/closed model, percentiles) · **Google SRE Books** (https://sre.google/books/ — free cả 3)
- **Gil Tene "How NOT to Measure Latency"** — coordinated omission, bắt buộc cho trading
- OpenTelemetry (opentelemetry.io)

### Security
- **OWASP**: Top 10, API Top 10, **Cheat Sheet Series**, ASVS
- **PortSwigger Web Security Academy** — hands-on free tốt nhất (đặc biệt lab race condition, business logic)
- **Building Secure and Reliable Systems** (Google — free full)
- Threat modeling: STRIDE, threatmodelingmanifesto.org · NIST SP 800-63, 800-207
- **Cryptopals** (https://cryptopals.com) + Dan Boneh Crypto I (Coursera)
- Go: govulncheck, gosec, OWASP Go-SCP · SLSA (supply chain) · CIS Benchmarks, NSA K8s Hardening
- **Ngành tài chính**: SWIFT CSP/CSCF · PCI DSS · TT 09/2020/TT-NHNN
- Thực hành: TryHackMe / HackTheBox / OverTheWire Bandit · Stanford CS 253

**Insight:** race condition + idempotency vừa là bug perf vừa là lỗ hổng security (double-spend ≈ double-submit order).

## 13. Agile — Quy trình

- **Bản gốc**: Agile Manifesto (agilemanifesto.org) · **Scrum Guide** (scrumguides.org, có tiếng Việt) · Kanban Guide · XP (extremeprogramming.org)
- **Thực chứng**: **DORA** (https://dora.dev — 4 metrics) · Accelerate · Google eng-practices (code review)
- **Mô hình khác**: **Shape Up** (basecamp.com/shapeup — free full) · trunkbaseddevelopment.com · continuousdelivery.com + Dave Farley YouTube · SAFe/LeSS (biết để đối thoại)
- **Kỹ năng**: retromat.org · **ADR** (adr.github.io) · Design Docs at Google (industrialempathy.com) · SRE postmortem culture + PagerDuty response docs · **Diátaxis** (diataxis.fr)
- **Sách**: The Phoenix Project · The Goal · Mythical Man-Month
- **Regulated env**: ING agile case study · DORA data về CAB

**Lộ trình:** đọc bản gốc (~40 trang) → tự đo DORA 4 metrics của team → Shape Up + TBD (phản biện) → áp 1–2 practice thật (ADR, postmortem).

## 14. QA / QC / Testing

- **Nền**: ISTQB Foundation Syllabus (PDF free) · **SWE at Google phần Testing** (https://abseil.io/resources/swe-book — 5 chương hay nhất) · Practical Test Pyramid (Ham Vocke) · Google Testing Blog · Ministry of Testing
- **Nâng cao**: property-based testing (Hypothesis, gopter/rapid, fast-check) · mutation testing (Stryker, go-mutesting) · contract testing (**Pact**) · **Testcontainers** · Go fuzzing built-in
- **Deterministic simulation**: talk FoundationDB (Will Wilson) + Antithesis blog — HNXSim chính là tư tưởng này; hướng tiến hóa: fault injection
- **Performance test**: k6 + Grafana learning · Gatling Academy · JMeter/BlazeMeter · wrk2/vegeta · Gil Tene. Trading-specific: burst theo phiên, backlog drain, soak qua business date rollover
- **Security test**: **OWASP WSTG** · ZAP · Semgrep + govulncheck + gosec · nuclei
- **Process**: Agile Testing Quadrants (Crispin & Gregory) · shift-left + DORA change failure rate · IEEE 829/ISO 29119 (biết tên)

## 15. Nhóm 1 — Móng kỹ thuật

### Database internals
- **CMU 15-445** (https://15445.courses.cs.cmu.edu + YouTube) — xương sống · CMU 15-721 (advanced) · Database Internals (Petrov)
- Build your own: cstack.github.io/db_tutorial · SQL Server: Brent Ozar, SQLskills · Modern SQL · Jepsen consistency map

### Distributed systems
- **MIT 6.824** (https://pdos.csail.mit.edu/6.824/ — lab Raft bằng Go) · Kleppmann lecture series (YouTube ~7h)
- raft.github.io + thesecretlivesofdata.com/raft · Jepsen analyses · Patterns of Distributed Systems (Fowler) · NATS JetStream internals

### Networking & OS
- **HPBN** (https://hpbn.co — free full) · Beej's Guide · Kurose & Ross · Wireshark (lab: capture FIX với HNXSim)
- **TLS 1.3 illustrated** (tls13.xargs.org) · Julia Evans (jvns.ca)
- **OSTEP** (https://pages.cs.wisc.edu/~remzi/OSTEP/ — free full) · Gregg linuxperf · TLPI (Kerrisk, tra cứu)

### Observability
- **Observability Engineering** (Honeycomb PDF free) · **OpenTelemetry** docs + otel-demo · SRE Workbook (SLO/alerting)
- Grafana/Prometheus (PromQL) · Peter Bourgon "Metrics, tracing, logging" · RED method + USE method · trace-to-log correlation

**Lộ trình 6 tháng:** 15-445 (T1–2) → OSTEP concurrency + HPBN + Wireshark FIX (T2–3) → OpenTelemetry trace E2E OMS (T3–4) → MIT 6.824 lab Raft (T4–6).

## 16. Nhóm 2 — Nâng tầm nghề nghiệp

### Technical writing & tiếng Anh
- **Google Technical Writing Courses** (developers.google.com/tech-writing) · Diátaxis · Write the Docs · Design Docs at Google
- Elements of Style (public domain) · Hemingway Editor · **phương pháp chính: viết ADR/postmortem/PR tiếng Anh + AI review theo vòng**
- Mẫu đọc: postmortem Cloudflare/GitHub/incident.io, Rust RFCs, K8s KEPs

### Staff/Principal path
- **StaffEng** (https://staffeng.com — free full, archetypes) · The Staff Engineer's Path (Reilly) + talk "Being Glue" · Pragmatic Engineer · charity.wtf (engineer/manager pendulum)
- LeadDev YouTube · mtlynch.io code review · The Manager's Path (2 chương đầu) · "Work on what matters"
- **Gap thường gặp:** không thiếu kiến thức mà thiếu văn bản hóa (ADR, postmortem) + kể chuyện impact

### Risk & Quant cơ bản
- **Columbia FERM** (Coursera audit) · GARP FRM syllabus (bản đồ) · Hull (derivatives, slides free nhiều)
- **CME SPAN + quy chế ký quỹ VSDC** — nối vào công thức COMS · QuantLib + Python Cookbook · BIS/IOSCO margin papers · Matt Levine Money Stuff

**Artifact đề xuất đầu tiên:** bài tiếng Anh "VSDC margin methodology — from SPAN to code COMS".

## 17. Nhóm 3 — Đáng biết + Cloud/Infra

### Data engineering
- dbt Fundamentals (learn.getdbt.com) · **DuckDB** (thay Excel đối chiếu file VSD) · Fundamentals of Data Engineering · **Data Engineering Zoomcamp** (DataTalksClub — free bootcamp)

### DSA (chỉ khi phỏng vấn)
- **NeetCode 150** (neetcode.io) · Hello Interview (system design) · visualgo.net · chiến lược: dồn 6–8 tuần trước phỏng vấn, không học rải

### Nền tảng infra (dùng chung 3 cloud)
- Docker docs + Play with Docker · K8s tutorials + killercoda.com · **Kubernetes The Hard Way** · **Terraform HashiCorp Learn** · 12factor.net · SRE Book

### AWS
- Skill Builder (skillbuilder.aws) · **Well-Architected Framework** · workshops.aws · cert: CP → **SAA** (Cantrill trả phí / freeCodeCamp free) · free tier 12 tháng

### Google Cloud
- Cloud Skills Boost (Qwiklabs) · Architecture Framework · cert: CDL → ACE → PCA · điểm riêng: **BigQuery**, Cloud Run, Spanner · $300 credit

### Azure
- **Microsoft Learn** (bài bản nhất, sandbox free không cần thẻ) · cert: **AZ-900** → AZ-104/AZ-204 · Azure Architecture Center
- **Lý do chọn Azure làm chính:** bank/chứng khoán VN nghiêng Microsoft (Entra ID, compliance), Azure SQL sát stack SQL Server

### Bảng map khái niệm
| Khái niệm | AWS | GCP | Azure |
|---|---|---|---|
| VM | EC2 | Compute Engine | Virtual Machines |
| Object storage | S3 | Cloud Storage | Blob Storage |
| Function | Lambda | Cloud Functions | Azure Functions |
| Container serverless | Fargate | Cloud Run | Container Apps |
| Managed SQL | RDS | Cloud SQL | Azure SQL |
| IAM | IAM | IAM | Entra ID + RBAC |
| Mạng riêng | VPC | VPC | VNet |

**Chiến lược:** học khái niệm 1 lần, sâu 1 cloud (Azure), đọc-hiểu 2 cloud còn lại.

## 18. Định hướng nghề nghiệp & mức lương (ước lượng đầu 2026)

### Thị trường VN (gross/tháng)
| Vị trí | Mức |
|---|---|
| Senior Backend (Go/Node) | 50–80 triệu |
| Staff / Principal Engineer | 80–150 triệu+ |
| Trading Systems Engineer | 60–120 triệu |
| Solutions Architect | 70–130 triệu |
| SRE / Platform senior | 60–110 triệu |
| EM / Head of Engineering | 100–200 triệu |
| CTO / VP Eng (startup) | 150–300 triệu + equity |

### Khu vực / quốc tế (gross/năm)
| Vị trí | Mức |
|---|---|
| Trading Systems / Low-latency (SG prop firms) | SGD 150–300k+ |
| Quant Developer (SG/HK) | SGD 120–250k+ |
| Senior/Staff fintech khu vực (Grab, Sea, ByteDance) | SGD 100–200k |
| Remote US/EU (fintech, crypto exchange) | USD 60–150k |
| Principal/Distinguished (SG) | SGD 250–400k |

### Nguyên tắc định vị
- **Thị trường trả cho spike, không trả cho diện tích** — CV "biết tất cả" là red flag
- Spike căn cước: **"kỹ sư hệ thống giao dịch tài chính"** (VSD/G3/FIX/settlement — hiếm, khó copy)
- Độ rộng bán được ở vai trò: CTO, technical co-founder, enterprise architect, fractional CTO/consultant ($100–300/giờ)
- Đường tối ưu: Staff/Principal fintech (VN/SG) → tích vốn + thương hiệu → fractional CTO / founder
- Kiến thức chỉ là điều kiện cần; quyết định offer: track record + tiếng Anh + kỹ năng phỏng vấn

## 19. Chủ đề bổ sung đã gợi ý (chưa triển khai chi tiết)

- Frontend fundamentals (mức đọc-hiểu)
- Compilers / ngôn ngữ (ưu tiên thấp)
- Product thinking sâu (Inspired — Cagan; dành cho CTO track)

## 20. Lộ trình hành động

→ **[study-roadmap-cto-principal-24-thang.md](study-roadmap-cto-principal-24-thang.md)** — 24 tháng, 4 giai đoạn, 10–12h/tuần, artifact mỗi quý, rẽ nhánh CTO/Principal ở tháng 18.

Nguyên tắc xuyên suốt:
1. Spike trading systems + writing không bao giờ cắt; trượt tiến độ thì cắt độ rộng.
2. Mỗi quý 1 artifact tiếng Anh gắn công việc thật (ADR, blog, talk, tool).
3. Học có bối cảnh: mọi chủ đề đều có "bài lab" trên hệ bond/carbon/COMS thật.
4. Không thu thập tài nguyên mới khi chưa dùng hết tài nguyên của quý.
