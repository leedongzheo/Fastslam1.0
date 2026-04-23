Chắc chắn rồi! Để mã giả trông "chuẩn bài báo khoa học" và giống hệt phong cách của slide bạn gửi, chúng ta sẽ thay thế các hàm lập trình bằng các **ký hiệu toán học xác suất**.

Đây là thuật toán FastSLAM 1.0 (chính là bộ code Python của bạn) được viết lại hoàn toàn dưới góc độ toán học:

---

### **Algorithm 1: Standard RBPF for Map Learning (Your Code Implementation)**

**Require:**
$\quad S_{t-1}$, the sample set of the previous time step
$\quad z_t$, the most recent laser scan (consisting of $K$ beams $z_{t,k}$)
$\quad u_t$, the most recent odometry measurement
**Ensure:**
$\quad S_t$, the new sample set

$\quad S_t = \{\}$
$\quad \eta = 0$

$\quad$**for all** $s_{t-1}^{(i)} \in S_{t-1}$ **do**
$\quad\quad \langle x_{t-1}^{(i)}, w_{t-1}^{(i)}, m_{t-1}^{(i)} \rangle = s_{t-1}^{(i)}$

$\quad\quad$*// sample new pose (Odometry Motion Model)*
$\quad\quad x_t^{(i)} \sim p(x_t \mid x_{t-1}^{(i)}, u_t)$

$\quad\quad$*// update importance weights (Likelihood Field Sensor Model)*
$\quad\quad$*// (Lưu ý: Code của bạn ghi đè trọng số mới, thay vì nhân dồn $w_t = w_{t-1} \cdot p$)*
$\quad\quad w_t^{(i)} = p(z_t \mid m_{t-1}^{(i)}, x_t^{(i)})$
$\quad\quad \eta = \eta + w_t^{(i)}$
$\quad\quad S_t = S_t \cup \{ \langle x_t^{(i)}, w_t^{(i)}, m_{t-1}^{(i)} \rangle \}$
$\quad$**end for**

$\quad$*// normalize weights*
$\quad$**if** $\eta > 10^{-50}$ **then**
$\quad\quad$**for all** $s_t^{(i)} \in S_t$ **do**
$\quad\quad\quad w_t^{(i)} = w_t^{(i)} / \eta$
$\quad\quad$**end for**
$\quad$**else**
$\quad\quad$*// Kidnapped robot recovery*
$\quad\quad$**for all** $s_t^{(i)} \in S_t$ **do**
$\quad\quad\quad w_t^{(i)} = 1 / N$
$\quad\quad$**end for**
$\quad$**end if**

$\quad$*// compute effective number of particles*
$\quad N_{\text{eff}} = \frac{1}{\sum_{i=1}^N (w_t^{(i)})^2}$

$\quad$**if** $N_{\text{eff}} < T$ **then**
$\quad\quad S_t = \text{resample}(S_t)$
$\quad$**end if**

$\quad$*// map update (Throttled by distance)*
$\quad \hat{x}_t = \text{argmax}_{x_t^{(i)}} w_t^{(i)}$
$\quad$**if** $\| \hat{x}_t - \hat{x}_{\text{last}} \| > \Delta_{\text{linear}}$ **or** $| \hat{\theta}_t - \hat{\theta}_{\text{last}} | > \Delta_{\text{angular}}$ **then**
$\quad\quad$**for all** $s_t^{(i)} \in S_t$ **do**
$\quad\quad\quad m_t^{(i)} = \text{integrateScan}(m_{t-1}^{(i)}, x_t^{(i)}, z_t)$
$\quad\quad$**end for**
$\quad$**end if**

---

### Bảng đối chiếu Ký hiệu Toán học vs. Code Python của bạn:

Để bạn dễ dàng map (ánh xạ) giữa thuật toán hàn lâm này và code thực tế, đây là ý nghĩa của các dòng:

1.  **Motion Model (Dự đoán di chuyển):**
    * **Toán học:** $x_t^{(i)} \sim p(x_t \mid x_{t-1}^{(i)}, u_t)$
    * **Ý nghĩa:** Bốc ngẫu nhiên (ký hiệu $\sim$) vị trí mới $x_t$ dựa trên phân phối xác suất di chuyển từ vị trí cũ $x_{t-1}$ dưới tác động của lệnh bánh xe $u_t$.
    * **Code của bạn:** `p.pose = self.motion_model.sample(p.pose, delta)`

2.  **Sensor Model (Chấm điểm Lidar):**
    * **Toán học:** $w_t^{(i)} = p(z_t \mid m_{t-1}^{(i)}, x_t^{(i)})$
    * **Phân rã (Log-weight trong code):** Thực chất $p(z_t \mid \dots) = \prod_{k=1}^K p(z_{t,k} \mid \dots)$. Code của bạn tính $\exp\big(\sum \ln(p)\big)$ chính là phép nhân xác suất $\prod$ này.
    * **Code của bạn:** `p.weight = self.sensor_model.compute_weight(...)`

3.  **Map Update (Cập nhật bản đồ):**
    * **Toán học:** $m_t^{(i)} = \text{integrateScan}(m_{t-1}^{(i)}, x_t^{(i)}, z_t)$
    * **Ý nghĩa:** Tích hợp (integrate) dữ liệu Lidar $z_t$ vào bản đồ lưới cũ $m_{t-1}$ tại vị trí $x_t$ để ra bản đồ mới. (Trong bài báo khoa học, người ta giữ nguyên tên hàm `integrateScan` vì nó là một thuật toán chuẩn).
    * **Code của bạn:** `p.grid.update_with_scan(...)`

Cách viết này vừa đảm bảo độ chính xác của các định lý Bayes, vừa làm nổi bật bản chất xác suất (Probabilistic) của thuật toán Bộ lọc Hạt.
