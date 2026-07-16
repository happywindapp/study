# 08 — Cheat Sheet: Tra nhanh khi viết prompt

> Tổng hợp từ cả 2 giáo trình. In ra / mở cạnh màn hình khi viết prompt thật.

## Checklist viết prompt (theo thứ tự ưu tiên)

1. ☐ **Rõ ràng chưa?** — đồng nghiệp đọc không ngữ cảnh có hiểu không? Format output, ràng buộc, phạm vi đã tường minh?
2. ☐ **Có WHY chưa?** — mỗi quy tắc kèm lý do để model tổng quát hóa đúng.
3. ☐ **Có ví dụ chưa?** — 3–5 ví dụ relevant + diverse + bọc `<example>`.
4. ☐ **Có XML tags chưa?** — tách instruction / context / data / examples.
5. ☐ **Có vai trò chưa?** — system prompt gán vai cụ thể.
6. ☐ **Tài liệu dài?** — data lên đầu, câu hỏi xuống cuối (+~30%), yêu cầu trích quote trước.
7. ☐ **Cần hành động?** — ra lệnh trực tiếp ("Change X"), không hỏi gợi ý.
8. ☐ **Task khó?** — yêu cầu suy luận/tự kiểm tra; chỉnh `effort`.
9. ☐ **Sợ bịa?** — cho "lối thoát" ("nếu không chắc, nói không biết") + đòi bằng chứng trước.

## Template 10 thành phần (tutorial chương 9)

```text
[1. Lượt user bắt đầu]
[2. Task context]     You are <vai>, your goal is <mục tiêu>.
[3. Tone context]     Maintain a <tone> tone.
[4. Rules chi tiết]   Rules: ... If unsure, say "I don't know".
[5. Examples]         <examples><example>...</example></examples>
[6. Input data]       <data>{{INPUT}}</data>
[7. Nhắc lại task]    Answer the user's question about <task>.  ← gần cuối
[8. Precognition]     Think step by step in <thinking> tags first.
[9. Output format]    Put your answer in <answer> tags.
[10. Prefill]         (cũ — nay dùng Structured Outputs)
```

Nguyên tắc: bắt đầu đủ thành phần → test → tinh gọn dần.

## Mẫu câu hay dùng (copy-paste)

| Mục đích | Câu prompt |
|---|---|
| Bỏ mở đầu | "Skip the preamble; respond directly without 'Here is...'" |
| Ép chọn 1 đáp án | "If you absolutely had to pick one, which would it be?" |
| Chống bịa | "Only answer if you know the answer with certainty. Otherwise say you don't know." |
| Neo tài liệu | "First extract relevant quotes in `<quotes>` tags, then answer based only on them." |
| Suy luận lộ ra | "Think step by step in `<thinking>` tags before giving your final answer in `<answer>` tags." |
| Tự kiểm tra | "Before finishing, verify your answer against [criteria]." |
| Văn xuôi, ít bullet | "Write in flowing prose paragraphs. Do not use lists unless items are truly discrete." |
| Ra lệnh hành động | "Change/Fix/Implement X" (không phải "Can you suggest...") |
| Tool song song | "If multiple tool calls have no dependencies, make them all in parallel." |
| Chống overengineering | "Only make directly requested changes. No extra features, abstractions, or refactors." |
| Giải pháp tổng quát | "Implement a solution that works for ALL valid inputs, not just the test cases." |
| Đọc code trước khi nói | "Never speculate about code you haven't opened. Read the file first." |

## Quy đổi kỹ thuật cũ → mới (Claude 3 era → Claude 4.x/5)

| Kỹ thuật trong tutorial | Hiện trạng | Thay thế |
|---|---|---|
| Prefill lượt assistant | ❌ Bỏ từ 4.6+ (lỗi 400) | Structured Outputs / system prompt "no preamble" |
| CoT thủ công `<thinking>` | ⚠️ Vẫn dùng được khi thinking tắt | Adaptive thinking + tham số `effort` |
| Extended thinking `budget_tokens` | ❌ Deprecated | Tham số `effort` (low→max) |
| Tool use tự parse XML | ❌ Lỗi thời | Tool use native (`tools`, `tool_use`/`tool_result`) |
| Lệnh ALL CAPS, lặp nhiều lần | ⚠️ Quá tay với model mới | Viết thường, rõ, một lần — model theo nghĩa đen |
| "Cứ N bước thì báo cáo" | ⚠️ Không cần | Model tự cập nhật tiến độ; chỉ mô tả style mong muốn |

## Tham số `effort`

`low` (latency) → `medium` (chi phí) → `high` (mức tối thiểu cho task cần trí tuệ) → `xhigh` (coding/agentic — tốt nhất) → `max` (lợi ích giảm dần).

## Bảng số ví dụ few-shot

| Task | Số ví dụ |
|---|---|
| Phân loại đơn giản | 2–3 |
| Format phức tạp | 3–5 (kèm edge case) |
| Task mở | 4–5 (đa dạng ngữ cảnh) |

## 7 quy tắc vàng (thuộc lòng)

1. Đồng nghiệp đọc không hiểu → Claude không hiểu.
2. Giải thích WHY, không chỉ WHAT.
3. 3–5 ví dụ > mọi mô tả dài dòng.
4. XML tags cho mọi prompt trộn nhiều loại nội dung.
5. Muốn hành động → ra lệnh, đừng hỏi.
6. Tài liệu dài: data trên, câu hỏi dưới.
7. Nói điều CẦN làm, không phải điều CẤM.
