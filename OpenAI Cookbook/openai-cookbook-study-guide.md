# OpenAI Cookbook — Tài liệu nghiên cứu & học tập

> Nguồn: [github.com/openai/openai-cookbook](https://github.com/openai/openai-cookbook) · Website: [cookbook.openai.com](https://cookbook.openai.com)
> License: MIT · Ngôn ngữ chính: Python (Jupyter Notebook)
> Số liệu tại thời điểm khảo sát (2026-07-17): **304 bài viết/guide**, ~3.360 file trong repo.

---

## 1. OpenAI Cookbook là gì?

OpenAI Cookbook là **kho ví dụ code và hướng dẫn chính thức của OpenAI** cho các tác vụ phổ biến với OpenAI API. Đây KHÔNG phải là library/framework — nó là tập hợp các **Jupyter Notebook (`.ipynb`) và bài viết Markdown** minh hoạ cách giải quyết từng bài toán cụ thể: từ đếm token, gọi function, xây RAG, cho đến build agent đa bước, fine-tuning, và evals.

Đặc điểm quan trọng:

- Mỗi notebook là **self-contained** — chạy độc lập, giải quyết đúng 1 bài toán.
- Code chủ yếu bằng Python, nhưng concept áp dụng được cho mọi ngôn ngữ.
- Được cập nhật liên tục theo model/API mới (GPT-5.x, Realtime, Sora, Codex, gpt-oss, Agents SDK...).
- Website `cookbook.openai.com` được generate từ chính repo này (qua file `registry.yaml`).

## 2. Cấu trúc repository

```
openai-cookbook/
├── registry.yaml        # "Mục lục" — metadata của 304 bài (title, path, date, authors, tags)
│                        # Website cookbook.openai.com build từ file này
├── authors.yaml         # Thông tin tác giả
├── examples/            # PHẦN LÕI — toàn bộ notebook ví dụ
│   ├── *.ipynb          # ~85 notebook "kinh điển" nằm ngay gốc (embeddings, function calling...)
│   ├── vector_databases/    # 58 file — tích hợp 23 vector DB (Pinecone, Qdrant, Chroma, Redis...)
│   ├── chatgpt/             # 35 file — GPT Actions library, Workspace Agents, Enterprise
│   ├── agents_sdk/          # 26 file — OpenAI Agents SDK (multi-agent, memory, tracing...)
│   ├── evaluation/ + evals/ # 26 file — đánh giá chất lượng model/agent
│   ├── azure/               # 12 file — dùng OpenAI qua Azure
│   ├── codex/               # 11 file — Codex coding agent
│   ├── gpt-5/               # 9  file — prompting & tính năng GPT-5.x
│   ├── partners/ + third_party/  # tích hợp đối tác (Cerebras, Daytona, Oracle...)
│   ├── multimodal/          # 8  file — vision, image, video
│   ├── voice_solutions/     # 7  file — Realtime API, speech
│   ├── fine-tuned_qa/       # 5  file — fine-tune cho Q&A
│   ├── mcp/                 # 4  file — Model Context Protocol
│   ├── deep_research_api/, responses_api/, o1/, dalle/, sora/, object_oriented_agentic_approach/ ...
│   └── data/                # dataset mẫu dùng chung
├── articles/            # 9 bài viết thuần lý thuyết/khái niệm (không phải notebook)
└── images/              # hình ảnh minh hoạ
```

**Mẹo tra cứu:** muốn tìm bài về chủ đề X → mở `registry.yaml`, search theo `tags:` hoặc `title:`. Đây chính là index chính thức của toàn bộ cookbook.

## 3. Chuẩn bị môi trường để chạy ví dụ

1. Tạo tài khoản OpenAI + API key tại [platform.openai.com](https://platform.openai.com/signup).
2. Set biến môi trường `OPENAI_API_KEY` (hoặc tạo file `.env` ở root repo chứa `OPENAI_API_KEY=<key>` — notebook tự đọc).
3. Clone repo và chạy notebook:

```bash
git clone https://github.com/openai/openai-cookbook.git
cd openai-cookbook
pip install openai jupyter python-dotenv
jupyter notebook   # hoặc mở .ipynb trực tiếp trong VS Code
```

Mỗi notebook tự khai báo dependency riêng ở cell đầu (`%pip install ...`) — không có requirements.txt tổng.

## 4. Bản đồ chủ đề (theo tags trong registry)

Thống kê tags cho biết trọng tâm nội dung của cookbook:

| Nhóm chủ đề | Tags chính (số bài) | Nội dung |
|---|---|---|
| **Embeddings & RAG** | embeddings (99), tiktoken (13) | Nhóm LỚN NHẤT — search, classification, clustering, recommendation, vector DB |
| **Completions/Responses API** | completions (94), responses (33), api (4) | Gọi model, streaming, prompt engineering, structured outputs |
| **Agents** | agents (22), agents-sdk (22), mcp (8), tracing (9) | Multi-agent orchestration, Agents SDK, MCP, memory, tracing |
| **ChatGPT platform** | chatgpt (35), gpt-actions-library (29), chatgpt-productivity (11) | GPT Actions, Workspace Agents, Enterprise |
| **Evals** | evals (28), promptfoo (5) | Đánh giá model/agent, LLM-as-a-judge, image evals, realtime evals |
| **Tool use** | functions (27) | Function calling, tool required, OpenAPI spec |
| **Multimodal** | vision (16), images (9), audio (17), speech (12), realtime (5), dall-e (4) | GPT-4o/GPT-5 vision, GPT Image, Whisper, Realtime voice, Sora |
| **Fine-tuning** | fine-tuning (14) | SFT, DPO, RFT (reinforcement fine-tuning), distillation |
| **Reasoning models** | reasoning (13), gpt-5 (7) | o-series, GPT-5.x, reasoning + function calls |
| **Open models** | gpt-oss (13), open-models (13) | gpt-oss-20b/120b — chạy local, vLLM, Ollama |
| **Safety** | security (13), guardrails (5) | Moderation, hallucination guardrails, governed agents |
| **Codex** | codex (11) | Coding agent, repair loops, legacy migration |

## 5. Lộ trình học đề xuất

### Giai đoạn 1 — Nền tảng API (bắt buộc, ~1 tuần)

Học cách "nói chuyện" với model đúng cách trước khi làm gì phức tạp:

| Notebook | Học được gì |
|---|---|
| `examples/How_to_format_inputs_to_ChatGPT_models.ipynb` | Cấu trúc messages, roles |
| `examples/How_to_count_tokens_with_tiktoken.ipynb` | Token là gì, đếm token, ước lượng chi phí |
| `examples/How_to_stream_completions.ipynb` | Streaming response |
| `examples/How_to_handle_rate_limits.ipynb` | Retry, backoff, xử lý 429 |
| `examples/Structured_Outputs_Intro.ipynb` | Ép model trả JSON đúng schema — nền tảng của mọi integration |
| `examples/Using_logprobs.ipynb` | Đọc độ tự tin của model (classification, hallucination detection) |
| `examples/Prompt_Caching101.ipynb` + `Prompt_Caching_201.ipynb` | Giảm chi phí/latency với prompt caching |
| `examples/batch_processing.ipynb` | Batch API — xử lý hàng loạt giá rẻ |
| `articles/how_to_work_with_large_language_models.md` | Lý thuyết nền: LLM hoạt động thế nào |
| `articles/techniques_to_improve_reliability.md` | Kỹ thuật tăng độ tin cậy (CoT, self-consistency...) |

### Giai đoạn 2 — Prompt engineering (~3 ngày)

- `examples/gpt4-1_prompting_guide.ipynb` — guide prompting chính thức, rất chi tiết.
- `examples/gpt-5/` — prompting guide cho GPT-5.x (model mới nhất).
- `examples/Enhance_your_prompts_with_meta_prompting.ipynb` — dùng model viết prompt cho model.
- `examples/Optimize_Prompts.ipynb` — tối ưu prompt có hệ thống.
- `examples/Prompt_migration_guide.ipynb` — migrate prompt giữa các đời model.

### Giai đoạn 3 — Embeddings & RAG (~1-2 tuần, nhóm lớn nhất)

Thứ tự học:

1. `examples/Using_embeddings.ipynb` + `Get_embeddings_from_dataset.ipynb` — embedding là gì.
2. `examples/Semantic_text_search_using_embeddings.ipynb` — semantic search cơ bản.
3. `examples/Question_answering_using_embeddings.ipynb` — **notebook RAG kinh điển nhất của cookbook**.
4. `examples/Embedding_Wikipedia_articles_for_search.ipynb` + `Embedding_long_inputs.ipynb` — chunking, xử lý input dài.
5. Ứng dụng embeddings khác: `Classification_using_embeddings`, `Clustering`, `Recommendation_using_embeddings`, `Zero-shot_classification_with_embeddings`, `Code_search_using_embeddings`.
6. `examples/Search_reranking_with_cross-encoders.ipynb` — reranking nâng chất lượng retrieval.
7. `examples/Parse_PDF_docs_for_RAG.ipynb` + `RAG_with_graph_db.ipynb` — RAG thực chiến (PDF, graph DB).
8. `examples/vector_databases/` — chọn 1-2 provider để thực hành (23 provider: **pinecone, qdrant, chroma, redis, elasticsearch, weaviate, milvus, mongodb_atlas, supabase, azuresearch**...).
9. `examples/File_Search_Responses.ipynb` — file search built-in của Responses API (RAG không cần tự dựng vector DB).

### Giai đoạn 4 — Function calling & Tool use (~3 ngày)

- `examples/How_to_call_functions_with_chat_models.ipynb` — **notebook function calling kinh điển**.
- `examples/How_to_call_functions_for_knowledge_retrieval.ipynb` — function calling + retrieval.
- `examples/Function_calling_with_an_OpenAPI_spec.ipynb` — generate tool từ OpenAPI spec.
- `examples/Using_tool_required_for_customer_service.ipynb` — ép model luôn dùng tool.
- `examples/Fine_tuning_for_function_calling.ipynb` — fine-tune để gọi function chính xác hơn.
- `examples/reasoning_function_calls.ipynb` — function calling với reasoning models.

### Giai đoạn 5 — Agents (~1-2 tuần, trọng tâm hiện tại của OpenAI)

- `examples/Orchestrating_agents.ipynb` — pattern routines & handoffs (tiền thân của Agents SDK).
- `examples/agents_sdk/` — **26 notebook về OpenAI Agents SDK**: multi-agent portfolio analysis, dispute agent, session memory, deep research agent, deployment manager...
- `examples/Structured_outputs_multi_agent.ipynb` — multi-agent với structured outputs.
- `examples/object_oriented_agentic_approach/` — thiết kế agent theo OOP.
- `examples/mcp/` — Model Context Protocol: nối agent với tool/data ngoài.
- `examples/Build_a_coding_agent_with_GPT-5.1.ipynb` — tự build coding agent.
- Bài mới đáng chú ý (2026): *Building Reliable Agents with Memory and Compaction*, *Context Engineering for Personalization*, *Building Governed AI Agents*.

### Giai đoạn 6 — Evals (~1 tuần, kỹ năng bị đánh giá thấp nhưng quan trọng nhất khi làm production)

- `examples/evaluation/Getting_Started_with_OpenAI_Evals.ipynb` (trong thư mục `evaluation/`).
- `examples/Custom-LLM-as-a-Judge.ipynb` — dùng LLM chấm điểm LLM.
- `examples/evals/` — Evals API: bulk experimentation, monitoring, image evals.
- `examples/Developing_hallucination_guardrails.ipynb` — phát hiện hallucination.
- Bài mới: *Macro Evals for Agentic Systems* (2026-05), *Build an Agent Improvement Loop with Traces, Evals, and Codex* (2026-05).

### Giai đoạn 7 — Chuyên đề nâng cao (chọn theo nhu cầu)

| Chuyên đề | Điểm vào |
|---|---|
| **Fine-tuning** | `How_to_finetune_chat_models` → `Chat_finetuning_data_prep` → `Fine_tuning_direct_preference_optimization_guide` (DPO) → `Reinforcement_Fine_Tuning` (RFT) → `Leveraging_model_distillation_to_fine-tune_a_model` |
| **Vision / Image** | `Tag_caption_images_with_GPT4V` → `GPT_with_vision_for_video_understanding` → `Generate_Images_With_GPT_Image` → `examples/multimodal/` |
| **Audio / Voice** | `Whisper_prompting_guide` → `Whisper_processing_guide` → `Speech_transcription_methods` → `examples/voice_solutions/` (Realtime API) → `Realtime_prompting_guide` |
| **Video (Sora)** | `examples/sora/` + *Sora 2 Prompting Guide* (2026-03) |
| **Reasoning models** | `examples/o1/`, `examples/o-series/`, `examples/gpt-5/` |
| **Open models (gpt-oss)** | tag `gpt-oss` — chạy gpt-oss-20b/120b local (vLLM, Ollama), `articles/openai-harmony.md` (harmony response format), `articles/gpt-oss-safeguard-guide.md` |
| **Codex** | `examples/codex/` — Codex Prompting Guide, iterative repair loops, migrate legacy codebase, Goals |
| **Safety** | `How_to_use_moderation` → `How_to_use_guardrails` → `Developing_hallucination_guardrails` |
| **GPT Actions (ChatGPT)** | `examples/chatgpt/` — 29 bài GPT Actions library (BigQuery, Salesforce, SQL...), Workspace Agents |
| **Azure OpenAI** | `examples/azure/` — 12 notebook dùng API qua Azure |
| **Xử lý dữ liệu/text** | `Named_Entity_Recognition_to_enrich_text`, `Entity_extraction_for_long_documents`, `Summarizing_long_documents`, `Data_extraction_transformation`, `Unit_test_writing_using_a_multi-step_prompt` |

## 6. Thư mục `articles/` — 9 bài lý thuyết nên đọc

| Bài | Nội dung |
|---|---|
| `how_to_work_with_large_language_models.md` | Nhập môn: LLM là gì, prompt thế nào |
| `techniques_to_improve_reliability.md` | Kinh điển: CoT, few-shot, self-consistency, decomposition |
| `text_comparison_examples.md` | Tổng quan các bài toán so sánh text bằng embeddings |
| `what_makes_documentation_good.md` | Viết docs tốt (đáng đọc cho mọi dev) |
| `related_resources.md` | Danh sách tool/guide/course bên ngoài được OpenAI curate |
| `openai-harmony.md` | Harmony response format của gpt-oss |
| `gpt-oss-safeguard-guide.md` | Safety cho open models |
| `codex_exec_plans.md` | Codex execution plans |
| `chatgpt-agents-sales-meeting-prep.md` | Case study ChatGPT agents |

## 7. Xu hướng nội dung mới (2026) — cookbook đang tập trung vào đâu

Nhìn 30 bài mới nhất, trọng tâm hiện tại của OpenAI:

1. **Agents production-grade** — memory dài hạn (Oracle AI Agent Memory), compaction, governed agents, deployment manager, agent improvement loop (traces + evals + Codex).
2. **Codex như coding agent** — repair loops, Goals, migrate legacy codebase, sandbox agents (Daytona).
3. **Evals mọi thứ** — macro evals cho agentic systems, image evals, realtime evals, Promptfoo migration.
4. **Realtime/voice** — gpt-realtime-translate (live translation), realtime prompting guide.
5. **Multimodal thế hệ mới** — GPT-5.4/5.5 vision & document understanding, spatial reasoning, Sora 2, GPT Image prompting.
6. **ChatGPT Workspace Agents** — agent chạy trong ChatGPT Enterprise, trigger qua API.
7. **Hệ sinh thái mở rộng** — OpenAI models trên Amazon Bedrock, gpt-oss trên Cerebras, Skills in OpenAI API.

## 8. Cách dùng cookbook hiệu quả khi tự học

- **Đừng đọc tuần tự** — cookbook là reference, không phải sách giáo khoa. Chọn theo lộ trình mục 5 hoặc theo bài toán đang cần giải.
- **Chạy notebook, đừng chỉ đọc** — giá trị nằm ở code chạy được. Sửa input, đổi model, xem output thay đổi.
- **Dùng `registry.yaml` làm search index** — mỗi entry có `description` + `tags` + `date`, grep là ra bài cần tìm.
- **Chú ý `date`** — bài cũ (2022-2023) có thể dùng API cũ (Completions cũ, Assistants API). Ưu tiên bài mới hơn cho cùng chủ đề; API hiện hành là **Responses API**.
- **Áp dụng chéo** — concept (RAG, function calling, evals, agent orchestration) áp dụng được cho mọi LLM provider, không riêng OpenAI.
- Xem file kèm theo: `openai-cookbook-full-catalog.md` — danh mục đầy đủ 304 bài (title, path, date, tags) để tra cứu nhanh.

---
*Tài liệu tạo ngày 2026-07-17, dựa trên snapshot branch `main` của repo openai/openai-cookbook (304 entries trong registry.yaml).*
