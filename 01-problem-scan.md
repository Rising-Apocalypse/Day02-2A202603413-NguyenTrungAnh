# 01 — Problem Scan & Quick Cards

**Học viên:** Nguyễn Trung Anh — 2A202603413

> Các con số thời gian/khối lượng là **ước tính giả định** để scoping, cần xác minh với bộ phận vận hành.

---

## Phase 1 — SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại | Phản ánh cư dân trên App Resident (mất nước, hỏng đèn, ồn…) được nhân viên đọc và chuyển tay đến đúng bộ phận/tòa nhà. |
| 2 | **VinFast** | AI-upgrade | Khách mô tả lỗi xe bằng tiếng Việt tự do ("đi qua gờ kêu cụp cụp bánh trước"); cố vấn dịch vụ phải tự đoán hạng mục lỗi để đặt lịch xưởng. |
| 3 | **Vinpearl** | Tốn thời gian | Email đặt phòng theo đoàn từ công ty lữ hành (nhiều loại phòng, ngày lệch nhau) được nhập tay để kiểm tra quỹ phòng. |
| 4 | **Xanh SM** | Pain từ người khác | Khách báo quên đồ trên xe qua hotline; tổng đài phải dò lại chuyến và gọi từng tài xế. |
| 5 | **Vinmec** | Pain từ người khác | Bệnh nhân đặt lịch online chọn sai chuyên khoa, đến nơi phải chuyển khoa, mất lượt khám. |

**Top 3 chọn làm Quick Card:** #1, #2, #3 — dữ liệu đầu vào là văn bản, quy trình có bước "đọc–hiểu–phân loại" rõ ràng, rủi ro an toàn thấp hơn #5 (y tế).

---

## Phase 2 — QUICK-ASSESS

### Card #1 — Vinhomes: Phân loại & điều hướng phản ánh cư dân

```text
Bài toán: Phản ánh cư dân bị chuyển sai/chậm đến bộ phận xử lý.
Công ty: Vinhomes
Actor: Nhân viên CSKH ban quản lý (quá tải), cư dân (chờ lâu)

Workflow hiện tại:
  1. Cư dân gửi phản ánh trên App
  → 2. CSKH đọc, xác định loại sự cố + tòa nhà
  → 3. Tạo ticket, chuyển cho kỹ thuật/an ninh/vệ sinh
  → 4. Phản hồi cư dân "đã tiếp nhận"

Bước tốn nhất: Bước 2–3 (⏱ ~5 phút/phản ánh, dồn ứ ngoài giờ hành chính)
AI hỗ trợ: Bước 2–3 — LLM đọc nội dung, gán loại + mức khẩn + bộ phận,
           CSKH chỉ duyệt
Metric: Thời gian từ lúc gửi đến lúc vào đúng bộ phận: ~4 giờ → < 15 phút;
        tỉ lệ chuyển sai bộ phận < 5%
Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

### Card #2 — VinFast: Phân loại lỗi xe từ mô tả tiếng Việt

```text
Bài toán: Mô tả lỗi mơ hồ khiến xưởng xếp sai kỹ thuật viên/thiếu phụ tùng.
Công ty: VinFast
Actor: Cố vấn dịch vụ (CVDV), khách hàng (đến xưởng phải chờ)

Workflow hiện tại:
  1. Khách gọi/nhắn mô tả triệu chứng
  → 2. CVDV hỏi thêm, tự đoán hạng mục (gầm, điện, pin…)
  → 3. Đặt lịch + chuẩn bị phụ tùng
  → 4. Kỹ thuật viên kiểm tra thực tế

Bước tốn nhất: Bước 2 (⏱ ~10 phút/ca; đoán sai → khách quay lại lần 2)
AI hỗ trợ: Bước 2 — LLM gợi ý top-3 hạng mục lỗi + câu hỏi cần hỏi thêm.
           KHÔNG chẩn đoán an toàn (phanh, pin) — luôn chuyển kỹ sư.
Metric: Tỉ lệ khách phải quay lại lần 2 do chuẩn bị sai: ~20% → < 10%
Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

### Card #3 — Vinpearl: Đọc email đặt phòng theo đoàn

```text
Bài toán: Email booking đoàn nhập tay chậm, dễ sai số phòng/ngày.
Công ty: Vinpearl
Actor: Nhân viên Reservation, công ty lữ hành (chờ xác nhận)

Workflow hiện tại:
  1. Nhận email (thường kèm bảng/ghi chú tự do)
  → 2. Đọc và nhập từng dòng: loại phòng, số lượng, ngày
  → 3. Kiểm tra quỹ phòng trên PMS
  → 4. Soạn email xác nhận/báo giá

Bước tốn nhất: Bước 2 (⏱ ~20 phút/email đoàn lớn)
AI hỗ trợ: Bước 2 & 4 — LLM trích xuất thành bảng có cấu trúc,
           code (rule) kiểm tra quỹ phòng, LLM draft email để nhân viên duyệt
Metric: Thời gian xử lý 1 email đoàn: ~20 phút → < 5 phút;
        lỗi nhập liệu → ~0 nhờ bước duyệt
Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

---

## Kết quả bình chọn nhóm

**Đề tài nhóm chọn:** **Vinpearl — Tổng hợp & phân tích review khách sạn** (4/6 phiếu). Quét review trên Booking.com, Agoda, Google Maps, lọc phàn nàn khẩn cấp ("phòng bẩn", "nhân viên thái độ tệ") gửi về Manager. Phần Deep-Dive (02) và Workflow Diagram (04) của nhóm làm trên đề tài này.

**Vì sao các card của tôi không được chọn** (đánh giá cá nhân sau buổi bình chọn):

| Card | Lý do không chọn |
|---|---|
| #1 Vinhomes — phản ánh cư dân | Có vào vòng bình chọn nhưng chỉ được 1/6 phiếu. Dữ liệu nằm trong App nội bộ, nhóm khó mô tả quy trình hiện tại sát thực tế. |
| #2 VinFast — phân loại lỗi xe | Liên quan an toàn xe, ranh giới phải rất chặt (luôn cần kỹ sư duyệt). |
| #3 Vinpearl — email booking đoàn | Phải tích hợp hệ thống quản lý phòng (PMS) mới có giá trị; review khách sạn cùng mảng Vinpearl nhưng dữ liệu công khai, dễ làm prototype hơn. |
