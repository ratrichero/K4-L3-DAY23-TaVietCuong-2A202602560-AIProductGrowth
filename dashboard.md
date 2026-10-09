# OPERATING DASHBOARD — PricePolicy AI Agent (VLandFuture)

**Loại mô hình:** B2B2C · **Cập nhật:** 09/10/2026 · **Tạ Việt Cường – 2A202602560**  
**NORTH STAR:** Approved Quotes per Active Partner per Week — hiện tại: **0** (chuẩn bị go-live pilot) — mục tiêu: **75 hồ sơ/sàn/tuần**

### Đèn báo sớm (Leading — nhìn hằng ngày/tuần)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|:---:|:---:|:---:|---|
| **Partner Activation Rate** | 0% | $\ge 60\%$ / $30\% - 60\%$ / $< 30\%$ | [TB] | Volume hệ thống & Doanh thu/partner |
| **Time-to-First-End-User (TTFEU)** | 0 ngày | $< 14$ / $14 - 30$ / $> 30$ ngày | [TB] | Partner Activation & Tỷ lệ sàn bỏ xó app |
| **Tỷ lệ Containment (AI tự duyệt)** | 82.0% | $\ge 82.0\%$ / $74.1\% - 82.0\%$ / $< 74.1\%$ | [MH] | Chi phí HITL ($9/h) & Gross Margin sau rev-share |

### Đèn vận hành (Operating — nhìn hằng tuần/tháng)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|:---:|:---:|:---:|---|
| **Gross Margin sau Rev-Share 20%** | 52.6% | $\ge 52.6\%$ / $40.0\% - 52.6\%$ / $< 40.0\%$ | [MH] | Lợi nhuận ròng & Runway tài chính |
| **Inference ÷ Doanh thu từng Partner** | 4.6% | $< 15\%$ / $15\% - 25\%$ / $> 25\%$ | [BM] 08/10/2026 | Gross margin sàn & dữ liệu chính sách bị phình |
| **Tập trung Volume đối tác** | 100% | $< 40\%$ / $40\% - 60\%$ / $> 60\%$ | [BM] 08/10/2026 | Độ ổn định dòng tiền & rủi ro bị ép độc quyền |

### Đèn kết quả (Lagging — nhìn hằng quý)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn |
|---|:---:|:---:|:---:|
| **CAC Payback qua Partner-Led** | Chưa đo | $< 4$ / $4 - 12$ / $> 12$ tháng | [BM] 08/10/2026 |

### 5 luật quyết định (⏹ = luật dừng)

1. ⏹ **NẾU** Partner Activation Rate $< 30\%$ **TRONG** 45 ngày **VÀ** đã kết nối kỹ thuật cho $\ge 3$ sàn **THÌ** đóng băng toàn bộ việc ký mới, cử Product Lead cắm chốt trực tiếp 3 buổi/tuần tại sàn để gỡ nghẽn **KHÔNG THÌ** cấm lấy số lượng "sàn đã ký hợp tác" ra để báo cáo thành tích tăng trưởng.
2. ⏹ **NẾU** Tỷ lệ Containment $< 74.1\%$ **TRONG** 2 tuần liên tiếp **VÀ** có $\ge 200$ yêu cầu báo giá **THÌ** ngắt quyền tính giá tự động của các dự án chiết khấu đa tầng, siết lại bộ lọc regex và Python rule engine **KHÔNG THÌ** cấm tuyển thêm nhân sự HITL trực ca dọn rác, và cấm đổ lỗi cho sales.
3. **NẾU** Gross Margin sau Rev-share $< 40\%$ **TRÊN** 1 sàn trong 1 tháng **THÌ** áp dụng mức sàn phí hạ tầng $\$0.30$/báo giá (pass-through) hoặc tạm dừng sàn đó yêu cầu chuẩn hóa lại file Excel chính sách **KHÔNG THÌ** không được bù lỗ với hy vọng "chờ có volume lớn sẽ tự lãi".
4. ⏹ **NẾU** 1 sàn chiếm $> 60\%$ volume **TRONG** 2 tháng liên tiếp **THÌ** đóng băng toàn bộ yêu cầu làm tính năng riêng cho sàn đó, dồn 80% thời gian sang kích hoạt 3 sàn kế tiếp **KHÔNG THÌ** cấm ký cam kết độc quyền phân phối hay ưu đãi giá riêng dù họ dọa rời bỏ.
5. **NẾU** TTFEU $> 30$ ngày tại sàn đã bàn giao kỹ thuật **THÌ** tổ chức ngay buổi thực chiến 45 phút sáng thứ Hai, cầm tay chỉ việc cho 5 sales cứng tạo báo giá thật và tặng 50 lượt tạo báo giá miễn phí **KHÔNG THÌ** cấm gửi tài liệu PDF hướng dẫn qua Zalo rồi ngồi chờ sales tự đọc.

### Cổng gác 90 ngày

| Ngày | Metric (1) | Ngưỡng qua cổng | Bằng chứng | Nếu trượt |
|:---:|---|---|---|:---:|
| **30** | Lượt báo giá tạo thành công tại sàn pilot | $\ge 300$ báo giá, 0 lỗi sai số học, thời gian $< 15$s | 300 mã băm SHA-256 đối soát khớp 100% biên bản cọc của kế toán sàn VLandFuture | **FIX** |
| **60** | Tỷ lệ Containment toàn hệ thống | $\ge 82.0\%$ trên quy mô $\ge 5$ sàn đối tác Meey CRM; NPS sales $\ge 60$ | Dashboard log tỷ lệ HITL và kết quả khảo sát ẩn danh 100 sales từ 5 sàn đối tác | **FIX** |
| **90** | Tổng volume báo giá & MRR | $\ge 10.000$ báo giá/tháng trên $\ge 15$ sàn, MRR $\ge \$9.900$, Payback $< 4$ tháng | Biên bản đối soát doanh thu có chữ ký số giữa Meey Land và VLandFuture + sao kê rev-share | **PIVOT / KILL** |

**KILL CRITERIA:** Sau 90 ngày (hạn chót **10/01/2027**), nếu sau 1 lần FIX mà tỷ lệ Containment vẫn dưới 74.1% trên toàn hệ thống hoặc Meey CRM không kích hoạt được tối thiểu 5 sàn có sales phát sinh báo giá thật, dự án sẽ **DỪNG HOÀN TOÀN**, thanh lý hạ tầng máy chủ và hoàn tiền đối tác.

**CHƯA ĐO ĐƯỢC:** Net Revenue Retention (NRR) của các sàn đối tác và tỷ lệ churn sau 12 tháng. Cần tối thiểu 6–12 tháng vận hành thực tế để trải qua đủ chu kỳ gia hạn hợp đồng bán buôn của các sàn BĐS; dự kiến có số liệu sơ bộ vào ngày **10/04/2027** sau khi kết thúc quý 1/2027.
