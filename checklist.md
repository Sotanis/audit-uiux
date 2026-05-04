# Checklist — Hand-off Readiness

Checklist đánh giá mức độ sẵn sàng của design trước khi chuyển cho developer. Chia theo 4 hạng mục, mỗi hạng mục chiếm 25 điểm trong tổng 100 điểm audit.

---

## A. Structure (25 điểm)

Cấu trúc file Figma, layer organization, và component architecture.

### Layer Naming

- [ ] Layer names semantic, mô tả nội dung (VD: `header/nav-bar`, `card/product-item`)
- [ ] Không có layer name mặc định (`Frame 1`, `Group 2`, `Rectangle 3`)
- [ ] Naming convention nhất quán xuyên suốt (kebab-case hoặc PascalCase, không trộn)
- [ ] Layer order phản ánh visual order (top-to-bottom = left-to-right hoặc top-to-bottom trên screen)

### Auto-Layout

- [ ] Tất cả container dùng auto-layout thay vì absolute positioning
- [ ] Padding và gap values theo spacing scale (4, 8, 12, 16, 24, 32, 48)
- [ ] Fill container / hug contents được set phù hợp
- [ ] Min/max width constraints được định nghĩa cho responsive elements

### Component Usage

- [ ] Dùng library components thay vì local/detached copies
- [ ] Không có detached instances (detach = mất sync với library)
- [ ] Component overrides hợp lệ (text, icon, visibility)
- [ ] Nested components structured đúng (slot pattern nếu applicable)

### Responsive

- [ ] Constraints được set cho mọi element trong frame
- [ ] Breakpoint variants được cung cấp (nếu design responsive)
- [ ] Content reflow logic rõ ràng giữa breakpoints
- [ ] Text truncation / overflow behavior được định nghĩa

---

## B. Visual (25 điểm)

Token usage, visual consistency, và design system adherence.

### Color Tokens

- [ ] Tất cả colors dùng design tokens / variables, không hardcode hex
- [ ] Semantic color usage đúng (primary, secondary, error, warning, success)
- [ ] Text-on-background đạt WCAG AA contrast ratio (4.5:1 normal, 3:1 large)
- [ ] Dark mode support (nếu required): tất cả colors có variable mode tương ứng

### Typography Scale

- [ ] Dùng text styles từ design system, không custom
- [ ] Type hierarchy rõ ràng (H1 > H2 > H3 > body > caption)
- [ ] Body text >= 14px (mobile) / 16px (web)
- [ ] Line-height 1.4–1.6 cho body text
- [ ] Max 2–3 font families

### Spacing Consistency

- [ ] Spacing values tuân theo scale (base 4px hoặc 8px)
- [ ] Không có "magic number" spacing (13px, 17px, 22px)
- [ ] Padding nhất quán trong cùng loại component
- [ ] Margin/gap nhất quán giữa cùng loại section

### Icon & Asset

- [ ] Icons cùng style (outline, filled, hoặc duotone — không trộn)
- [ ] Icon size nhất quán (16, 20, 24, 32)
- [ ] Icons dùng component instances, không phải flattened vectors
- [ ] Image/illustration có placeholder hoặc aspect ratio constraints

---

## C. Interaction (25 điểm)

States, transitions, micro-interactions, và edge cases.

### Component States

- [ ] Button: default, hover, pressed, disabled, loading
- [ ] Input: default, focused, filled, error, disabled
- [ ] Link: default, hover, visited (nếu applicable)
- [ ] Toggle/Switch: on, off, disabled
- [ ] Checkbox/Radio: unchecked, checked, indeterminate, disabled

### Feedback States

- [ ] Loading states cho mọi async operation (skeleton, spinner, progress)
- [ ] Success feedback sau mỗi action quan trọng (toast, inline, redirect)
- [ ] Error states với message cụ thể và actionable
- [ ] Empty states với guidance (icon + message + CTA)

### Edge Cases

- [ ] Long text behavior: truncation, ellipsis, hoặc wrap
- [ ] Empty/zero data state
- [ ] First-time/onboarding state
- [ ] Offline/connectivity lost state (nếu applicable)
- [ ] Permission denied state
- [ ] Maximum data state (pagination, infinite scroll)

### Flow Completeness

- [ ] Entry points rõ ràng (user đến screen này từ đâu?)
- [ ] Exit points rõ ràng (user đi đâu sau screen này?)
- [ ] Error recovery path tồn tại (user gặp lỗi thì quay lại bằng cách nào?)
- [ ] Back/cancel behavior được định nghĩa
- [ ] Destructive actions có confirmation

---

## D. JTBD Alignment (25 điểm)

Design có phục vụ đúng job mà user cần hoàn thành không?

### Job Clarity

- [ ] Screen/flow có job chính rõ ràng (không "làm quá nhiều việc" trên 1 screen)
- [ ] Primary action (CTA) phản ánh đúng job step chính
- [ ] Secondary actions không lấn át primary action
- [ ] Content hierarchy ưu tiên thông tin phục vụ job chính

### Job Step Coverage

- [ ] Mỗi job step relevant có UI element tương ứng hỗ trợ
- [ ] Không có "gap" trong flow — user không bị bỏ rơi giữa chừng
- [ ] Không có "dead-end" — mọi state đều có path tiếp theo
- [ ] Transition giữa job steps mượt mà (không redirect bất ngờ, không mất context)

### Outcome Support

- [ ] **Speed**: Flow tối ưu số bước (không có bước thừa)
- [ ] **Speed**: Progressive disclosure — chỉ hiện thông tin cần thiết cho job step hiện tại
- [ ] **Accuracy**: Validation và constraints ngăn user làm sai
- [ ] **Accuracy**: Preview/confirm trước destructive hoặc irreversible actions
- [ ] **Satisfaction**: Feedback kịp thời sau mỗi action
- [ ] **Satisfaction**: Visual polish nhất quán, không có jarring inconsistencies

### User-Story Traceability

- [ ] Mỗi screen có ít nhất 1 user-story có thể articulate rõ ràng
- [ ] Outcome trong user-story đo lường được (dù chỉ qualitative)
- [ ] Hypothesis cho design decision chính có thể xây dựng và đánh giá

---

## Cách tính điểm

Mỗi hạng mục (A, B, C, D) tính trên 25 điểm:

| Mức | Điểm | Tiêu chí |
|-----|-------|----------|
| Excellent | 23–25 | Đạt >= 90% checklist items |
| Good | 18–22 | Đạt 70–89% checklist items |
| Fair | 12–17 | Đạt 50–69% checklist items |
| Poor | 0–11 | Đạt < 50% checklist items |

**Tổng điểm = A + B + C + D (max 100)**

| Tổng | Kết luận |
|------|----------|
| 85–100 | Ready for hand-off |
| 70–84 | Hand-off với minor fixes |
| 50–69 | Cần rework trước hand-off |
| < 50 | Chưa sẵn sàng, cần redesign đáng kể |
