# 04 — AI Agents

> Nguồn: promptingguide.ai → mục **AI Agents** (Introduction to Agents · Agent Components · AI Workflows vs AI Agents · Context Engineering · Function Calling · Deep Agents). Đây là mục **mới và được cập nhật nhất** của giáo trình — phản ánh chuyển dịch của ngành từ prompt đơn lẻ sang hệ thống agentic.

## 1. Introduction to Agents — Agent là gì

**Định nghĩa làm việc:** AI agent = **LLM + khả năng dùng tool + memory + planning**, hoạt động trong vòng lặp để tự hoàn thành mục tiêu nhiều bước, thay vì trả lời một lượt.

Khác biệt cốt lõi so với chatbot/prompt đơn:
- **Tự quyết định bước tiếp theo** dựa trên observation (kết quả tool, môi trường), không đi theo kịch bản cứng.
- **Vòng lặp** perceive → reason → act → observe (chính là khuôn ReAct ở bài 03).
- Có thể **dừng, hỏi lại, sửa sai, thử cách khác**.

Use case điển hình guide nêu: research assistant, coding agent, customer support tự tra hệ thống, data analysis, browser/computer-use.

## 2. Agent Components — các thành phần

### 2.1 Planning (lập kế hoạch)
- Tách goal thành subtask; quyết định thứ tự; điều chỉnh khi có thông tin mới.
- Kỹ thuật nền: CoT/reasoning nội tại của model; task decomposition; với bài khó — ToT, plan-and-solve.
- **Reflection**: tự đánh giá kết quả bước trước để sửa kế hoạch (khuôn Reflexion).

### 2.2 Memory (bộ nhớ)
- **Short-term memory** = context window — mọi thứ trong prompt hiện tại (lịch sử hội thoại, tool results). Giới hạn bởi độ dài context và "context rot".
- **Long-term memory** = lưu trữ ngoài — vector store/database/file; truy hồi khi cần (RAG cho ký ức). Dùng cho: sở thích người dùng, bài học từ task trước, tri thức tích lũy.

### 2.3 Tools (công cụ)
- Cách agent tác động thế giới ngoài: search, API, code execution, đọc/ghi file, browser...
- Định nghĩa tool tốt = một dạng prompt engineering: tên rõ, mô tả chuẩn, tham số đơn giản, trả lỗi hữu ích.
- Cơ chế kỹ thuật: function calling (mục 5).

## 3. AI Workflows vs AI Agents

Phân biệt quan trọng (guide theo cùng khung với bài "Building effective agents" của Anthropic):

| | **Workflow** | **Agent** |
|---|---|---|
| Đường đi | **Định trước bằng code** — LLM là các bước trong pipeline | **LLM tự quyết** đường đi, dùng tool trong vòng lặp |
| Tính dự đoán | Cao, dễ test, dễ debug | Thấp hơn, linh hoạt hơn |
| Chi phí/latency | Kiểm soát được | Khó đoán (số vòng lặp thay đổi) |
| Hợp với | Task có cấu trúc ổn định, quy trình rõ | Task mở, không biết trước số bước |

Các pattern workflow guide liệt kê: **prompt chaining** (chuỗi), **routing** (phân loại rồi rẽ nhánh), **parallelization** (chạy song song rồi gộp), **orchestrator-workers** (điều phối–thợ), **evaluator-optimizer** (sinh–chấm–sửa vòng lặp).

**Nguyên tắc vàng:** *bắt đầu bằng giải pháp đơn giản nhất* — prompt đơn → chaining/workflow → chỉ dùng agent khi thực sự cần tính tự chủ. Agent hóa mọi thứ là anti-pattern.

## 4. Context Engineering

Guide dành hẳn cụm trang cho khái niệm này — kế thừa và mở rộng prompt engineering:

**Định nghĩa:** thiết kế và quản lý **toàn bộ những gì nằm trong context window** của model ở mỗi bước — không chỉ câu instruction, mà gồm: system prompt, lịch sử hội thoại, tool definitions, tool results, tài liệu truy xuất (RAG), memory, few-shot examples.

Vấn đề phải giải:
- **Context có hạn và "thối" dần (context rot):** nhồi càng nhiều, model càng loãng chú ý → phải chọn lọc.
- **Chiến lược chính:** (1) **select** — chỉ đưa thứ liên quan (retrieval, tool search); (2) **compress** — tóm tắt lịch sử dài (compaction); (3) **isolate** — tách việc cho subagent với context riêng; (4) **offload** — ghi ra file/memory ngoài rồi đọc lại khi cần.
- **Thứ tự và cấu trúc** trong context ảnh hưởng kết quả (thông tin quan trọng nên gần đầu/cuối; format nhất quán).

Câu đáng nhớ của mục này: *"Prompt engineering là tập con của context engineering"* — viết instruction tốt vẫn cần, nhưng thắng thua ở việc **đưa đúng thông tin vào đúng lúc**.

## 5. Function Calling

**Là gì:** khả năng model **sinh ra lời gọi hàm dạng JSON có cấu trúc** thay vì text tự do — nền tảng kỹ thuật của tool use.

Cơ chế chuẩn (mọi vendor tương tự):
1. Khai báo danh sách tool: `name`, `description`, `parameters` (JSON Schema).
2. Model đọc câu hỏi + danh sách tool → quyết định gọi tool nào, sinh arguments JSON.
3. **Ứng dụng của bạn tự chạy hàm thật** (model không chạy) → gửi kết quả lại cho model.
4. Model dùng kết quả để trả lời hoặc gọi tiếp tool khác.

Ví dụ guide dùng (OpenAI-style):
```json
{
  "name": "get_current_weather",
  "description": "Get the current weather in a given location",
  "parameters": {
    "type": "object",
    "properties": {
      "location": {"type": "string", "description": "The city and state, e.g. San Francisco, CA"},
      "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
    },
    "required": ["location"]
  }
}
```

Kinh nghiệm viết tool tốt (PE cho tool):
- `description` viết như prompt: nói rõ khi nào NÊN và KHÔNG NÊN dùng tool.
- Ít tham số, kiểu đơn giản, đặt tên không mơ hồ; dùng `enum` để chặn giá trị lạ.
- Use case: chuyển câu hỏi tự nhiên → truy vấn API/DB có cấu trúc; trích xuất entity ra JSON; agent đa tool.

## 6. Deep Agents

Phần mới nhất của guide — agent cho **task dài hơi, nhiều giờ/nhiều bước** (deep research, coding project lớn). 4 trụ:

1. **Explicit planning** — kế hoạch dạng danh sách việc (to-do) được cập nhật liên tục, không chỉ "nghĩ trong đầu"; giúp agent không lạc hướng sau hàng chục bước.
2. **Sub-agents** — tách việc cho agent con với context sạch riêng (isolate context); agent cha chỉ nhận kết luận, không nhận rác trung gian.
3. **File system / external memory** — ghi notes, kết quả trung gian, draft ra file; vượt giới hạn context window, cho phép resume.
4. **Long-horizon control** — vòng lặp bền bỉ: verify từng bước, xử lý lỗi, tránh trôi mục tiêu (goal drift).

Ví dụ hệ thật cùng khuôn: Claude Code, Deep Research (OpenAI/Google/Anthropic), coding agent chạy CI. Bài học tổng: deep agent = **context engineering + workflow discipline** đặt lên trên một model mạnh — không phải phép màu prompt đơn lẻ.

---

## Checklist đọng lại bài 04

- [ ] Nói được agent = model + tools + memory + planning trong vòng lặp perceive→reason→act.
- [ ] Phân biệt workflow (đường cố định) vs agent (tự quyết) — và chọn cái ĐƠN GIẢN trước.
- [ ] Nắm 4 chiến lược context engineering: select · compress · isolate · offload.
- [ ] Viết được function definition chuẩn JSON Schema với description tốt.
- [ ] Hiểu 4 trụ deep agents: planning rõ ràng, sub-agents, file system, long-horizon control.
