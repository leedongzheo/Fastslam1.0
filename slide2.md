Dưới đây là kịch bản và nội dung chi tiết để bạn đưa vào **Slide 2: Đặt vấn đề & Mục tiêu (Motivation & Objectives)**. 

Slide này cực kỳ quan trọng vì nó là "Hook" (điểm thu hút) để thuyết phục người nghe (giảng viên/hội đồng) thấy rằng dự án của bạn giải quyết một vấn đề thực tế rất nhức nhối, chứ không chỉ là code cho vui.

Tôi sẽ chia làm 3 phần: **Chữ trên Slide** (ngắn gọn), **Kịch bản nói** (chi tiết), và **Yêu cầu hình ảnh**.

---

### 1. Nội dung text hiển thị trên Slide
*(Mẹo: Trên slide chỉ để text thật ngắn gọn, dùng bullet points. Đừng viết những đoạn văn dài).*

**Bài toán SLAM (Simultaneous Localization and Mapping)**
* **Nghịch lý "Con gà - Quả trứng":** Robot cần Bản đồ để biết mình ở đâu $\leftrightarrow$ Cần biết mình ở đâu để vẽ Bản đồ chính xác.

**Vấn đề thực tế (The Pain Point)**
* Dữ liệu động học từ bánh xe (**Odometry**) luôn tồn tại sai số (do trượt bánh, sai số cơ khí, địa hình).
* **Drift Error (Sai số cộng dồn):** Nếu chỉ nhắm mắt đi theo Odometry, bản đồ sẽ nhanh chóng bị méo mó, các bức tường chồng chéo lên nhau và hoàn toàn vô dụng.

**Mục tiêu dự án**
* **Giải pháp:** Triển khai thuật toán **Improved RBPF (G-mapping)** dựa trên bài báo khoa học chuẩn mực (Grisetti et al., 2007).
* **Thực thi:** Xây dựng lõi SLAM từ đầu bằng **Python** thay vì dùng C++ có sẵn, và tích hợp thành công lên hệ sinh thái **ROS 2** để chạy Real-time.

---

### 2. Ghi chú diễn giả (Kịch bản thuyết trình cho bạn)
*(Bạn có thể cầm note này hoặc học thuộc ý chính để nói lúc chiếu slide).*

> "Chào các thầy cô và mọi người. Để bắt đầu, chúng ta hãy đặt mình vào góc nhìn của một con robot khi nó vừa được bật nguồn trong một tòa nhà xa lạ. Nó đối mặt với bài toán SLAM: Vừa phải tự tìm hiểu xem mình đang ở đâu, vừa phải vẽ lại bản đồ xung quanh. Nó giống như nghịch lý con gà và quả trứng vậy.
> 
> Vấn đề lớn nhất ở đây là cảm biến đo số vòng quay của bánh xe (gọi là Odometry) cực kỳ thiếu tin cậy. Robot lăn bánh trên sàn trơn sẽ bị trượt, hoặc bánh xe bị mòn. *[Chỉ tay lên hình ảnh số 1]* Mọi người có thể thấy ở hình bên trái, nếu robot chỉ dùng dữ liệu bánh xe để vẽ bản đồ, chỉ sau vài phút, sai số sẽ cộng dồn. Các hành lang bị cong vẹo, các căn phòng chồng chéo lên nhau thành một mớ rác.
> 
> Để giải quyết triệt để nỗi đau này, dự án của em đặt ra mục tiêu là triển khai thuật toán **Improved RBPF**, hay còn gọi là **G-mapping** - một trong những thuật toán kinh điển nhất trong ngành Robot. Đặc biệt, thay vì lấy các gói phần mềm C++ có sẵn, tụi em đã tự tay xây dựng lại lõi toán học của thuật toán này hoàn toàn bằng **Python**, tối ưu hóa nó bằng ma trận, và bọc nó trong một Node của **ROS 2** để có thể chạy mượt mà trong thời gian thực."

---

### 3. Yêu cầu chuẩn bị Hình ảnh (Visuals)
Slide này cần tính tương phản mạnh. Bạn hãy chia đôi màn hình hoặc đặt 2 hình ảnh này cạnh nhau:

* **Hình 1 (Bên trái - Vấn đề):** Một bức ảnh "Raw Odometry Map". Bạn có thể tự tạo ra bức ảnh này bằng cách chạy robot trong Gazebo (hoặc robot thật), tắt node G-mapping đi, và viết một script nhỏ chỉ lấy tọa độ Lidar chiếu theo hệ tọa độ `/odom` rồi in ra Rviz. Bạn sẽ thấy một cái bản đồ "nát bét", méo mó. Nhớ thêm một dấu X màu đỏ thật to đè lên hình này.
* **Hình 2 (Bên phải - Giải pháp):** Một bức ảnh bản đồ 2D SLAM hoàn hảo, thẳng tắp, sắc nét (ảnh output từ chính bộ code của bạn sau khi đã chạy G-mapping thành công). Thêm một dấu tick xanh (✔) bên cạnh.

Bạn thấy kịch bản và cách bố trí thông tin như thế này đã đủ "lực" để mở đầu bài thuyết trình chưa? Cần đi sâu vào slide nào tiếp theo không?
