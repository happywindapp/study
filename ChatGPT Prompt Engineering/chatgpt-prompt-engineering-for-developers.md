# ChatGPT Prompt Engineering for Developers — Tài liệu học tập chi tiết

> Nguồn: [DeepLearning.AI Short Courses](https://www.deeplearning.ai/short-courses/) · Khóa học: [ChatGPT Prompt Engineering for Developers](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)
> Giảng viên: **Isa Fulford** (OpenAI) & **Andrew Ng** (DeepLearning.AI)
> Thời lượng: ~1h40m · Trình độ: Beginner · Yêu cầu: Python cơ bản

---

## Phần 0 — Tổng quan hệ sinh thái DeepLearning.AI Short Courses

Trước khi đi vào khóa học chính, cần hiểu bối cảnh của trang [deeplearning.ai/short-courses](https://www.deeplearning.ai/short-courses/):

- **Short course là gì**: các khóa ngắn 45 phút – 3 giờ, tập trung vào MỘT kỹ năng cụ thể, do chuyên gia từ chính các công ty đối tác giảng dạy (OpenAI, Anthropic, Google, Meta, Hugging Face, Redis, Nvidia...). Học qua video + notebook code chạy trực tiếp trên trình duyệt (Jupyter embedded).
- **Chi phí**: miễn phí xem video + code; membership PRO mở khóa certificate và graded quiz.
- **Quy mô catalog** (thời điểm khảo sát): ~101 short courses, 13 full courses, 11 professional certificates. Chia theo level: Beginner (64), Intermediate (61).
- **Các chủ đề chính**: GenAI Applications, Prompt Engineering, Agents, RAG, Generative Models, LLMOps, Search & Retrieval, AI Frameworks, Chatbots, Evaluation & Monitoring, NLP.
- **Vị trí của khóa này**: *ChatGPT Prompt Engineering for Developers* là short course **đầu tiên và kinh điển nhất** của cả series (ra mắt 4/2023, hợp tác OpenAI), đặt nền móng cho mọi khóa GenAI sau nó. Nếu học theo lộ trình, đây là điểm bắt đầu chuẩn.

### Lộ trình gợi ý sau khóa này
1. **Building Systems with the ChatGPT API** — ghép nhiều prompt thành hệ thống hoàn chỉnh (moderation, chaining, evaluation).
2. **LangChain for LLM Application Development** — framework hoá các pattern đã học.
3. **Building and Evaluating Advanced RAG** — retrieval-augmented generation.
4. **Functions, Tools and Agents with LangChain** — hướng agent.

---

## Phần 1 — Cấu trúc khóa học

| # | Bài học | Hình thức | Thời lượng | Nội dung cốt lõi |
|---|---------|-----------|-----------|------------------|
| 1 | Introduction | Video | 6m | Base LLM vs Instruction-tuned LLM |
| 2 | Guidelines | Video + Code | 17m | 2 nguyên tắc vàng + 6 tactics |
| 3 | Iterative | Video + Code | 13m | Quy trình lặp để tinh chỉnh prompt |
| 4 | Summarizing | Video + Code | 7m | Tóm tắt có kiểm soát |
| 5 | Inferring | Video + Code | 11m | Trích xuất sentiment, topic, entity |
| 6 | Transforming | Video + Code | 12m | Dịch, đổi tone, đổi format, sửa lỗi |
| 7 | Expanding | Video + Code | 6m | Sinh nội dung + tham số temperature |
| 8 | Chatbot | Video + Code | 12m | Vai trò system/user/assistant, context |
| 9 | Conclusion | Video | 2m | Tổng kết |
| 10 | Quiz | Graded (PRO) | 10m | Kiểm tra kiến thức |

### Setup code dùng xuyên suốt khóa

```python
import openai
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

def get_completion(prompt, model="gpt-3.5-turbo", temperature=0):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=temperature,  # 0 = deterministic, phù hợp task cần độ tin cậy
    )
    return response.choices[0].message["content"]
```

> Lưu ý 2024+: SDK OpenAI mới dùng `from openai import OpenAI; client = OpenAI()` và `client.chat.completions.create(...)`. Nguyên lý prompt không đổi.

---

## Phần 2 — Bài 1: Introduction

### Hai loại LLM
| | Base LLM | Instruction-Tuned LLM |
|---|---|---|
| Huấn luyện | Dự đoán từ tiếp theo từ dữ liệu web | Base LLM + fine-tune theo instruction + RLHF |
| Hỏi "Thủ đô của Pháp là gì?" | Có thể trả về... một danh sách câu hỏi trắc nghiệm khác (vì trên web hay xuất hiện dạng đó) | "Thủ đô của Pháp là Paris." |
| Ví dụ | GPT-3 gốc | ChatGPT, GPT-4, Claude |

**Điểm mấu chốt**: khóa học tập trung vào Instruction-tuned LLM — loại được khuyến nghị cho ứng dụng thực tế vì an toàn hơn, dễ điều khiển hơn. Khi prompt, hãy hình dung mình đang giao việc cho **một người thông minh nhưng không biết gì về ngữ cảnh công việc của bạn** — phải nói rõ ràng, cụ thể.

---

## Phần 3 — Bài 2: Guidelines (bài quan trọng nhất)

### Nguyên tắc 1: Viết chỉ dẫn RÕ RÀNG và CỤ THỂ
> Clear ≠ Short. Prompt dài, chi tiết thường tốt hơn prompt ngắn mơ hồ.

**Tactic 1.1 — Dùng delimiter tách phần dữ liệu khỏi phần chỉ dẫn**

Delimiter có thể là ``` ``` ```, `"""`, `< >`, `<tag></tag>`, `---`. Vừa giúp model không nhầm lẫn, vừa **chống prompt injection** (user chèn "quên chỉ dẫn cũ đi..." vào text đầu vào).

```python
text = f"""..."""
prompt = f"""
Summarize the text delimited by triple backticks into a single sentence.
```{text}```
"""
```

**Tactic 1.2 — Yêu cầu output có cấu trúc (JSON, HTML)**

```python
prompt = """
Generate a list of three made-up book titles along with their authors and genres.
Provide them in JSON format with the following keys: book_id, title, author, genre.
"""
```
→ Output parse thẳng vào `json.loads()` được, dễ tích hợp pipeline.

**Tactic 1.3 — Yêu cầu model kiểm tra điều kiện trước khi làm**

```python
prompt = f"""
You will be provided with text delimited by triple quotes.
If it contains a sequence of instructions, re-write those instructions as:
Step 1 - ...
Step 2 - ...
If the text does not contain a sequence of instructions,
then simply write "No steps provided."
\"\"\"{text}\"\"\"
"""
```
→ Xử lý edge case ngay trong prompt, tránh model "bịa" ra kết quả khi input không hợp lệ.

**Tactic 1.4 — Few-shot prompting: cho ví dụ mẫu trước khi giao việc**

```python
prompt = """
Your task is to answer in a consistent style.

<child>: Teach me about patience.
<grandparent>: The river that carves the deepest valley flows from a modest spring...

<child>: Teach me about resilience.
"""
```
→ Model bắt chước style/format của ví dụ. Rất hiệu quả khi khó mô tả yêu cầu bằng lời.

### Nguyên tắc 2: Cho model THỜI GIAN ĐỂ "SUY NGHĨ"
> Ép model trả lời ngay một bài toán phức tạp → dễ sai. Bắt nó đi qua các bước trung gian → chính xác hơn (chain-of-thought).

**Tactic 2.1 — Chỉ định rõ các bước để hoàn thành task**

```python
prompt = f"""
Perform the following actions:
1 - Summarize the following text delimited by <> with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains: french_summary, num_names.

Use the following format:
Text: <text to summarize>
Summary: <summary>
Translation: <summary translation>
Names: <list of names>
Output JSON: <json with summary and num_names>

Text: <{text}>
"""
```

**Tactic 2.2 — Bắt model tự giải trước khi phán xét lời giải có sẵn**

Ví dụ kinh điển: bài toán chi phí solar farm — học sinh giải SAI (nhân 100x thay vì 10x ở chi phí bảo trì), nhưng nếu hỏi thẳng "lời giải đúng không?" model trả lời "đúng" vì chỉ lướt qua. Cách sửa:

```python
prompt = """
Your task is to determine if the student's solution is correct or not.
To solve the problem do the following:
- First, work out your own solution to the problem.
- Then compare your solution to the student's solution
  and evaluate if the student's solution is correct or not.
Don't decide if the student's solution is correct until
you have done the problem yourself.
...
"""
```
→ Bài học tổng quát: **muốn model đánh giá thứ gì, bắt nó tự làm thứ đó trước.**

### Giới hạn của model: Hallucination
- Model có thể **bịa ra thông tin nghe rất thuyết phục**. Ví dụ trong khóa: hỏi về "AeroGlide UltraSlim Smart Toothbrush by Boie" (sản phẩm không tồn tại) → model mô tả chi tiết như thật.
- Giải pháp giảm thiểu: yêu cầu model **tìm trích dẫn liên quan trong tài liệu nguồn trước**, rồi chỉ trả lời dựa trên trích dẫn đó (tiền thân của RAG).

---

## Phần 4 — Bài 3: Iterative Prompt Development

**Không ai viết prompt đúng ngay lần đầu — và điều đó không quan trọng.** Quan trọng là có quy trình lặp tốt:

```
Idea → Viết prompt → Chạy thử → Phân tích kết quả sai ở đâu → Sửa prompt → Lặp lại
```
(giống hệt vòng lặp train ML model: Idea → Code → Experiment → Error Analysis)

### Case study xuyên suốt: viết mô tả sản phẩm ghế văn phòng từ fact sheet kỹ thuật

| Lần lặp | Vấn đề phát hiện | Cách sửa prompt |
|---------|------------------|-----------------|
| 1 | Output quá dài | `Use at most 50 words.` (có thể dùng câu/ký tự — nhưng model đếm **từ** tương đối, đếm ký tự kém vì tokenizer) |
| 2 | Sai trọng tâm — mô tả cho người mua lẻ trong khi khách là nhà bán nội thất | `The description is intended for furniture retailers, so should be technical and focus on materials.` |
| 3 | Thiếu thông tin cần | `At the end, include every 7-character Product ID.` |
| 4 | Cần format web | `Format everything as HTML. Include a table with the product's dimensions.` |

### Bài học rút ra
- Tinh chỉnh với **một ví dụ** trước; khi ứng dụng chín muồi mới đánh giá trên **nhiều ví dụ** (batch evaluation) — tránh tối ưu quá sớm.
- Đừng tìm "prompt hoàn hảo trên mạng"; hãy có **quy trình lặp** phù hợp với task của bạn.

---

## Phần 5 — Bài 4: Summarizing

Ứng dụng: tóm tắt review sản phẩm, bài báo, email... để đọc nhanh hơn.

### Các kỹ thuật kiểm soát bản tóm tắt

```python
# 1. Giới hạn độ dài
prompt = f"""Summarize the review below, in at most 30 words. Review: ```{prod_review}```"""

# 2. Tóm tắt CÓ TRỌNG TÂM theo mục đích sử dụng
# — cho bộ phận Shipping:
"""... focusing on any aspects that mention shipping and delivery of the product."""
# — cho bộ phận Pricing:
"""... focusing on any aspects relevant to the price and perceived value."""

# 3. "Extract" thay vì "Summarize" — khi cần thông tin thô, không cần văn vẻ
prompt = f"""From the review below, extract the information relevant to
shipping and delivery. Limit to 30 words."""
```

**Phân biệt quan trọng**: `summarize` vẫn có thể kèm thông tin ngoài trọng tâm; `extract` chỉ lấy đúng thông tin liên quan → sạch hơn cho downstream processing.

### Pattern production: tóm tắt hàng loạt
```python
for i in range(len(reviews)):
    prompt = f"""Summarize the review below in at most 20 words. Review: ```{reviews[i]}```"""
    response = get_completion(prompt)
    print(i, response, "\n")
```

---

## Phần 6 — Bài 5: Inferring

Đây là bài "ăn tiền" nhất với dev: những task trước đây phải train model ML riêng (sentiment analysis, NER, topic classification) giờ **chỉ cần một prompt** — zero-shot, không cần labeled data, không cần deploy model riêng.

### Các task minh họa (trên review một chiếc đèn)

```python
# 1. Sentiment (dạng tự do)
"""What is the sentiment of the following product review?"""

# 2. Sentiment (dạng gọn cho lập trình)
"""... Give your answer as a single word, either "positive" or "negative"."""

# 3. Nhận diện cảm xúc
"""Identify a list of emotions the writer is expressing. No more than five items."""

# 4. Phát hiện giận dữ (boolean → dùng làm trigger alert)
"""Is the writer expressing anger? Answer yes or no."""

# 5. Trích xuất entity — nhiều thông tin trong MỘT lần gọi
"""Identify the following items from the review text:
- Item purchased by reviewer
- Company that made the item
Format your response as a JSON object with "Item" and "Brand" as the keys.
If the information isn't present, use "unknown" as the value."""

# 6. Tất cả trong một (sentiment + anger + item + brand → 1 JSON)
"""Identify: Sentiment, Is the reviewer expressing anger (boolean),
Item purchased, Company. Format as JSON with keys:
Sentiment, Anger, Item, Brand."""
```

### Topic extraction + zero-shot classification
```python
# Suy ra 5 chủ đề của bài báo
"""Determine five topics being discussed in the following text.
Make each item one or two words long. Format as comma-separated list."""

# Zero-shot: kiểm tra bài báo có nói về các topic cho trước không
"""Determine whether each item in the following list of topics
is a topic in the text below.
Give your answer as a JSON: 0 or 1 for each topic.
List of topics: {", ".join(topic_list)}"""
# → làm hệ thống news-alert không cần train classifier
```

**Ứng dụng thực tế gợi ý**: dashboard phân tích review khách hàng, hệ thống cảnh báo review giận dữ cho CSKH, phân loại ticket support tự động.

---

## Phần 7 — Bài 6: Transforming

Biến đổi text từ dạng này sang dạng khác. Bốn nhóm:

### 7.1 Dịch thuật (Translation)
```python
"""Translate the following English text to Spanish: ```Hi, I would like to order a blender```"""
"""Tell me which language this is: ```Combien coûte le lampadaire?```"""
"""Translate the following text to Spanish in both the formal and informal forms."""
```
**Universal Translator** — pattern production: nhận message đa ngôn ngữ từ user toàn cầu, tự phát hiện ngôn ngữ rồi dịch sang tiếng Anh + tiếng Hàn cho team support:
```python
for issue in user_messages:
    lang = get_completion(f"Tell me what language this is: ```{issue}```")
    translation = get_completion(
        f"Translate the following text to English and Korean: ```{issue}```")
```

### 7.2 Chuyển đổi tone (Tone Transformation)
```python
"""Translate the following from slang to a business letter:
'Dude, This is Joe, check out this spec on this standing lamp.'"""
```

### 7.3 Chuyển đổi format (JSON → HTML, v.v.)
```python
"""Translate the following python dictionary from JSON to an HTML table
with column headers and title: {data_json}"""
```

### 7.4 Sửa ngữ pháp & chính tả (Proofreading)
```python
"""Proofread and correct the following text and rewrite the corrected version.
If you don't find any errors, just say "No errors found". Don't use any
punctuation around the text: ```{text}```"""

# Nâng cao: sửa + đổi giọng văn + đổi format cùng lúc
"""Proofread and correct this review. Make it more compelling.
Ensure it follows APA style guide and targets an advanced reader.
Output in markdown format."""
```
Tip từ khóa học: dùng lib `redlines` (Python) để hiển thị diff giữa bản gốc và bản sửa — hữu ích khi build tool review văn bản.

---

## Phần 8 — Bài 7: Expanding

Nhiệm vụ "mở rộng": từ input ngắn (chủ đề, danh sách ý) → text dài (email, bài viết).

⚠️ **Lưu ý đạo đức từ Andrew Ng**: dùng để hỗ trợ con người (draft để người duyệt), không dùng để spam hàng loạt. Nếu gửi nội dung AI sinh ra cho người dùng → nên minh bạch là AI viết.

### Case study: email trả lời khách hàng tự động theo sentiment
```python
prompt = f"""
You are a customer service AI assistant.
Your task is to send an email reply to a valued customer.
Given the customer email delimited by ```, generate a reply to thank
the customer for their review.
If the sentiment is positive or neutral, thank them for their review.
If the sentiment is negative, apologize and suggest that they can
reach out to customer service.
Make sure to use specific details from the review.
Write in a concise and professional tone.
Sign the email as `AI customer agent`.
Customer review: ```{review}```
Review sentiment: {sentiment}
"""
```
(sentiment lấy từ bài Inferring — đây là ví dụ **chaining prompt**: output của prompt này là input của prompt kia.)

### Tham số `temperature` — kiến thức nền quan trọng
| Temperature | Hành vi | Dùng cho |
|---|---|---|
| **0** | Luôn chọn token xác suất cao nhất → output ổn định, lặp lại được | Ứng dụng cần tin cậy, dự đoán được (extraction, phân loại, hệ thống production) |
| 0.3–0.7 | Đa dạng vừa phải | Viết lách có kiểm soát |
| **1.0+** | Chọn token "liều" hơn → sáng tạo, mỗi lần chạy một khác | Brainstorm, sáng tác |

Ví dụ trực quan trong khóa: với câu "my favorite food is..." — pizza (53%), sushi (30%), tacos (5%). Temperature 0 → luôn "pizza". Temperature cao → có lúc "sushi", "tacos".

---

## Phần 9 — Bài 8: Chatbot

### Cấu trúc messages của Chat API — 3 vai trò
```python
messages = [
    {"role": "system",    "content": "You are an assistant that speaks like Shakespeare."},
    {"role": "user",      "content": "tell me a joke"},
    {"role": "assistant", "content": "Why did the chicken cross the road"},
    {"role": "user",      "content": "I don't know"},
]
```
- **system**: đặt hành vi/persona tổng thể, user cuối không nhìn thấy — "thì thầm vào tai" model.
- **user**: câu người dùng nói.
- **assistant**: câu model đã trả lời (hoặc do dev chèn vào để mớm ngữ cảnh).

```python
def get_completion_from_messages(messages, model="gpt-3.5-turbo", temperature=0):
    response = openai.ChatCompletion.create(
        model=model, messages=messages, temperature=temperature)
    return response.choices[0].message["content"]
```

### Điểm mấu chốt: model KHÔNG có trí nhớ
Mỗi lần gọi API là độc lập. Hỏi "What is my name?" ở call mới → model không biết, dù call trước đã nói tên. **Muốn hội thoại liên tục, phải tự gửi lại toàn bộ lịch sử messages (context) trong mỗi request.** Đây là nền tảng của mọi chatbot — và cũng là nguồn gốc bài toán quản lý context window sau này.

### Project cuối khóa: OrderBot — bot nhận order pizza
```python
context = [{'role': 'system', 'content': """
You are OrderBot, an automated service to collect orders for a pizza restaurant.
You first greet the customer, then collect the order,
and then ask if it's a pickup or delivery.
You wait to collect the entire order, then summarize it and check for a final
time if the customer wants to add anything else.
If it's a delivery, you ask for an address.
Finally you collect the payment.
Make sure to clarify all options, extras and sizes to uniquely
identify the item from the menu.
You respond in a short, very conversational friendly style.
The menu includes: pepperoni pizza 12.95, 10.00, 7.00 ...
"""}]

def collect_messages(user_input):
    context.append({'role': 'user', 'content': user_input})
    response = get_completion_from_messages(context, temperature=1)
    context.append({'role': 'assistant', 'content': response})
    return response
```
Kết thúc phiên: thêm 1 system message *"create a json summary of the previous food order"* với `temperature=0` → xuất order JSON gửi cho hệ thống bếp. **Pattern rất đáng nhớ: hội thoại dùng temperature cao, trích xuất dữ liệu cuối phiên dùng temperature 0.**

---

## Phần 10 — Bài 9: Conclusion + Tổng kết toàn khóa

### Bản đồ kiến thức 1 trang

```
2 NGUYÊN TẮC
├── 1. Chỉ dẫn rõ ràng, cụ thể
│   ├── Delimiter tách data khỏi instruction (chống injection)
│   ├── Yêu cầu output có cấu trúc (JSON/HTML)
│   ├── Bắt kiểm tra điều kiện, xử lý edge case
│   └── Few-shot: cho ví dụ mẫu
└── 2. Cho model thời gian suy nghĩ
    ├── Chỉ định từng bước + format trung gian
    └── Tự giải trước khi đánh giá lời giải khác

QUY TRÌNH: Iterative — viết → chạy → phân tích lỗi → sửa → lặp

4 NĂNG LỰC ỨNG DỤNG
├── Summarizing  — tóm tắt có trọng tâm, extract vs summarize
├── Inferring    — sentiment/topic/entity không cần train model
├── Transforming — dịch, đổi tone, đổi format, proofread
└── Expanding    — sinh nội dung, hiểu temperature

CHATBOT: system/user/assistant + tự quản lý context + OrderBot
GIỚI HẠN: hallucination → bắt trích dẫn nguồn trước khi trả lời
```

### Checklist tự đánh giá sau khóa
- [ ] Giải thích được vì sao instruction-tuned LLM phù hợp production hơn base LLM
- [ ] Viết prompt có delimiter + yêu cầu JSON output + xử lý edge case
- [ ] Dùng few-shot khi khó mô tả yêu cầu bằng lời
- [ ] Áp dụng "work out your own solution first" cho task đánh giá/chấm điểm
- [ ] Có quy trình lặp tinh chỉnh prompt thay vì tìm "prompt hoàn hảo"
- [ ] Làm sentiment analysis + entity extraction chỉ bằng prompt, output JSON
- [ ] Giải thích temperature và chọn giá trị đúng theo use case
- [ ] Tự build chatbot quản lý context, kết thúc bằng JSON extraction

### Liên hệ với công việc thực tế (góc nhìn dev)
- Các tactic delimiter + JSON output + edge-case check chính là nền của **structured output / function calling** hiện đại.
- Pattern "Inferring → Expanding" (phân tích rồi sinh phản hồi) là khung của mọi pipeline CSKH tự động.
- OrderBot = nguyên mẫu thu nhỏ của **agent thu thập thông tin có schema đích** (form-filling agent).
- Kỹ thuật "trích dẫn nguồn trước khi trả lời" là tiền thân trực tiếp của **RAG**.

---

## Phụ lục — Tài nguyên

- Trang khóa học: https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/
- Catalog toàn bộ short courses: https://www.deeplearning.ai/short-courses/
- OpenAI Prompt Engineering Guide (bổ trợ): https://platform.openai.com/docs/guides/prompt-engineering
- Khóa kế tiếp nên học: *Building Systems with the ChatGPT API* (cùng bộ đôi giảng viên)
