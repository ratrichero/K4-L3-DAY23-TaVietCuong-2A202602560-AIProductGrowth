# Worksheet — PricePolicy AI Agent (VLandFuture)

Họ tên: Tạ Việt Cường · MSSV: 2A202602560 · Ngày làm: 09/10/2026

---

## Trạm 1 — Loại mô hình

**Câu chốt loại:** Chúng tôi là **B2B2C** vì tiền trả đến từ Ngân sách Vận hành Bán hàng & Quản trị Doanh thu của Chủ đầu tư BĐS và các Sàn phân phối lớn (thông qua cơ chế chia sẻ doanh thu 20% với đối tác nền tảng Meey CRM), người dùng thật là hàng trăm nhân viên kinh doanh (môi giới) tuyến đầu cùng khách mua nhà cuối của các sàn, và chúng tôi chạm trực tiếp được họ qua Zalo Mini App & Web Extension nhúng ngay trên giao diện giỏ hàng Meey CRM.

**Bảng đèn §3.3 của B2B2C** (rà soát toàn bộ theo thực tế hôm nay):

| Đèn | ✅ / 🔧 / ❌ | Số nằm ở đâu / cần gì để đo |
|---|:---:|---|
| **Partner activation rate** ⭐ | 🔧 | Cần gắn event tracking đếm số sàn có $\ge 10$ báo giá thật trong 30 ngày đầu từ ngày kết nối Meey CRM (hoàn tất trong 2 tuần pilot). |
| **End-user reach trong partner** | 🔧 | Cần Meey CRM cung cấp webhook báo tổng số tài khoản sales đang active của từng sàn để làm mẫu số (đang cấu hình API trong sprint này). |
| **Time-to-first-end-user** | ✅ | Đo được ngay trên Supabase: lấy timestamp bàn giao kỹ thuật trừ đi timestamp phát sinh báo giá thật đầu tiên của sales sàn đó. |
| **Volume volatility** | 🔧 | Cần tích lũy dữ liệu tối thiểu 2 tháng vận hành mới tính được độ lệch chuẩn volume giữa các tuần (hiện mới chạy pilot tháng 1). |
| **GM sau rev-share** | ✅ | Đo được hằng tháng từ dữ liệu kế toán: `(Doanh thu gộp - 20% rev-share Meey CRM - COGS) ÷ Doanh thu gộp`. |
| **Chi phí inference ÷ doanh thu — theo TỪNG partner** | ✅ | Đo được ngay hằng tuần từ LangSmith & Supabase logs nhờ tách riêng tenant ID và API key cho từng sàn. |
| **Tập trung volume** | ✅ | Đo được ngay trên database: `Volume của sàn lớn nhất ÷ Tổng volume toàn bộ hệ thống`. |
| **Chất lượng nhìn từ end-user (SLA)** | ✅ | Đo được hằng tuần trên bộ kiểm chuẩn Eval Engine: đếm tỷ lệ báo giá dính lỗi tính toán hoặc vi phạm quy tắc chiết khấu do sales phản ánh. |
| **Doanh thu/partner · partner NRR · GM tổng** | ❌ | Chưa đo được NRR do đang ở tháng đầu pilot; cần tối thiểu 6–12 tháng vận hành để trải qua chu kỳ gia hạn hợp đồng của các sàn BĐS. |

---

## Trạm 2 — Thẻ đèn

**North Star:** **Approved Quotes per Active Partner per Week** (Số hồ sơ báo giá hợp lệ được tạo trên mỗi sàn đối tác hoạt động hằng tuần) — hiện tại: **0** (chuẩn bị go-live pilot sàn VLandFuture) — mục tiêu: **75 hồ sơ/sàn/tuần** (~15 báo giá/ngày làm việc, tương đương 40 sales tạo trung bình 2 báo giá/tuần).

| # | Tầng (L/O/G) | Đèn | Định nghĩa (đếm gì · **không** đếm gì) | Công thức | Nhịp · ai lấy số | Báo trước cho |
|---|:---:|---|---|---|---|---|
| 1 | **L** | **Partner Activation Rate** ⭐ | Tỷ lệ % sàn phân phối đã tích hợp phát sinh $\ge 10$ báo giá thật trong 30 ngày. **Không đếm** tài khoản test nội bộ của ban quản trị hoặc data test của dev. | (Số sàn có $\ge 10$ báo giá thật) ÷ (Tổng số sàn đã go-live kỹ thuật trên Meey CRM) | Hằng tuần · Partnership Lead | Báo trước cho Volume toàn hệ thống (tầng 2) và Doanh thu/partner (tầng 3) |
| 2 | **L** | **Time-to-First-End-User (TTFEU)** | Số ngày từ lúc bàn giao Zalo Mini App cho sàn đến khi sales đầu tiên tạo thành công 1 báo giá thật cho khách. **Không đếm** các lượt chạy thử trong buổi training. | Ngày tạo báo giá thật đầu tiên − Ngày bàn giao kỹ thuật | Mỗi partner · Solution Architect | Báo trước cho Partner Activation Rate (tầng 1) và nguy cơ sàn bỏ xó sản phẩm |
| 3 | **L** | **Tỷ lệ Containment (AI tự duyệt)** | % lượt yêu cầu tính giá được AI xử lý trọn vẹn và đóng dấu SHA-256 không cần chuyên viên HITL can thiệp. **Không đếm** ca sales tự hủy giữa chừng hoặc lỗi mạng client. | (Số báo giá AI tự hoàn thành thành công) ÷ (Tổng số yêu cầu tính giá hợp lệ) | Hằng ngày · AI Tech Lead | Báo trước cho Chi phí HITL (tầng 2) và Gross Margin sau rev-share (tầng 2) |
| 4 | **O** | **Gross Margin sau Rev-Share 20%** | Biên lợi nhuận gộp thực tế của công ty sau khi trừ 20% chia sẻ cho Meey CRM và toàn bộ COGS (token, pgvector, nhân sự HITL). **Không tính** chi phí marketing hay lương dev cố định. | (Doanh thu gộp − 20% phí Meey CRM − Tổng COGS) ÷ Doanh thu gộp | Hằng tháng · Kế toán trưởng | Báo trước cho Lợi nhuận ròng và Runway tài chính của công ty (tầng 3) |
| 5 | **O** | **Chi phí Inference ÷ Doanh thu theo từng Partner** | Tỷ lệ chi phí API LLM (Claude Haiku 4.5) + Vector DB trên doanh thu thu về từ riêng sàn đó. **Không lấy trung bình** cào bằng toàn bộ các sàn. | (Tiền API + Infra của riêng sàn X) ÷ (Doanh thu gộp từ sàn X) | Hằng tuần · AI Tech Lead | Báo trước cho GM sau rev-share của từng sàn và phát hiện dữ liệu chính sách bị rác |
| 6 | **O** | **Tập trung Volume đối tác** | Tỷ trọng lượt báo giá đến từ 01 sàn đối tác lớn nhất trên tổng volume toàn hệ thống. **Không tính** volume chạy dồn trong sự kiện demo nội bộ. | Lượt báo giá của sàn lớn nhất ÷ Tổng lượt báo giá toàn hệ thống | Hằng tháng · Partnership Lead | Báo trước cho Rủi ro dòng tiền bị kiểm soát và nguy cơ bị đối tác ép độc quyền |
| 7 | **G** | **CAC Payback qua Partner-Led** | Số tháng cần thiết để lãi gộp từ một sàn bù đắp toàn bộ chi phí kết nối, đào tạo và onboard sàn đó vào Meey CRM. **Không tính** chi phí R&D lõi sản phẩm. | Chi phí onboard & setup sàn ÷ Lợi nhuận gộp bình quân tháng thu từ sàn đó | Hằng quý · CFO & CEO | Đèn kết quả chứng minh kênh Partner-Led có thực sự rẻ hơn Direct Sales hay không |

**Đèn chi phí AI là đèn số:** **3** (Tỷ lệ Containment — quyết định chi phí HITL ăn 79.5% chi phí) và **5** (Tỷ lệ chi phí Inference/Doanh thu theo từng partner).

---

## Trạm 3 — Ngưỡng

| # | Đèn | 🟢 | 🟡 | 🔴 | Nguồn | Lý do (1 câu) · ngày kiểm tra nếu [BM] |
|---|---|:---:|:---:|:---:|:---:|---|
| 1 | **Partner Activation Rate** | $\ge 60\%$ | $30\% - 60\%$ | $< 30\%$ | **[TB]** | Baseline kinh nghiệm kênh đối tác: dưới 30% kích hoạt thì công sức bỏ ra hỗ trợ kỹ thuật ăn sạch doanh thu thu về. |
| 2 | **Time-to-First-End-User** | $< 14$ ngày | $14 - 30$ ngày | $> 30$ ngày | **[TB]** | Quá 30 ngày kể từ lúc bàn giao mà sales chưa phát sinh báo giá thật thì sàn đã mất nhiệt huyết sau training và quay lại dùng Excel cũ. |
| 3 | **Tỷ lệ Containment** | $\ge 82\%$ | $74.1\% - 82\%$ | $< 74.1\%$ | **[MH] 1** | Suy ngược từ mô hình tài chính Day 22: khi containment dưới 74.1%, chi phí nhân sự HITL ($9/h) xử lý ngoại lệ nuốt chửng biên lợi nhuận, làm GM tụt dưới 60%. |
| 4 | **GM sau Rev-Share 20%** | $\ge 52.6\%$ | $40\% - 52.6\%$ | $< 40\%$ | **[MH] 2** | Suy từ Unit Economics: sau khi chia 20% cho Meey CRM và trả COGS ($0.2715), mức 52.6% neo sát trung vị AI-native 2026 của ICONIQ (53%); dưới 40% là mô hình gãy. |
| 5 | **Chi phí Inference ÷ Doanh thu từng Partner** | $< 15\%$ | $15\% - 25\%$ | $> 25\%$ | **[BM]** | Benchmark ICONIQ State of AI 2026 (kiểm tra ngày 08/10/2026): chi phí inference chiếm 20–23% cấu trúc chi phí AI; vượt 25% là dữ liệu chính sách bị phình hoặc sales spam prompt. |
| 6 | **Tập trung Volume đối tác** | $< 40\%$ | $40\% - 60\%$ | $> 60\%$ | **[BM]** | Quy ước quản trị rủi ro B2B2C (Handbook §3.3, kiểm tra ngày 08/10/2026): trên 60% từ 1 sàn là công ty đang bị sàn đó "bắt làm con tin", họ dọa đi là sập nguồn thu. |
| 7 | **CAC Payback qua Partner-Led** | $< 4$ tháng | $4 - 12$ tháng | $> 12$ tháng | **[BM]** | Benchmark Bessemer SMB Payback < 12 tháng (kiểm tra ngày 08/10/2026); với kênh Partner-Led đã cắt 20% rev-share thì payback bắt buộc phải dưới 4 tháng mới bõ công đi kênh. |

---

### Phụ lục [MH] — phép tính (≥2)

**[MH] 1 — Tỷ lệ Breakeven Containment Rate (Tối thiểu 74.1%)**

```
Đầu vào (từ Sheet 1_Cost_Job & 2_Pricing trong mô hình Day 22):
- Giá bán đề xuất: P = $0.99 / hồ sơ báo giá hợp lệ được phê duyệt
- Gross Margin mục tiêu tối thiểu để vận hành an toàn: GM = 60% (0.60)
- Chi phí biến đổi thuần (API Haiku 4.5 có cache $0.0237 + Infra $0.02 + Retry $0.0019): v = $0.045596 / job
- Chi phí QA nội bộ (5% ca × 2 phút × $9/giờ): q = $0.015 / job
- Chi phí xử lý 1 ca ngoại lệ HITL leo thang (6 phút × $9/giờ): e = $0.90 / ca ngoại lệ

Phép tính:
Áp dụng công thức giải phương trình Breakeven Containment (R):
  R ≥ (v + q + e) ÷ [ P × (1 − GM) + e ]
  Tử số  = 0.045596 + 0.015 + 0.90 = 0.960596
  Mẫu số = 0.99 × (1 − 0.60) + 0.90 = 0.99 × 0.40 + 0.90 = 0.396 + 0.90 = 1.296
  R ≥ 0.960596 ÷ 1.296 = 0.7412006 (74.12%)

Kết quả:
→ 🟢 ≥ 82.0% (mức thiết kế hiện tại, Gross Margin đạt 72.6%)
→ 🟡 74.1% – 82.0% (Gross Margin dao động 60% – 72.6%, chi phí HITL bắt đầu tăng)
→ 🔴 < 74.1% (Gross Margin rơi xuống dưới 60%; nếu containment rớt về 60% thì GM chỉ còn 29.2%, mô hình lỗ nặng)
```

**[MH] 2 — Gross Margin sau Rev-share 20% (Tối thiểu 52.6%)**

```
Đầu vào (từ Sheet 2_Pricing & 4_Channel_Fit trong mô hình Day 22):
- Giá bán cho sàn BĐS: P = $0.99 / job
- Tỷ lệ chia sẻ doanh thu cho đối tác nền tảng Meey CRM: RevShare = 20% (0.20)
- Doanh thu ròng thực nhận về sau khi chia đối tác: P_net = $0.99 × (1 − 0.20) = $0.792 / job
- Chi phí COGS đơn vị chuẩn tại containment 82%: COGS = $0.2715 / job
- Benchmark Gross Margin công ty AI-Native năm 2026 của ICONIQ: ~53% (0.53)

Phép tính:
Biên lợi nhuận gộp thực tế của công ty trên mỗi job bán ra:
  Lãi gộp đơn vị sau chia sẻ = P_net − COGS = $0.792 − $0.2715 = $0.5205 / job
  GM sau rev-share (tính trên giá niêm yết) = $0.5205 ÷ $0.99 = 52.5758% ≈ 52.6%
  (Nếu tính trên doanh thu ròng P_net: GM_net = $0.5205 ÷ $0.792 = 65.7%)

Ngưỡng cảnh báo khi chi phí HITL leo thang hoặc đối tác đòi tăng tỷ lệ chia sẻ:
  Nếu GM sau rev-share tụt xuống < 40% (tức Lãi gộp < $0.396/job, tương đương COGS vọt lên > $0.396 do containment tụt về 70%):

Kết quả:
→ 🟢 ≥ 52.6% (vận hành chuẩn, bám sát benchmark ICONIQ 53%, bù đắp hoàn toàn chi phí GTM)
→ 🟡 40.0% – 52.6% (chi phí HITL tăng hoặc đối tác đòi chiết khấu phụ, biên bị mỏng)
→ 🔴 < 40.0% (mô hình không còn là công ty sản phẩm mà trở thành dịch vụ gia công bán lỗ)
```

---

## Trạm 4 — 5 luật quyết định

Đánh dấu ⏹ cho luật dừng (có đủ 3 luật dừng).

1. ⏹ **NẾU** Partner Activation Rate $< 30\%$ **TRONG** 45 ngày **VÀ** đã kết nối kỹ thuật cho $\ge 3$ sàn phân phối **THÌ** đóng băng toàn bộ việc ký kết và onboarding sàn mới, cử Product Lead cùng Solution Architect xuống cắm chốt trực tiếp 3 buổi/tuần tại văn phòng sàn để quan sát 10 sales tư vấn thực tế xem họ tắc ở bước nào **KHÔNG THÌ** tuyệt đối không được mang số lượng "sàn đã ký biên bản hợp tác" ra để báo cáo thành tích tăng trưởng hay tự huyễn hoặc về quy mô.

2. ⏹ **NẾU** Tỷ lệ Containment $< 74.1\%$ **TRONG** 2 tuần liên tiếp **VÀ** hệ thống tiếp nhận $\ge 200$ yêu cầu báo giá **THÌ** ngắt quyền tính giá tự động của các dự án BĐS có cấu trúc chiết khấu đa tầng phức tạp (chỉ cho phép dùng bảng tính giá chuẩn 1 tầng chiết khấu), đồng thời khóa sprint của đội AI dev để siết lại bộ lọc regex và luật Python math engine chặn rò rỉ ngoại lệ **KHÔNG THÌ** cấm tuyển thêm nhân viên hỗ trợ HITL để giải quyết sự vụ bằng cơm, và cấm đổ lỗi cho nhân viên sales không biết dùng app.

3. **NẾU** Gross Margin sau Rev-share $< 40\%$ **TRÊN** dữ liệu của 1 sàn cụ thể trong 1 tháng **THÌ** kích hoạt ngay điều khoản phụ lục hợp đồng áp mức sàn phí hạ tầng tối thiểu $\$0.30$/báo giá (pass-through inference fee) hoặc tạm dừng dịch vụ của sàn đó yêu cầu Giám đốc sàn chuẩn hóa lại toàn bộ bảng Excel chính sách theo template quy định trước khi mở lại **KHÔNG THÌ** không được cắn răng chịu lỗ với suy nghĩ ngây thơ rằng "đợi sàn đẩy volume lớn hơn thì biên lợi nhuận sẽ tự động tăng".

4. ⏹ **NẾU** Mức độ tập trung Volume của 1 sàn duy nhất $> 60\%$ **TRONG** 2 tháng liên tiếp **THÌ** đóng băng toàn bộ các yêu cầu tùy biến tính năng riêng của sàn lớn đó, chuyển 80% thời lượng làm việc của Partnership Lead sang việc thúc đẩy và chăm sóc 3 sàn phân phối xếp kế tiếp **KHÔNG THÌ** cấm ký bất kỳ điều khoản độc quyền phân phối nào hoặc cam kết giữ giá ưu đãi riêng cho sàn lớn đó, dù họ có đem việc cắt hợp đồng ra để gây áp lực.

5. **NẾU** Time-to-First-End-User (TTFEU) $> 30$ ngày tại một sàn đã bàn giao xong kỹ thuật **THÌ** tổ chức ngay một buổi thực chiến 45 phút vào phiên họp sáng thứ Hai của sàn, cầm tay chỉ việc cho 5 sales cứng nhất tạo 3 báo giá thật ngay tại chỗ và tặng ngay 50 lượt tạo báo giá miễn phí cho sàn để phá băng tâm lý **KHÔNG THÌ** cấm gửi tài liệu hướng dẫn sử dụng dạng file PDF hay video quay màn hình qua nhóm Zalo rồi ngồi rung đùi chờ sales tự đọc.
