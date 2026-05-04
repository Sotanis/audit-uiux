# Audit UI/UX Agent — Giới thiệu

## Agent này là gì?

Audit UI/UX là một AI agent chuyên **review thiết kế Figma** trước khi bàn giao cho developer. Agent đọc trực tiếp file Figma, phân tích theo các bộ tiêu chuẩn thiết kế quốc tế, và trả về báo cáo tiếng Việt có bằng chứng, điểm số, và đề xuất sửa cụ thể.

Agent hoạt động trên **Cursor** và **Claude Code**.

---

## Agent làm được gì?

### 1. Đọc và phân tích thiết kế Figma tự động

- Chụp ảnh màn hình, đọc cấu trúc layer, lấy thông tin token/style/component — tất cả qua Figma MCP, không cần xuất file hay chụp màn hình thủ công.
- Hỗ trợ nhiều phạm vi: một màn hình, một luồng nhiều màn, hoặc một component cụ thể.

### 2. Đánh giá theo bộ tiêu chuẩn thiết kế quốc tế

Kết hợp **3 bộ tiêu chuẩn** thành 8 nhóm đánh giá:

| Nhóm | Kiểm tra gì |
|------|-------------|
| Hiển thị & phản hồi | Trạng thái tải, thành công, lỗi, rỗng |
| Nhất quán & chuẩn | Thuật ngữ, pattern, màu ngữ nghĩa |
| Điều hướng & kiến trúc thông tin | Vị trí, lối thoát, không đường cụt |
| Chữ & khả năng đọc | Thang chữ, tương phản, độ dài dòng |
| Bố cục & khoảng cách | Lưới, scale spacing, vùng chạm |
| Màu & độ tương phản | Design token, WCAG, không chỉ dựa vào màu |
| Trạng thái tương tác | Default, hover, pressed, focus, disabled, error, loading |
| Ngăn lỗi & phục hồi | Xác nhận hành động nguy hiểm, thông báo lỗi rõ ràng |

Nguồn tham chiếu: **Apple HIG**, **Material Design**, **10 nguyên tắc Nielsen**.

### 3. Phân tích theo JTBD (Jobs to Be Done)

Không chỉ kiểm tra "đẹp hay xấu" — agent phân tích thiết kế có **giúp người dùng hoàn thành công việc** hay không:

- Xác định công việc chính (job) mà người dùng cần làm trên màn hình
- Viết câu chuyện người dùng (user-story) cho từng bước
- Đặt giả thuyết (hypothesis) cho từng quyết định thiết kế
- Đánh giá kết quả mong đợi theo 3 chiều: **Nhanh**, **Đúng**, **Hài lòng**

### 4. Phân tích tác động hành vi

Với mỗi lỗi tìm được, agent phân tích sâu 3 tầng:

- **Hành vi bị ảnh hưởng**: Tăng gánh nặng suy nghĩ? Gây nhầm lẫn? Tạo bế tắc?
- **Câu chuyện người dùng bị ảnh hưởng**: Người dùng không thể hoàn thành bước nào?
- **Kết quả cuối cùng**: Chậm hơn, sai nhiều hơn, hay bực bội hơn?

### 5. Phân loại mức độ nghiêm trọng

| Mức | Ý nghĩa | Ví dụ |
|-----|---------|-------|
| **P0** | Chặn hoàn toàn — không thể bàn giao | Nút chính bị ẩn, luồng bế tắc, thiếu trạng thái lỗi |
| **P1** | Gây khó khăn lớn — cần sửa ngay | Chữ quá nhỏ, tương phản không đạt, spacing lộn xộn |
| **P2** | Ảnh hưởng nhẹ — nên sửa khi có thể | Icon chưa nhất quán, padding lệch nhẹ |

### 6. Kiểm tra nhất quán design system

- Phát hiện màu/font/spacing viết tay thay vì dùng design token
- Phát hiện component bị tách khỏi thư viện (detached)
- Kiểm tra thiếu trạng thái (hover, disabled, error...)
- Kiểm tra biến (variable) và hỗ trợ chế độ sáng/tối

### 7. Sửa trực tiếp trên Figma (chế độ COOK NOW)

Sau khi nhận báo cáo, nếu muốn agent sửa luôn:

1. Agent tạo checklist các việc cần sửa
2. Bạn chọn mục muốn sửa
3. Gõ **COOK NOW** để xác nhận
4. Agent tạo bản sao dự phòng rồi sửa theo từng nhóm nhỏ, chụp ảnh trước/sau

---

## Hỗ trợ những công việc gì?

| Công việc | Cách dùng |
|-----------|-----------|
| **Review trước bàn giao** | Gửi URL Figma → nhận báo cáo chất lượng + checklist sửa |
| **Kiểm tra luồng người dùng** | Gửi nhiều URL theo thứ tự → agent đánh giá cả luồng |
| **Kiểm tra component** | Gửi URL node component → agent kiểm tra variant, state, token |
| **Đánh giá design system** | Agent kiểm tra token, detached style, hardcoded value |
| **Chuẩn bị tài liệu bàn giao** | Báo cáo có sẵn bằng chứng, đề xuất, điểm số — dùng ngay |
| **Sửa nhanh trên Figma** | Chọn mục cần sửa → COOK NOW → agent sửa, bạn kiểm tra |

---

## Lợi ích

### Tiết kiệm thời gian

- Review thủ công một màn hình: **30–60 phút**
- Với agent: **5–10 phút** (bao gồm đọc báo cáo)
- Không cần chụp màn hình, mở tab so sánh, tra cứu tiêu chuẩn

### Nhất quán chất lượng

- Cùng một bộ tiêu chuẩn cho mọi màn hình, mọi lần review
- Không bỏ sót vì mệt hay vội — agent luôn kiểm tra đủ 8 nhóm
- Phân loại P0/P1/P2 rõ ràng, không tranh cãi "nghiêm trọng hay không"

### Báo cáo có bằng chứng

- Mỗi phát hiện đi kèm ảnh chụp, vị trí node, và đề xuất sửa cụ thể
- Developer đọc báo cáo biết ngay sửa ở đâu, sửa gì
- Giảm qua lại hỏi đáp giữa designer và developer

### Gắn với mục đích người dùng

- Không chỉ kiểm tra "đúng chuẩn" mà kiểm tra "có giúp người dùng hoàn thành việc không"
- Mỗi lỗi đều giải thích ảnh hưởng đến ai, ảnh hưởng thế nào
- Đề xuất sửa gắn với việc khôi phục kết quả cho người dùng

### Dễ dàng chia sẻ trong team

- Cài đặt bằng 1 lệnh (`./install.sh`)
- Hỗ trợ cả Cursor và Claude Code
- Báo cáo tiếng Việt, có bảng thuật ngữ cho từ chuyên dụng

---

## Giới hạn cần biết

| Giới hạn | Giải thích |
|----------|-----------|
| Không thay thế review của người | Agent hỗ trợ, không thay design lead hay PM sign-off |
| JTBD có thể suy luận sai | Nếu không cung cấp chân dung + công việc, agent đoán từ giao diện |
| Không chạy prototype thật | Đánh giá luồng dựa trên ảnh tĩnh + liên kết, không tương tác |
| Trạng thái động hạn chế | Hover, animation chỉ đánh giá được nếu có frame/variant riêng |
| Accessibility trên thiết kế | Đánh giá WCAG trên design, không thay kiểm tra trên bản build |

---

## Bắt đầu

Xem [README.md](README.md) để cài đặt và sử dụng.
