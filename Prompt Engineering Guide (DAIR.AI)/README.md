# Prompt Engineering Guide (DAIR.AI) — Bộ tài liệu học tập

Tổng hợp chi tiết từ giáo trình mã nguồn mở nổi tiếng nhất về Prompt Engineering (cập nhật 07/2026):

- **Website:** [promptingguide.ai](https://www.promptingguide.ai) — 13 ngôn ngữ, hơn 3 triệu người học.
- **Repo:** [dair-ai/Prompt-Engineering-Guide](https://github.com/dair-ai/Prompt-Engineering-Guide) — ~76.6k stars, nội dung MDX + Jupyter notebooks + slide bài giảng.
- **Tác giả:** DAIR.AI (Democratizing AI Research) — Elvis Saravia và cộng sự. Điểm mạnh nhất của giáo trình: **mọi kỹ thuật đều gắn với paper nghiên cứu gốc**, không phải kinh nghiệm truyền miệng.

## Mục lục

| File | Nội dung | Nguồn trên site |
|---|---|---|
| [01-nhap-mon-va-cau-hinh-llm.md](01-nhap-mon-va-cau-hinh-llm.md) | PE là gì · LLM settings (temperature, top_p...) · Cấu trúc prompt · 4 thành phần · Tips thiết kế · 7 dạng prompt mẫu | Introduction |
| [02-ky-thuat-co-ban.md](02-ky-thuat-co-ban.md) | Zero-shot · Few-shot · Chain-of-Thought (+ Zero-shot CoT, Auto-CoT) · Meta Prompting · Self-Consistency · Generated Knowledge · Prompt Chaining | Techniques (1/2) |
| [03-ky-thuat-nang-cao.md](03-ky-thuat-nang-cao.md) | Tree of Thoughts · RAG · ART · APE · Active-Prompt · Directional Stimulus · PAL · ReAct · Reflexion · Multimodal CoT · Graph Prompting | Techniques (2/2) |
| [04-ai-agents.md](04-ai-agents.md) | Agent là gì · Thành phần (planning, memory, tools) · Workflow vs Agent · Context Engineering · Function Calling · Deep Agents | AI Agents |
| [05-ung-dung-va-prompt-hub.md](05-ung-dung-va-prompt-hub.md) | Sinh data · Synthetic dataset cho RAG · Code generation · Case study phân loại job · Fine-tuning · Context caching · Prompt Hub theo 12 nhóm task | Applications + Prompt Hub |
| [06-models-va-research-findings.md](06-models-va-research-findings.md) | Cách guide phân tích từng model (ChatGPT, GPT-4, Claude, Gemini, Llama, Mistral, Phi...) · LLM Collection · Tóm tắt LLM Research Findings | Models + Research |
| [07-rui-ro-adversarial-prompting.md](07-rui-ro-adversarial-prompting.md) | Prompt injection · Prompt leaking · Jailbreaking (DAN...) · Các lớp phòng thủ · Factuality (chống bịa) · Biases | Risks & Misuses |
| [08-cheat-sheet.md](08-cheat-sheet.md) | Bảng chọn kỹ thuật theo task · Bảng settings · Bảng paper gốc · Anti-patterns · So sánh với giáo trình Anthropic/OpenAI | Tổng hợp |

## Lộ trình học gợi ý

### Người mới
`01 → 02` (nền tảng bắt buộc) → làm thử từng kỹ thuật trong playground → `03` (đọc hiểu, chưa cần thuộc) → `07` (rủi ro — nên biết sớm) → `08` đọng lại. `04–06` đọc khi cần đến.

### Người đã học giáo trình Anthropic/OpenAI trước đó
Giáo trình này bổ sung đúng phần 2 giáo trình kia thiếu: **gốc học thuật của từng kỹ thuật** (paper, năm, tác giả, benchmark) và **bức tranh toàn cảnh không gắn với một vendor**. Đọc nhanh `01`, đọc kỹ `02 → 03` (đây là phần giá trị nhất), rồi `04` và `07`. Dùng `08` để đối chiếu 3 giáo trình.

### Học sâu như nghiên cứu
Mỗi kỹ thuật ở `02`/`03` đều ghi paper gốc — đọc tài liệu này trước để nắm khung, sau đó đọc paper trên arXiv, rồi chạy notebook trong repo (`/notebooks`).

## 4 điểm cần nhớ về giáo trình này

1. **Research-first:** khác Anthropic docs (thực dụng, gắn Claude), guide này đi từ paper → kỹ thuật. Rất tốt để hiểu *tại sao* kỹ thuật hoạt động, và biết giới hạn đã được đo đạc của nó.
2. **Một số nội dung có tuổi:** nhiều trang viết ở thời GPT-3/GPT-3.5/Claude 3 (2022–2024). Nguyên lý vẫn đúng, nhưng với model reasoning hiện đại (GPT-5.x, Claude 4.x/5, Gemini 3...) nhiều kỹ thuật CoT thủ công đã được nội hóa vào model — xem bảng "còn dùng / đã lỗi thời" trong [08-cheat-sheet.md](08-cheat-sheet.md).
3. **Phần AI Agents là phần mới nhất** và cập nhật nhất — phản ánh chuyển dịch của ngành từ prompt engineering đơn lẻ sang context engineering + agentic systems.
4. **Prompt Hub + Notebooks là chỗ để thực hành:** ví dụ prompt thật theo 12 nhóm task, notebook chạy được trong repo.
