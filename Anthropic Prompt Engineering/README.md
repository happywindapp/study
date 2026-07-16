# Anthropic Prompt Engineering — Bộ tài liệu học tập

Tổng hợp chi tiết từ 2 giáo trình chính thức của Anthropic (cập nhật 07/2026):

1. **Docs chính thức** — [docs.anthropic.com](https://docs.anthropic.com) → mục Prompt Engineering (nay redirect về platform.claude.com/docs): lý thuyết chuẩn, cập nhật theo model mới nhất.
2. **Interactive Tutorial** — [anthropics/prompt-eng-interactive-tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial): khóa học 9 chương + 3 phụ lục dạng Jupyter Notebook, học bằng thực hành (viết cho Claude 3 nhưng nguyên lý vẫn đúng).

## Mục lục

| File | Nội dung | Nguồn |
|---|---|---|
| [01-tong-quan-prompt-engineering.md](01-tong-quan-prompt-engineering.md) | PE là gì, điều kiện tiên quyết, thứ tự áp dụng 9 kỹ thuật, 7 quy tắc vàng, thay đổi ở model mới | Docs |
| [02-cac-ky-thuat-cot-loi.md](02-cac-ky-thuat-cot-loi.md) | Clear & direct · Add context (WHY) · Few-shot · XML tags · Role prompting | Docs |
| [03-ky-thuat-nang-cao.md](03-ky-thuat-nang-cao.md) | Long context · Thinking/effort · Kiểm soát output · Prefill → Structured Outputs | Docs |
| [04-agentic-prompting.md](04-agentic-prompting.md) | Tool use · Parallel calls · Long-horizon · Tự chủ/an toàn · Chống overengineering/hardcoding/hallucination · Subagents · Chaining | Docs |
| [05-tutorial-chuong-1-4.md](05-tutorial-chuong-1-4.md) | Ch.0–4: API structure · Clear & direct · Roles · Tách data/instruction | Tutorial |
| [06-tutorial-chuong-5-9.md](06-tutorial-chuong-5-9.md) | Ch.5–9: Format output & prefill · CoT · Few-shot · Chống bịa · **Template 10 thành phần** | Tutorial |
| [07-tutorial-phu-luc.md](07-tutorial-phu-luc.md) | Phụ lục: Chaining prompts · Tool use · RAG | Tutorial |
| [08-cheat-sheet.md](08-cheat-sheet.md) | Checklist, template, mẫu câu copy-paste, bảng quy đổi cũ→mới | Cả hai |

## Lộ trình học gợi ý

### Người mới (học từ thực hành)
`05 → 06 → 07` (theo mạch tutorial, có bài tập từng chương) → `01–04` (hệ thống hóa bằng lý thuyết chuẩn) → `08` (đọng lại).

Làm bài tập thật: clone repo tutorial, chạy notebook trong thư mục `Anthropic 1P` với API key riêng (hoặc dùng bản Google Sheets thân thiện hơn).

### Người đã biết cơ bản (học từ lý thuyết)
`01 → 02 → 03 → 04` → lướt `06` để lấy **template 10 thành phần** + `07` cho chaining/RAG → `08` làm tài liệu tra cứu hằng ngày.

## 3 điểm cần nhớ khi đối chiếu 2 nguồn

1. **Tutorial viết cho Claude 3** — một số kỹ thuật đã đổi ở Claude 4.x/5: prefill bị bỏ (→ Structured Outputs), CoT thủ công → adaptive thinking + `effort`, tool use XML thủ công → tool use native. Bảng quy đổi đầy đủ trong [08-cheat-sheet.md](08-cheat-sheet.md).
2. **Nguyên lý không đổi:** rõ ràng, ví dụ, XML tags, vai trò, lối thoát chống bịa, data-trên-câu-hỏi-dưới — vẫn là nền của mọi prompt tốt.
3. **PE là thực nghiệm khoa học:** định nghĩa tiêu chí thành công → có cách đo → thử-sửa lặp lại. Không có tiêu chí đo thì chưa nên tối ưu prompt.
