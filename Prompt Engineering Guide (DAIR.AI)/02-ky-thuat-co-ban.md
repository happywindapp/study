# 02 — Các kỹ thuật Prompting cơ bản

> Nguồn: promptingguide.ai → **Prompting Techniques** (nửa đầu). Đặc sản của giáo trình: mỗi kỹ thuật gắn paper gốc — ghi kèm bên dưới để tra arXiv khi cần học sâu.

## 1. Zero-shot Prompting

**Là gì:** ra chỉ dẫn trực tiếp, **không kèm ví dụ**. Hoạt động được nhờ model đã qua instruction tuning + RLHF (paper nền: Wei et al. 2022 — *Finetuned Language Models are Zero-Shot Learners* (FLAN); Ouyang et al. 2022 — InstructGPT).

```
Classify the text into neutral, negative or positive.
Text: I think the vacation is okay.
Sentiment:
```
→ `Neutral` — model hiểu khái niệm "sentiment" mà không cần dạy.

**Khi nào dùng:** luôn thử zero-shot TRƯỚC. Chỉ khi fail mới nâng cấp lên few-shot. Model càng mạnh, zero-shot càng đủ.

## 2. Few-shot Prompting

**Là gì:** kèm vài **demonstrations** (ví dụ input→output) để model học trong ngữ cảnh (in-context learning). Khả năng này xuất hiện khi model đủ lớn (Brown et al. 2022 — GPT-3; Kaplan et al. 2020 — scaling laws).

Ví dụ kinh điển dùng từ bịa (từ paper GPT-3):
```
A "whatpu" is a small, furry animal native to Tanzania. An example of a sentence that uses the word whatpu is:
We were traveling in Africa and we saw these very cute whatpus.

To do a "farduddle" means to jump up and down really fast. An example of a sentence that uses the word farduddle is:
```
→ model tự sinh câu dùng "farduddle" đúng — học cách làm task chỉ từ **1 ví dụ** (one-shot).

**Phát hiện quan trọng từ Min et al. 2022 (*Rethinking the Role of Demonstrations*):**
- **Label space và phân bố input text** trong ví dụ quan trọng — kể cả khi **label bị gán sai/ngẫu nhiên**, vẫn tốt hơn hẳn không có ví dụ!
- **Format** nhất quán là yếu tố then chốt.
- Chọn label ngẫu nhiên từ phân bố label thật > phân bố uniform.

→ Bài học thực dụng: ví dụ few-shot dạy model **format và phân bố**, nhiều hơn là dạy "kiến thức đúng".

**Giới hạn:** few-shot **không đủ cho reasoning phức tạp**. Guide minh họa bài "odd numbers sum" — kể cả cho nhiều ví dụ đáp án đúng, model vẫn sai, vì ví dụ không chứa **các bước suy luận** → dẫn tới Chain-of-Thought.

## 3. Chain-of-Thought (CoT) Prompting

📄 Wei et al. 2022 — *Chain-of-Thought Prompting Elicits Reasoning in LLMs*

**Là gì:** cho model thấy **các bước suy luận trung gian** trong ví dụ few-shot, model sẽ bắt chước cách suy luận đó → cải thiện mạnh các task số học, thường thức, suy luận ký hiệu.

```
The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1.
A: Adding all the odd numbers (9, 15, 1) gives 25. The answer is False.

The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1.
A:
```
→ model tự viết "Adding all the odd numbers (15, 5, 13, 7, 1) gives 41. The answer is False." — đúng, chỉ cần **1 ví dụ có lời giải**.

**Lưu ý gốc:** CoT là **emergent ability** — chỉ phát huy với model đủ lớn.

### 3.1 Zero-shot CoT

📄 Kojima et al. 2022 — *Large Language Models are Zero-Shot Reasoners*

Chỉ cần thêm câu thần chú **"Let's think step by step."** vào cuối prompt — không cần ví dụ nào. Bài táo kinh điển: hỏi trực tiếp model trả lời sai (11 quả), thêm câu thần chú → suy luận từng bước ra 10 quả, đúng. Cực hữu ích khi không có sẵn ví dụ để đưa vào prompt.

### 3.2 Automatic CoT (Auto-CoT)

📄 Zhang et al. 2022

Tự động hóa việc tạo ví dụ CoT (đỡ công viết tay, tránh lỗi trong demo thủ công), 2 giai đoạn:
1. **Question clustering** — gom câu hỏi của dataset thành cụm (embedding + k-means).
2. **Demonstration sampling** — chọn câu hỏi đại diện mỗi cụm, sinh lời giải bằng Zero-shot CoT (với heuristics: câu hỏi ≤ 60 tokens, lời giải ≤ 5 bước — ưu tiên demo ngắn, đơn giản).

→ Đa dạng cụm giúp giảm tác hại khi một lời giải sinh tự động bị sai.

**Ghi chú 2026:** với reasoning model hiện đại (o-series, Claude thinking, Gemini thinking), CoT đã được nội hóa — thêm "think step by step" thủ công thường thừa, đôi khi phản tác dụng. Nhưng hiểu CoT vẫn bắt buộc: nó là nền của self-consistency, ToT, ReAct... và vẫn cần khi dùng model nhỏ/open-weight.

## 4. Meta Prompting

📄 Zhang et al. 2024 — *Meta Prompting for AI Systems*

**Là gì:** kỹ thuật tập trung vào **cấu trúc và cú pháp** của task thay vì nội dung cụ thể — mô tả "khuôn" giải bài thay vì cho ví dụ chứa nội dung thật.

Đặc điểm (theo paper): (1) structure-oriented — quan tâm khuôn mẫu, format; (2) syntax-focused — cú pháp là khung dẫn; (3) abstract examples — ví dụ trừu tượng hóa, không nội dung thật; (4) versatile — áp dụng đa lĩnh vực; (5) categorical approach — nền tảng lý thuyết từ type theory.

Ví dụ: thay vì few-shot với bài toán thật, đưa khuôn:
```
Problem: [question]
Solution Structure:
1. Begin the response with "Let's think step by step."
2. Follow with the reasoning steps, ensuring the solution process is broken down clearly and logically.
3. End the solution with the final answer encapsulated in a LaTeX-formatted box \boxed{...}.
4. State "The answer is [final answer to the problem].".
```

**Ưu điểm so với few-shot:** tiết kiệm token (không nhồi ví dụ dài); công bằng khi so sánh model (không phụ thuộc ví dụ cụ thể); như một dạng zero-shot — không bị ảnh hưởng bởi ví dụ chọn sai. **Điều kiện:** model phải đủ mạnh và task có cấu trúc rõ; task cần kiến thức chuyên sâu mới lạ thì kém hơn.

## 5. Self-Consistency

📄 Wang et al. 2022 — *Self-Consistency Improves Chain of Thought Reasoning*

**Là gì:** thay vì decode tham lam (greedy) một lời giải duy nhất, **sample nhiều đường suy luận đa dạng** (chạy CoT nhiều lần với temperature > 0), rồi **chọn đáp án nhất quán nhất** (majority vote).

Ví dụ của guide: bài "When I was 6 my sister was half my age. Now I'm 70 how old is my sister?" — chạy 1 lần model có thể ra 35 (sai); chạy 3 lần ra {67, 67, 35} → vote ra 67 (đúng).

**Trade-off:** tốn N lần chi phí. Dùng cho task số học/commonsense quan trọng, có đáp án ngắn dễ so khớp. Đây là tổ tiên của các kỹ thuật "best-of-N / majority voting" trong các hệ thống LLM hiện đại.

## 6. Generated Knowledge Prompting

📄 Liu et al. 2022 — *Generated Knowledge Prompting for Commonsense Reasoning*

**Là gì:** trước khi trả lời, **bảo model tự sinh kiến thức liên quan**, rồi đưa kiến thức đó vào prompt trả lời. 2 bước:

1. **Sinh knowledge** (few-shot): `Input: <câu hỏi> Knowledge:` → model sinh các fact liên quan.
2. **Tích hợp và trả lời:** `Question: ... Knowledge: ... Explain and Answer:`

Ví dụ của guide: "Part of golf is trying to get a higher point total than others. Yes or No?" — hỏi trực tiếp model trả lời sai "Yes". Sinh knowledge trước ("In golf, the objective is to complete the course with the lowest score...") → trả lời đúng "No" với độ tự tin cao.

**Lưu ý:** các knowledge sinh ra có thể mâu thuẫn nhau → chọn câu trả lời từ knowledge cho độ tự tin cao nhất. Kỹ thuật này là tiền thân tư duy của RAG (khác ở chỗ: RAG lấy kiến thức từ nguồn ngoài thay vì từ chính model).

## 7. Prompt Chaining

**Là gì:** tách task phức tạp thành **chuỗi subtask**, output của prompt trước làm input cho prompt sau. Tăng độ tin cậy, minh bạch (debug được từng khâu), dễ kiểm soát — nền tảng của mọi LLM pipeline/workflow thực tế.

Ví dụ chuẩn của guide — **Document QA 2 bước:**

Prompt 1 — trích quote liên quan:
```
You are a helpful assistant. Your task is to help answer a question given in a document. The first step is to extract quotes relevant to the question from the document, delimited by ####. Please output the list of quotes using <quotes></quotes>. Respond with "No relevant quotes found!" if no relevant quotes were found.
####
{{document}}
####
```

Prompt 2 — trả lời từ quotes + document:
```
Given a set of relevant quotes (delimited by <quotes></quotes>) extracted from a document and the original document (delimited by ####), please compose an answer to the question. Ensure that the answer is accurate, has a friendly tone, and sounds helpful.
```

Ứng dụng điển hình: trích xuất → biến đổi → tổng hợp; sinh → thẩm định → sửa (generate-critique-refine); conversational agents nhiều tầng.

---

## Bảng tóm tắt bài 02

| Kỹ thuật | Ý tưởng 1 dòng | Chi phí | Paper |
|---|---|---|---|
| Zero-shot | Chỉ dẫn trực tiếp, không ví dụ | 1x | FLAN (Wei 2022) |
| Few-shot | Kèm demonstrations — dạy format qua ngữ cảnh | 1x + tokens ví dụ | GPT-3 (Brown 2020), Min 2022 |
| CoT | Ví dụ kèm bước suy luận trung gian | 1x, output dài hơn | Wei 2022 |
| Zero-shot CoT | "Let's think step by step" | 1x | Kojima 2022 |
| Auto-CoT | Cluster câu hỏi + tự sinh demo CoT | Tiền xử lý | Zhang 2022 |
| Meta Prompting | Đưa khuôn cấu trúc thay vì ví dụ nội dung | 1x, ít token | Zhang 2024 |
| Self-Consistency | Sample N lời giải → majority vote | **Nx** | Wang 2022 |
| Generated Knowledge | Sinh kiến thức trước → trả lời sau | 2x | Liu 2022 |
| Prompt Chaining | Tách task thành chuỗi prompt nối nhau | Kx (K khâu) | — (pattern kỹ nghệ) |
