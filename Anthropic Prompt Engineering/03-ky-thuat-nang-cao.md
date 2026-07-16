# 03 — Kỹ thuật nâng cao (docs.anthropic.com)

> Long context, thinking/reasoning, kiểm soát output, và các thay đổi API quan trọng ở thế hệ model mới.

## 1. Long Context Prompting (đầu vào 20k+ tokens)

Khi làm việc với tài liệu dài / dữ liệu lớn, cấu trúc prompt quyết định chất lượng.

### 3 quy tắc

1. **Đặt dữ liệu dài LÊN ĐẦU prompt** — tài liệu nằm trên, câu hỏi/chỉ dẫn nằm dưới.
   - Test của Anthropic: câu hỏi đặt ở **cuối** prompt cải thiện chất lượng **~30%**, đặc biệt với nhiều tài liệu.
2. **Bọc tài liệu bằng XML tags** — `<documents>` / `<document index="n">` với `<source>` và `<document_content>`.
3. **Yêu cầu trích quote trước khi phân tích** — bảo Claude tìm và liệt kê các trích dẫn liên quan TRƯỚC, rồi mới trả lời. Giúp model tập trung vào phần liên quan, bỏ qua nhiễu.

### Ví dụ: quote extraction

```xml
You are an AI physician's assistant. Help doctors diagnose patient illnesses.

<documents>
  <document index="1">
    <source>patient_symptoms.txt</source>
    <document_content>{{PATIENT_SYMPTOMS}}</document_content>
  </document>
  <document index="2">
    <source>patient_records.txt</source>
    <document_content>{{PATIENT_RECORDS}}</document_content>
  </document>
</documents>

Find quotes from the records and history relevant to diagnosis.
Place them in <quotes> tags. Then list diagnostic information in <info> tags.
```

## 2. Thinking / Reasoning — Suy luận

### Bối cảnh model mới (quan trọng)

- Model đời mới (Opus 4.6+) dùng **adaptive thinking** — tự quyết định khi nào và suy nghĩ bao nhiêu, hiệu chỉnh theo tham số `effort` + độ khó câu hỏi.
- **Extended thinking với `budget_tokens` thủ công đã deprecated** (từ Opus 4.7+).

### Quy tắc prompt cho thinking

1. **Ưu tiên chỉ dẫn tổng quát hơn là kê từng bước** — "Think thoroughly about this problem" thường tốt hơn danh sách bước suy luận tự chế; model biết cách suy luận, hãy để nó tự do trong khung.
2. **Dùng tag `<thinking>` trong ví dụ few-shot** — Claude sẽ học phong cách suy luận đó và áp vào thinking block của chính nó.
3. **Chain-of-thought thủ công (fallback khi thinking tắt):** yêu cầu suy nghĩ trong `<thinking>` rồi trả lời trong `<answer>` — cách "cổ điển" trong tutorial GitHub (chương 6).
4. **Yêu cầu tự kiểm tra:** "Before finishing, verify your answer against [tiêu chí]."

### Điều hướng hành vi thinking

Tăng chất lượng phản tư sau khi dùng tool:

```text
After receiving tool results, carefully reflect on their quality and
determine optimal next steps before proceeding. Use your thinking to plan
and iterate based on new information, then take the best next action.
```

Giảm suy nghĩ thừa (tiết kiệm latency):

```text
Extended thinking adds latency; only use it when it meaningfully improves
answer quality — typically multistep reasoning problems. When in doubt,
respond directly.
```

### Tham số `effort` (thay cho budget_tokens)

| Mức | Dùng cho |
|---|---|
| `max` | Cố gắng tối đa — lợi ích giảm dần, có thể "overthink" |
| `xhigh` | Tốt nhất cho coding + agentic |
| `high` | Cân bằng token/trí tuệ — mức tối thiểu cho task cần thông minh |
| `medium` | Nhạy chi phí — đánh đổi trí tuệ lấy tốc độ |
| `low` | Nhạy latency, task không cần suy luận |

## 3. Kiểm soát Output & Formatting

### Nguyên tắc số 1: nói điều CẦN LÀM, không phải điều CẤM

```text
❌ "Do not use markdown"
✅ "Compose your response in smoothly flowing prose paragraphs"
```

### Các kỹ thuật

1. **XML format indicator:** "Write prose sections in `<smoothly_flowing_prose_paragraphs>` tags".
2. **Style của prompt lây sang output:** prompt viết bằng markdown dày đặc → output cũng markdown dày đặc. Muốn output là văn xuôi, viết prompt bằng văn xuôi.
3. **Khối chỉ dẫn format chi tiết** (dùng cho system prompt sản phẩm):

```xml
<avoid_excessive_markdown_and_bullet_points>
Write in clear, flowing prose using complete paragraphs. Use paragraph
breaks for organization, reserve markdown for `inline_code`, code blocks,
and headings (## ###). Avoid bold/italics. DO NOT use lists unless:
a) presenting truly discrete items, or b) the user explicitly requests
lists. Incorporate items into sentences instead. Your goal is readable,
flowing text, not fragmented bullet points.
</avoid_excessive_markdown_and_bullet_points>
```

4. **Tắt LaTeX** (model mới mặc định trả công thức bằng LaTeX):

```text
Format response in plain text only. Do not use LaTeX, MathJax, or markup
notation like \( \), $, or \frac{}{}. Use standard text: "/" for division,
"*" for multiplication, "^" for exponents.
```

### Phong cách giao tiếp mặc định của model mới

- Trực tiếp, bám sự thật (không tự khen kết quả).
- Ít máy móc, tự nhiên hơn.
- Ít dài dòng — bỏ qua đoạn tóm tắt nếu không được yêu cầu. Muốn có tóm tắt sau tool use: *"After tool use, provide a quick summary of the work."*

## 4. Prefill Response → Structured Outputs (thay đổi API lớn)

**Prefill** (điền sẵn phần đầu câu trả lời của assistant) là kỹ thuật kinh điển — được dạy trong tutorial GitHub chương 5. **Nhưng từ Claude 4.6+ đã bị loại bỏ** (trả lỗi 400 nếu prefill ở lượt cuối).

### Bảng migration

| Mục đích cũ của prefill | Cách thay thế hiện nay |
|---|---|
| Ép format (vd. bắt đầu bằng `{` để ra JSON) | **Structured Outputs API** — ràng buộc schema |
| Bỏ lời mở đầu ("Here is...") | System prompt: *"Respond directly without preamble. No 'Here is...', 'Based on...'"* |
| Viết tiếp đoạn dở | Đưa vào user message: *"Your previous response ended with [text]. Continue from where you left off."* |
| Bơm ngữ cảnh vào vai assistant | Đưa vào user turn hoặc dùng tool |

> Khi đọc tutorial GitHub (file 06), phần "Speaking for Claude / prefill" hãy hiểu là **kỹ thuật lịch sử** — nguyên lý vẫn đáng học (định hình đầu ra bằng cách "mớm" đầu câu), nhưng cách thực thi hiện đại là Structured Outputs.

## 5. Vision & các mẹo theo năng lực

- Model mới (Opus 4.5/4.6+) mạnh hơn hẳn về **xử lý ảnh + trích xuất dữ liệu**, kể cả nhiều ảnh một lúc.
- **Crop tool / skill** giúp model "zoom" vào vùng ảnh → tăng độ chính xác đọc chi tiết nhỏ.
- Phân tích video: tách thành các frame rồi đưa lần lượt.

---

Tiếp theo: [04 — Agentic Prompting](04-agentic-prompting.md)
