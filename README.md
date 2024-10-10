# Kill Monsters Game - Build on AK Embedded Base Kit

## I. Giới thiệu

### 1.1 Phần cứng.

<p align="center"><img src="https://github.com/user-attachments/assets/e028e082-bb9b-48f5-b66c-7f73ef28f6ae" alt="AK Embedded Base Kit - STM32L151"></p>
<p align="center"><strong><em>Hình 1:</em></strong> AK Embedded Base Kit - STM32L151.</p>

Archery game là một tựa game chạy trên AK Embedded Base Kit. Được xây dựng nhằm mục đích giúp ta có thể tìm hiểu và thực hành về lập trình event – driven. Trong quá trình xây dựng nên archery game, ta sẽ hiểu thêm về cách thiết kế và ứng dụng UML, Task, Signal, Timer, Message, State-machine, …

KIT tích hợp LCD **OLED 1.3", 3 nút nhấn, và 1 loa Buzzer phát nhạc**, với các trang bị này thì đã đủ để học hệ thống event – driven thông qua thực hành thiết kế máy chơi game.

KIT cũng tích hợp **RS485**, **NRF24L01+**, và **Flash** lên đến 32MB, thích hợp cho prototype các ứng dụng thực tế trong hệ thống nhúng hay sử dụng như: truyền thông có dây, không dây wireless, các ứng dụng lưu trữ data logger,...

### 1.2 Mô tả trò chơi và đối tượng.

Phần mô tả sau đây về **“Kill monster game”**, giải thích cách chơi và cơ chế xử lý của trò chơi.

[Chèn menu game sau]

Trò chơi bắt đầu bằng màn hình **Menu game** với các lựa chọn sau: 
- **Archery Game:** Chọn vào để bắt đầu chơi game.
- **Setting:** Chọn vào để cài đặt các thông số của game.
- **Charts:** Chọn vào để xem top 3 điểm cao nhất đạt được.
- **Exit:** Thoát menu vào màn hình chờ.

[Chèn màn hình game lúc chơi game sau]

#### 1.2.1 Các đối tượng (Object) trong game:
|Đối tượng|Tên đối tượng|Mô tả|
|---|---|---|
|**Súng**|Archery|Di chuyển lên/xuống để chọn vị trí bắn ra đạn.|
|**Đạn**|Bullet|Bắn ra từ súng, dùng để tiêu diệt quái vật.|
|**Vụ nổ**|Bang|Hiệu ứng xuất hiện khi quái vật bị hạ.|
|**Ranh giới**|Border|Vùng an toàn phải bảo vệ không cho quái vật tiến tới.|
|**Quái vật**|Meteoroid|Vật thể bay về phía súng với tốc độ tăng dần, có khả năng phá hủy ranh giới.|
|**Quà**|Gift|Vật thể bay về phía súng, có khả năng tiêu diệt quái vật đang tiến tới.|

**(*)** Trong phần còn lại của tài liệu sẽ dùng tên của các đối tượng để đề cập đến đối tượng.

#### 1.2.2 Cách chơi game.
- Trong trò chơi này bạn sẽ điều khiển Archery, di chuyển **lên/xuống** bằng hai nút **[Up]/[Down]**, để chọn vị trí bắn ra Bullet.
- Khi nhấn nút **[Mode]** Bullet sẽ được bắn ra, nhằm phá hủy các Monster đang bay đến.
Mục tiêu trò chơi là kiếm được càng nhiều điểm càng tốt, trò chơi sẽ kết thúc khi các Monster chạm vào Border.
- Ngoài ra, khi nhặt các Gift bằng cách bắn trúng Bullet thì sẽ được tăng thêm 1 Bullet dữ trữ hoặc có thể bắn ra 2 Bullet cùng 1 lúc trong 5 giây.

#### 1.2.3 Cơ chế hoạt động:
- **Cách tính điểm:** Điểm được tính bằng số lượng Monster bị phá hủy. Mỗi Monster  bị phá hủy tương ứng với 10 điểm. Số điểm tích lũy được sẽ hiển thị ở góc dưới bên phải màn hình.
- **Độ khó:** Mỗi khi tích lũy được 100 điểm, tốc độ bay của Monster sẽ tăng lên một cấp độ. Đồng thời lượng đạn cần bắn để tiêu diệt chúng cũng tăng lên. Độ khó ban đầu có thể cài đặt trong phần **Setting**.

- **Giới hạn của Arrow:** Khi bắn thì số lượng Arrow hiện có sẽ giảm đi tương ứng số lượng Arrow đang bay, nếu Arrow hiện có giảm về "0" thì không thể bắn được và sẽ có âm thanh báo. Số lượng Arrow hiện có sẽ được hồi lại khi phá hủy được Meteoroid hoặc Arrow bay hết màn hình game. Số lượng Arrow được hiển thị ở góc dưới bên trái màn hình và có thể thay đổi trong phần **Setting**.

- **Vật phảm quà:** Để giúp người chơi tiêu diệt Monsters nhanh hơn, vật phẩm quà giúp người chơi tiêu diệt Monsters dang tiến lại gần Border.

- **Kết thúc trò chơi:** Khi Meteoroid chạm vào Border, trò chơi sẽ kết thúc. Các đối tượng sẽ được reset và số điểm sẽ được lưu. Bạn sẽ vào màn hình “Game Over” với 3 lựa chọn là:
  - **Restart:** chơi lại.
  - **Charts:** vào xem bảng xếp hạng.
  - **Home:** về lại menu game.

[Chèn màn hình game over sau]

## II. Thiết kế - KILL MONSTERS GAME.
**Sơ đồ trình tự** được sử dụng để mô tả trình tự của các Message và luồng tương tác giữa các đối tượng trong một hệ thống.
<p align="center"><img src="https://github.com/user-attachments/assets/115439bb-fe61-42e9-b97a-b5d203bd63b4" alt="Mô tả hình ảnh"></p>
<p align="center"><strong><em>Hình 5:</em></strong> The sequence diagram.</p>

### Ghi chú:
**SCREEN_ENTRY:** Cài đặt các thiết lập ban đầu cho đối tượng trong game.
- **Level setup:** Thiết lập thông số cấp độ cho game.
- **AR_GAME_ARCHERY_SETUP:** Thiết lập thông số ban đầu cho đối tượng Archery
- **AR_GAME_ARROW_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Arrow
- **AR_GAME_METEOROID_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Meteoroid
- **AR_GAME_BANG_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Bang
- **AR_GAME_BORDER_SETUP:** Thiết lập thông số ban đầu cho đối tượng Border
- **Setup timer - Time tick:** Khởi tạo Timer - Time tick cho game.
- **STATE (GAME_ON):** Cập nhật trạng thái game -> GAME_ON

**GAME PLAY:** Quá trình hoạt động của game.

**GAME PLAY - Normal:** Game hoạt động ở trạng thái bình thường.
- **AR_GAME_TIME_TICK:** Signal do Timer - Time tick gửi đến.
- **AR_GAME_ARCHERY_UPDATE:** Cập nhật trạng thái Archery.
- **AR_GAME_ARROW_RUN:** Cập nhật di chuyển của các Arrow theo thời gian.
- **AR_GAME_METEOROID_RUN:** Cập nhật di chuyển của các Meteoroid theo thời gian.
- **AR_GAME_METEOROID_DETONATOR:** Kiểm tra các Meteoroid có bị Arrow phá hủy.
- **AR_GAME_BANG_UPDATE:** Cập nhật hoạt ảnh vụ nổ theo thời gian
- **AR_GAME_BORDER_UPDATE:** Kiểm tra số điểm hiện tại để cập nhật tăng độ khó game.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra Meteoroid chạm vào Border. Nếu chạm vào thì gửi Signal - **AR_GAME_RESET** đến **Screen**.

**GAME PLAY - Action:** Game hoạt động ở trạng thái có tác động của các nút nhấn.
- **AR_GAME_ARCHERY_UP:** Player nhấn nút **[Up]** điều khiển Archery di chuyển lên.
- **AR_GAME_ARCHERY_DOWN:** Player nhấn nút **[Down]** điều khiển Archery di chuyển xuống.
- **AR_GAME_ARROW_SHOOT:** Player nhấn nút **[Mode]** điều khiển Archery bắn Arrow ra.

**RESET GAME:** Quá trình cài đặt lại các thông số trước khi thoát game.
- **STATE (GAME_OVER):** Cập nhật trạng thái game -> GAME_OVER
- **AR_GAME_RESET:** Signal cài đặt lại game do Border gửi đến.
- **AR_GAME_ARCHERY_RESET:** Cài đặt lại đối tượng Archery trước khi thoát.
- **AR_GAME_ARROW_RESET:** Cài đặt lại đối tượng Arrow trước khi thoát.
- **AR_GAME_METEOROID_RESET:** Cài đặt lại đối tượng Meteoroid trước khi thoát.
- **AR_GAME_BANG_RESET:** Cài đặt lại đối tượng Bang trước khi thoát.
- **AR_GAME_BORDER_RESET:** Cài đặt lại đối tượng Border trước khi thoát.
- **Save and reset Score:** Lưu số điểm hiện tại và Cài đặt lại.
- **Timer remove - Timer tick:** Xóa Timer - Time tick
- **Setup timer - Timer exit:** Tạo 1 timer one shot để thoát game. Nhằm tạo ra một khoảng delay cho người chơi có thể nhận thức được là mình đã game over trước khi chuyển sang màn hình thông báo game over.

**EXIT:** Thoát khỏi game và chuyển sang màn hình Game Over.
- **AR_GAME_EXIT:** Signal do Timer exit gửi đến.
- **STATE (GAME_OFF):** Cập nhật trạng thái game -> GAME_OFF
- **Change the screen - SCREEN_TRAN(scr_game_over_handle, &scr_game_over):** Chuyển màn hình sang màn hình Game Over.

### 2.2 Chi tiết
