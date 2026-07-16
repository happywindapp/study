# 01 — Tổng quan Prompt Engineering (theo docs.anthropic.com)

> Nguồn: [Anthropic Prompt Engineering Overview](https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/overview) (docs.anthropic.com redirect về platform.claude.com)

## 1. Prompt Engineering là gì và khi nào cần

Prompt engineering là quá trình thiết kế, tinh chỉnh câu lệnh (prompt) để mô hình LLM cho ra kết quả đúng ý, ổn định và đo lường được. Anthropic nhấn mạnh: **prompt engineering là phương pháp cải thiện nhanh nhất, rẻ nhất** so với fine-tuning — sửa prompt chỉ mất vài phút, không cần dữ liệu huấn luyện, không cần GPU.

### Điều kiện tiên quyết TRƯỚC KHI tối ưu prompt

Anthropic yêu cầu 3 thứ phải có trước:

1. **Định nghĩa rõ tiêu chí thành công** (success criteria) cho use case — thế nào là "kết quả tốt"?
2. **Có cách kiểm chứng thực nghiệm** (empirical testing) so với tiêu chí đó — eval set, test case.
3. **Có bản prompt nháp đầu tiên** để cải thiện — không tối ưu từ hư vô.

### Khi nào KHÔNG nên prompt engineering

Một số tiêu chí thành công không giải quyết được bằng prompt — ví dụ yêu cầu về **latency** hoặc **chi phí** thường được giải quyết tốt hơn bằng cách **chọn model khác** (model nhỏ hơn, nhanh hơn), không phải bằng cách viết prompt hay hơn.

## 2. Thứ tự áp dụng kỹ thuật (từ hiệu quả rộng nhất → hẹp nhất)

Đây là "xương sống" của toàn bộ giáo trình docs. Khi cần cải thiện prompt, áp dụng **theo đúng thứ tự** sau — kỹ thuật đứng trước có tác động rộng và lớn hơn:

| # | Kỹ thuật | Tác dụng chính | File chi tiết |
|---|----------|----------------|----------------|
| 1 | **Be clear and direct** — rõ ràng, trực tiếp | Nền tảng, tác động lớn nhất | [02](02-cac-ky-thuat-cot-loi.md) |
| 2 | **Add context** — giải thích lý do (WHY) | Giúp model tổng quát hóa đúng | [02](02-cac-ky-thuat-cot-loi.md) |
| 3 | **Use examples** — few-shot/multishot | Định hình format, tone, cấu trúc | [02](02-cac-ky-thuat-cot-loi.md) |
| 4 | **XML tags** — cấu trúc hóa prompt | Parse chính xác, tách instruction/data | [02](02-cac-ky-thuat-cot-loi.md) |
| 5 | **Role prompting** — gán vai trò (system prompt) | Neo tone, độ sâu, góc nhìn | [02](02-cac-ky-thuat-cot-loi.md) |
| 6 | **Long context** — prompt với 20k+ tokens | Xử lý tài liệu dài | [03](03-ky-thuat-nang-cao.md) |
| 7 | **Tool use optimization** — hướng dẫn hành động | Agent thực sự làm, không chỉ gợi ý | [04](04-agentic-prompting.md) |
| 8 | **Thinking / reasoning** — suy luận | Bài toán phức tạp nhiều bước | [03](03-ky-thuat-nang-cao.md) |
| 9 | **Agentic patterns** — điều phối đa bước | Hệ thống agent dài hơi | [04](04-agentic-prompting.md) |

## 3. Bảy "quy tắc vàng" xuyên suốt

1. **Golden Rule of Clear Prompting**: đưa prompt cho đồng nghiệp đọc mà không có ngữ cảnh gì thêm — nếu họ bối rối thì Claude cũng bối rối.
2. **Giải thích WHY, không chỉ WHAT** — model tổng quát hóa từ lý do tốt hơn từ lệnh cấm.
3. **3–5 ví dụ** là cách steering đáng tin cậy nhất cho format/tone.
4. **XML tags** giảm hiểu nhầm khi prompt trộn nhiều loại nội dung.
5. **Yêu cầu hành động tường minh** ("Hãy sửa hàm này") thay vì gợi ý ("Bạn có thể gợi ý sửa gì không?").
6. **Câu hỏi đặt CUỐI prompt** với tài liệu dài — tăng chất lượng ~30% trong test của Anthropic.
7. **Nói điều CẦN LÀM, không phải điều KHÔNG được làm** ("viết văn xuôi liền mạch" thay vì "đừng dùng markdown").

## 4. Tư duy nền: Claude như "nhân viên mới xuất sắc"

Anthropic khuyên hình dung Claude như **một nhân viên mới rất thông minh nhưng chưa biết gì về quy ước nội bộ của bạn**. Nhân viên đó:

- Không đọc được suy nghĩ của bạn → phải nói rõ kỳ vọng.
- Không biết "ngầm hiểu" của team → phải cung cấp ngữ cảnh.
- Làm đúng những gì được bảo, theo nghĩa đen → mơ hồ ở đâu, kết quả lệch ở đó.

## 5. Những thay đổi quan trọng ở thế hệ model mới (Claude 4.x / 5)

Tài liệu hiện tại đã cập nhật vài điểm **khác với tài liệu/tutorial cũ** — cần lưu ý khi học:

- **Prefill response bị loại bỏ** (Claude 4.6+ trả lỗi 400) → thay bằng **Structured Outputs** hoặc system prompt "trả lời thẳng, không mở đầu". Chi tiết ở [03](03-ky-thuat-nang-cao.md).
- **Extended thinking với `budget_tokens` bị deprecated** → thay bằng **adaptive thinking + tham số `effort`**.
- Model mới **theo sát chỉ dẫn theo nghĩa đen hơn** — prompt cũ viết "quá tay" (ALL CAPS, lặp lệnh nhiều lần) nên được viết dịu lại.
- Model mới **tự biết dùng subagent, tự chạy tool song song** — prompt chỉ cần định hướng khi muốn khác mặc định.

## 6. Cách dùng bộ tài liệu này

- Đọc theo thứ tự 01 → 08.
- File 02–04 là lý thuyết chuẩn từ docs chính thức.
- File 05–07 là bài học + bài tập thực hành từ tutorial tương tác trên GitHub (học bằng cách làm).
- File 08 là cheat sheet tổng hợp để tra nhanh khi viết prompt thật.
