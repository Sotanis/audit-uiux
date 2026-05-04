# Gate Rules — Quy tắc đánh giá bàn giao thiết kế

File này định nghĩa **ngưỡng cứng**, **công thức tính %**, và **logic quyết định** để agent ra status: READY / READY WITH NOTES / NEEDS REWORK / BLOCKED.

Đọc đầu mỗi review (P0). Tính ở P3.

**Nguyên tắc**: thiết kế đạt **≥80% trên cả 4 trục** mới được bàn giao. Quyết định cuối = `MIN(% UI, % UX, % Nghiệp vụ, % Use-case)`.

---

## Kiến trúc 3 tầng gate

```
┌─────────────────────────────────────────────────────┐
│ Tầng 1: HARD GATE     — vi phạm = BLOCKED ngay      │
├─────────────────────────────────────────────────────┤
│ Tầng 2: SCORE GATE    — MIN(4 trục) ≥ 80% mới READY │
├─────────────────────────────────────────────────────┤
│ Tầng 3: SEVERITY GATE — bất kỳ P0 mở = BLOCKED      │
└─────────────────────────────────────────────────────┘

Quyết định cuối = MIN(Tầng 1, Tầng 2, Tầng 3)
```

---

## Tầng 1 — HARD GATE

Vi phạm **bất kỳ 1 mục** dưới đây → **BLOCKED**, dừng compute tầng 2/3, báo cáo lý do và hành động sửa bắt buộc.

| ID | Mục | Ngưỡng | Ghi chú |
|----|-----|--------|---------|
| H1 | Contrast WCAG AA | ≥95% text element đạt 4.5:1 (normal) hoặc 3:1 (large ≥18pt hoặc 14pt bold) | Đo trên text-on-background thực tế |
| H2 | Tap target | ≥95% interactive element đạt 24×24 CSS px (WCAG 2.5.8). Mobile khuyến nghị 44×44pt | Áp cho button, link, icon button, checkbox, radio |
| H3 | Primary CTA | Tồn tại + visible above fold + duy nhất / màn | Multiple primary CTA → user phân vân, fail |
| H4 | Empty state | Mọi list / data view có empty state | Không để blank screen |
| H5 | Error state | Mọi form input + mọi async action có error state | Message cụ thể, không "Đã xảy ra lỗi" |
| H6 | Loading state | Mọi async action có loading indicator | Skeleton / spinner / progress |
| H7 | Destructive action | Có confirmation dialog HOẶC undo affordance | Áp cho delete, send, submit, charge, transfer |
| H8 | Layer naming | ≤20% layer mang tên mặc định (`Frame N`, `Group N`, `Rectangle N`) | AI codegen sẽ đẻ tên rác nếu vi phạm |
| H9 | Detached components | ≤5% instance bị detach | Detach = mất sync với library |
| H10 | Hardcoded color | ≤10% color không bind variable / token | Hardcode = AI codegen viết hex rác, không reuse |
| H11 | Auto-layout | ≥80% container dùng auto-layout (không absolute positioning) | Absolute → AI codegen sinh `position: absolute` rác |

### Tính toán Hard Gate

Mỗi mục H1–H11:
- **Đếm tổng** element thuộc loại đó (ví dụ H1: tổng text node)
- **Đếm vi phạm** (ví dụ H1: text node có contrast < 4.5:1)
- **Tỉ lệ đạt** = (tổng - vi phạm) / tổng × 100%
- **So với ngưỡng** ở cột "Ngưỡng"

Nếu >1 mục fail → liệt kê tất cả trong báo cáo, không chỉ cái đầu.

### Output khi BLOCKED

```
═══════════════════════════════════
HAND-OFF DECISION: ⛔ BLOCKED
═══════════════════════════════════
Tầng 1 (Hard Gate):    ❌ FAIL
  - H1 Contrast: 78% (cần ≥95%)
    → 12 text node fail: [list node IDs]
  - H2 Tap target: 91% (cần ≥95%)
    → 8 icon button <44×44

Tầng 2 (Score):        N/A (chưa qua tầng 1)
Tầng 3 (Severity):     N/A

Hành động bắt buộc:
  1. Sửa 12 text node contrast <4.5:1 (xem screenshot-Hard-H1.png)
  2. Tăng size 8 icon button lên ≥44×44 (Material §accessibility)
═══════════════════════════════════
```

---

## Tầng 2 — SCORE GATE (4 trục × 100%)

Chỉ tính khi qua tầng 1. Mỗi trục có checklist riêng, mỗi item check pass/fail (1/0). **% trục = (số item pass / tổng item) × 100**.

### Trục UI (Craft) — 12 items

| # | Item | Pass khi |
|---|------|----------|
| UI-01 | Token color | ≥90% color element bind variable |
| UI-02 | Token typography | ≥90% text dùng text style từ design system |
| UI-03 | Spacing scale | 100% spacing ∈ {4, 8, 12, 16, 24, 32, 48} (hoặc 8/16/24…) |
| UI-04 | Type hierarchy | ≤6 distinct font size, hierarchy rõ (H1>H2>H3>body>caption) |
| UI-05 | Border radius | ≤3 distinct values |
| UI-06 | Color palette | ≤7 distinct color trong palette chính |
| UI-07 | Icon consistency | Cùng style (outline / filled / duotone), size ∈ {16, 20, 24, 32} |
| UI-08 | Auto-layout | ≥80% container dùng auto-layout |
| UI-09 | Constraint set | 100% element trong frame có constraint |
| UI-10 | State coverage button | Button có ≥4/5 state (default/hover/pressed/focused/disabled) |
| UI-11 | State coverage input | Input có ≥4/5 state (default/focused/filled/error/disabled) |
| UI-12 | Zoom 200% test | Text vẫn đọc được, không cắt nội dung (WCAG 1.4.4) |

**Ngưỡng pass trục UI: ≥80% (≥10/12 item)**

### Trục UX (Usability) — 12 items

| # | Item | Pass khi |
|---|------|----------|
| UX-01 | First-glance test | Trả lời được 4/4 câu trong 5 giây (job, primary CTA, info chính, escape) |
| UX-02 | Cognitive load | ≤12 (interactive_count + info_groups×0.5 + decisions×2) trên 1 viewport |
| UX-03 | Hierarchy attention | Primary CTA top-3 attention; thông tin quan trọng top-5 |
| UX-04 | Feedback timing | <1s instant / <3s loading / >3s progress với ETA |
| UX-05 | Loading state đầy đủ | 100% async action có loading |
| UX-06 | Empty state guidance | 100% empty state có icon + message + CTA |
| UX-07 | Error message chất lượng | 100% error message nói rõ vấn đề + cách sửa |
| UX-08 | Dead-end | 0 dead-end (mọi state có path tiếp) |
| UX-09 | Escape hatch | Mọi luồng có nút thoát/hủy/quay lại |
| UX-10 | Confirmation destructive | 100% destructive action có confirm hoặc undo |
| UX-11 | Consistency platform | Tuân HIG (iOS) / Material (Android, Web) cho navigation pattern |
| UX-12 | Recognition over recall | Quan trọng visible, không bắt user nhớ từ màn trước |

**Ngưỡng pass trục UX: ≥80% (≥10/12 item)**

### Trục Nghiệp vụ (Business Logic) — 10 items

| # | Item | Pass khi |
|---|------|----------|
| NV-01 | Happy path | 100% bước job map có UI tương ứng |
| NV-02 | Empty data state | Có UI cho zero data |
| NV-03 | Network error / timeout | Có UI + retry option |
| NV-04 | Permission denied | Có UI giải thích + hành động tiếp theo |
| NV-05 | Session expired | Có UI cảnh báo + recovery |
| NV-06 | Invalid input | Inline validation + format hint trước khi submit |
| NV-07 | Role-based view | Nếu nhiều role: mỗi role có view phù hợp; disabled vs hidden chọn đúng |
| NV-08 | Data scale | Design xử lý zero / 1 / few / many / max (truncation, pagination) |
| NV-09 | Trust signal | Bước nhạy cảm (payment, PII) có trust signal (lock icon, security note, policy link) |
| NV-10 | Confirmation summary | Action không reversible kèm summary (cái gì, ai, bao nhiêu, khi nào) |

**Ngưỡng pass trục Nghiệp vụ: ≥80% (≥8/10 item)**

### Trục Use-case (JTBD Coverage) — 8 items

| # | Item | Pass khi |
|---|------|----------|
| UC-01 | JTBD rõ ràng | Articulate được "Khi X, tôi muốn Y, để Z" |
| UC-02 | US ≥2, ≤5 | Số US hợp lý cho scope màn |
| UC-03 | AC testable | Mỗi US có 3–5 AC testable |
| UC-04 | US coverage | ≥80% US có UI element phục vụ (✅ Tốt) |
| UC-05 | Outcome metrics | Mỗi US có speed/accuracy/satisfaction rõ |
| UC-06 | Hypothesis có bằng chứng | Mọi 🔴/🟡 hypothesis trích Baymard/WCAG/Nielsen/cognitive law/data |
| UC-07 | Không US orphan | Không có US nào không có UI |
| UC-08 | Không UI orphan | Không có UI element nào không phục vụ US (over-design) |

**Ngưỡng pass trục Use-case: ≥80% (≥7/8 item)**

### Mapping điểm trục → mức

| % trục | Mức |
|--------|-----|
| 90–100 | Xuất sắc |
| 80–89  | Tốt (đạt ngưỡng pass) |
| 65–79  | Trung bình (chưa đạt) |
| < 65   | Yếu |

---

## Tầng 3 — SEVERITY GATE

| Tình trạng P0/P1 | Quyết định |
|------------------|-----------|
| Có ≥1 P0 mở (chưa fix) | BLOCKED, bất kể điểm |
| 0 P0, ≤3 P1 | OK (chuyển tầng 2 quyết định) |
| 0 P0, 4–7 P1 | Hạ 1 mức (READY → READY WITH NOTES) |
| 0 P0, >7 P1 | NEEDS REWORK, bất kể điểm |

### Quy tắc nâng severity

Một finding mặc định severity từ ma trận tần suất × tác động, nhưng **bắt buộc nâng** trong các trường hợp:

| Điều kiện | Nâng tới |
|-----------|---------|
| Vi phạm hard gate H1–H7 | P0 |
| Chặn job step Execute hoặc Confirm | ≥ P1 |
| Khiến user không đạt outcome chính | P0 |
| Vi phạm WCAG AA rõ ràng | ≥ P1 |
| Có thể dẫn đến mất tiền / sai data quan trọng | P0 |
| US-XX bị mark ❌ Chưa đáp ứng | ≥ P1 |
| Finding chỉ ảnh hưởng polish / consistency nhẹ | P2 |

---

## Logic quyết định cuối

```
1. Compute Tầng 1 (Hard Gate)
   - Nếu FAIL bất kỳ H1–H11 → BLOCKED. STOP.

2. Compute Tầng 2 (Score Gate)
   - Tính % cho 4 trục: UI, UX, Nghiệp vụ, Use-case
   - MIN_SCORE = MIN(4 trục)
   - Nếu MIN_SCORE ≥ 90 và mọi trục ≥ 80 → tier_2 = READY
   - Nếu MIN_SCORE 80–89 và mọi trục ≥ 80 → tier_2 = READY WITH NOTES
   - Nếu bất kỳ trục < 80 → tier_2 = NEEDS REWORK

3. Compute Tầng 3 (Severity Gate)
   - Nếu P0 mở > 0 → tier_3 = BLOCKED
   - Nếu P1 > 7 → tier_3 = NEEDS REWORK
   - Nếu P1 4–7 → tier_3 = downgrade 1 step
   - Else → tier_3 = pass-through

4. Quyết định cuối = MIN(tier_1, tier_2, tier_3)
   Theo thứ tự: BLOCKED < NEEDS REWORK < READY WITH NOTES < READY
```

---

## Output — Gate Decision Box

Mọi báo cáo BẮT ĐẦU bằng box này, ngay sau title:

```
═══════════════════════════════════════════════
HAND-OFF DECISION: [READY / READY WITH NOTES / NEEDS REWORK / BLOCKED]
═══════════════════════════════════════════════

📊 Score 4 trục:
  UI         ████████░░  82% [Tốt]
  UX         █████████░  91% [Xuất sắc]
  Nghiệp vụ  ██████░░░░  64% [Trung bình] ← KÉO TỔNG XUỐNG
  Use-case   ████████░░  85% [Tốt]
  ─────────────────────────────────
  MIN        64% — chưa đạt ngưỡng 80%

🚦 3 tầng gate:
  Tầng 1 Hard Gate:  ✅ PASS
  Tầng 2 Score Gate: ❌ NEEDS REWORK (Nghiệp vụ 64% < 80%)
  Tầng 3 Severity:   ⚠️ 1 P0 mở, 5 P1 mở

⚠️ Hành động bắt buộc trước bàn giao:
  1. Sửa P0 [F-003]: thiếu confirmation dialog ở action xóa
  2. Bổ sung 4 unhappy path ở trục Nghiệp vụ
     (network error, session expired, permission denied, invalid input)
  3. Sau khi fix → audit lại để check lại Score Gate

📈 Khoảng cách đến READY:
  - Trục Nghiệp vụ cần +16 điểm (2 item nữa)
  - Đóng 1 P0 + đóng ít nhất 2 P1
═══════════════════════════════════════════════
```

---

## Workflow tính trong P3

```
1. Đọc scratchpad full, đếm finding theo severity
2. Với mỗi trục UI / UX / NV / UC:
   a. Mở checklist tương ứng (12/12/10/8 item)
   b. Đánh dấu pass/fail mỗi item dựa trên evidence từ Phase 2
   c. Tính % = (pass / total) × 100
3. Compute Tầng 1: kiểm 11 hard gate
4. Compute Tầng 2: MIN(4 trục) + check ngưỡng riêng
5. Compute Tầng 3: đếm P0 + P1
6. Quyết định cuối = MIN
7. Render Gate Decision Box
8. Chèn lên đầu báo cáo, trước Framing
```

---

## Quy tắc tinh thần

- **MIN, không AVERAGE**: 1 trục yếu kéo cả thiết kế. Tránh "trục mạnh đỡ trục yếu".
- **80% không phải target lười**: 100% là trạng thái lý tưởng không tồn tại trên design tĩnh. 80% nghĩa là "đủ chín để dev không phải hỏi lại quá 2 lần / màn".
- **Hard gate không thương lượng**: contrast, tap target, error/empty state thiếu — không có chỗ cho "ship trước, fix sau" trong những thứ này.
- **Severity gate chống "điểm cao gian lận"**: 1 P0 chí mạng vô hiệu mọi điểm số. Tránh tình huống "82% tổng nhưng không gửi được tiền".
- **Khoảng cách đến READY** phải hiển thị trong Gate Decision Box: designer biết cần +bao nhiêu điểm ở trục nào → action rõ ràng.
