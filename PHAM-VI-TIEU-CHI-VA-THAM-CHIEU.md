# Phạm vi Agent, Căn cứ Tham chiếu và Tiêu chí Đánh giá

Tài liệu mô tả **phạm vi** skill **Audit UI/UX**, **nguồn thông tin** agent dùng, và **tiêu chí** chấm điểm / phân loại. Dùng chung cho Cursor và Claude.

**Workflow chi tiết từng bước:** [SKILL.md](SKILL.md)

---

## 1. Phạm vi Agent có thể thực hiện

### 1.1 Trong phạm vi (agent thiết kế để làm)

| Hạng mục | Mô tả |
|----------|--------|
| **Đọc thiết kế Figma qua MCP** | Ảnh chụp khung, cây layer (`metadata`), ngữ cảnh thiết kế (`design context`: token, style, component), tìm trong thư viện (`search_design_system`). |
| **Xác định phạm vi audit** | Một màn hình, một luồng nhiều URL, hoặc một component / khung cụ thể (theo `node-id`). |
| **Ánh xạ JTBD** | Công việc chính, bản đồ bước công việc, câu chuyện người dùng, giả thuyết thiết kế, kết quả mong đợi (tốc độ / chính xác / hài lòng) — có thể **suy luận** từ giao diện nếu thiếu brief. |
| **Đánh giá theo nguyên tắc** | Tám nhóm tiêu chí (mục 3.1), bám [heuristics.md](heuristics.md). |
| **Đánh giá sẵn sàng bàn giao** | Bốn trụ cột × 25 điểm (mục 3.2), bám [checklist.md](checklist.md). |
| **Phân tích tác động hành vi** | Với mỗi phát hiện: ảnh hưởng hành vi → ảnh hưởng câu chuyện người dùng → ảnh hưởng kết quả. |
| **Phân loại mức độ** | P0 / P1 / P2 kết hợp tần suất × tác động và trọng số JTBD (mục 3.3). |
| **Xuất báo cáo** | Tiếng Việt, theo [report-template.md](report-template.md), có **bảng thuật ngữ** cuối báo cáo. |
| **Hỏi bổ sung có kiểm soát** | Tối đa **3 câu** (chân dung, công việc, tiêu chí thành công) khi thiếu — xem [SKILL.md — Cơ chế hỏi bổ sung](SKILL.md). |

### 1.2 Ngoài phạm vi (agent không cam kết hoặc chỉ hỗ trợ một phần)

| Hạng mục | Giải thích ngắn |
|----------|------------------|
| **Thay review người** | Không thay design lead / PM / dev sign-off. |
| **Xác minh đúng nghiệp vụ tuyệt đối** | Chỉ kiểm tra **phù hợp** với giả định và brief đã cho. |
| **Usability test thật** | Không có người dùng thật, không có dữ liệu phân tích sản phẩm trừ khi bạn đưa vào prompt. |
| **Prototype giống thiết bị** | Không chạy prototype đầy đủ như trên máy thật; luồng nhánh là **suy luận** từ màn hình và mô tả. |
| **Trạng thái động** | Hover, focus, animation: chỉ đánh giá được nếu có **khung/variant** riêng cho từng trạng thái. |
| **Tiếp cận (accessibility) trên bản build** | Đánh giá WCAG dựa trên thiết kế và giá trị hiển thị; **không** thay công cụ a11y trên code (axe, VoiceOver, …). |
| **Tuân thủ pháp lý / thương hiệu đầy đủ** | Chỉ khi bạn cung cấp quy tắc hoặc yêu cầu rõ trong prompt. |
| **Đảm bảo code giống Figma 1:1** | Thuộc triển khai và QC sau bàn giao. |
| **Quét cả thư viện Figma không giới hạn** | Cần giới hạn `node` / luồng; không quét hết file khổng lồ trong một lượt hợp lý. |

Chi tiết giới hạn kỹ thuật và phương pháp: [SKILL.md — Giới hạn đã biết](SKILL.md).

---

## 2. Căn cứ và thông tin tham chiếu

### 2.1 Nguồn từ Figma (qua MCP)

| Nguồn | Công cụ MCP (tên thường dùng) | Dùng để |
|--------|-------------------------------|---------|
| Ảnh khung | `get_screenshot` | Bằng chứng trực quan, so sánh mong đợi / thực tế |
| Cây layer | `get_metadata` | Tên layer, cấu trúc, kích thước, autolayout |
| Ngữ cảnh triển khai | `get_design_context` | Token, style, component, gợi ý token |
| Biến (variable) | `get_variable_defs` (khi cần) | Kiểm tra gán biến, chế độ sáng/tối |
| Thư viện | `get_libraries`, `search_design_system` | Khớp component/token với design system |
| Kiểm tra sâu trong file | `use_figma` (chỉ đọc) | Thuộc tính node khi metadata không đủ |

Công cụ cụ thể phụ thuộc máy chủ MCP bạn cài; danh mục đầy đủ theo tài liệu Figma MCP phiên bản hiện tại.

### 2.2 Nguồn từ folder skill (tài liệu đính kèm)

| File | Vai trò |
|------|---------|
| [SKILL.md](SKILL.md) | Quy trình 9 bước, hỏi bổ sung 3 câu, ranh giới skill, giới hạn đã biết |
| [heuristics.md](heuristics.md) | Tám nhóm tiêu chí đánh giá + ví dụ đạt / không đạt |
| [jtbd-framework.md](jtbd-framework.md) | Bản đồ công việc, mẫu câu chuyện người dùng, giả thuyết, ánh xạ bước → loại vấn đề UX |
| [checklist.md](checklist.md) | Checklist bàn giao: Cấu trúc / Hình ảnh / Tương tác / Phù hợp JTBD + thang điểm 25×4 |
| [report-template.md](report-template.md) | Khung báo cáo tiếng Việt, bảng thuật ngữ |

### 2.3 Nguồn từ người dùng (prompt)

- Đường dẫn Figma có `node-id`
- Chân dung, công việc cần làm, tiêu chí thành công (hoặc trả lời sau 3 câu hỏi)
- Nền tảng (web / iOS / Android), mức tiếp cận mong muốn (nếu có)
- Phạm vi: một màn / luồng / component; có thể yêu cầu chỉ đánh giá một số nhóm tiêu chí

---

## 3. Tiêu chí đánh giá

### 3.1 Tám nhóm heuristic (đánh giá định tính + bằng chứng)

Tham chiếu đầy đủ: [heuristics.md](heuristics.md).

| # | Nhóm (tên gốc trong tài liệu kỹ thuật) | Nội dung kiểm tra (tóm tắt) |
|---|----------------------------------------|-----------------------------|
| 1 | Hiển thị & phản hồi | Trạng thái hệ thống, tải, thành công/lỗi, trống |
| 2 | Nhất quán & chuẩn | Thuật ngữ, pattern, màu ngữ nghĩa, khoảng cách |
| 3 | Điều hướng & kiến trúc thông tin | Vị trí, lối thoát, tìm kiếm/lọc, không đường cụt |
| 4 | Chữ & khả năng đọc | Thang chữ, độ tương phản, độ dài dòng |
| 5 | Bố cục & khoảng cách | Lưới, scale khoảng cách, vùng chạm |
| 6 | Màu & độ tương phản | Token, WCAG, không chỉ dựa vào màu |
| 7 | Trạng thái tương tác | Mặc định, hover, nhấn, focus, vô hiệu, lỗi, đang tải |
| 8 | Ngăn lỗi & phục hồi | Xác nhận hành động nguy hiểm, thông báo lỗi rõ, hoàn tác |

### 3.2 Điểm tổng /100 (bốn trụ cột × 25 điểm)

Tham chiếu: [checklist.md](checklist.md).

| Trụ cột | Tiếng Việt trong báo cáo | Nội dung chính |
|---------|-------------------------|----------------|
| A | Cấu trúc | Đặt tên layer, autolayout, component, responsive |
| B | Hình ảnh | Token màu, thang chữ, khoảng cách, icon |
| C | Tương tác | Trạng thái, phản hồi, trường hợp biên, tính đầy đủ luồng |
| D | Phù hợp mục đích người dùng | Rõ job, đủ bước, hỗ trợ kết quả, không đường cụt |

**Quy ước mức theo tỷ lệ checklist (tham chiếu checklist):** Xuất sắc / Tốt / Trung bình / Yếu tương ứng ~90%+ / 70–89% / 50–69% / <50% mục đạt trong từng trụ.

**Kết luận theo điểm tổng:**

| Điểm | Ý nghĩa |
|------|---------|
| 85–100 | Sẵn sàng bàn giao |
| 70–84 | Bàn giao được, nên sửa các mục quan trọng |
| 50–69 | Cần chỉnh sửa đáng kể trước bàn giao |
| < 50 | Chưa đạt — cần làm lại phần lớn |

### 3.3 Mức độ nghiêm trọng (P0 / P1 / P2)

| Mức | Điều kiện (tóm tắt) | Trọng số JTBD |
|-----|---------------------|----------------|
| **P0** | Chặn hoàn thành công việc chính; người dùng không đạt kết quả mong đợi; hoặc không thể triển khai đúng nếu giữ nguyên thiết kế | Ảnh hưởng trực tiếp tới bước **Thực hiện** / **Xác nhận** hoặc chặn outcome |
| **P1** | Ma sát lớn; có cách vòng nhưng giảm chất lượng kết quả | Giảm tốc độ / chính xác / hài lòng rõ rệt |
| **P2** | Không chặn outcome; chủ yếu làm giảm độ mượt hoặc độ nhất quán | Ảnh hưởng nhẹ tới hài lòng hoặc nhất quán |

**Quy tắc nâng mức (ôn lại):** Chặn bước Thực hiện/Xác nhận → ít nhất P1. Không đạt outcome → có thể P0. Vi phạm WCAG AA rõ (theo đánh giá trên thiết kế) → ít nhất P1.

### 3.4 Ma trận tần suất × tác động

Dùng kết hợp với P0–P2: lỗi **hay gặp + hậu quả lớn** → ưu tiên cao nhất trong báo cáo và khuyến nghị.

---

## 4. Liên kết nhanh

| Nội dung | File |
|----------|------|
| Giới thiệu agent | [GIOI-THIEU.md](GIOI-THIEU.md) |
| Cài đặt & sử dụng | [README.md](README.md) |
| Workflow agent | [SKILL.md](SKILL.md) |
