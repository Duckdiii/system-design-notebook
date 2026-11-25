# Bài 3: Latency, Throughput, Percentile (`p50`/`p99`), SLA/SLO & "Số 9"

[← Bài 2: Load Balancer, Proxy & CDN](./02-load-balancer-proxy-cdn.md) · [Mục lục Concepts](./README.md) · [Bài 4: CAP & PACELC →](./04-cap-va-pacelc.md)

---

## 3.1. Latency vs Response Time vs Throughput & Điểm bão hòa

- **Latency (Độ trễ):** Thời gian một request phải chờ để được phục vụ hoặc thời gian xử lý thuần túy bên trong hệ thống (không tính đường truyền khứ hồi của client, tùy ngữ cảnh).
- **Response Time (Thời gian phản hồi):** Tổng thời gian người dùng thực sự phải đợi từ lúc gửi request đến lúc nhận đủ byte cuối cùng:
  $$\text{Response Time} = \text{Network RTT} + \text{Queueing Time} + \text{Service Processing Time}$$
- **Throughput (Thông lượng):** Lượng công việc hệ thống hoàn thành trong 1 đơn vị thời gian: **QPS** (*Queries Per Second*), **RPS** (*Requests Per Second*), hoặc **MB/s**.

**Mối quan hệ sống còn khi tăng tải (Little's Law & Điểm bão hòa):**
- Khi số người dùng đồng thời (concurrency) còn thấp: tăng tải → **Throughput tăng đều** và **Latency giữ ổn định**.
- Khi chạm ngưỡng bão hòa tài nguyên (CPU 100%, DB connection pool đầy 100/100, hoặc khóa dòng dữ liệu): request mới bị kẹt trong hàng đợi chờ (**Queueing Time tăng vọt**), CPU tốn nhiều thời gian đảo ngữ cảnh (**Context Switching**) → **Throughput chững lại hoặc tụt xuống**, còn **Latency dựng đứng theo đường cong chữ J (Hockey-stick curve)**!

---

## 3.2. Tại sao phải nhìn Percentile (`p50`, `p95`, `p99`) thay vì Trung bình (`avg`)?

Độ trễ web **không phân bố đối xứng hình chuông** mà có **đuôi dài lệch phải (Right-skewed / Long-tail Distribution)**: đa số request rất nhanh, nhưng một tỷ lệ nhỏ cực kỳ chậm.

- **`p50` (Median):** 50% số request nhanh hơn giá trị này — đại diện cho trải nghiệm "thông thường".
- **`p95`:** 95% số request nhanh hơn mốc này (5% chậm nhất nằm phía trên).
- **`p99`:** 99% số request nhanh hơn mốc này — phản ánh trực diện nhóm **1% chậm nhất**.

**3 lý do thực tế buộc phải đo theo `p99` (DDIA Ch.1):**
1. **Trung bình (`avg`) che giấu thảm họa:** Giả sử 99 request mất **10 ms**, 1 request dính DB lock mất **2,010 ms** → `avg` = **30 ms** (nhìn báo cáo rất đẹp!), nhưng `p99` = **2,010 ms** (1% người dùng đang chờ hơn 2 giây!).
2. **Những khách hàng giá trị nhất hay rơi vào `p99`:** Khách VIP mua nhiều nhất ở TechStore có giỏ hàng nặng nhất, lịch sử đơn hàng dài nhất, nên các câu query của họ chính là những câu chậm nhất nằm ở `p99`!
3. **Hiệu ứng khuếch đại Tail Latency (*Tail at Scale*):** Một trang chi tiết sản phẩm thường gọi **song song 20 API/microservice con** (giá, tồn kho, khuyến mãi, đánh giá, gợi ý...). Trang web chỉ render xong khi **API chậm nhất** trong 20 lời gọi trả về! Nếu mỗi API con có `1%` xác suất chậm (`> p99`), thì xác suất người dùng bị dính chậm khi mở trang là:
   $$P(\text{ít nhất 1 call chậm}) = 1 - 0.99^{20} \approx \mathbf{18.2\%}$$
   *(Gần 1/5 lượt tải trang bị chậm — `p99` của từng service con bị khuếch đại thành `~p82` ở đầu người dùng! Để giảm hiệu ứng này, các hệ lớn dùng **Hedged Requests**: quá `p95` ms chưa thấy hồi âm thì gửi thêm bản sao sang replica khác).*

---

## 3.3. SLI, SLO, SLA, Error Budget & Bảng "Số 9"

- **SLI (Service Level Indicator):** Chỉ số kỹ thuật đo được thực tế (ví dụ: *Tỷ lệ HTTP 2xx trong 30 ngày là `99.94%`*, *`p99` API `/products` là `180ms`*).
- **SLO (Service Level Objective):** Ngưỡng mục tiêu **nội bộ** đội kỹ thuật tự cam kết (ví dụ: `uptime >= 99.9%` và `p99 < 250ms` mỗi tháng).
  - **Error Budget (Ngân sách lỗi):** Phần dư `100% - SLO`. Với `SLO = 99.9%` trong 30 ngày, bạn có `43.8 phút` downtime cho phép. Nếu cả tháng mới mất `5 phút`, team còn dư `38.8 phút` Error Budget để mạnh dạn deploy tính năng mới; nếu đã cháy hết Error Budget → **tạm dừng ra tính năng mới**, tập trung vá lỗi & tăng độ ổn định!
- **SLA (Service Level Agreement):** Hợp đồng **pháp lý / thương mại** ký với khách hàng (vi phạm phải đền tiền/hoàn cước). **SLO luôn phải đặt khắt khe hơn SLA** (ví dụ SLA `99.9%` thì SLO nội bộ đặt `99.95%`) để có vùng đệm cảnh báo trước khi vi phạm hợp đồng.

**Bảng quy đổi Availability "Số 9" (thuộc lòng khi làm Bước 1 phỏng vấn):**

| Availability | Downtime / Năm | Downtime / Tháng (30d) | Downtime / Ngày | Mức độ hệ thống |
| :--- | :--- | :--- | :--- | :--- |
| **99% (2 số 9)** | 3.65 ngày | 7.31 giờ | 14.4 phút | Hệ thống nội bộ, MVP |
| **99.9% (3 số 9)** | 8.77 giờ | 43.8 phút | 1.44 phút | Web app / e-commerce tiêu chuẩn |
| **99.95%** | 4.38 giờ | 21.9 phút | 43.2 giây | Dịch vụ B2B quan trọng |
| **99.99% (4 số 9)** | 52.6 phút | 4.38 phút | 8.64 giây | Hệ thống thanh toán, nền tảng lớn |
| **99.999% (5 số 9)** | 5.26 phút | 26.3 giây | 0.86 giây | Viễn thông, hạ tầng lõi ngân hàng |

**Công thức tính Availability tổng của hệ thống nhiều thành phần:**
- **Phụ thuộc nối tiếp** (Web App **VÀ** Database cùng phải sống):
  $$A_{\text{total}} = A_{\text{app}} \times A_{\text{db}}$$
  *(Ví dụ: App `99.9%` nối với 1 DB `99.9%` → tổng chỉ còn `0.999 × 0.999 = 99.8%` — tụt mất một bậc!)*
- **Dự phòng song song** (Chạy 2 instance App độc lập, chỉ cần 1 cái sống):
  $$A_{\text{parallel}} = 1 - (1 - A)^2$$
  *(Ví dụ: 2 máy `99.9%` chạy song song → xác suất cả 2 chết cùng lúc là `0.001 × 0.001 = 0.000001` → Availability vọt lên **`99.9999%`**!)*
