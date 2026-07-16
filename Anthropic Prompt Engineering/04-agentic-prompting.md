# 04 — Agentic Prompting: Tool Use & hệ thống Agent (docs.anthropic.com)

> Nhóm kỹ thuật cho ứng dụng agent: tool use, chạy song song, kiểm soát mức tự chủ, chống overengineering/hardcoding/hallucination, điều phối subagent.

## 1. Tool Use: yêu cầu HÀNH ĐỘNG tường minh

Model mới được huấn luyện theo sát chỉ dẫn — muốn nó **dùng tool để làm** (chứ không chỉ gợi ý), phải nói tường minh:

```text
❌ Claude chỉ gợi ý:  "Can you suggest some changes to improve this function?"
✅ Claude thực sự sửa: "Change this function to improve its performance."
```

### Điều chỉnh mức chủ động qua system prompt

Chủ động hành động:

```xml
<default_to_action>
By default, implement changes rather than only suggesting them. If the
user's intent is unclear, infer the most useful likely action and proceed,
using tools to discover missing details instead of guessing.
</default_to_action>
```

Thận trọng, chỉ làm khi được yêu cầu:

```xml
<do_not_act_before_instructions>
Do not jump into implementation unless clearly instructed. When intent is
ambiguous, default to providing information, research, and recommendations
rather than action. Only proceed with edits/modifications when explicitly
requested.
</do_not_act_before_instructions>
```

## 2. Parallel Tool Calling — chạy tool song song

Model mới tự chạy song song các tool call độc lập. Để đạt ~100% hiệu suất song song:

```xml
<use_parallel_tool_calls>
If you intend to make multiple tool calls with NO dependencies between
them, make all calls in parallel. Prioritize simultaneous execution.
Example: reading 3 files = 3 parallel tool calls. If calls depend on
previous results, call them sequentially. Never use placeholders or
guess missing parameters.
</use_parallel_tool_calls>
```

## 3. Long-Horizon Work — làm việc dài hơi, theo dõi trạng thái

Cho agent chạy nhiều giờ / nhiều context window:

1. **Chia theo cửa sổ:** window đầu dựng khung (viết test, setup script); các window sau bám todo-list mà lặp.
2. **Trạng thái dạng cấu trúc** (JSON) cho những thứ đếm được:

```json
{
  "tests": [
    { "id": 1, "name": "auth_flow", "status": "passing" },
    { "id": 2, "name": "user_mgmt", "status": "failing" }
  ],
  "total": 200, "passing": 150, "failing": 25
}
```

3. **Setup script** để khởi động server/test/linter — tránh làm lại từ đầu mỗi phiên.
4. **3 kênh lưu trạng thái:** JSON có cấu trúc · ghi chú tự do · git (checkpoint + log).
5. **Prompt nhận thức context** (cho harness có auto-compaction):

```text
Your context window will auto-compact at limit, allowing indefinite
continuation. Do not stop early due to token budget. Save progress/state
before refresh. Be persistent; complete tasks fully.
```

## 4. Cân bằng tự chủ và an toàn

Không có hướng dẫn, agent có thể xóa file, force-push, đăng lên dịch vụ ngoài. Mẫu system prompt:

```xml
<balancing_autonomy>
Encourage local, reversible actions (file edits, tests). For hard-to-reverse
or shared-system actions, ask the user before proceeding.

Warrant confirmation:
- Destructive: deleting files/branches, dropping tables, rm -rf
- Hard to reverse: git push --force, git reset --hard, amending published commits
- Visible to others: pushing code, commenting on PRs, posting to external services

Avoid shortcuts via destructive actions (don't bypass with --no-verify,
don't discard unfamiliar files).
</balancing_autonomy>
```

## 5. Subagent Orchestration

Model mới **tự nhận biết** khi nào nên tách subagent. Định hướng khi cần:

```text
Use subagents when: tasks can run in parallel, require isolated context,
or involve independent workstreams without shared state.

Work directly for: simple tasks, sequential operations, single-file edits,
tasks needing context across steps.
```

## 6. Chain Complex Prompts — chuỗi prompt tường minh

Model mới xử lý đa số chuỗi bước **bên trong** (adaptive thinking + subagent). Chỉ tách chuỗi prompt tường minh khi:

- Cần **kiểm tra kết quả trung gian**, hoặc
- Cần **ép một pipeline cố định**.

Pattern phổ biến nhất — **self-correction**:

1. Sinh bản nháp
2. Review bản nháp theo tiêu chí
3. Tinh chỉnh theo review

Mỗi bước một API call riêng → log được, đánh giá được, rẽ nhánh được.

## 7. Chống các "bệnh" của agent

### a) Overengineering (làm quá đà)

```xml
<minimize_overengineering>
Only make directly requested or clearly necessary changes. Keep solutions
simple and focused:
- Scope: don't add features, refactor, or improve beyond what was asked.
- Documentation: don't add docstrings/comments/types to unchanged code.
- Defensive coding: don't handle impossible scenarios; validate only at
  system boundaries.
- Abstractions: no helpers for one-time operations; no design for
  hypothetical futures. Minimum complexity for the current task.
</minimize_overengineering>
```

### b) Hardcoding để qua test

```text
Write a high-quality, general-purpose solution using standard tools.
Implement a solution that works for ALL valid inputs, not just the test
cases. Do not hardcode test-specific values. Tests verify correctness;
they don't define the solution. If the task is unreasonable or tests are
incorrect, inform me rather than working around them.
```

### c) Hallucination khi trả lời về code

```xml
<investigate_before_answering>
Never speculate about code you have not opened. If the user references a
file, you MUST read it before answering. Investigate and read relevant
files BEFORE answering questions about the codebase. Give grounded,
hallucination-free answers.
</investigate_before_answering>
```

### d) File tạm bừa bãi

```text
If you create temporary files/scripts/helpers for iteration, clean up by
removing them at the end of the task.
```

## 8. Frontend Aesthetics — chống "AI slop"

Không định hướng, model sinh giao diện "đúng phân phối" — chung chung, na ná nhau. Mẫu chỉ dẫn:

```xml
<frontend_aesthetics>
Avoid converging to generic outputs. Make creative, distinctive frontends.
- Typography: distinctive fonts (Fraunces, Playfair...); avoid Inter/Roboto/Arial.
- Color: cohesive theme via CSS variables; dominant color + sharp accents,
  not timid palettes.
- Motion: well-orchestrated page load with staggered reveals beats
  scattered micro-interactions. CSS-only for HTML; Motion library for React.
- Backgrounds: gradients, geometric patterns, contextual effects over
  solid colors.
AVOID: overused fonts, purple-gradient-on-white cliché, cookie-cutter layouts.
</frontend_aesthetics>
```

Hai cách chỉnh "gu" mặc định của model hiệu quả: (1) đặc tả cụ thể thay thế (hex màu, font); (2) bắt model đề xuất 4 hướng thị giác khác nhau rồi người dùng chọn.

## 9. Ghi chú model-specific (ví dụ Opus 4.8)

- **Verbosity tự hiệu chỉnh** theo độ khó — muốn ngắn: "Provide concise, focused responses. Skip non-essential context."
- **Literal instruction following:** hiểu prompt theo nghĩa đen hơn — muốn áp rộng phải nói rõ phạm vi ("Apply to every section").
- **Effort ảnh hưởng tool use:** effort cao → dùng tool nhiều hơn (search/coding tăng đáng kể).
- **Bỏ scaffolding cũ:** các câu ép "cứ 3 tool call thì báo cáo" không cần nữa — model tự cập nhật tiến độ tốt.
- **Tone mặc định:** thẳng, có chính kiến, ít khen — muốn ấm hơn phải yêu cầu.

---

Tiếp theo: [05 — Tutorial GitHub: Chương 1–4](05-tutorial-chuong-1-4.md)
