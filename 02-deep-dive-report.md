# 📄 Báo Cáo Phân Tích Sâu (Deep-Dive Report)

**Tên Nhóm:** z
**Thành viên tham gia:**
1. **Đặng Tiến Quyền* — MSSV: 2A202600896
---

## 🗳️ 1. Quyết định lựa chọn dự án

Nhóm quyết định chọn bài toán **"Card #1 — Xanh SM Xử lý sự cố sạc pin thực địa"** để thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các thẻ khác:
* **Tại sao chọn Sự cố sạc pin thực địa:** Đây là một bài toán thời gian thực ảnh hưởng trực tiếp đến hiệu suất vận hành của đội xe GSM (Xanh SM) và trải nghiệm của khách hàng. Việc tự động hóa giúp giảm trực tiếp thời gian chết của xe, giải phóng áp lực cho điều phối viên trong giờ cao điểm.
* **Tại sao loại bỏ Vinhomes CSKH:** Mặc dù tốn thời gian nhưng rủi ro sai sót thông tin liên quan đến các vấn đề cư dân (phí quản lý, tranh chấp) có thể dẫn đến khiếu nại pháp lý phức tạp. Cần thêm thời gian tích lũy dữ liệu và chuẩn hóa quy trình trước.
* **Tại sao loại bỏ Vinmec Discharge Summary:** Mặc dù có tính ứng dụng cao nhưng bài toán y tế có yêu cầu cực kỳ nghiêm ngặt về độ chính xác lâm sàng, quyền riêng tư dữ liệu y tế (HIPAA/GDPR) và cần tích hợp sâu với hệ thống EMR hiện tại của Vinmec. Scope này quá lớn cho giai đoạn hiện tại.

---

## 🏗️ 2. Problem Statement (6-field) — Tiêu chuẩn Vin Smart Future

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | Khi tài xế báo sự cố hết pin/sắp hết pin thực địa:<br>1. Tài xế gọi điện/gửi thông tin khẩn cấp.<br>2. Điều phối viên tra cứu thủ công tọa độ GPS của xe trên hệ thống.<br>3. Tra cứu thủ công Dashboard trạm sạc VinFast xem trạm nào gần nhất còn trụ trống và phù hợp loại cổng sạc (VF5/VF8/VF9).<br>4. Soạn thảo thủ công tin nhắn hướng dẫn đường đi chi tiết gửi qua ứng dụng tài xế.<br>5. Liên hệ xe cứu hộ sạc di động nếu pin xe dưới 5%. |
| **3. Bottleneck** | Bước 3 & 4 (mất trung bình 10-12 phút): Việc đối chiếu thủ công giữa vị trí xe, loại cổng sạc tương thích và tình trạng trụ trống của trạm sạc VinFast, sau đó gõ tay tin nhắn bằng tiếng Việt tốn rất nhiều thời gian và dễ nhầm lẫn dưới áp lực cuộc gọi dồn dập. |
| **4. Business Impact** | Hà Nội trung bình ghi nhận ~80 sự cố pin/ngày. Việc xử lý thủ công gây lãng phí ~20 giờ làm việc/ngày của đội ngũ điều phối. Thời gian xử lý lâu khiến xe nằm chờ lâu trên đường đón khách, làm tăng tỷ lệ hủy chuyến của khách hàng và gây rò rỉ khoảng 15% doanh thu hàng ngày trên các đầu xe gặp sự cố. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút (Tiết kiệm >80% thời gian).<br>2. Tỷ lệ đề xuất trạm sạc chính xác về loại cổng sạc và vị trí đạt ít nhất 98%.<br>3. Thời gian điều phối cứu hộ khi pin dưới 5% giảm xuống dưới 1 phút. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Tự động lấy tọa độ xe, truy vấn danh sách trạm sạc trống và soạn thảo tin nhắn hướng dẫn dạng nháp (Draft).<br>**CẤM (Operational Boundary):**<br>1. Tuyệt đối không được tự ý gửi tin nhắn hướng dẫn đi khi chưa có sự xác nhận của Điều phối viên (Bắt buộc phải có thẻ `[DRAFT_ONLY]`).<br>2. Nếu lượng pin của xe dưới 5% (Mức nguy hiểm), tuyệt đối không được chỉ dẫn tài xế đến trạm sạc cách xa quá 5km để tránh chết máy giữa đường. Thay vào đó, AI phải lập tức tạo yêu cầu điều động xe cứu hộ sạc di động (`dispatch_mobile_charger`). |

---

## ↩️ 3. Future-State Flow & AI Fit

### Mức độ AI Fit (AI-Fit Matrix):
Nhóm lựa chọn cấp độ **LLM Feature** tích hợp vào dashboard điều vận hiện tại. Quy trình có cấu trúc rõ ràng, có các ranh giới nghiệp vụ cụ thể nên không cần sử dụng Agent tự trị hoàn toàn, tránh rủi ro hallucination và tối ưu chi phí vận hành API.

### Sơ đồ quy trình tương lai (Future-State Workflow):

```text
                     [Tài xế báo sự cố sạc pin]
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │   Bước 1: Auto-pull   │
                     │  Vị trí GPS xe & Pin  │
                     └───────────────────────┘
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │ 🔵 Bước 2: AI Engine  │
                     │   Kiểm tra ranh giới  │
                     └───────────────────────┘
                                 │
                 ┌───────────────┴───────────────┐
         (Pin < 5%?)                      (Pin >= 5%?)
                 │                               │
                 ▼                               ▼
     ┌───────────────────────┐       ┌───────────────────────┐
     │ 🔵 Bước 3a: AI Auto-  │       │ 🔵 Bước 3b: AI Auto-  │
     │ Dispatch cứu hộ di động│       │  Draft trạm sạc gần   │
     │  (Mobile Charger JSON)│       │  (<5km) kèm [DRAFT]   │
     └───────────────────────┘       └───────────────────────┘
                 │                               │
                 └───────────────┬───────────────┘
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │ 🟢 Bước 4: HITL       │
                     │  Điều phối viên duyệt │
                     │   & click Gửi đi      │
                     └───────────────────────┘
                                 │
                 ┌───────────────┴───────────────┐
             (Duyệt OK?)                    (Lỗi/Không khớp?)
                 │                               │
                 ▼                               ▼
        [Gửi tin cho tài xế]            ↩️ Fallback:
                                        Điều phối viên tự
                                        sửa thủ công hoặc
                                        điều phối cứu hộ thủ công.
```

---

## 🏁 4. Phân tích Độ Khả Thi & Đánh Giá (Evaluate)

### AI Readiness Checklist:
- [x] **Dữ liệu:** Hệ thống GSM đã có sẵn logs tọa độ GPS xe, dung lượng pin thời gian thực và API hiện trạng các trạm sạc VinFast.
- [x] **Kiểm soát rủi ro:** Đã thiết lập cơ chế Human-in-the-loop (HITL) phê duyệt thủ công trước khi gửi tin nhắn và cơ chế Fallback quay lại quy trình thủ công nếu AI gặp lỗi.
- [x] **Mức độ sẵn sàng của nhân viên:** Các điều phối viên rất ủng hộ giải pháp vì giúp họ giảm tải công việc nhập liệu thủ công trong giờ cao điểm.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
👉 **GO (Bắt đầu xây dựng Prototype)**

### Lý do (Justification):
1. **Khả năng sinh lời cao:** Giảm thời gian nằm chờ sạc của tài xế từ 15 phút xuống 3 phút giúp tăng số cuốc xe chạy được trong ngày, nhanh chóng hoàn vốn chi phí xây dựng hệ thống trong vòng 3 tháng.
2. **Kỹ thuật khả thi:** Bài toán sử dụng LLM Feature kết hợp với kiểm soát ranh giới nghiêm ngặt (System Prompt) có độ phức tạp trung bình, thời gian phát triển ngắn (khoảng 2 tuần) và độ chính xác cao.
3. **An toàn tuyệt đối:** Nhờ cơ chế kiểm soát ranh giới pin dưới 5% và yêu cầu duyệt thủ công `[DRAFT_ONLY]`, rủi ro điều vận sai gây cạn kiệt pin hoàn toàn được loại bỏ.
