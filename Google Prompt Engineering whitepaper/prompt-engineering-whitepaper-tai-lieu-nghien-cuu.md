# Google "Prompt Engineering" Whitepaper — Tài liệu nghiên cứu chi tiết

> Tài liệu học tập tiếng Việt, biên soạn từ whitepaper **"Prompt Engineering"** của Google (Kaggle).
> Nguồn gốc: https://www.kaggle.com/whitepaper-prompt-engineering

---

## 1. Thông tin chung về whitepaper

| Mục | Nội dung |
|---|---|
| Tên | Prompt Engineering |
| Tác giả chính | **Lee Boonstra** (Software Engineer Tech Lead, Google) |
| Contributors | Michael Sherman, Yuan Cao, Erick Armbrust, Anant Nawalgaria, Antonio Gulli, Simone Cammel, Grace Mollison, Joey Haymaker, Michael Lanning |
| Phát hành | Bản gốc September 2024, bản phổ biến rộng (v4) February 2025 — nằm trong chuỗi Kaggle *5-Day Gen AI Intensive Course* |
| Độ dài | ~65–68 trang |
| Model minh họa | Gemini (gemini-pro) trên Vertex AI |
| Tầm ảnh hưởng | Hơn 400.000 lượt download, 550.000+ lượt xem trong cộng đồng AI |

**Luận điểm mở đầu quan trọng:**
- *"You don't need to be a data scientist or a machine learning engineer — everyone can write a prompt."* Ai cũng viết được prompt, nhưng viết prompt **hiệu quả** là một kỹ năng kỹ thuật cần rèn luyện có hệ thống.
- LLM bản chất là **prediction engine**: nhận vào chuỗi text, dự đoán token tiếp theo dựa trên xác suất, rồi lặp lại. Prompt engineering là quá trình "sắp đặt" đầu vào để chuỗi dự đoán đó đi đúng hướng mình muốn.
- Prompt engineering là **quá trình lặp (iterative)**: viết → chạy → phân tích → tinh chỉnh → ghi chép lại.

---

## 2. LLM Output Configuration — Cấu hình đầu ra

Trước khi viết prompt, phải hiểu các tham số điều khiển cách model chọn token. Đây là phần nhiều người bỏ qua nhưng ảnh hưởng trực tiếp đến kết quả.

### 2.1. Output length (giới hạn độ dài)

- Là số token tối đa model được sinh ra. Token nhiều → chi phí cao, latency cao, tốn năng lượng hơn.
- **Bẫy cần nhớ:** giảm output length KHÔNG làm model viết súc tích hơn — nó chỉ **cắt cụt** output khi chạm giới hạn. Muốn ngắn gọn thật sự phải yêu cầu trong prompt (ví dụ "trả lời trong 1 câu").
- Đặc biệt quan trọng với kỹ thuật như ReAct — nếu không giới hạn, model có thể sinh tiếp các token vô ích sau khi đã có câu trả lời.

### 2.2. Sampling controls: Temperature, Top-K, Top-P

Mỗi bước sinh, model tính xác suất cho toàn bộ vocabulary. Ba tham số này quyết định cách chọn 1 token từ phân phối đó.

**Temperature**
- Điều khiển độ ngẫu nhiên khi chọn token.
- `temperature = 0` → **greedy decoding**: luôn chọn token xác suất cao nhất → output gần như deterministic. Phù hợp bài toán có 1 đáp án đúng (toán, phân loại, trích xuất).
- Temperature cao → phân phối xác suất "phẳng" hơn (liên hệ hàm softmax) → các token ít khả năng vẫn có cơ hội được chọn → output đa dạng, sáng tạo, nhưng dễ lan man.

**Top-K**
- Chỉ giữ lại K token có xác suất cao nhất để sample.
- K lớn → sáng tạo hơn; K nhỏ → gò bó, chính xác hơn; `K = 1` tương đương greedy decoding.

**Top-P (nucleus sampling)**
- Giữ lại tập token nhỏ nhất có tổng xác suất tích lũy ≤ P (0 → 1).
- `P = 0` (hoặc rất nhỏ) → chỉ token xác suất cao nhất; `P = 1` → toàn bộ vocabulary.

### 2.3. Cách 3 tham số tương tác nhau (rất hay bị hiểu sai)

Thứ tự áp dụng chung: token phải lọt qua **cả Top-K và Top-P**, sau đó temperature mới quyết định cách sample trong tập còn lại.

| Thiết lập | Hệ quả |
|---|---|
| `temperature = 0` | Top-K, Top-P **vô nghĩa** — luôn chọn token tốt nhất |
| Temperature cực cao | Temperature vô nghĩa — sample ngẫu nhiên đều trong tập đã qua lọc Top-K/Top-P |
| `top-K = 1` | Temperature, Top-P vô nghĩa — chỉ 1 token qua được |
| `top-P = 0` (≈0) | Temperature, Top-K vô nghĩa — chỉ token tốt nhất qua được |

### 2.4. Giá trị khởi điểm khuyến nghị (theo whitepaper)

| Use case | Temperature | Top-P | Top-K |
|---|---|---|---|
| Kết quả mạch lạc, sáng tạo vừa phải (mặc định tốt) | 0.2 | 0.95 | 30 |
| Cần sáng tạo cao | 0.9 | 0.99 | 40 |
| Cần ít sáng tạo, bám sát | 0.1 | 0.9 | 20 |
| Bài toán có 1 đáp án đúng (ví dụ toán) | **0** | — | — |

**Cảnh báo:** temperature cao dễ gây **repetition loop bug** — model lặp từ/cụm từ vô hạn. Temperature quá thấp cũng có thể gây loop kiểu khác. Cần cân bằng.

---

## 3. Các kỹ thuật prompting (11 kỹ thuật)

### 3.1. Zero-shot prompting

- Dạng đơn giản nhất: chỉ mô tả task + input, **không kèm ví dụ nào**.
- Dùng khi task đơn giản, model đã "biết sẵn" cách làm.

**Ví dụ trong whitepaper (Table 1):** phân loại review phim thành `POSITIVE / NEUTRAL / NEGATIVE`.
- Config: temperature 0.1, token limit 5, top-P 1.
- Prompt gist: `Classify movie reviews as POSITIVE, NEUTRAL or NEGATIVE. Review: "Her"... Sentiment:`
- Output: `POSITIVE`.
- Lưu ý: task phân loại không cần sáng tạo → temperature thấp; đầu ra chỉ 1 từ → token limit nhỏ.

### 3.2. One-shot & Few-shot prompting

- **One-shot:** kèm đúng 1 ví dụ mẫu để model bắt chước.
- **Few-shot:** kèm nhiều ví dụ (khuyến nghị tối thiểu **3–5 ví dụ** cho task thông thường, phức tạp hơn thì nhiều hơn).
- Đây là cách mạnh nhất để "dạy" format đầu ra mong muốn.

**Ví dụ (Table 2):** parse đơn đặt pizza của khách thành JSON.
- Config: temperature 0.1, token limit 250.
- Prompt cho 2 ví dụ order → JSON, rồi đưa order phức tạp ("half-half" pizza) — model trả JSON đúng cấu trúc `{size, type, ingredients[]}`.

**Nguyên tắc chọn ví dụ:**
- Ví dụ phải **liên quan, đa dạng, chất lượng cao, không lỗi** — một lỗi nhỏ trong ví dụ có thể phá hỏng toàn bộ hành vi model.
- Nên kèm **edge case**: input bất thường nhưng hợp lệ, nếu muốn output robust.

### 3.3. System prompting

- Đặt **bối cảnh tổng thể và mục đích** cho model: "bức tranh lớn" về việc model phải làm (dịch thuật, phân loại, trả về format nào...).
- Rất hữu ích để **ép format đầu ra** (chỉ trả label viết hoa, chỉ trả JSON hợp lệ...) và bổ sung yêu cầu an toàn ("You should be respectful in your answer").

**Ví dụ (Table 3):** phân loại review + "Only return the label in uppercase" → output `NEGATIVE` sạch, dễ dùng trong pipeline.

**Ví dụ (Table 4):** phân loại review nhưng yêu cầu trả **JSON theo schema định nghĩa sẵn** (enum Sentiment, tên phim...). Lợi ích của việc ép trả JSON:
- Không phải tự format lại dữ liệu;
- Có thể yêu cầu dữ liệu trả về đã sắp xếp;
- **Ép model tạo cấu trúc → hạn chế hallucination.**

### 3.4. Role prompting

- Gán cho model một **vai/nhân vật** cụ thể → xác lập tone, style, chuyên môn.
- Ví dụ vai: giáo viên, biên tập viên, travel guide, motivational speaker...

**Ví dụ (Table 5):** "I want you to act as a travel guide..." + vị trí Amsterdam + chỉ gợi ý bảo tàng → 3 gợi ý bảo tàng chi tiết.

**Ví dụ (Table 6):** cùng vai travel guide (Manhattan) nhưng thêm "in a humorous style" → output chuyển hẳn sang giọng hài hước, chơi chữ.

**Các style whitepaper gợi ý thử:** Confrontational, Descriptive, Direct, Formal, Humorous, Influential, Informal, Inspirational, Persuasive.

### 3.5. Contextual prompting

- Cung cấp **bối cảnh/thông tin nền cụ thể** cho task hiện tại (khác system prompting — vốn là mục đích tổng thể; khác role — vốn là danh tính).
- Giúp model hiểu sắc thái yêu cầu, trả lời chính xác và phù hợp hơn.

**Ví dụ (Table 7):** `Context: You are writing for a blog about retro 80's arcade video games.` + "Suggest 3 topics to write an article about..." → 3 chủ đề bài viết đúng ngách retro arcade.

**Phân biệt nhanh System vs Role vs Contextual:**

| Loại | Trả lời câu hỏi | Ví dụ |
|---|---|---|
| System | Model phải LÀM GÌ (nhiệm vụ gốc, format) | "Trả về JSON hợp lệ theo schema" |
| Role | Model LÀ AI (danh tính, tone) | "Act as a travel guide" |
| Contextual | Tình huống CỤ THỂ nào (dữ kiện nền) | "Blog về game arcade thập niên 80" |

Ba loại có thể chồng lấp và **dùng kết hợp** trong một prompt.

### 3.6. Step-back prompting

- Kỹ thuật 2 bước: (1) hỏi model một **câu hỏi tổng quát hơn** liên quan đến task; (2) đưa câu trả lời đó vào prompt của task cụ thể.
- Cơ chế: "lùi một bước" kích hoạt kiến thức nền + quá trình suy luận rộng của model trước khi giải bài cụ thể → tăng độ chính xác, giảm bias (vì tập trung vào nguyên lý chung thay vì chi tiết vụn).

**Ví dụ chuỗi 3 bảng (Table 8–10):** viết cốt truyện 1 đoạn cho level game FPS.
1. Hỏi trực tiếp (Table 8) → ra kịch bản chung chung (phục kích đô thị).
2. Step-back question (Table 9): "5 bối cảnh/theme hư cấu nào làm nên level FPS hấp dẫn?" → căn cứ quân sự, thành phố cyberpunk, tàu vũ trụ alien, thị trấn zombie, cơ sở dưới nước.
3. Đưa 5 theme đó làm context vào prompt gốc (Table 10) → cốt truyện "underwater facility" giàu không khí, chi tiết gameplay cụ thể hơn hẳn.

### 3.7. Chain of Thought (CoT) prompting

- Cải thiện khả năng **suy luận** bằng cách yêu cầu model sinh ra **các bước suy luận trung gian** trước khi đưa đáp án cuối.
- Cách kích hoạt zero-shot đơn giản nhất: thêm **"Let's think step by step."**

**Ví dụ kinh điển (Table 11–12):**
- Không CoT: *"Khi tôi 3 tuổi, partner của tôi gấp 3 lần tuổi tôi. Giờ tôi 20. Partner bao nhiêu tuổi?"* → model trả **63** (SAI).
- Có CoT ("Let's think step by step"): model suy luận từng bước (lúc đó partner 9 tuổi, chênh 6 tuổi, giờ 20 + 6) → **26** (ĐÚNG).
- Table 13: few-shot CoT — đưa 1 bài giải mẫu tương tự → model bắt chước cách giải, cũng ra 26.

**Ưu điểm:**
- Chi phí công sức thấp, hiệu quả cao, hoạt động với model "off-the-shelf" (không cần fine-tune);
- **Interpretability**: đọc được chuỗi suy luận → biết model sai ở bước nào;
- Robust hơn khi đổi version model.

**Nhược điểm:** output dài hơn → tốn token, tiền, thời gian.

**Use case:** code generation (chia nhỏ yêu cầu thành từng bước), tạo synthetic data, và mọi bài toán giải được bằng cách "nói qua từng bước".

### 3.8. Self-consistency prompting

- Kết hợp **sampling + majority voting**: chạy cùng một CoT prompt **nhiều lần với temperature cao** để sinh nhiều đường suy luận khác nhau, trích đáp án mỗi lần, rồi **chọn đáp án xuất hiện nhiều nhất**.
- Cho "pseudo-probability" về độ đúng của đáp án; tăng accuracy và coherence.
- **Đánh đổi:** chi phí cao (chạy N lần).

**Ví dụ (Table 14):** phân loại email `IMPORTANT / NOT IMPORTANT` — email báo bug WordPress từ "Harry the Hacker" giọng đùa cợt:
- Lần 1: IMPORTANT (lỗ hổng JavaScript là rủi ro bảo mật);
- Lần 2: NOT IMPORTANT (giọng không khẩn cấp, người gửi bảo cứ để bug đó);
- Lần 3: IMPORTANT (rủi ro nghiêm trọng, không rõ độ tin cậy người gửi).
→ Majority vote: **IMPORTANT**. Minh họa việc cùng 1 prompt có thể ra kết luận khác nhau tùy đường suy luận, và voting làm kết quả đáng tin hơn.

### 3.9. Tree of Thoughts (ToT)

- Tổng quát hóa CoT: thay vì **một chuỗi** suy luận tuyến tính, model duy trì **một cây** các "thought" — mỗi thought là một chuỗi ngôn ngữ mạch lạc đóng vai trò bước trung gian.
- Model có thể **rẽ nhánh từ các node khác nhau** để khám phá nhiều đường suy luận song song.
- Phù hợp task phức tạp cần **exploration** (tìm kiếm trong không gian lời giải).

### 3.10. ReAct (Reason & Act)

- Paradigm cho phép LLM giải task phức tạp bằng cách **kết hợp suy luận ngôn ngữ tự nhiên với công cụ bên ngoài** (search, code interpreter, API...) — mô phỏng cách con người vừa nghĩ vừa hành động.
- Vòng lặp **thought → action → observation**: suy luận về vấn đề → lập kế hoạch hành động → thực thi → quan sát kết quả → cập nhật suy luận → lặp đến khi ra lời giải.
- Đây là nền móng tư duy của **agent** hiện đại.

**Ví dụ (Table/Snippet 15, dùng LangChain + VertexAI + SerpAPI):** *"How many kids do the band members of Metallica have?"*
- Model tự thực hiện 5 lượt search: (1) Metallica có mấy thành viên → 4; (2–5) số con của từng người: James Hetfield 3, Lars Ulrich 3, Kirk Hammett 2, Robert Trujillo 2.
- Kết luận: **10 người con**.
- Lưu ý triển khai: phải quản lý việc gửi prompt/nhận response liên tục, cắt token thừa, và thiết lập ví dụ/hướng dẫn phù hợp cho model.

### 3.11. Automatic Prompt Engineering (APE)

- "Dùng prompt để viết prompt": tự động hóa việc tạo prompt → giảm công sức người, tăng hiệu năng model.
- Quy trình:
  1. Prompt model sinh ra nhiều biến thể prompt/output;
  2. Đánh giá các ứng viên bằng metric (**BLEU**, **ROUGE**...);
  3. Chọn ứng viên điểm cao nhất;
  4. Tinh chỉnh và đánh giá lại nếu cần.

**Ví dụ (Table 16):** huấn luyện chatbot cho web bán merchandise — yêu cầu model sinh **10 cách diễn đạt cùng nghĩa** với đơn hàng "One Metallica t-shirt size S" ("I'd like to purchase a Metallica t-shirt in size small", "One Metallica shirt, size small, please"...) → dùng làm dữ liệu train/test đa dạng.

---

## 4. Code prompting — Prompt cho lập trình

Cả 4 ví dụ dùng chung config: **temperature 0.1, token limit 1024, top-P 1** (code cần chính xác, ít sáng tạo).

### 4.1. Viết code (Table 17)
- Prompt: viết Bash script hỏi tên folder, rồi đổi tên toàn bộ file trong đó bằng cách thêm prefix `draft_`.
- Output: script hoàn chỉnh có comment, check folder tồn tại, vòng lặp rename.
- **Bài học quan trọng:** model có thể "nhại" từ training data — **luôn đọc và test code trước khi chạy**. Tác giả đã test thật và xác nhận file được rename đúng (`filename.txt → draft_filename.txt`).

### 4.2. Giải thích code (Table 18)
- Đưa script Bash (đã bỏ comment) + yêu cầu giải thích.
- Output: giải thích 5 phần — nhận input, kiểm tra folder tồn tại, liệt kê file, vòng lặp rename, thông báo thành công. Hữu ích khi đọc code người khác/code cũ.

### 4.3. Dịch code giữa ngôn ngữ (Table 19)
- Bash → Python: model trả script Python tương đương dùng `os`, `shutil`.
- Lưu ý thực dụng: trong Vertex AI cần bật Markdown render để **giữ indentation** — Python sai indent là sai code.

### 4.4. Debug và review code (Table 20)
- Đưa traceback lỗi (`NameError: name 'toUpperCase' is not defined`) + code Python lỗi, yêu cầu debug và đề xuất cải thiện.
- Output: (1) fix đúng lỗi — dùng `.upper()` thay `toUpperCase()`, sửa biến `new_filename` vs `new_file_name` không khớp; (2) **chủ động đề xuất cải thiện thêm**: giữ file extension, xử lý khoảng trắng trong tên, error handling — minh họa giá trị review vượt yêu cầu ban đầu.

### 4.5. Multimodal prompting (đề cập ngắn)
- Dùng nhiều định dạng input kết hợp (text, ảnh, audio, code...) thay vì chỉ text; khả năng phụ thuộc model và task. Whitepaper chỉ giới thiệu khái niệm, không đi sâu.

---

## 5. Best Practices — 13 nhóm thực hành tốt nhất

### 5.1. Cung cấp ví dụ (quan trọng nhất)
One-shot/few-shot là best practice **quan trọng số 1** — ví dụ là "công cụ dạy học" mạnh, cho model điểm tham chiếu về độ chính xác, style, tone.

### 5.2. Thiết kế đơn giản (Design with simplicity)
- Prompt phải súc tích, rõ ràng, dễ hiểu. **Quy tắc vàng: nếu chính bạn đọc còn thấy rối thì model cũng sẽ rối.**
- Tránh ngôn ngữ phức tạp, thông tin thừa.
- Ví dụ viết lại:
  - TRƯỚC (dở): "I am visiting New York right now, and I'd like to hear more about great locations. I am with two 3 year old kids. Where should we go during our vacation?"
  - SAU (tốt): "Act as a travel guide for tourists. Describe great places to visit in New York Manhattan with a 3 year old."
- Dùng **động từ hành động** rõ ràng: Act, Analyze, Categorize, Classify, Compare, Contrast, Create, Describe, Define, Evaluate, Extract, Find, Generate, Identify, List, Measure, Organize, Parse, Pick, Predict, Provide, Rank, Recommend, Return, Retrieve, Rewrite, Select, Show, Sort, Summarize, Translate, Write.

### 5.3. Cụ thể về đầu ra (Be specific about the output)
- Nêu rõ format, style, nội dung, độ dài mong muốn — chỉ dẫn chung chung không đủ định hướng.
- DO: "Generate a 3 paragraph blog post about the top 5 video game consoles. The blog post should be informative and engaging, written in a conversational style."
- DON'T: "Generate a blog post about video game consoles."

### 5.4. Ưu tiên Instructions hơn Constraints
- **Instruction** = nói model NÊN làm gì; **Constraint** = nói model KHÔNG được làm gì.
- Nghiên cứu cho thấy chỉ dẫn tích cực hiệu quả hơn danh sách cấm đoán — instruction truyền đạt trực tiếp kết quả mong muốn, còn constraint dễ khiến model bối rối về những gì được phép và dễ xung đột lẫn nhau.
- DO: "...Only discuss the console, the company who made it, the year, and total sales."
- DON'T: "...Do not list video game names."
- Constraint vẫn cần khi liên quan **safety** hoặc yêu cầu cứng về format.

### 5.5. Kiểm soát max token length
Hai cách: đặt giới hạn trong config, hoặc yêu cầu ngay trong prompt ("Explain quantum physics in a tweet length message.").

### 5.6. Dùng biến (variables) trong prompt
- Tách phần thay đổi thành biến → prompt tái sử dụng được, dynamic, dễ tích hợp vào ứng dụng.
```
VARIABLES: {city} = "Amsterdam"
PROMPT: You are a travel guide. Tell me a fact about the city: {city}
```

### 5.7. Thử nhiều format input và cách hành văn
Cùng một mục tiêu, thử cả 3 dạng — **question / statement / instruction** — vì kết quả sẽ khác nhau:
- Question: "What was the Sega Dreamcast and why was it so revolutionary?"
- Statement: "The Sega Dreamcast was a sixth-generation video game console released by Sega in 1999. It..."
- Instruction: "Write a single paragraph that describes the Sega Dreamcast console and explains why it was so revolutionary."

### 5.8. Trộn thứ tự class trong few-shot classification
- Với task phân loại, **xáo trộn thứ tự** các ví dụ thuộc các class khác nhau — tránh model overfit vào thứ tự thay vì học đặc trưng thật của từng class.
- Rule of thumb: bắt đầu với **6 ví dụ few-shot**, đo accuracy rồi điều chỉnh.

### 5.9. Thích ứng khi model cập nhật
Model đổi version/kiến trúc/dữ liệu → prompt cũ có thể cần chỉnh. Chủ động test trên version mới, tận dụng tính năng mới; dùng công cụ như Vertex AI Studio để lưu, version hóa và test prompt.

### 5.10. Thử nghiệm với output format có cấu trúc
- Với task phi sáng tạo (extract, select, parse, order, rank, categorize) → yêu cầu trả **JSON/XML**.
- Lợi ích JSON: khỏi format tay, dữ liệu có thể trả đã sort, và quan trọng nhất — **ép cấu trúc giúp giảm hallucination**.

### 5.11. Thử nghiệm cùng các prompt engineer khác
Nhiều người cùng viết prompt cho một bài toán sẽ ra kết quả chênh lệch nhau → thử đa dạng tăng xác suất tìm được prompt tối ưu.

### 5.12. Best practices riêng cho CoT
- **Đáp án phải đặt SAU phần suy luận** — vì thứ tự sinh token quyết định: suy luận sinh trước sẽ thay đổi ngữ cảnh token khi model dự đoán đáp án cuối.
- Với self-consistency: phải **tách được đáp án cuối khỏi phần suy luận** để đếm vote.
- **Đặt temperature = 0 cho CoT**: suy luận để ra đáp án cuối thường chỉ có một đáp án đúng duy nhất.

### 5.13. Ghi chép đầy đủ các lần thử prompt (cực kỳ quan trọng)
- Output khác nhau giữa các model, sampling setting, và cả giữa các lần chạy cùng model (khác biệt nhỏ về wording/format vẫn xảy ra).
- **Template ghi chép khuyến nghị (Table 21):**

| Trường | Nội dung |
|---|---|
| Name | Tên + version của prompt |
| Goal | 1 câu mô tả mục tiêu lần thử |
| Model | Tên + version model |
| Temperature | 0–1 |
| Token Limit | số |
| Top-K / Top-P | số |
| Prompt | Toàn văn prompt |
| Output | Output (hoặc nhiều output) |

- Nên track thêm: version iteration, trường kết quả (OK / NOT OK / SOMETIMES OK), feedback.
- Nếu dùng Vertex AI Studio: lưu prompt cùng tên/version với bản ghi chép, kèm hyperlink để re-run 1 click.
- Với hệ thống **RAG**: ghi cả query, chunk setting, chunk output và các yếu tố ảnh hưởng đến việc nhét nội dung vào prompt.
- Khi đưa vào production: **lưu prompt tách khỏi code** (file riêng) cho dễ bảo trì; đưa prompt vào hệ thống vận hành có automated test + evaluation.

---

## 6. Sơ đồ tổng kết toàn bộ whitepaper

```
PROMPT ENGINEERING (Google/Kaggle whitepaper)
│
├── 1. Nền tảng: LLM = prediction engine (dự đoán token kế tiếp)
│
├── 2. Output Configuration
│     ├── Output length (cắt cụt ≠ súc tích)
│     ├── Temperature (0 = greedy/deterministic ↔ cao = sáng tạo)
│     ├── Top-K (K token tốt nhất)
│     └── Top-P (xác suất tích lũy / nucleus sampling)
│
├── 3. Kỹ thuật prompting
│     ├── Cơ bản:    Zero-shot → One-shot → Few-shot
│     ├── Định khung: System / Role / Contextual prompting
│     ├── Suy luận:  Step-back → CoT → Self-consistency → ToT
│     ├── Hành động: ReAct (reason + act, dùng tool ngoài) → agent
│     └── Tự động:   APE (prompt sinh prompt, chấm bằng BLEU/ROUGE)
│
├── 4. Code prompting: viết / giải thích / dịch / debug+review code
│     └── (+ Multimodal prompting — giới thiệu khái niệm)
│
└── 5. Best practices (13 nhóm)
      ├── Ví dụ là số 1 · Đơn giản · Cụ thể về output
      ├── Instructions > Constraints · Giới hạn token · Variables
      ├── Thử format/style · Trộn class few-shot · Theo kịp model mới
      ├── Output JSON/XML · Làm việc nhóm
      ├── CoT: đáp án sau suy luận, temperature = 0
      └── DOCUMENT MỌI LẦN THỬ (template Table 21) — iterative!
```

---

## 7. Lộ trình tự học đề xuất theo whitepaper

1. **Tuần 1 — Nền tảng:** hiểu LLM là prediction engine; thực hành chỉnh temperature/top-K/top-P trên cùng 1 prompt và quan sát khác biệt (dùng Vertex AI Studio, Google AI Studio, hoặc bất kỳ playground nào).
2. **Tuần 2 — Kỹ thuật cơ bản:** làm lại các ví dụ zero-shot (phân loại review), few-shot (parse pizza order → JSON), system/role/contextual (travel guide, blog retro games).
3. **Tuần 3 — Kỹ thuật suy luận:** tự tái hiện bài toán tuổi (Table 11–13) có/không CoT; thử step-back với một bài viết sáng tạo; chạy self-consistency 3–5 lần trên 1 bài phân loại mơ hồ.
4. **Tuần 4 — Nâng cao + kỷ luật:** dựng một ReAct agent nhỏ (LangChain hoặc tương đương) làm lại bài Metallica; bắt đầu **bảng ghi chép prompt** theo template Table 21 và duy trì như thói quen làm việc.

**3 ý cần khắc cốt ghi tâm:**
1. Prompt engineering là **iterative** — không có prompt hoàn hảo ngay lần đầu.
2. **Ví dụ (few-shot) là công cụ mạnh nhất**, instruction tích cực tốt hơn danh sách cấm.
3. **Không ghi chép = không tiến bộ** — model đổi version là prompt có thể phải chỉnh lại, chỉ có ghi chép mới giúp bạn biết mình đã thử gì.

---

## 8. Nguồn tham khảo

- Trang whitepaper trên Kaggle: https://www.kaggle.com/whitepaper-prompt-engineering
- Trang tác giả Lee Boonstra: https://www.leeboonstra.dev/writing/write-prompting-whitepaper/
- Bản full text (Internet Archive): https://archive.org/details/whitepaper-prompt-engineering-v-4
- Khóa liên quan: Kaggle 5-Day Gen AI Intensive Course (Google)

> Ghi chú biên soạn: các bảng ví dụ (Table 1–21), giá trị config và trích dẫn trong tài liệu này bám theo nội dung bản whitepaper v4; phần "Lộ trình tự học" (mục 7) là gợi ý của người biên soạn, không thuộc whitepaper gốc.
