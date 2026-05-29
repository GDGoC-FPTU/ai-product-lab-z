# Phase 1 — SCAN: Tìm Kiếm Cơ Hội (Cá Nhân)

Sử dụng **4 Lenses** quét qua hoạt động vận hành của các công ty thành viên Vingroup để tìm kiếm các bài toán thực tế có thể giải quyết bằng AI.

## 📝 Bảng Quét Cơ Hội (SCAN)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối viên (Dispatcher) xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin hoặc va chạm thực địa (mất 15-20 phút/lượt). |
| 2 | **VinFast** | AI-upgrade | Tự động đề xuất lịch trình sạc tối ưu và trạm sạc trống phù hợp với loại cổng sạc (CCS2/GBT) của từng dòng xe điện (VF5, VF8, VF9). |
| 3 | **Vinhomes** | Lặp lại | Phân loại tự động các khiếu nại (ví dụ: mất nước, hỏng đèn, tiếng ồn) gửi qua App Vinhomes Resident đến đúng ban quản lý từng tòa nhà. |
| 4 | **Vinpearl** | Stakeholder Pain | Quét qua các review trên Booking.com, Agoda, Google Map của Vinpearl để lọc ra các phàn nàn khẩn cấp (ví dụ: "phòng bẩn", "nhân viên thái độ tệ") gửi về quản lý. |
| 5 | **Vinmec** | Tốn thời gian | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện (Discharge Summary) từ hồ sơ bệnh án điện tử và ghi chú lâm sàng (mất 20-30 phút/bệnh nhân). |

---

# Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **top 3 bài toán** tiềm năng nhất từ danh sách trên để lập thẻ đánh giá nhanh.

## 🃏 QUICK PROBLEM CARD #1 — Xanh SM Xử lý sự cố sạc pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo cáo sự cố sạc pin / hết pin    │
│ giữa đường cần điều phối cứu hộ hoặc trạm sạc gần nhất.     │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Tài xế (chờ đợi), Điều phối viên (quá tải)     │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài điều vận báo hết pin               │
│   → 2. Điều phối viên tra cứu thủ công vị trí xe trên bản đồ│
│   → 3. Tra cứu thủ công các trạm sạc VinFast còn trụ trống   │
│   → 4. Viết tin nhắn chỉ dẫn/đường đi gửi qua App tài xế    │
│   → 5. Liên hệ đội xe cứu hộ nếu xe đã cạn kiệt pin         │
│                                                             │
│ Bước nào tốn nhất? Bước 3-4 (⏱ 12 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4              │
│ (Tự động hóa lấy vị trí -> Tra cứu trạm trống -> Draft tin) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút.      │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Tự động soạn chỉ dẫn)   │
└─────────────────────────────────────────────────────────────┘
```

## 🃏 QUICK PROBLEM CARD #2 — Vinhomes Phân loại và Điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Phân loại phản ánh cư dân qua ứng dụng Vinhomes    │
│ Resident để gửi đến đúng bộ phận xử lý của từng tòa nhà.    │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Nhân viên CSKH (quá tải phân loại tin),        │
│ Cư dân (chờ đợi phản hồi lâu).                              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi phản ánh bằng văn bản/hình ảnh lên app      │
│   → 2. Nhân viên CSKH trung tâm đọc và phân loại thủ công   │
│   → 3. Chuyển tiếp phiếu yêu cầu đến BQL tòa nhà tương ứng  │
│   → 4. BQL tiếp nhận và phân công kỹ thuật viên/vệ sinh     │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 30 phút/phản ánh)            │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Phân loại tự động ý định phản ánh và tự động định tuyến)    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian chuyển tiếp từ 2 tiếng ──> dưới 5 phút.      │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Tự động phân loại)     │
└─────────────────────────────────────────────────────────────┘
```

## 🃏 QUICK PROBLEM CARD #3 — Vinmec Soạn thảo tóm tắt hồ sơ xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ tốn thời gian tổng hợp hồ sơ lâm sàng để   │
│ viết tóm tắt bệnh án xuất viện cho bệnh nhân dễ hiểu.      │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Bác sĩ điều trị (quá tải hành chính),          │
│ Bệnh nhân (chờ đợi làm thủ tục xuất viện lâu).              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ mở hồ sơ bệnh án điện tử (EMR) của bệnh nhân    │
│   → 2. Tổng hợp thủ công các kết quả xét nghiệm, chẩn đoán   │
│   → 3. Gõ tay tóm tắt quá trình điều trị bằng từ ngữ dễ hiểu │
│   → 4. In ấn, ký nhận và giao bản cứng cho bệnh nhân        │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 20 phút/bệnh nhân)           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Trích xuất dữ liệu thô EMR -> Tự động draft văn bản tóm tắt)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian chuẩn bị hồ sơ từ 25 phút ──> dưới 5 phút.   │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Draft Summary)          │
└─────────────────────────────────────────────────────────────┘
```
