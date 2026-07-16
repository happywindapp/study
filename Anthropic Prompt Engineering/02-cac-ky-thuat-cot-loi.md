# 02 — Các kỹ thuật cốt lõi (docs.anthropic.com)

> 5 kỹ thuật nền tảng, xếp theo thứ tự hiệu quả giảm dần. Đây là nhóm phải thành thạo trước khi học các kỹ thuật nâng cao.

## 1. Be Clear and Direct — Rõ ràng, trực tiếp

**Nguyên lý:** Claude phản hồi tốt nhất với chỉ dẫn tường minh, cụ thể. Sự mơ hồ trong prompt được model coi là mơ hồ thật — nó sẽ tự đoán, và có thể đoán sai ý bạn.

**Golden Rule:** *Đưa prompt cho một đồng nghiệp đọc mà không giải thích gì thêm. Nếu họ bối rối, Claude cũng sẽ bối rối.*

### Quy tắc

- Nêu cụ thể **format đầu ra** mong muốn và các **ràng buộc**.
- Khi thứ tự quan trọng → viết chỉ dẫn thành **các bước đánh số**.
- Muốn model làm "vượt mong đợi" → phải **yêu cầu tường minh**, đừng mong nó tự suy ra.
- Hình dung Claude như "nhân viên mới xuất sắc nhưng thiếu ngữ cảnh về quy ước của bạn".

### Ví dụ

```text
❌ KÉM HIỆU QUẢ:
"Create an analytics dashboard"

✅ HIỆU QUẢ:
"Create an analytics dashboard. Include as many relevant features and
interactions as possible. Go beyond the basics to create a fully-featured
implementation."
```

**Vì sao hoạt động:** loại bỏ mơ hồ về phạm vi và mức độ đầu tư. Claude tự "hiệu chỉnh" độ chi tiết theo kỳ vọng được nêu tường minh.

## 2. Add Context — Cung cấp lý do (WHY)

**Nguyên lý:** Cho Claude biết **động cơ/lý do** đằng sau chỉ dẫn, không chỉ mệnh lệnh. Model tổng quát hóa từ lời giải thích tốt hơn nhiều so với từ lệnh cấm khô khan.

### Ví dụ

```text
❌ KÉM HIỆU QUẢ:
"NEVER use ellipses"

✅ HIỆU QUẢ:
"Your response will be read aloud by a text-to-speech engine, so never
use ellipses since the text-to-speech engine will not know how to
pronounce them."
```

**Vì sao hoạt động:** khi biết WHY, Claude suy luận đúng cả trong những tình huống chưa được dặn trước (ví dụ tự tránh cả các ký hiệu khác mà TTS không đọc được).

## 3. Use Examples — Few-shot / Multishot Prompting

**Nguyên lý:** Ví dụ là cách **đáng tin cậy nhất** để điều khiển format, tone, cấu trúc đầu ra. Vài ví dụ tốt tăng cả độ chính xác lẫn độ ổn định.

### Quy tắc

- Dùng **3–5 ví dụ** cho kết quả tốt nhất.
- Ví dụ phải **RELEVANT** — phản ánh sát use case thật.
- Ví dụ phải **DIVERSE** — phủ edge case, đủ đa dạng để model không học nhầm pattern ngoài ý muốn.
- Ví dụ phải **STRUCTURED** — bọc trong tag `<example>` (nhiều ví dụ thì bọc chung trong `<examples>`), để model phân biệt ví dụ với chỉ dẫn.

### Bảng: dùng bao nhiêu ví dụ

| Tình huống | Số ví dụ | Đặc điểm |
|---|---|---|
| Phân loại đơn giản | 2–3 | Mapping input → output rõ ràng |
| Format phức tạp | 3–5 | Cho thấy edge case, biến thể tone |
| Task mở (open-ended) | 4–5 | Output đa dạng, ngữ cảnh khác nhau |

**Vì sao hoạt động:** pattern matching từ ví dụ cụ thể mạnh hơn chỉ dẫn trừu tượng — nhất là với format và tone (những thứ rất khó mô tả bằng lời).

## 4. XML Tags — Cấu trúc hóa prompt

**Nguyên lý:** Khi prompt trộn nhiều loại nội dung (chỉ dẫn + ngữ cảnh + ví dụ + dữ liệu đầu vào), XML tags giúp Claude parse **không nhầm lẫn** phần nào là gì.

### Quy tắc

- Dùng tên tag **nhất quán, mô tả rõ nghĩa**: `<instructions>`, `<context>`, `<input>`, `<examples>`, `<document>`...
- **Lồng tag** khi nội dung có phân cấp tự nhiên.
- Không có bộ tag "chuẩn" bắt buộc — quan trọng là nhất quán trong cùng prompt.

### Ví dụ: nhiều tài liệu đầu vào

```xml
<documents>
  <document index="1">
    <source>annual_report_2023.pdf</source>
    <document_content>
      {{ANNUAL_REPORT}}
    </document_content>
  </document>
  <document index="2">
    <source>competitor_analysis_q2.xlsx</source>
    <document_content>
      {{COMPETITOR_ANALYSIS}}
    </document_content>
  </document>
</documents>

Analyze the annual report and competitor analysis. Identify strategic
advantages and recommend Q3 focus areas.
```

**Vì sao hoạt động:** vùng nội dung được gắn nhãn → model xác định ranh giới chính xác, giảm hẳn việc lẫn dữ liệu vào chỉ dẫn (nguồn gốc của nhiều lỗi khó hiểu và cả prompt injection).

## 5. Role Prompting — Gán vai trò qua System Prompt

**Nguyên lý:** Đặt vai trò trong system prompt giúp **neo** hành vi, tone, độ sâu chuyên môn cho toàn bộ hội thoại. Chỉ một câu cũng tạo khác biệt.

### Ví dụ

```text
System: "You are a helpful coding assistant specializing in Python."
```

```text
System: "You are the General Counsel of a Fortune 500 tech company."
→ Phân tích hợp đồng sẽ sâu, thận trọng, nhìn rủi ro pháp lý —
  khác hẳn khi không có vai trò.
```

**Vì sao hoạt động:** vai trò tự động kéo theo tone, mức chi tiết, khung nhìn — không cần liệt kê từng hành vi một.

### Lưu ý thực tế

- Vai trò đặt ở **system prompt**; dữ liệu và yêu cầu cụ thể đặt ở **user message**.
- Vai trò càng cụ thể càng tốt: "senior Go engineer chuyên hệ thống trading độ trễ thấp" > "lập trình viên".

---

## Tổng kết chương

| Kỹ thuật | Khi nào dùng | Sai lầm phổ biến |
|---|---|---|
| Clear & direct | Luôn luôn — mặc định | Nghĩ model "tự hiểu ý" |
| Add context (WHY) | Khi có quy tắc/ràng buộc | Chỉ ra lệnh cấm, không giải thích |
| Examples | Format/tone khó mô tả bằng lời | Ví dụ quá giống nhau, thiếu edge case |
| XML tags | Prompt trộn nhiều loại nội dung | Tag đặt tên tùy tiện, không nhất quán |
| Role | Cần chuyên môn/tone ổn định | Nhét cả dữ liệu vào system prompt |

Tiếp theo: [03 — Kỹ thuật nâng cao](03-ky-thuat-nang-cao.md)
