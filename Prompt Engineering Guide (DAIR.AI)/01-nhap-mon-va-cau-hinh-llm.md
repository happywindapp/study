# 01 — Nhập môn & Cấu hình LLM

> Nguồn: promptingguide.ai → mục **Introduction** (LLM Settings · Basics of Prompting · Prompt Elements · General Tips · Examples of Prompts)

## 1. Prompt Engineering là gì

Prompt engineering (PE) là kỷ luật **thiết kế và tối ưu prompt** để dùng LLM hiệu quả cho mọi loại tác vụ. Theo guide, PE không chỉ là "viết câu lệnh hay" mà là một tập kỹ năng rộng:

- Hiểu **năng lực và giới hạn** của LLM (biết model làm được gì, hay bịa chỗ nào).
- Dùng để **đánh giá model** (safety, khả năng reasoning), xây tính năng mới, tích hợp tool/domain knowledge.
- Là kỹ năng **giao tiếp với AI** — interface chính giữa người và model.

Tư duy cốt lõi: PE là **quá trình lặp thực nghiệm** (iterative): thử prompt đơn giản → đo kết quả → thêm dần context/ví dụ/cấu trúc → đo lại.

## 2. LLM Settings — các tham số sinh văn bản

Trước khi chỉnh prompt, phải hiểu các tham số decode. Đây là "núm vặn" đầu tiên ảnh hưởng output:

| Tham số | Ý nghĩa | Khuyến nghị |
|---|---|---|
| **Temperature** | Độ ngẫu nhiên khi chọn token. Thấp → chọn token xác suất cao nhất (deterministic); cao → đa dạng, sáng tạo | Thấp (0–0.3) cho QA/fact/code/extraction; cao (0.7–1) cho viết sáng tạo, brainstorm |
| **Top P** (nucleus sampling) | Chỉ xét các token có tổng xác suất tích lũy ≤ p | Thấp → chính xác; cao → đa dạng. **Quy tắc: chỉ chỉnh temperature HOẶC top_p, không chỉnh cả hai** |
| **Max Length** | Số token tối đa của output | Chặn output lan man + kiểm soát chi phí |
| **Stop Sequences** | Chuỗi khiến model dừng sinh | Cách kiểm soát độ dài/cấu trúc (vd dừng ở "11" để danh sách ≤ 10 mục) |
| **Frequency Penalty** | Phạt token theo số lần đã xuất hiện | Giảm lặp từ nguyên văn |
| **Presence Penalty** | Phạt token đã xuất hiện (phạt đều, không theo tần suất) | Giảm lặp ý/cụm; tăng để đa dạng chủ đề. **Cũng chỉ chỉnh một trong hai penalty** |

Ghi chú thực tế 2026: các API reasoning model đời mới thường khóa hoặc bỏ các tham số này (thay bằng `effort`/`reasoning`); nhưng khi dùng open-weight model (Llama, Mistral, Qwen...) qua vLLM/Ollama thì bảng trên vẫn nguyên giá trị.

## 3. Basics of Prompting

### 3.1 Cấu trúc một prompt

Prompt = **câu hỏi/chỉ dẫn** + (tùy chọn) **context, input data, examples**. Ví dụ tối giản:

```
The sky is
```
→ model chỉ hoàn tất câu (completion). Cải thiện bằng chỉ dẫn rõ:
```
Complete the sentence:
The sky is
```

Bài học đầu tiên của guide: **phải nói cho model biết chính xác bạn muốn gì** — đó chính là bản chất của PE.

### 3.2 Prompt formatting

- Dạng cơ bản: zero-shot — hỏi trực tiếp không kèm ví dụ (`Q: <câu hỏi>? A:` hoặc câu hỏi trần).
- Với chat model, prompt được tổ chức theo **role**: `system` (định hành vi tổng), `user`, `assistant`.
- Few-shot format — kèm ví dụ minh họa:

```
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
```
→ model trả `Negative`. Ví dụ (demonstrations) chính là cách "dạy trong ngữ cảnh" — in-context learning.

## 4. Prompt Elements — 4 thành phần của prompt

Một prompt có thể chứa (không bắt buộc đủ cả 4):

1. **Instruction** — tác vụ cần làm ("Classify the text into neutral, negative or positive").
2. **Context** — thông tin nền/bên ngoài giúp model trả lời tốt hơn.
3. **Input Data** — dữ liệu/câu hỏi cụ thể cần xử lý.
4. **Output Indicator** — chỉ định kiểu/format output ("Sentiment:").

Ví dụ đủ 4 thành phần:
```
Classify the text into neutral, negative or positive   ← Instruction
Text: I think the food was okay.                        ← Input Data
Sentiment:                                              ← Output Indicator
```

## 5. General Tips for Designing Prompts

Đây là phần "quy tắc vàng" của guide:

1. **Start Simple** — bắt đầu đơn giản, lặp dần (iterate). Tách task lớn thành subtask, đừng nhồi mọi thứ vào một prompt ngay từ đầu.
2. **The Instruction** — dùng động từ mệnh lệnh rõ: `Write`, `Classify`, `Summarize`, `Translate`, `Order`... Đặt instruction ở **đầu prompt**, tách instruction khỏi data bằng separator rõ ràng như `###` hoặc dòng trống:
   ```
   ### Instruction ###
   Translate the text below to Spanish:
   Text: "hello!"
   ```
3. **Specificity** — càng cụ thể càng tốt: mô tả chi tiết task, format mong muốn, kèm ví dụ. Nhưng đừng nhồi chi tiết thừa — chi tiết phải **liên quan trực tiếp** tới task (context window có hạn).
   ```
   Extract the name of places in the following text.
   Desired format:
   Place: <comma_separated_list_of_places>
   ```
4. **Avoid Impreciseness** — tránh mô tả mơ hồ kiểu "hơi ngắn thôi, đừng quá kỹ thuật". Thay bằng đo được: "Use 2-3 sentences to explain the concept of prompt engineering to a high school student."
5. **To do or not to do?** — **nói điều CẦN làm thay vì điều KHÔNG được làm.** Ví dụ kinh điển của guide: chatbot phim bảo "DO NOT ASK FOR INTERESTS" vẫn hỏi sở thích; đổi thành "recommend a movie from the top global trending movies; refrain from asking users for their preferences; if no movie found, respond 'Sorry, couldn't find...'" thì hết lỗi.

## 6. Examples of Prompts — 7 dạng task mẫu

Guide minh họa PE qua 7 nhóm task cơ bản (đây cũng là khung phân loại dùng lại ở Prompt Hub):

| Task | Prompt mẫu (rút gọn) | Bài học |
|---|---|---|
| **Text Summarization** | `Explain antibiotics. A:` → rồi `Explain the above in one sentence:` | Điều khiển độ dài/độ sâu bằng chỉ dẫn nối tiếp |
| **Information Extraction** | `Mention the large language model based product mentioned in the paragraph above:` | Instruction đặt sau đoạn văn vẫn hoạt động; chỉ rõ thứ cần trích |
| **Question Answering** | `Answer the question based on the context below. Keep the answer short. Respond "Unsure about answer" if not sure about the answer.` + Context + Question + Answer | Khung QA chuẩn: instruction + context + lối thoát chống bịa |
| **Text Classification** | `Classify the text into neutral, negative or positive.` + few-shot ví dụ giữ nguyên chữ thường `neutral` | Muốn format label chính xác → **cho ví dụ đúng format đó**, không chỉ mô tả |
| **Conversation** | `The following is a conversation with an AI research assistant. The assistant tone is technical and scientific.` | **Role prompting** — đổi persona ("answers should be easy to understand even by primary school students") để đổi giọng |
| **Code Generation** | `"""Table departments... Create a MySQL query for all students in the Computer Science Department"""` | Chỉ cần schema trong comment là sinh được SQL đúng |
| **Reasoning** | Bài "odd numbers add up to an even number" — model sai khi hỏi trực tiếp, đúng khi yêu cầu `Solve by breaking the problem into steps` | Toán/logic là điểm yếu bản chất của LLM → dẫn vào các kỹ thuật CoT ở bài 02 |

## Checklist đọng lại bài 01

- [ ] Hiểu và biết chọn temperature/top_p theo loại task (chỉ chỉnh một).
- [ ] Viết prompt có đủ instruction rõ + separator + output indicator.
- [ ] Cụ thể, đo được; nói điều cần làm thay vì cấm đoán.
- [ ] Luôn có "lối thoát" cho model ở task QA/extraction ("Unsure about answer").
- [ ] Bắt đầu đơn giản → iterate; task phức tạp → tách nhỏ.
