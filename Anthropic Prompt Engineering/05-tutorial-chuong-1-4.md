# 05 — Tutorial GitHub: Chương 0–4 (Nền tảng)

> Nguồn: [anthropics/prompt-eng-interactive-tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial) — tutorial tương tác dạng Jupyter Notebook (thư mục `Anthropic 1P`), có bản Google Sheets và bản AWS Bedrock.
>
> **Mục tiêu khóa học:** nắm cấu trúc prompt tốt · nhận diện các lỗi thường gặp và kỹ thuật 80/20 để xử lý · hiểu điểm mạnh/yếu của Claude · tự xây prompt mạnh từ đầu.
>
> Tutorial dùng Claude 3 Haiku, `temperature = 0` để kết quả lặp lại được. Học tuần tự từng chương, mỗi chương có bài tập (exercise) kèm đáp án.

## Chương 0 — Tutorial How-To (Setup)

- Pattern chuẩn: hàm helper `get_completion()` gửi prompt lên API, nhận response.
- Tham số: model `claude-3-haiku`, `max_tokens=2000`, `temperature=0`.
- Tinh thần xuyên suốt: **"Play around with the prompt string"** — prompt rất nhạy, thay đổi nhỏ cho output khác; học bằng cách thử-sửa liên tục.

## Chương 1 — Basic Prompt Structure

**Mục tiêu:** hiểu "hợp đồng" của Messages API và quy tắc định dạng message.

### Nội dung chính

- 3 tham số bắt buộc: `model`, `max_tokens`, `messages`.
- Mỗi message trong `messages` cần `role` (`user` hoặc `assistant`) + `content`.
- **Role phải xen kẽ** user/assistant; **message đầu tiên luôn là `user`**.
- **System prompt** là tham số `system` riêng, KHÔNG nằm trong mảng `messages` — dùng để đặt ngữ cảnh, chỉ dẫn, luật chơi trước câu hỏi. System prompt tốt giúp Claude bám luật hơn rõ rệt.

### Lỗi thường gặp

- Thiếu `role` hoặc `content` → API error.
- Hai message `user` liên tiếp (role không xen kẽ) → API error.

**Bài tập:** format message đúng chuẩn, debug các API call sai.

## Chương 2 — Being Clear and Direct

**Mục tiêu:** loại bỏ mơ hồ bằng chỉ dẫn tường minh.

### Quy tắc cốt lõi

> "Claude has no context on what to do aside from what you literally tell it." — Claude chỉ biết đúng những gì bạn viết ra.

**Golden Test:** đưa prompt cho đồng nghiệp tự làm theo — họ bối rối thì Claude cũng bối rối.

### Ví dụ trước/sau

| Mơ hồ | Rõ ràng |
|---|---|
| "Write a haiku about robots." | "Write a haiku about robots. **Skip the preamble; go straight into the poem.**" |
| "Who is the best basketball player of all time?" | "Who is the best basketball player of all time? **If you absolutely had to pick one player, who would it be?**" |

**Insight:** sự trực tiếp buộc Claude bỏ vòng vo, bỏ mở đầu, bỏ "còn tùy quan điểm..." — muốn một đáp án thì phải ép chọn một đáp án.

**Bài tập:** sửa các prompt mơ hồ bằng cách thêm ràng buộc tường minh và câu hỏi ép ra quyết định.

## Chương 3 — Assigning Roles (Role Prompting)

**Mục tiêu:** tăng chất lượng và điều chỉnh phong cách qua việc gán vai (persona).

### 4 lợi ích

1. **Tăng hiệu năng** trên nhiều lĩnh vực — viết, code, tóm tắt.
2. **Điều chỉnh tone/giọng văn** — góc nhìn, cách diễn đạt đổi theo vai.
3. **Cải thiện suy luận** — đặc biệt hiệu quả với logic và toán ("You are a logic bot designed to answer complex logic problems" giúp giải được câu đố trước đó sai).
4. **Tinh chỉnh theo khán giả** — "a cat talking to skateboarders" cho output khác hẳn chỉ "a cat".

### Ví dụ

- Không vai: "Explain skateboarding" → trả lời trung tính, chung chung.
- Có vai: "You are a cat. Explain skateboarding." → góc nhìn của mèo.

Vai có thể đặt ở system prompt hoặc trong lượt user.

**Bài tập:** dùng role prompting để cải thiện câu đố logic; quan sát thay đổi phong cách khi thêm ngữ cảnh khán giả.

## Chương 4 — Separating Data and Instructions

**Mục tiêu:** tách dữ liệu biến thiên khỏi chỉ dẫn → tạo **prompt template tái sử dụng**.

### Vấn đề

Khi dữ liệu không được tách khỏi chỉ dẫn, Claude nhầm ranh giới. Ví dụ kinh điển:

```text
Yo Claude. {EMAIL} Make this email more polite
```

→ Claude tưởng "Yo Claude" là một phần của email cần sửa.

### Giải pháp: XML tags

> "Claude was trained specifically to recognize XML tags as a prompt organizing mechanism." — Claude được huấn luyện riêng để nhận diện XML tag làm cơ chế tổ chức prompt.

```text
Yo Claude. <email>{EMAIL}</email> Make this email more polite
```

Nội dung biến thiên nằm **trong tag**, chỉ dẫn nằm **ngoài tag** — ranh giới tuyệt đối rõ.

### Nguyên tắc

- **"Small details matter"** — pattern prompt rất nhạy; lỗi nhỏ ở template lan thành lỗi lớn ở response.
- Đây cũng là **lớp phòng thủ đầu tiên chống prompt injection**: dữ liệu người dùng bị "nhốt" trong tag, khó bị hiểu nhầm thành lệnh.

**Bài tập:** chuyển prompt phi cấu trúc thành template dùng XML tag; test với nhiều bộ dữ liệu đầu vào.

---

Tiếp theo: [06 — Tutorial chương 5–9](06-tutorial-chuong-5-9.md)
