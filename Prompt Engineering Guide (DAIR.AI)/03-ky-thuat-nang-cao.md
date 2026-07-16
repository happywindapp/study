# 03 — Các kỹ thuật Prompting nâng cao

> Nguồn: promptingguide.ai → **Prompting Techniques** (nửa sau). Đây là các kỹ thuật kết hợp search, tool, chương trình, vòng lặp phản tư — tiền thân trực tiếp của agentic systems (bài 04).

## 1. Tree of Thoughts (ToT)

📄 Yao et al. 2023 & Long 2023 — *Tree of Thoughts: Deliberate Problem Solving with LLMs*

**Là gì:** tổng quát hóa CoT thành **cây tìm kiếm trên các "thought"** (bước suy luận trung gian). Model sinh nhiều thought ứng viên ở mỗi bước → **tự đánh giá** tiến độ (sure/maybe/impossible, hoặc voting) → duyệt cây bằng **BFS/DFS + lookahead/backtracking**.

- Bài Game of 24: ToT tách 3 bước, mỗi bước giữ b=5 ứng viên tốt nhất → thắng áp đảo CoT thường.
- Biến thể Long 2023: "ToT Controller" học bằng RL điều khiển backtracking — có thể tự tiến hóa, khác BFS/DFS cố định.

**ToT Prompting** (Hulbert 2023) — bản "một prompt" dân dã hóa ý tưởng:
```
Imagine three different experts are answering this question.
All experts will write down 1 step of their thinking, then share it with the group.
Then all experts will go on to the next step, etc.
If any expert realises they're wrong at any point then they leave.
The question is...
```

**Khi nào dùng:** bài toán cần **khám phá + nhìn xa** (planning, search, game, tổ hợp) mà một mạch suy luận tuyến tính dễ kẹt ngõ cụt. Chi phí rất cao (nhiều lần gọi model).

## 2. Retrieval Augmented Generation (RAG)

📄 Lewis et al. 2021 (Meta AI) — *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*

**Là gì:** ghép **retriever** (tìm tài liệu liên quan từ nguồn ngoài, vd Wikipedia/vector DB) với **generator** — tài liệu tìm được được nối vào context của prompt. Giải quyết 2 vấn đề bản chất của LLM: (1) kiến thức đóng băng tại thời điểm train, (2) hallucination ở task knowledge-intensive.

- Kiến thức cập nhật **không cần retrain** — chỉ cần cập nhật kho tài liệu.
- Tăng độ nhất quán fact, câu trả lời bám nguồn (grounded, có thể trích dẫn).
- Guide xếp RAG vừa là "kỹ thuật prompting" (nghĩa hẹp: nhét context truy xuất vào prompt) vừa có trang research riêng (RAG for LLMs) về các kiến trúc nâng cao.

**Vị trí 2026:** RAG là kiến trúc mặc định cho chatbot tri thức doanh nghiệp; cạnh tranh/bổ trợ bởi long-context + context caching (xem bài 05) và agentic search.

## 3. Automatic Reasoning and Tool-use (ART)

📄 Paranjape et al. 2023

**Là gì:** framework tự động ghép CoT + tool use mà không cần viết demo tay cho từng task:
1. Nhận task mới → chọn **demonstrations về reasoning + tool use từ task library** (thư viện mẫu đa-task).
2. Khi sinh, **tạm dừng ở chỗ cần gọi tool** (search, calculator, code), chèn output của tool rồi sinh tiếp.
3. Zero-shot tổng quát hóa sang task mới; con người có thể sửa/thêm tool (extensible).

Vượt few-shot thủ công và auto-CoT trên BigBench, MMLU. Ý nghĩa lịch sử: khuôn "pause generation → call tool → resume" chính là tổ tiên của function calling native ngày nay.

## 4. Automatic Prompt Engineer (APE)

📄 Zhou et al. 2022 — *Large Language Models are Human-Level Prompt Engineers*

**Là gì:** dùng LLM để **tự sinh và tìm kiếm instruction tối ưu**: một model sinh nhiều ứng viên instruction từ các cặp input/output mẫu → chấm điểm từng ứng viên trên tập đánh giá → chọn instruction điểm cao nhất.

Kết quả nổi tiếng: APE tìm ra zero-shot CoT prompt **tốt hơn** câu người viết: *"Let's work this out in a step by step way to be sure we have the right answer."* (hơn "Let's think step by step" trên MultiArith/GSM8K).

Cùng dòng nghiên cứu prompt tự động mà guide nhắc: AutoPrompt (Shin 2020), Prefix Tuning (Li & Liang 2021), Prompt Tuning (Lester 2021). Hậu duệ hiện đại: DSPy — tối ưu prompt/pipeline tự động.

## 5. Active-Prompt

📄 Diao et al. 2023

**Vấn đề:** CoT few-shot dùng bộ ví dụ cố định do người viết — chưa chắc hiệu quả cho mọi task.

**Giải pháp — chọn câu hỏi đáng công gán nhãn nhất theo độ bất định (uncertainty):**
1. Query model (k lần) cho từng câu hỏi trong tập → nhận k đáp án.
2. Tính **uncertainty** (vd độ bất đồng giữa k đáp án).
3. Chọn các câu hỏi **uncertain nhất** cho người gán CoT mẫu.
4. Dùng các ví dụ mới gán để suy luận các câu còn lại.

Bản chất: active learning áp vào việc chọn exemplar — tiết kiệm công người annotate mà tăng hiệu quả nhất.

## 6. Directional Stimulus Prompting (DSP)

📄 Li et al. 2023

**Là gì:** dùng một **policy LM nhỏ, tune được** (T5) sinh ra "stimulus/hint" (vd keywords cần có trong bản tóm tắt) → nhét hint vào prompt cho LLM đen-hộp thực hiện task. Policy model được tối ưu bằng SL + RL để sinh hint làm LLM ra kết quả tốt hơn.

Ý nghĩa: điều khiển LLM đóng (không tune được) một cách **có thể huấn luyện** — thay vì tune LLM, tune "người nhắc bài" nhỏ hơn nhiều.

## 7. Program-Aided Language Models (PAL)

📄 Gao et al. 2022

**Là gì:** thay vì bắt model tự tính toán trong ngôn ngữ tự nhiên (hay sai số học), yêu cầu model **sinh chương trình Python làm bước suy luận trung gian**, rồi **giao cho interpreter chạy** ra đáp án.

Ví dụ của guide — tính ngày tháng:
```
Q: Today is 27 February 2023. I was born exactly 25 years ago. What is the date I was born in MM/DD/YYYY?
# If today is 27 February 2023 and I was born exactly 25 years ago, then I was born 25 years before.
today = datetime(2023, 2, 27)
born = today - relativedelta(years=25)
# The answer formatted with %m/%d/%Y is
born.strftime('%m/%d/%Y')
```

Khác CoT: bước suy luận là **code chạy được**, không phải văn tự do → chính xác tuyệt đối phần tính toán. Hậu duệ trực tiếp: code interpreter / code execution tool của các model hiện đại.

## 8. ReAct (Reason + Act)

📄 Yao et al. 2022 — *ReAct: Synergizing Reasoning and Acting in Language Models*

**Là gì:** đan xen **reasoning trace** và **action** (gọi tool/API/search) trong một vòng lặp:

```
Thought 1 → Action 1 (vd Search[Apple Remote]) → Observation 1
Thought 2 → Action 2 → Observation 2 → ... → Action: Finish[đáp án]
```

- **Thought** lập kế hoạch, theo dõi tiến độ, xử lý ngoại lệ; **Action** lấy thông tin từ nguồn ngoài (search engine, API); **Observation** là kết quả trả về.
- Khắc phục 2 điểm yếu: CoT thuần bị hallucination (không có nguồn ngoài); action thuần không có kế hoạch.
- Kết quả: thắng baseline trên HotpotQA/Fever (kết hợp CoT + self-consistency + ReAct tốt nhất), thắng lớn trên ALFWorld/WebShop (decision making).
- Guide có hands-on: dựng ReAct agent bằng **LangChain** (`AgentType.ZERO_SHOT_REACT_DESCRIPTION` + SerpAPI + LLM-math) — mẫu code lịch sử đáng biết vì hầu hết agent framework khởi nguồn từ khuôn ReAct.

**Vị trí 2026:** ReAct chính là "vòng lặp agent" — mọi coding agent/computer-use agent hiện đại đều là biến thể ReAct với function calling native thay cho text parsing.

## 9. Reflexion

📄 Shinn et al. 2023 — *Reflexion: Language Agents with Verbal Reinforcement Learning*

**Là gì:** framework "học tăng cường bằng lời" — agent tự **phản tư về thất bại bằng ngôn ngữ** và lưu vào memory để lần thử sau tốt hơn. 3 model thành phần:

1. **Actor** — sinh hành động/văn bản (có thể là CoT hay ReAct) + memory.
2. **Evaluator** — chấm điểm output của Actor (reward score).
3. **Self-Reflection** — sinh nhận xét bằng lời (verbal feedback) cụ thể hơn con số reward, lưu vào **episodic memory** cho vòng sau.

Vòng lặp: define task → generate trajectory → evaluate → reflect → generate next trajectory. Hiệu quả trên sequential decision-making (ALFWorld), reasoning (HotpotQA), coding (HumanEval — đạt SOTA thời điểm đó).

**Khi nào dùng:** task thử-sai được (có tín hiệu đánh giá: test pass/fail, môi trường phản hồi); khi cần cải thiện qua nhiều lần thử mà không fine-tune. **Giới hạn:** phụ thuộc năng lực tự đánh giá của model; memory dài bị cắt; không có bảo đảm thành công.

## 10. Multimodal CoT

📄 Zhang et al. 2023

CoT hai giai đoạn trên input **văn bản + hình ảnh**: (1) sinh rationale từ thông tin đa phương thức; (2) suy đáp án từ rationale đó. Model 1B đã vượt GPT-3.5 trên ScienceQA benchmark thời điểm công bố. Ngày nay tư tưởng này sống trong các VLM reasoning (thinking with images).

## 11. Graph Prompting

📄 Liu et al. 2023 — *GraphPrompt*

Framework prompting cho **dữ liệu đồ thị** (GNN): thống nhất pre-training và downstream task qua template prompt chung, giúp few-shot trên graph tasks. Trang này trong guide ngắn — chủ yếu là pointer sang paper; ít liên quan LLM text thuần.

---

## Bảng tóm tắt bài 03 — chọn kỹ thuật theo bài toán

| Bài toán | Kỹ thuật | Chi phí | Paper |
|---|---|---|---|
| Suy luận cần khám phá nhiều nhánh, backtrack | Tree of Thoughts | Rất cao | Yao 2023 |
| Cần kiến thức ngoài/mới, chống bịa | RAG | +retriever | Lewis 2021 |
| Tự động ghép reasoning + tool đa task | ART | Trung bình | Paranjape 2023 |
| Tự tìm instruction tối ưu | APE | Offline search | Zhou 2022 |
| Chọn exemplar CoT đáng annotate nhất | Active-Prompt | Offline | Diao 2023 |
| Điều khiển LLM đen-hộp bằng hint học được | DSP | +policy model | Li 2023 |
| Tính toán chính xác (toán, ngày tháng) | PAL | 1x + interpreter | Gao 2022 |
| Task cần tool + kế hoạch (QA đa bước, web) | ReAct | Nhiều vòng | Yao 2022 |
| Cải thiện qua thử-sai có feedback | Reflexion | Nhiều trajectory | Shinn 2023 |
| Reasoning trên ảnh + text | Multimodal CoT | 2 giai đoạn | Zhang 2023 |
| Few-shot trên graph/GNN | Graph Prompting | — | Liu 2023 |

**Mạch tiến hóa đáng nhớ:** CoT → (nhiều mạch) Self-Consistency → (cây) ToT · CoT → (thêm tool) ReAct/ART → (thêm memory + phản tư) Reflexion → **AI Agents** (bài 04). RAG và PAL là hai nhánh "mượn ngoại lực": mượn kho tri thức và mượn interpreter.
