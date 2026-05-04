---
name: audit-uiux
description: >-
  Audit UI/UX cho thiết kế Figma trước khi hand-off cho developer.
  Kết hợp JTBD framework với heuristic evaluation (Apple HIG, Material Design, Nielsen).
  Phân tích behavioral impact, tạo user-story + hypothesis cho từng finding.
  Báo cáo có bằng chứng với phân loại P0/P1/P2.
  Use when the user asks to audit, review, or evaluate a Figma design for quality,
  usability, accessibility, or hand-off readiness, or uses /audit-uiux.
---

# Audit UI/UX — Figma Design Review

Skill thực hiện UI/UX audit cho thiết kế Figma trước khi hand-off cho developer. Kết hợp JTBD (Jobs to Be Done) framework với heuristic evaluation để đảm bảo design vừa đúng tiêu chuẩn, vừa phục vụ đúng mục đích người dùng.

## Trigger Conditions

Áp dụng skill khi user:
- Yêu cầu audit, review, hoặc đánh giá thiết kế Figma
- Kiểm tra chất lượng trước hand-off cho developer
- Đánh giá usability, accessibility, hoặc design consistency
- Dùng `/audit-uiux`
- Cung cấp Figma URL và hỏi về chất lượng design

## Skill Boundaries

- Mặc định, skill này **đọc và phân tích** design, không tự ý chỉnh sửa Figma file.
- Nếu user muốn **áp dụng chỉnh sửa trực tiếp trên Figma** theo checklist đã thống nhất, dùng **Chế độ COOK NOW** (xem mục bên dưới). Chỉ thực hiện khi user **chọn hạng mục** và xác nhận **COOK NOW**.
- Nếu user muốn sửa design theo mô tả tự do (không theo checklist từ audit), chuyển sang [edit-figma-design](../edit-figma-design/SKILL.md).
- Nếu user muốn kiểm tra design-system token mapping, chuyển sang [get-design-context](../get-design-context/SKILL.md).
- Nếu user muốn reconnect design vào design system, chuyển sang [apply-design-system](../apply-design-system/SKILL.md).

## Prerequisites

- Figma MCP server phải connected và accessible.
- User cung cấp Figma URL dạng: `https://figma.com/design/:fileKey/:fileName?node-id=1-2`
- Các tool cần thiết: `get_design_context`, `get_screenshot`, `get_metadata`, `search_design_system`.

## Cơ chế hỏi bổ sung (3 câu tối thiểu)

Sau khi user cung cấp **Figma URL** (và tùy chọn context ngắn), agent **chủ động kiểm tra** các mục sau. Nếu **thiếu hoặc mơ hồ** bất kỳ mục nào, **dừng lại trước Step 2** và gửi **một tin nhắn** chứa đúng **3 câu hỏi** (không gộp thành một câu dài), đánh số rõ ràng.

### Ba câu bắt buộc (luôn dùng tiếng Việt)

| # | Chủ đề | Câu hỏi mẫu |
|---|--------|------------|
| **1** | Chân dung người dùng | Ai là người dùng chính của màn hình/luồng này? (vai trò, bối cảnh sử dụng ngắn gọn) |
| **2** | Công việc cần làm (JTBD) | Người dùng cần **hoàn thành công việc gì** trên màn hình/luồng này? (một câu, tránh mô tả tính năng chung chung) |
| **3** | Tiêu chí thành công | Thế nào được coi là **thành công** sau khi dùng xong? (ví dụ: đặt xong lịch, gửi được đơn, tìm được sản phẩm đúng — có thể định tính) |

**Quy tắc:**

- Chỉ hỏi những câu tương ứng với **thông tin đang thiếu**. Nếu user đã viết rõ trong tin đầu (VD: đã có persona + job + tiêu chí), **không** lặp lại câu đó.
- **Không** vượt quá **3 câu** trong một lượt, trừ khi user **chủ động** yêu cầu audit sâu hoặc scope là **flow phức tạp** — khi đó có thể thêm **tối đa 2 câu bổ sung** (xem dưới).
- Nếu user trả lời **ngắn hoặc một phần**, coi là đủ để tiếp tục; chỉ hỏi lại phần **vẫn trống**.
- Nếu user nói **"bỏ qua" / "không cần" / "audit luôn"**: ghi trong báo cáo phần **JTBD Context** là *được suy luận từ giao diện*, rồi tiếp tục workflow.

### Hai câu tùy chọn (chỉ khi cần)

Chỉ thêm khi **một trong các điều kiện** sau đúng **và** thông tin chưa có trong chat:

| Điều kiện | Câu hỏi bổ sung (chọn 1–2, tiếng Việt) |
|-----------|--------------------------------------|
| Phạm vi là **luồng nhiều màn** hoặc **prototype** | Thứ tự màn hình / nhánh chính trong luồng là gì? Có điểm rẽ nhánh quan trọng nào cần audit không? |
| Cần đánh giá **tiếp cận (accessibility)** kỹ | Mức tiêu chí mong muốn là gì (ví dụ: WCAG AA), và nền tảng chính (web / iOS / Android)? |
| **Nhiều persona** hoặc **nhiều use-case** | Persona hoặc kịch bản nào là **ưu tiên** cho lần audit này? |

### Vì sao cần cơ chế này

Giảm gap **JTBD suy luận sai**, giảm báo cáo **thiếu tiêu chí thành công**, và giúp phần **severity / behavioral impact** bám sát nghiệp vụ thật khi user có thể bổ sung trong vài câu trả lời.

## Required Workflow

**Thực hiện tuần tự 12 bước. Không bỏ bước.**

### Step 1: Thu thập context

1. Parse Figma URL để extract `fileKey` và `nodeId`.
2. Xác định scope audit:
   - **Full page**: audit toàn bộ page/screen
   - **Flow**: audit một user flow trải qua nhiều screen
   - **Component**: audit một component cụ thể
3. Áp dụng [Cơ chế hỏi bổ sung (3 câu tối thiểu)](#cơ-chế-hỏi-bổ-sung-3-câu-tối-thiểu): nếu thiếu persona / job / tiêu chí thành công, **hỏi trước**, chờ trả lời (hoặc xác nhận bỏ qua), rồi mới sang Step 2.
4. Ghi nhận platform (web/iOS/Android) và mức accessibility nếu user đã cung cấp (hoặc từ câu hỏi tùy chọn).

### Step 2: Capture current state

Thu thập dữ liệu từ Figma song song:

1. `get_screenshot(fileKey, nodeId)` — visual snapshot làm evidence.
2. `get_metadata(fileKey, nodeId)` — layer structure, hierarchy, positions, sizes.
3. `get_design_context(fileKey, nodeId)` — tokens, styles, component references, variables.

**Lưu ảnh chụp để nhúng vào báo cáo:**
- Dùng `get_screenshot` cho toàn bộ screen (ảnh tổng quan) và cho từng vùng có finding (ảnh chi tiết).
- Lưu ảnh vào thư mục cùng cấp với file báo cáo, đặt tên theo mã finding: `screenshot-overview.png`, `screenshot-F001.png`, `screenshot-F002.png`, ...
- Nhúng vào báo cáo bằng cú pháp markdown: `![Mô tả](screenshot-F001.png)`

Nếu design quá lớn (get_design_context bị truncate), dùng `get_metadata` để lấy child node IDs rồi fetch từng phần.

### Step 3: JTBD Analysis

Trước khi đánh giá heuristic, xác định JTBD context. Tham chiếu [jtbd-framework.md](jtbd-framework.md).

1. **Xác định Job chính**: Mô tả công việc cốt lõi mà user cần hoàn thành trên screen/flow này.
2. **Xây dựng Job Map**: Phân tách job thành các Job Steps theo chuỗi:
   `Define → Locate → Prepare → Confirm → Execute → Monitor → Resolve`
   Chỉ chọn các step relevant cho screen đang audit.
3. **Viết User-Story** cho mỗi job step:
   `"As a [persona], I want to [action trên screen] so that [desired outcome]"`
   Outcome phải là kết quả cụ thể, không phải mô tả tính năng.
4. **Xây dựng Hypothesis**:
   `"We believe [design decision] will help [persona] achieve [outcome] because [rationale]"`
   Hypothesis là baseline để đánh giá design hiện tại.
5. **Xác định Desired Outcome** cho mỗi user-story theo 3 chiều:
   - **Speed**: Nhanh đến mức nào?
   - **Accuracy**: Đúng không? Có lỗi không?
   - **Satisfaction**: Mượt mà không? Frustration không?

### Step 4: Phân tích cấu trúc design

Dùng dữ liệu từ Step 2 để kiểm tra:

1. **Layer naming**: Tên layer có semantic, dễ hiểu cho developer không?
2. **Hierarchy**: Cấu trúc parent-child hợp lý không?
3. **Auto-layout**: Có sử dụng auto-layout thay vì absolute positioning không?
4. **Component usage**: Dùng library component hay local/detached?
5. **Responsive constraints**: Có set constraints phù hợp không?

Dùng `use_figma` (read-only) nếu cần inspect chi tiết mà `get_metadata` không cung cấp đủ.

### Step 5: Heuristic Evaluation

Áp dụng bộ tiêu chuẩn kết hợp từ [heuristics.md](heuristics.md). Đánh giá theo [checklist.md](checklist.md).

8 nhóm đánh giá:

1. Visibility & Feedback
2. Consistency & Standards
3. Navigation & Information Architecture
4. Typography & Readability
5. Layout & Spacing
6. Color & Contrast
7. Interactive States
8. Error Prevention & Recovery

**Quy tắc quan trọng**: Khi ghi nhận finding, luôn đặt trong bối cảnh JTBD — finding này cản trở job step nào? Ảnh hưởng user-story nào?

**Chụp ảnh cho từng finding:**
Mỗi khi phát hiện lỗi, xác định nodeId của vùng có vấn đề (từ metadata ở Step 2), rồi gọi `get_screenshot(fileKey, nodeId)` cho đúng node đó. Lưu thành `screenshot-FXXX.png`. Ảnh này phải chụp sát vùng lỗi (node chứa vấn đề), không phải ảnh toàn màn hình — để người đọc nhìn vào biết ngay lỗi ở đâu.

### Step 6: UX Writing Analysis

Phân tích chất lượng nội dung chữ trên màn hình/luồng. Dùng dữ liệu text từ `get_design_context` và `get_metadata`.

1. **Microcopy & Labels**:
   - Label có rõ nghĩa, đúng hành động không? (VD: "Gửi" vs "Gửi yêu cầu" vs "Submit")
   - Placeholder có giúp user hiểu cần nhập gì không?
   - Tooltip / helper text có đủ và đúng ngữ cảnh không?

2. **Error Messages**:
   - Thông báo lỗi có nói rõ **lỗi gì** + **cách sửa** không?
   - Tránh thông báo chung chung ("Đã xảy ra lỗi") hoặc mã kỹ thuật ("Error 500")
   - Giọng văn có thân thiện, không đổ lỗi cho user không?

3. **Tone of Voice**:
   - Nhất quán giọng văn xuyên suốt (trang trọng / thân thiện / trung tính)?
   - Phù hợp với chân dung người dùng và bối cảnh sử dụng?
   - Có chỗ nào dùng thuật ngữ kỹ thuật mà user không hiểu?

4. **Empty State & Onboarding Copy**:
   - Trạng thái trống có hướng dẫn user làm gì tiếp không?
   - Copy có tạo động lực hành động (actionable) không?

5. **CTA Clarity**:
   - Nút hành động có nói rõ sẽ xảy ra gì khi nhấn không?
   - Phân biệt được hành động chính / phụ qua ngôn ngữ?

Mỗi vấn đề về UX Writing tạo finding riêng, gắn mã F-XXX, chụp ảnh vùng có vấn đề.

### Step 7: UX Flow Analysis

Phân tích luồng trải nghiệm tổng thể. Nếu scope là luồng nhiều màn, đánh giá toàn bộ; nếu scope là 1 màn, đánh giá luồng nội bộ trong màn đó.

1. **Số bước hoàn thành**:
   - Đếm số bước để hoàn thành job chính
   - So sánh với mức hợp lý (có bước nào thừa / gộp được không?)
   - Áp dụng nguyên tắc: càng ít bước càng tốt, nhưng mỗi bước phải rõ ràng

2. **Điểm rẽ nhánh (Decision Points)**:
   - Có bao nhiêu chỗ user phải chọn / quyết định?
   - Mỗi điểm rẽ có đủ thông tin để quyết định không?
   - Có nhánh nào dẫn đến dead-end (bế tắc) không?

3. **Drop-off Risk**:
   - Bước nào user dễ bỏ dở nhất? (form dài, chờ lâu, yêu cầu thông tin nhạy cảm)
   - Có cơ chế lưu tiến trình / quay lại không?
   - Có thoát khẩn cấp (escape hatch) ở mỗi bước không?

4. **Luồng ngược (Back Flow)**:
   - User có thể quay lại bước trước để sửa không?
   - Dữ liệu đã nhập có được giữ lại khi quay lại không?

5. **Luồng ngoại lệ**:
   - Lỗi mạng / timeout: có xử lý không?
   - Input không hợp lệ: validate inline hay chờ cuối?
   - Hết phiên đăng nhập giữa chừng: có cảnh báo / recovery không?

Tạo finding cho mỗi vấn đề về flow, gắn mã F-XXX.

### Step 8: UX Emotion Mapping

Ánh xạ cảm xúc người dùng qua từng bước trong luồng. Phân tích dựa trên thiết kế (visual cues, copy, flow structure) — không phải dữ liệu người dùng thật.

1. **Emotion Map**: Với mỗi bước công việc từ Step 3 (Job Map), đánh giá cảm xúc chủ đạo:

| Cảm xúc | Dấu hiệu trên thiết kế |
|----------|------------------------|
| **Tin tưởng (Trust)** | Logo uy tín, chính sách rõ, biểu tượng bảo mật, social proof |
| **Lo lắng (Anxiety)** | Yêu cầu thông tin nhạy cảm, hành động không thể hoàn tác, thiếu giải thích |
| **Vui / Hài lòng (Delight)** | Phản hồi thành công rõ ràng, animation vui, tiến trình hiển thị, reward |
| **Bực bội (Frustration)** | Form dài, lỗi không rõ, phải nhập lại, chờ không biết bao lâu |
| **Bối rối (Confusion)** | Quá nhiều lựa chọn, thuật ngữ lạ, không biết bước tiếp |
| **An tâm (Reassurance)** | Xác nhận rõ, tóm tắt trước khi gửi, cho phép sửa |

2. **Emotion Journey**: Tạo bảng ánh xạ cảm xúc theo bước:

| Bước | Cảm xúc dự kiến | Cảm xúc thực tế (từ thiết kế) | Chênh lệch |
|------|-----------------|-------------------------------|------------|
| [Bước] | [Mong đợi user cảm thấy gì] | [Thiết kế thực tế gợi cảm xúc gì] | [Tích cực / Tiêu cực / Phù hợp] |

3. **Điểm đỉnh & điểm đáy (Peak & Valley)**:
   - Điểm đỉnh: bước nào thiết kế tạo trải nghiệm tích cực nhất? Có đủ mạnh không?
   - Điểm đáy: bước nào gây cảm xúc tiêu cực nhất? Có cách giảm nhẹ không?
   - Áp dụng Peak-End Rule: cảm xúc ở bước cuối cùng ảnh hưởng lớn đến trải nghiệm tổng thể

4. **Trust Signals**:
   - Có đủ dấu hiệu tin cậy tại các bước nhạy cảm (thanh toán, nhập thông tin cá nhân)?
   - Thiếu trust signal nào quan trọng?

Tạo finding cho mỗi chênh lệch cảm xúc tiêu cực, gắn mã F-XXX.

### Step 9: UX Behavioral Impact Analysis

Với mỗi finding từ Step 5–8, phân tích sâu 3 tầng tác động:

1. **Behavioral Impact**: Finding thay đổi/cản trở hành vi sử dụng như thế nào?
   - Tăng cognitive load?
   - Gây confusion/mismatch expectation?
   - Tạo friction/dead-end trong flow?
   - Buộc user phải học pattern mới không cần thiết?

2. **User-Story Impact**: Finding ảnh hưởng đến user-story nào từ Step 3?
   - User không thể hoàn thành action gì?
   - User phải thực hiện thêm bước nào không cần thiết?
   - Flow bị gián đoạn ở đâu?

3. **Outcome Impact**: Ảnh hưởng cuối cùng đến desired outcome:
   - **Speed** giảm: thêm bước, thêm thời gian chờ, thêm suy nghĩ
   - **Accuracy** giảm: dễ nhầm, dễ bỏ sót, undo nhiều
   - **Satisfaction** giảm: frustration, confusion, thiếu tin tưởng

### Step 10: Design System consistency

Dùng `search_design_system` + `get_design_context` để kiểm tra:

1. **Detached styles**: Raw hex/RGB thay vì design tokens.
2. **Hardcoded values**: Spacing, border-radius, font-size không theo scale.
3. **Component drift**: Local component thay vì library instance.
4. **Missing states**: Component thiếu hover, disabled, error states.
5. **Variable usage**: Có bind variables cho color, spacing không? Multi-mode (Light/Dark) support?

### Step 11: Severity classification

Phân loại theo ma trận Frequency × Impact, tính thêm JTBD weight:

| Severity | Tiêu chí | JTBD Weight |
|----------|----------|-------------|
| **P0 (Critical)** | Impact: High, Frequency: High | Chặn hoàn toàn job chính. Developer không thể implement đúng. User không đạt outcome. |
| **P1 (Major)** | Impact: High/Medium, Frequency: Medium | Gây friction đáng kể trong job step. Có workaround nhưng giảm chất lượng outcome. |
| **P2 (Minor)** | Impact: Low/Medium, Frequency: Low | Ảnh hưởng nhỏ đến trải nghiệm. Không chặn outcome nhưng giảm satisfaction. |

Quy tắc nâng severity:
- Finding chặn job step "Execute" hoặc "Confirm" → nâng ít nhất P1.
- Finding khiến user không thể hoàn thành outcome → nâng P0 bất kể frequency.
- Finding ảnh hưởng accessibility (WCAG A/AA fail) → nâng ít nhất P1.

### Step 12: Tạo báo cáo audit

Tạo báo cáo theo template từ [report-template.md](report-template.md).

**Quy tắc output**:
- Ngôn ngữ: Tiếng Việt.
- Mỗi finding phải có: evidence (screenshot), user-story liên quan, hypothesis tham chiếu, phân tích behavioral impact, đề xuất khắc phục.
- Đề xuất khắc phục phải liên kết với việc khôi phục outcome cho user.
- Sắp xếp findings theo severity: P0 → P1 → P2.
- Tổng điểm audit /100 dựa trên: Structure (20), Visual (20), Interaction (20), JTBD Alignment (20), Design System (20).

**Xuất báo cáo:**

1. Tạo thư mục `~/Downloads/audit-report-[tên-màn-hình]-[YYYY-MM-DD]/` chứa:
   - `bao-cao.md` — báo cáo markdown
   - `bao-cao.html` — báo cáo HTML (ảnh nhúng base64, xem được ngay trên trình duyệt)
   - `screenshot-overview.png`, `screenshot-FXXX.png` — ảnh gốc

2. File HTML được tạo bằng cách:
   - Chuyển markdown sang HTML
   - Đọc từng file ảnh, encode base64, thay thế `src="screenshot-*.png"` thành `src="data:image/png;base64,..."`
   - Bọc trong template HTML có CSS đẹp, responsive, hỗ trợ in ấn

3. Ưu tiên giao file HTML khi user cần chia sẻ — 1 file duy nhất, mở trên mọi trình duyệt, không cần gửi kèm ảnh.

## Chế độ COOK NOW (tùy chọn) — checklist → bạn chọn → agent sửa trên Figma

Chế độ này dùng khi user muốn: **biến phần “đề xuất hành động” thành checklist có mô tả sửa**, sau đó user **chọn** các mục cần làm và xác nhận **COOK NOW**, rồi agent **thực hiện chỉnh sửa trên Figma**.

### Điều kiện bắt buộc

- User nói rõ muốn **COOK NOW** và đồng ý cho agent **chỉnh sửa** file Figma.
- Token/MCP phải có quyền **edit** file.
- Trước mọi thao tác chỉnh sửa bằng `use_figma`, **bắt buộc** phải nạp skill [figma-use](../figma-use/SKILL.md) (quy tắc môi trường).

### Workflow COOK NOW (thực hiện sau Step 12)

1. **Tạo “Checklist áp dụng”** trong phần khuyến nghị:
   - Mỗi mục có mã: `A-001`, `A-002`, ...
   - Gắn với finding: `F-XXX`
   - Có mô tả **cách sửa trên Figma** (node/section nào, thay đổi gì: autolayout, spacing, text style, constraints, swap component, bind variables, thêm state frame/variant, v.v.)
   - Đánh dấu loại công việc:
     - `auto` (agent có thể sửa an toàn)
     - `needs_decision` (cần user chọn phương án trước)
     - `manual` (khó tự động; chỉ hướng dẫn)

2. **Hỏi user chọn mục**:
   - Trình bày danh sách theo P0 → P1 → P2.
   - Yêu cầu user trả lời: danh sách mã cần áp dụng (VD: `A-001, A-004, A-007`) hoặc chọn “COOK NOW tất cả P0”.

3. **Trước khi sửa: tạo bản sao dự phòng (backup)**:
   - Dùng `use_figma` để duplicate frame/page mục tiêu, đặt tên `Backup - Before COOK NOW - <timestamp>`.
   - Trả về ID của backup trong log.

4. **Áp dụng theo từng batch nhỏ**:
   - Mỗi batch chỉ sửa 1–3 mục checklist để giảm rủi ro.
   - Sau mỗi batch: chụp `get_screenshot` để user kiểm tra.

5. **Báo cáo kết quả COOK NOW**:
   - Mục nào đã áp dụng, node IDs thay đổi, còn mục nào bị chặn (vì `needs_decision`/`manual`).
   - Đính kèm ảnh trước/sau (hoặc ít nhất ảnh sau).

### Quy tắc an toàn

- Không xóa nội dung cũ nếu chưa có bản sao backup.
- Không “đoán” các quyết định thiết kế (copy, IA lớn, layout chiến lược). Nếu cần, gắn `needs_decision`.
- Khi gặp parent không auto-layout hoặc layout phức tạp: ưu tiên sửa **bên cạnh** (tạo bản mới) rồi xin user duyệt trước khi thay thế.

## Conditional Workflows

### Khi audit full page

Dùng `get_metadata` cho page-level node trước, sau đó drill-down vào từng screen/frame.

### Khi audit một flow

Yêu cầu user cung cấp node IDs cho tất cả screens trong flow. Audit từng screen rồi đánh giá thêm flow continuity giữa các screen.

### Khi audit một component

Tập trung vào component variants, states, token usage. Bỏ qua flow-level JTBD, chỉ đánh giá component-level job (VD: "click button để submit form").

## Common Issues

### Design quá lớn, get_design_context bị truncate

Dùng `get_metadata` lấy tree structure trước, rồi fetch từng subtree bằng `get_design_context` với child node IDs.

### Không có design system / library

Bỏ qua Step 7 (Design System consistency). Ghi nhận trong báo cáo rằng design chưa có design system, khuyến nghị tạo.

### User không cung cấp đủ domain context cho JTBD

Áp dụng [Cơ chế hỏi bổ sung (3 câu tối thiểu)](#cơ-chế-hỏi-bổ-sung-3-câu-tối-thiểu). Nếu user không trả lời hoặc yêu cầu bỏ qua, suy luận JTBD từ nội dung visual trên screen (text, labels, buttons, flow pattern) và ghi rõ trong báo cáo rằng JTBD context được suy luận.

## Giới hạn đã biết

Mục này đặt **kỳ vọng đúng**: audit qua MCP và LLM không thay thế toàn bộ quy trình con người và công cụ chuyên dụng.

**Giới hạn kỹ thuật Figma MCP**

- Không có quyền đọc file/token không khớp scope → không audit được; agent chỉ báo lỗi và nhắc kiểm tra quyền hoặc URL.
- File **Figma Make** không khớp một số luồng đọc (`get_metadata` có giới hạn theo mô tả tool của MCP).
- Node **quá lớn**: có thể truncate output → audit có nguy cơ sót chi tiết dù đã tách `get_metadata` + gọi từng phần.
- **Một trạng thái tĩnh**: screenshot/context phản ánh thiết kế tại thời điểm đó; **hover, focus, animation, chuyển cảnh** không được “chạy” như prototype trừ khi có frame/variant riêng cho từng state.
- **Prototype** (nhánh, điều kiện, overlay) không được mô phỏng end-to-end như trên thiết bị thật; phân tích luồng dựa trên liên kết màn hình và mô tả, có thể thiếu rẽ nhánh thực tế.

**Giới hạn nội dung & phương pháp**

- **JTBD / user-story / hypothesis** nếu thiếu brief từ product: agent **suy luận** từ UI — có thể lệch nghiệp vụ; cần ghi rõ “suy luận” trong báo cáo.
- **Hành vi người dùng thật** (phỏng vấn, quan sát, analytics) không có trong Figma; phần “tác động hành vi” là **lập luận từ heuristic + thiết kế**, không phải kết quả nghiên cứu thực địa.
- **Accessibility (WCAG)**: đánh giá dựa trên quy tắc và giá trị từ design context/screenshot, **không** thay thế audit trên bản build với công cụ a11y (`axe`, VoiceOver, v.v.).

**Việc skill không cam kết**

- Không thay thế **review thiết kế bởi design lead/PM** trước bàn giao.
- Không xác minh **đúng nghiệp vụ** (chỉ kiểm tra mức phù hợp với giả định đã cho).
- Không đảm bảo **bản lập trình** trùng Figma 1:1.
- Không tự quét toàn bộ tệp Figma với hàng nghìn màn mà không giới hạn phạm vi (node/flow).
- **Copy pháp lý / thương hiệu / compliance** đầy đủ: chỉ khi user cung cấp guideline hoặc yêu cầu rõ trong prompt.

Khi một trong các giới hạn trên áp dụng, agent nên **nói thẳng** trong phần tổng quan hoặc ghi chú cuối báo cáo (ví dụ: thiếu variant trạng thái, JTBD suy luận, prototype chưa xem trên thiết bị).

## Reference Files

- Bộ tiêu chuẩn heuristic: [heuristics.md](heuristics.md)
- JTBD framework cho audit: [jtbd-framework.md](jtbd-framework.md)
- Checklist hand-off readiness: [checklist.md](checklist.md)
- Template báo cáo: [report-template.md](report-template.md)
- Hướng dẫn xuất HTML: [html-template.md](html-template.md)
