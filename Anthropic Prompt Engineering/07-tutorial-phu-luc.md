# 07 — Tutorial GitHub: Phụ lục (Chaining · Tool Use · RAG)

> 3 phụ lục mở rộng vượt ra ngoài "một prompt đơn lẻ" — nền móng của kiến trúc agent hiện đại.

## 10.1 — Chaining Prompts (Chuỗi prompt)

**Nguyên lý:** *"Writing is rewriting"* — Claude chính xác hơn khi được yêu cầu xem lại chính kết quả của mình.

### Use case

- **Refinement:** yêu cầu sửa/cải thiện response ban đầu ("Make this story better").
- **Task nhiều bước:** chia bài toán phức tạp thành các bước tuần tự (trích tên → rồi sắp alphabet).
- **Conditional logic:** dùng output của Claude quyết định bước tiếp theo.
- **Verification / self-correction:** Claude tự review lỗi của mình (kèm "lối thoát" nếu không có lỗi).

### Kiến trúc

```text
1. Prompt ban đầu        → response 1
2. Response 1 + chỉ dẫn mới → response tinh chỉnh
3. Lặp tiếp nếu cần
```

Chìa khóa: **giữ nguyên lịch sử hội thoại** trong mảng `messages` qua các lần gọi.

> Liên hệ docs hiện tại: model mới tự xử lý đa số chuỗi bước bên trong (adaptive thinking); chỉ tách chain tường minh khi cần kiểm tra kết quả trung gian hoặc ép pipeline cố định. Pattern self-correction (draft → review → refine) vẫn là chain phổ biến nhất. Xem [04 §6](04-agentic-prompting.md).

**Bài tập:** chain nhiều lần gọi để trích xuất rồi xử lý thông tin qua các bước tinh chỉnh.

## 10.2 — Tool Use (Function Calling)

**Nguyên lý:** Claude **không tự gọi hàm được**. Cơ chế 4 bước:

1. Claude **xuất ra** tên tool + tham số (dưới dạng text có cấu trúc).
2. Claude **dừng** sinh response.
3. **Code của bạn** gọi hàm thật, lấy kết quả.
4. **Reprompt** Claude kèm kết quả → Claude trả lời tiếp.

### Cách triển khai trong tutorial (phong cách "thủ công" thời Claude 3)

- System prompt khai báo: "You have access to a set of functions..." + định nghĩa tool (tên, mô tả, tham số) theo **format XML**.
- Dùng `stop_sequences=["</function_calls>"]` để dừng đúng lúc Claude gọi hàm.
- Parse output lấy tên tool + tham số, chạy hàm thật.
- Trả kết quả về dạng:
  ```xml
  <function_results>
    <result><tool_name>...</tool_name><stdout>...</stdout></result>
  </function_results>
  ```
- Gửi lại toàn bộ hội thoại kèm kết quả.

### Điểm quan trọng

- Claude **tự biết khi nào cần tool** — câu hỏi kiến thức chung thì không gọi; cần dữ liệu real-time mới gọi.

> Liên hệ hiện tại: API ngày nay có **tool use native** (`tools` parameter, `tool_use`/`tool_result` blocks) — không cần tự parse XML nữa. Nhưng hiểu cơ chế thủ công này giúp nắm bản chất: tool use = vòng lặp "model đề xuất → runtime thực thi → model tiếp tục". Các kỹ thuật prompt cho tool use hiện đại (yêu cầu hành động tường minh, parallel calls) ở [04 §1–2](04-agentic-prompting.md).

## 10.3 — Search & Retrieval (RAG)

**Nguyên lý:** RAG (Retrieval-Augmented Generation) bổ sung kiến thức huấn luyện của Claude bằng dữ liệu ngoài được truy xuất lúc chạy → câu trả lời **có căn cứ** và cập nhật.

### Năng lực

- Tăng độ chính xác và độ liên quan nhờ thông tin truy xuất được.
- Nguồn: Wikipedia, internet, bộ tài liệu nội bộ.
- Truy xuất nâng cao: **embeddings + vector database**.

### Use case thực tế

1. **Knowledge synthesis:** gom và tổng hợp thông tin từ nhiều bài viết.
2. **Custom document search:** truy vấn tài liệu riêng của tổ chức.
3. **Vector DB integration:** embedding + vector store cho retrieval tinh vi.

### Kết nối với các kỹ thuật đã học

RAG là nơi hội tụ của: **XML tags** (bọc tài liệu truy xuất — [02 §4]), **long context** (tài liệu trên, câu hỏi dưới — [03 §1]), **quote extraction** (trích quote trước khi trả lời — [03 §1]), và **chống hallucination** (cho "lối thoát" khi tài liệu không chứa đáp án — [06 chương 8]).

> Anthropic cung cấp thêm cookbook RAG (Wikipedia search, vector DB) — tham khảo [anthropic-cookbook](https://github.com/anthropics/anthropic-cookbook).

---

Tiếp theo: [08 — Cheat Sheet tổng hợp](08-cheat-sheet.md)
