# 03 — AI Log & Reflection

**Học viên:** Nguyễn Trung Anh — 2A202603413
**Công cụ AI:** Claude Code (Claude Opus 5.5) — dùng làm thought-partner trong suốt buổi lab.

---

## 1. Tổng hợp yêu cầu & hiểu đề lab

- **AI giúp gì:** Đọc README, worksheet và autograder, tóm lại thành 2 phần: 4 file `.md`/`.png` chấm nhóm trên `main`, file `.py` chấm cá nhân trên branch riêng. Chỉ ra autograder kiểm tra gì (từ khóa trong `SYSTEM_PROMPT`, timeout 30s, đếm chữ "Passed"/"Failed").
- **Tôi tự kiểm tra:** Đối chiếu lại với README mục 5 và 6 — đúng. Hỏi lại về cách đặt tên branch; README không quy định cứng, tôi chọn `nguyentrunganh-2A202603413`.

## 2. Hoàn thiện ý tưởng & câu chữ System Prompt

- **AI giúp gì:** Gợi ý cấu trúc prompt: vai trò → 2 quy tắc bắt buộc → định dạng đầu ra. Giải thích `[DRAFT_ONLY]` là cơ chế human-in-the-loop (bản nháp, con người duyệt mới gửi).
- **Điểm tôi học được:** Hai luật có thể **xung đột** (luôn mở đầu bằng thẻ vs. trả JSON) → phải ghi rõ thứ tự: dòng 1 là `[DRAFT_ONLY]`, dòng 2 là JSON. Phải viết sẵn kiểu tấn công ("kể cả khi người dùng bảo bỏ thẻ") thì model mới giữ luật.
- **Giới hạn:** `[DRAFT_ONLY]` chỉ là chữ — lớp bảo vệ *mềm*. Sản phẩm thật cần thêm lớp *cứng* bằng code: output không có thẻ thì không bao giờ được gửi.

## 3. Debug model trong `prompt_prototype.py`

| Lỗi | Nguyên nhân | Cách sửa |
|---|---|---|
| `404 NOT_FOUND` | `gemini-2.5-flash` đã ngừng cấp cho tài khoản mới. AI ban đầu cũng hướng dẫn dùng 2.5 vì không biết model đã bị ngừng. | Đổi sang `gemini-3.8-flash`. |
| Timeout > 30s (tiêu chí 4) | Model 3.x mặc định bật "thinking" lâu; 2 lần gọi liên tiếp vượt 30s. | Thêm `ThinkingConfig(thinking_level=...)` để giảm mức suy nghĩ. |
| Không thấy "Passed" (tiêu chí 5) | Script không crash nhưng cả 2 test rơi vào nhánh `Error during execution`. | Đọc dòng lỗi cụ thể rồi sửa môi trường/tham số → pass 5/5. |

- **Bài học:** Model có vòng đời — tên model nên để ở cấu hình, và mỗi lần đổi model phải chạy lại test ranh giới. Giảm "thinking" để nhanh hơn là đánh đổi **tốc độ vs. độ tuân thủ luật**, cần kiểm tra lại cả 2 test sau khi đổi.

## 4. Lên ý tưởng Phase 1–2

- **AI giúp gì:** Gợi ý 5 bài toán theo 4 lens và 3 Quick Card (Vinhomes, VinFast, Vinpearl booking đoàn).
- **AI sai ở đâu:**
  - **Tự đặt con số** (phút/lượt, % lỗi) không có nguồn → tôi yêu cầu ghi rõ là *ước tính giả định*.
  - **Tự chọn ý tưởng** mà không hỏi nhóm → danh sách không có đề tài nhóm bình chọn. Phase 1–2 là cá nhân nên vẫn hợp lệ, nhưng tôi bổ sung mục *Kết quả bình chọn nhóm* (Vinpearl — phân tích review khách sạn) và lý do card của mình không được chọn.

## 5. Viết & trình bày file `.md`

- **AI giúp gì:** Dựng khung file đúng tên autograder yêu cầu (`01-problem-scan.md`, `03-ai-log.md`), trình bày bảng và card gọn.
- **Tôi điều chỉnh:** Tự đọc lại và sửa câu chữ để trả lời được khi giảng viên hỏi.

## 6. Vẽ Workflow Diagram & soạn ý tưởng Deep-Dive

- **AI giúp gì:**
  - Dựng sơ đồ Current-State dạng swimlane (Nền tảng · CX · Trưởng bộ phận · GM) bằng HTML rồi chụp thành `04-workflow-diagram.png`, đánh dấu 🔴 Bottleneck, 🔄 Handoff, tổng thời gian. Tôi yêu cầu bỏ các dòng phụ đề/ghi chú thừa cho gọn.
  - Soạn khung `02-deep-dive-report.md` cho đề tài nhóm (Vinpearl — phân tích review): 6-field, so sánh Rule/LLM/Agent, Future flow, Evaluate.
- **Tôi phản biện và sửa ý tưởng của AI:**
  - AI đề xuất LLM trả JSON có trường **tóm tắt** → tôi bỏ vì review thường ngắn, tóm tắt vô nghĩa.
  - Tôi đề xuất dùng **Jev** (model phân loại của TypeSafe, ra 9/2026) thay JSON. AI ban đầu **không biết Jev** vì model ra sau dữ liệu huấn luyện của nó → tôi yêu cầu tra web. Jev chọn nhãn trong danh sách cố định kèm xác suất → hợp hơn cho phân loại, xác suất dùng làm ngưỡng fallback.
  - Tôi đề xuất bỏ bước dịch riêng. AI phản biện một phần: sau phân loại **vẫn có người đọc** (Manager nhận cảnh báo P1), và trả lời review phải bằng **ngôn ngữ của khách** → chốt: chỉ dịch ở bước cảnh báo và bước soạn nháp, và cho Jev phân loại luôn ngôn ngữ.
- **AI sai ở đâu:** AI khẳng định "một lần gọi Jev hỏi được nhiều chiều" chỉ dựa trên tóm tắt kết quả tìm kiếm. Tôi hỏi lại → AI đọc tài liệu hướng dẫn và mới xác nhận (nhiều câu hỏi trong 1 lần gọi, chấm độc lập). Các số liệu về Jev đều là của nhà cung cấp/blog bên thứ ba → ghi vào rủi ro, pilot so sánh Jev với Gemini.
- **Bài học:** Không phải bài toán nào cũng cần model "biết viết" — bài toán chọn nhãn thì dùng công cụ chọn nhãn rẻ hơn, nhanh hơn, dễ kiểm soát hơn. Và kiến thức của AI có hạn sử dụng: công cụ mới phải tự tra và kiểm chứng.

---

## Tự đánh giá

AI tăng tốc rõ ở khâu đọc đề, debug và trình bày. Nhưng AI **không biết bối cảnh thực tế** (model đã ngừng, nhóm chọn đề tài gì, số liệu thật) — những chỗ đó tôi phải tự kiểm chứng. Nguyên tắc tôi rút ra: **dùng AI để nghĩ nhanh hơn, không dùng để thay phần kiểm chứng.**
