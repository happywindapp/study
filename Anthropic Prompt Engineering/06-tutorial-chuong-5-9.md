# 06 — Tutorial GitHub: Chương 5–9 (Kỹ thuật + Tổng hợp)

> Tiếp nối [05](05-tutorial-chuong-1-4.md). Chương 5–8 là các kỹ thuật tinh chỉnh; chương 9 tổng hợp tất cả thành template 10 thành phần cho prompt production.

## Chương 5 — Formatting Output & Speaking for Claude

**Mục tiêu:** kiểm soát format đầu ra bằng XML tags và prefill.

### 3 cách kiểm soát format

1. **Bọc output bằng XML:** "Write a haiku about [ANIMAL]. Put it in `<haiku>` tags." → trích xuất bằng code dễ, lọc bỏ lời mở đầu.
2. **Ép JSON:** "Write a haiku using JSON format with keys 'first_line', 'second_line', 'third_line'."
3. **Tag động theo biến:** "Rewrite it in [STYLE]. Use `<[STYLE]_email>` tags."

### Kỹ thuật "Speaking for Claude" (prefill)

Đặt sẵn text vào lượt `assistant` → Claude tin rằng nó **đã nói** phần đó và viết tiếp từ đó:

```text
User:      Write a haiku about cats.
Assistant: <haiku>        ← prefill
→ Claude điền thẳng nội dung haiku, không có preamble.
```

Lợi ích: parse sạch, bỏ mở đầu, tiết kiệm token (kết hợp `stop_sequences`).

> ⚠️ **Cập nhật quan trọng:** prefill ở lượt cuối **không còn được hỗ trợ từ Claude 4.6+** (lỗi 400). Nguyên lý "mớm đầu câu để định hình output" vẫn đúng, nhưng cách làm hiện đại là **Structured Outputs API** hoặc system prompt "trả lời thẳng, không mở đầu". Xem bảng migration trong [03](03-ky-thuat-nang-cao.md#4-prefill-response--structured-outputs-thay-đổi-api-lớn).

**Bài tập:** format haiku/JSON/email bằng XML và JSON; thực hành prefill.

## Chương 6 — Precognition (Thinking Step by Step)

**Mục tiêu:** tăng độ chính xác cho task phức tạp bằng suy luận thành lời.

### Nguyên lý

- Cho Claude "thời gian suy nghĩ" từng bước làm nó chính xác hơn, nhất là task phức tạp.
- **Insight then chốt: "Thinking only counts when it's out loud"** — suy nghĩ phải được viết ra mới có tác dụng; model không "suy nghĩ ngầm" được.
- Bước suy luận trung gian được viết ra giúp tự sửa sai lầm ban đầu.
- Lưu ý: Claude nhạy với **thứ tự lựa chọn** — có xu hướng thiên về option thứ hai.

### 2 pattern

1. **Phân tích 2 chiều trước khi kết luận** (review phim):
   ```text
   Write the best arguments for each side in <positive-argument> and
   <negative-argument> XML tags, then answer.
   ```
   → lộ ra ngữ cảnh tinh tế, tránh kết luận vội.

2. **Brainstorm trước khi trả lời** (fact check):
   ```text
   First brainstorm about some actors and their birth years in
   <brainstorm> tags, then give your answer.
   ```
   → giảm hallucination nhờ ép suy luận dựa trên bằng chứng.

Pattern chung: dùng XML tag (`<brainstorm>`, `<analysis>`, `<argument>`) tạo **vùng suy luận tường minh** trước đáp án cuối.

> Ghi chú model mới: đây là CoT "thủ công" thời Claude 3. Model hiện tại có **adaptive thinking** tự động — nhưng pattern này vẫn hữu ích khi thinking tắt hoặc khi muốn suy luận nằm trong output để kiểm tra. Xem [03](03-ky-thuat-nang-cao.md).

**Bài tập:** áp CoT vào câu đố logic và câu hỏi sự kiện; đo mức cải thiện độ chính xác.

## Chương 7 — Using Examples (Few-Shot Prompting)

**Mục tiêu:** dùng ví dụ để dẫn dắt format và hành vi.

### Khái niệm

- **Zero-shot** = không ví dụ · **Few-shot** = có ví dụ · **N-shot** = n ví dụ.
- "Giving Claude examples of how you want it to behave is extremely effective" — cho cả độ chính xác lẫn format.

### 2 ứng dụng minh họa

1. **Chỉnh tone:** hỏi "Will Santa bring me presents on Christmas?" — không ví dụ → trả lời khô cứng; kèm vài Q&A mẫu giọng phụ huynh ấm áp → Claude tự bắt giọng đó.
2. **Trích xuất theo format:** đưa 1 ví dụ "Dr. Liam Patel [NEUROSURGEON]" → Claude tự ngoại suy pattern trích tên + nghề, không cần mô tả dài dòng. Kết hợp prefill tag mở đầu để chốt cấu trúc.

**Insight:** ví dụ thường hiệu quả hơn chỉ dẫn dài — "show, don't tell".

**Bài tập:** phân loại email khách hàng bằng few-shot thay vì viết rubric chi tiết.

## Chương 8 — Avoiding Hallucinations

**Mục tiêu:** giảm khẳng định sai/bịa.

**Định nghĩa:** hallucination = khẳng định sai được sinh ra trong lúc model cố tỏ ra hữu ích.

### 3 kỹ thuật

1. **Cho Claude một "lối thoát" (out):**
   ```text
   Only answer if you know the answer with certainty.
   ```
   → "I don't know" tốt hơn một đáp án bịa. Không có lối thoát, model bị ép "phải trả lời gì đó".

2. **Đòi bằng chứng trước:**
   ```text
   Extract the most relevant quote from the document and consider whether
   it answers the question or lacks sufficient detail before responding.
   ```
   → neo câu trả lời vào tài liệu; nếu thông tin không tồn tại, bước trích quote sẽ lộ ra ngay.
   - Ví dụ trong bài: hỏi "Matterport's subscriber base on May 31, 2020?" → model lẫn ngày, bịa số; ép trích quote trước → phát hiện tài liệu không có dữ liệu ngày đó.

3. **Hạ temperature:** 0 = ổn định, đáng tin; 1 = sáng tạo nhưng kém tin cậy.

Ngoài ra, các kỹ thuật đã học (role, CoT, examples) đều gián tiếp giảm hallucination.

**Bài tập:** áp "out" + evidence-grounding vào câu hỏi sự kiện, so sánh độ tin cậy.

## Chương 9 — Complex Prompts from Scratch (Tổng hợp)

**Mục tiêu:** ghép tất cả kỹ thuật thành **template 10 thành phần** cho prompt production.

### Cấu trúc prompt 10 thành phần (thứ tự chuẩn)

| # | Thành phần | Nội dung | Bắt buộc? |
|---|---|---|---|
| 1 | **User Role** | Messages API luôn bắt đầu bằng lượt `user` | ✔ |
| 2 | **Task Context** | Vai Claude đảm nhận + mục tiêu tổng thể | ✔ |
| 3 | **Tone Context** | Phong cách giao tiếp mong muốn | ○ |
| 4 | **Detailed Task Description & Rules** | Chi tiết task + ràng buộc; kèm "lối thoát" nếu không chắc | ✔ |
| 5 | **Examples** | Mẫu response lý tưởng trong XML tags; nhiều ví dụ càng mạnh | ✔ khuyến nghị |
| 6 | **Input Data** | Dữ liệu cần xử lý, bọc trong XML tags | ✔ nếu có data |
| 7 | **Immediate Task Description** | Nhắc lại việc cần làm ngay — đặt **gần cuối** prompt | ✔ |
| 8 | **Precognition** | Yêu cầu suy nghĩ từng bước trước khi trả lời | ○ |
| 9 | **Output Formatting** | Cấu trúc response mong muốn | ○ |
| 10 | **Prefilling** | Mớm đầu response qua lượt assistant (nay → Structured Outputs) | ○ |

### Triết lý sử dụng

- "Not all prompts need every element" — **bắt đầu với nhiều thành phần, rồi tinh gọn dần** theo kết quả test.
- "Prompt engineering is about scientific trial and error" — thử nghiệm khoa học, mix & match, đo lường.
- Thứ tự có thể đảo tùy độ ưu tiên của task (ví dụ legal đưa rule trích dẫn lên cao).

### 4 case study trong chương

1. **Career Coach Chatbot ("Joe")** — task context (nhân vật Joe), tone thân thiện, rule giữ vai, examples hội thoại, prefill mở đầu; giữ lịch sử hội thoại trong `messages`.
2. **Legal Services** — phân tích tài liệu pháp lý: vai luật sư, rule bắt buộc trích nguồn, examples format trả lời pháp lý, precognition, output formatting có citation.
3. **Financial Services (bài tập 9.1)** — phân tích văn bản thuế, trả lời câu hỏi quy định — tự điền các thành phần theo khung legal.
4. **Coding Assistant (bài tập 9.2)** — review code theo hướng dạy học, tự thiết kế các thành phần prompt phù hợp phản hồi sư phạm.

---

Tiếp theo: [07 — Phụ lục: Chaining, Tool Use, RAG](07-tutorial-phu-luc.md)
