## I. Giới thiệu

Kill monster game là một tựa game chạy trên AK Embedded Base Kit. Được xây dựng nhằm mục đích giúp ta có thể tìm hiểu và thực hành về lập trình event – driven. Trong quá trình xây dựng nên gun game, ta sẽ hiểu thêm về cách thiết kế và ứng dụng UML, Task, Signal, Timer, Message, State-machine, …

### 1.1 Phần cứng.

<p align="center"><img src="https://github.com/user-attachments/assets/e028e082-bb9b-48f5-b66c-7f73ef28f6ae" alt="AK Embedded Base Kit - STM32L151"></p>
<p align="center"><strong><em>Hình 1:</em></strong> AK Embedded Base Kit - STM32L151.</p>

KIT tích hợp LCD **OLED 1.3", 3 nút nhấn, và 1 loa Buzzer phát nhạc**, với các trang bị này thì đã đủ để học hệ thống event – driven thông qua thực hành thiết kế máy chơi game.

KIT cũng tích hợp **RS485**, **NRF24L01+**, và **Flash** lên đến 32MB, thích hợp cho prototype các ứng dụng thực tế trong hệ thống nhúng hay sử dụng như: truyền thông có dây, không dây wireless, các ứng dụng lưu trữ data logger,...

### 1.2 Mô tả trò chơi và đối tượng.

Phần mô tả sau đây về **“Kill monster game”**, giải thích cách chơi và cơ chế xử lý của trò chơi.

<p align="center"><img src="https://github.com/user-attachments/assets/4bb837f7-7427-4a96-b94c-8a4590cad417" alt="MENU TRÒ CHƠI"></p>
<p align="center"><strong><em>Hình 2:</em></strong> Menu game.</p>

Trò chơi bắt đầu bằng màn hình **Menu game** với các lựa chọn sau: 
- **Kill Monster Game:** Chọn vào để bắt đầu chơi game.
- **Setting:** Chọn vào để cài đặt các thông số của game.
- **Charts:** Chọn vào để xem top 3 điểm cao nhất đạt được.
- **Exit:** Thoát menu vào màn hình chờ.

<p align="center"><img src="https://github.com/user-attachments/assets/d2625c61-7c25-4f12-b9c1-14cd66517b6e" alt="MÀN HÌNH GAME"></p>
<p align="center"><strong><em>Hình 3:</em></strong>Màn hình game.</p>

#### 1.2.1 Các đối tượng (Object) trong game.
|Đối tượng|Tên đối tượng|Mô tả|
|---|---|---|
|**Súng**|Gun|Di chuyển lên/xuống để chọn vị trí bắn ra đạn.|
|**Đạn**|Bullet|Bắn ra từ súng, dùng để tiêu diệt quái vật.|
|**Vụ nổ**|Bang|Hiệu ứng xuất hiện khi quái vật bị hạ.|
|**Ranh giới**|Border|Vùng an toàn phải bảo vệ không cho quái vật tiến tới.|
|**Quái vật**|Monster|Vật thể bay về phía súng với tốc độ và độ khó tăng dần, có khả năng phá hủy ranh giới.|
|**Quà**|Gift|Vật thể bay về phía súng, khi bị bắn trúng sẽ xóa bỏ hết quái trên màn hình.|
|**Bom**|boom|Vật thể bay về phía súng, khi bị bắn trúng thì người chơi sẽ thua.|

**(*)** Trong phần còn lại của tài liệu sẽ dùng tên của các đối tượng để đề cập đến đối tượng.

#### 1.2.2 Cách chơi game.
- Trong trò chơi này bạn sẽ điều khiển Gun, di chuyển **lên/xuống** bằng hai nút **[Up]/[Down]**, để chọn vị trí bắn ra Bullet.
- Khi nhấn nút **[Mode]** Bullet sẽ được bắn ra, nhằm phá hủy các Monster đang bay đến.
Mục tiêu trò chơi là kiếm được càng nhiều điểm càng tốt, trò chơi sẽ kết thúc khi các Monster chạm vào Border.
- Ngoài ra, khi nhặt các Gift bằng cách dùng Bullet bắn trúng thì sẽ tiêu diệt hết Monster trong màn hình.

#### 1.2.3 Cơ chế hoạt động.
- **Cách tính điểm:** Điểm được tính bằng số lượng Monster bị phá hủy. Mỗi Monster bị tiêu diệt tương ứng với 10 điểm. Số điểm tích lũy được sẽ hiển thị ở góc dưới bên phải màn hình.

- **Độ khó:** Mỗi khi tích lũy được 100 điểm, tốc độ bay của Monster sẽ tăng lên một cấp độ. Đồng thời, xuất hiện thêm các Monster mới với lượng đạn cần bắn để tiêu diệt chúng là 2 viên. Độ khó ban đầu có thể cài đặt trong phần **Setting**.

- **Giới hạn của Bullet:** Khi bắn thì số lượng Bullet hiện có sẽ giảm đi tương ứng số lượng Bullet đang bay, nếu Bullet hiện có giảm về "0" thì không thể bắn được và sẽ có âm thanh báo. Số lượng Bullet hiện có sẽ được hồi lại khi tiêu diệt được Monster hoặc Bullet bay hết màn hình game. Số lượng Bullet được hiển thị ở góc dưới bên trái màn hình và có thể thay đổi trong phần **Setting**.

- **Vật phẩm quà:** Để giúp người chơi tiêu diệt Monster nhanh hơn, vật phẩm quà giúp người chơi tiêu diệt Monster dang tiến lại gần Border.

- **Boom:** Để tăng thêm thử thách cho trò chơi, ngoài việc tiêu diệt Monster, người chơi cần cẩn thận tránh bắn trúng Boom. Nếu không may bắn trúng, trò chơi sẽ kết thúc ngay lập tức.

- **Kết thúc trò chơi:** Khi Monster chạm vào Border và khi bán trúng Boom, trò chơi sẽ kết thúc. Các đối tượng sẽ được reset và số điểm sẽ được lưu. Màn hình sẽ hiển thị “Game Over” với 3 lựa chọn là:
  - **Restart:** chơi lại.
  - **Charts:** vào xem bảng xếp hạng.
  - **Home:** về lại menu game.
<p align="center"><img src="https://github.com/user-attachments/assets/fa56b5cb-73a6-4b3f-80fc-afb0207ec82d" alt="MÀN HÌNH GAME OVER"></p>
<p align="center"><strong><em>Hình 3:</em></strong> MÀN HÌNH GAME OVER.</p>

## II. Thiết kế - KILL MONSTER GAME.
**Sơ đồ trình tự** được sử dụng để mô tả trình tự của các Message và luồng tương tác giữa các đối tượng trong một hệ thống.
<p align="center"><img src="https://github.com/user-attachments/assets/787d7933-8231-4383-9e66-352d5cbce7ee" alt="Mô tả hình ảnh"></p>
<p align="center"><strong><em>Hình 5:</em></strong> The sequence diagram.</p>

### Ghi chú:
**SCREEN_ENTRY:** Cài đặt các thiết lập ban đầu cho đối tượng trong game.
- **Level setup:** Thiết lập thông số cấp độ cho game.
- **AR_GAME_ARCHERY_SETUP:** Thiết lập thông số ban đầu cho đối tượng Gun.
- **AR_GAME_ARROW_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Bullet.
- **AR_GAME_MONSTER_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Monster.
- **AR_GAME_BANG_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Bang.
- **AR_GAME_BORDER_SETUP:** Thiết lập thông số ban đầu cho đối tượng Border.
- **Setup timer - Time tick:** Khởi tạo Timer - Time tick cho game.
- **STATE (GAME_ON):** Cập nhật trạng thái game -> GAME_ON

**GAME PLAY:** Quá trình hoạt động của game.

**GAME PLAY - Normal:** Game hoạt động ở trạng thái bình thường.
- **AR_GAME_TIME_TICK:** Signal do Timer - Time tick gửi đến.
- **AR_GAME_ARCHERY_UPDATE:** Cập nhật trạng thái Gun.
- **AR_GAME_ARROW_RUN:** Cập nhật di chuyển của các Bullet theo thời gian.
- **AR_GAME_MONSTER_RUN:** Cập nhật di chuyển của các Monster theo thời gian.
- **AR_GAME_MONSTER_DETONATOR:** Kiểm tra các Monster có bị Bullet phá hủy.
- **AR_GAME_BANG_UPDATE:** Cập nhật hoạt ảnh vụ nổ theo thời gian.
- **AR_GAME_BORDER_UPDATE:** Kiểm tra số điểm hiện tại để cập nhật tăng độ khó game.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra Monster chạm vào Border. Nếu chạm vào thì gửi Signal - **AR_GAME_RESET** đến **Screen**.

**GAME PLAY - Action:** Game hoạt động ở trạng thái có tác động của các nút nhấn.
- **AR_GAME_ARCHERY_UP:** Player nhấn nút **[Up]** điều khiển Gun di chuyển lên.
- **AR_GAME_ARCHERY_DOWN:** Player nhấn nút **[Down]** điều khiển Bullet di chuyển xuống.
- **AR_GAME_ARROW_SHOOT:** Player nhấn nút **[Mode]** điều khiển Gun bắn Bullet ra.

**RESET GAME:** Quá trình cài đặt lại các thông số trước khi thoát game.
- **STATE (GAME_OVER):** Cập nhật trạng thái game -> GAME_OVER.
- **AR_GAME_RESET:** Signal cài đặt lại game do Border gửi đến.
- **AR_GAME_ARCHERY_RESET:** Cài đặt lại đối tượng Gun trước khi thoát.
- **AR_GAME_ARROW_RESET:** Cài đặt lại đối tượng Bullet trước khi thoát.
- **AR_GAME_MONSTER_RESET:** Cài đặt lại đối tượng Monster trước khi thoát.
- **AR_GAME_BANG_RESET:** Cài đặt lại đối tượng Bang trước khi thoát.
- **AR_GAME_BORDER_RESET:** Cài đặt lại đối tượng Border trước khi thoát.
- **Save and reset Score:** Lưu số điểm hiện tại và Cài đặt lại.
- **Timer remove - Timer tick:** Xóa Timer - Time tick.
- **Setup timer - Timer exit:** Tạo 1 timer one shot để thoát game. Nhằm tạo ra một khoảng delay cho người chơi có thể nhận thức được là mình đã game over trước khi chuyển sang màn hình thông báo game over.

**EXIT:** Thoát khỏi game và chuyển sang màn hình Game Over.
- **AR_GAME_EXIT:** Signal do Timer exit gửi đến.
- **STATE (GAME_OFF):** Cập nhật trạng thái game -> GAME_OFF
- **Change the screen - SCREEN_TRAN(scr_game_over_handle, &scr_game_over):** Chuyển màn hình sang màn hình Game Over.

### 2.2 Chi tiết.

Sau khi xác định được các đối tượng trong game, tiếp theo liệt kê ra các thuộc tính, các task, các signal và bitmap mà trong game sẽ sử dụng tới.
Việc liệt kê càng chi tiết thì việc làm game diễn ra càng nhanh và tạo tình rõ ràng minh bạch cho phần tài nguyên giúp phần code game diễn ra suông sẻ hơn.

#### 2.2.1 Thuộc tính đối tượng.
Việc liệt kê các thuộc tính của đối tượng trong game có các tác dụng quan trọng sau:
- Giúp xác định rõ thông tin về đối tượng trong game.
- Giúp xác định cấu trúc dữ liệu phù hợp để lưu trữ thông tin của đối tượng.
- Khi bạn xác định trước các thuộc tính cần thiết, bạn giảm thiểu khả năng bỏ sót hoặc nhầm lẫn trong việc xử lý và sử dụng các thuộc tính.

**Trạng thái** của một đối tượng được biểu diễn bởi các **thuộc tính**. Trong trò chơi này các đối tượng có các thuộc tính cụ thể là:
- **visible:** Quy định hiển thị, ẩn/hiện của đối tượng.
- **x, y:** Quy định vị trí của đối tượng trên màn hình.
- **action_image:** Quy định hoạt ảnh tạo animation.

Ví dụ:

    typedef struct {
        bool visible;
        uint32_t x, y;
        uint8_t action_image;
        int8_t direction;
        uint8_t hit_count;
        } ar_game_monster_t;
        extern ar_game_monster_t monster[NUM_MONSTER + NUM_MONSTER_III + GIFT + BOSS];

**Áp dụng struct cho các đối tượng:**
|struct|Các biến|
|------|--------|
|ar_game_archery_t|gun|
|ar_game_arrow_t|bullet[MAX_NUM_ARROW]|
|ar_game_bang_t|bang[NUM_BANG]|
|ar_game_border_t|border|
|ar_game_monster_t|monster[NUM_MONSTER]|

**(*)** Các đối tượng có số lượng nhiều thì sẽ được khai báo dạng mảng.

**Các biến quan trọng:**
- **ar_game_score:** Điểm của trò chơi.
- **ar_game_status:** Trạng thái trò chơi.
  - GAME_OFF: Tắt.
  - GAME_ON: Bật.
  - GAME_OVER: Đã thua.

- **ar_game_setting_t** settingsetup : Cấu hình cấp độ của trò chơi.
  - settingsetup.silent : Bật/tắt chế độ im lặng.
  - settingsetup.num_arrow : Cấu hình số lượng đạn.
  - settingsetup.arrow_speed : Cấu hình tốc độ đạn
  - settingsetup.monster_speed : Cấu hình tốc độ của quái vật.

#### 2.2.2 Task
Trong lập trình event-driven, task là một yếu tố quan trọng giúp quản lý các sự kiện, luồng điều khiển, và đồng bộ hóa trong các hệ thống xử lý theo sự kiện, là một đơn vị độc lập đảm nhiệm một nhóm công việc nhất định. Khi hệ thống scheduler tìm thấy message liên quan đến task trong hàng đợi, hệ thống sẽ gọi hàm thực thi của task để xử lý message được gửi đến. Một số tác dụng quan trọng của task:
- **Xử lý sự kiện:** Task được sử dụng để xử lý các message được bắn đến khi có sự kiện xảy ra. Mỗi task có thể được liên kết với một sự kiện cụ thể, và khi hệ thống phát hiện sự kiện này, nó sẽ kích hoạt task tương ứng để xử lý sự kiện đó.
- **Đồng bộ hóa:** Task đóng vai trò quan trọng trong việc đồng bộ hóa hoạt động của hệ thống, đảm bảo rằng các tác vụ được thực thi theo trình tự hợp lý và không gây ra xung đột tài nguyên. Cơ chế này giúp việc xử lý sự kiện diễn ra tuần tự, tránh tình trạng tranh chấp tài nguyên hoặc xử lý không đồng bộ, từ đó duy trì sự ổn định và hiệu quả của hệ thống.
- **Quản lý luồng điều khiển:** Task giúp quản lý luồng sự kiện trong một hệ thống phức tạp. Khi có nhiều sự kiện xảy ra, task sẽ xác định thứ tự và cách thức xử lý các sự kiện này, giúp hệ thống hoạt động mượt mà và có tổ chức hơn.
- **Tách biệt logic:** Task giúp tách biệt rõ ràng các phần logic xử lý sự kiện, điều này làm cho mã nguồn dễ hiểu và dễ bảo trì hơn. Mỗi task chỉ đảm nhận một nhóm công việc nhất định, giúp cho việc quản lý sự kiện trở nên trực quan, điều này giúp Source code rõ ràng, dễ đọc hơn.
- **Phân cấp nhiệm vụ:** Các task trong hệ thống có thể được phân cấp theo mức độ ưu tiên, gọi là task level, giúp sắp xếp thứ tự xử lý các message trong hàng đợi một cách hợp lý. Nhờ đó, những công việc quan trọng sẽ được xử lý trước, trong khi các công việc ít quan trọng hơn sẽ được thực hiện sau. Trong game các task level của game đều là 4 và task nào được gọi trước sẽ được ưu tiên xử lý trước, đảm bảo trải nghiệm mượt mà.

<p align="center"><img src="https://github.com/user-attachments/assets/81b08014-93b5-48f4-8449-1af2d77b2eb4" alt="kill monster tasks design" width="720"/></p>
<p align="center"><strong><em>Hình 6:</em></strong> Bảng Task của các đối tượng.</p>

#### 2.2.3 Message & Signal
**Message** được chia làm 2 loại chính, Message chỉ chứa Signal và Message vừa chứa Signal và Data. **Message** tương đương với **Signal**

<p align="center"><img src="https://github.com/user-attachments/assets/c66362df-9ba1-4d06-b3c9-cf2b9ecfacc1" alt="kill monster signals design" width="720"/></p>
<p align="center"><strong><em>Hình 7:</em></strong> Bảng Signal của từng Task.</p>

**(*)** Tác dụng của các Signal trong game: xem tại Ghi chú - Hình 5.

## III. Hướng dẫn chi tiết code trong đối tượng.
### 3.1 Gun.
**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/c24adb44-dcf2-4822-b823-709def12564e" width="640"/></p>
<p align="center"><strong><em>Hình 8:</em></strong> Gun sequence.</p>

**Tóm tắt nguyên lý:** Gun sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Button. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Gun như vị trí và hình ảnh.
- **Giai đoạn 2:** Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal cập nhật cho Gun mỗi 100ms để cập nhật trạng thái hiện tại của Gun.
  - Hành động: Button gửi Signal di chuyển lên/xuống cho Gun mỗi khi nhấn nút.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Gun trước khi thoát game.

**Code:**

Trong code bạn có thể dùng macro để thay thế hàm void trong nhiều trường hợp.

    #define TEN_DOAN_CODE()
    do { \
        /*code*/ \
    } while(0);

Khai báo: Thư viện, struct và biến.

    #include "ar_game_archery.h"

    ar_game_archery_t archery;
    static uint32_t archery_y = AXIS_Y_ARCHERY;

ar_GAME_ARCHERY_SETUP() là một macro được dùng định nghĩa để cài đặt trạng thái ban đầu của trò chơi tiêu diệt quái vật. Nó đặt các giá trị của biến gun và sử dụng các hằng số được định nghĩa trước đó để thiết lập tọa độ, màu sắc và hình ảnh của súng.

    #define ar_GAME_ARCHERY_SETUP() \
    do { \
        archery.x = AXIS_X_ARCHERY; \
        archery.y = AXIS_Y_ar; \
        archery.visible = WHITE; \
        archery.action_image = 1; \
    } while (0);

AR_GAME_ar_UP() là một macro được sử dụng để di chuyển súng lên trên. Nó giảm giá trị của archery_y bằng một giá trị STEP_ar_AXIS_Y và kiểm tra nếu giá trị mới bằng 0, nó được gán lại là 10.

    #define AR_GAME_ar_UP() \
    do { \
        archery_y -= STEP_ar_AXIS_Y; \
        if (archery_y == 0) {archery_y = 10;} \
    } while(0);

ar_GAME_ar_DOWN() là một macro được sử dụng để di chuyển súng xuống dưới. Nó tăng giá trị của archery_y bằng một giá trị STEP_ar_AXIS_Y và kiểm tra nếu giá trị mới vượt quá 50, nó được gán lại là 50.

    #define ar_GAME_ar_DOWN() \
    do { \
        archery_y += STEP_ar_AXIS_Y; \
        if (archery_y > 50) {archery_y = 50;} \
    } while(0);

AR_GAME_ar_RESET() là một macro được sử dụng để đặt lại trạng thái ban đầu của trò chơi tiêu diệt quái vật. Nó đặt lại giá trị của archery, archery_y và làm cho súng trở nên không hiển thị.

    #define AR_GAME_ar_RESET() \
    do { \
        archery.x = AXIS_X_ar; \
        archery.y = AXIS_Y_ar; \
        archery.visible = BLACK; \
        archery_y = AXIS_Y_ar; \
    } while(0);

Hàm ar_game_archery_handle() là một hàm xử lý các thông điệp (messages) liên quan đến trò chơi cung bắn. Nó chứa một câu lệnh switch-case để xử lý các thông điệp khác nhau. Các thông điệp được gửi đến hàm này thông qua một tham số msg có kiểu dữ liệu ak_msg_t. Mỗi case trong switch-case xử lý một thông điệp cụ thể.

    void ar_game_archery_handle(ak_msg_t* msg) {
        switch (msg->sig) {
        case AR_GAME_ar_SETUP: {
            APP_DBG_SIG("AR_GAME_ar_SETUP\n");
            AR_GAME_ar_SETUP();
        }
            break;

        case AR_GAME_ar_UPDATE: {
            APP_DBG_SIG("AR_GAME_ar_UPDATE\n");
            archery.y = ar_y;
        }
            break;

        case AR_GAME_ARCHERY_UP: {
            APP_DBG_SIG("AR_GAME_ARCHERY_UP\n");
            AR_GAME_ARCHERY_UP();
        }
            break;

        case AR_GAME_ARCHERY_DOWN: {
            APP_DBG_SIG("AR_GAME_ARCHERY_DOWN\n");
            AR_GAME_ARCHERY_DOWN();
        }
            break;

        case AR_GAME_ARCHERY_RESET: {
            APP_DBG_SIG("AR_GAME_ARCHERY_RESET\n");
            AR_GAME_ARCHERY_RESET();
        }
            break;

        default:
            break;
        }
    }

### 3.2 Bullet.

**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/ef3686b0-a59f-4e75-ae14-420a5357ffc0" alt="bullet sequence" width="640"/></p>
<p align="center"><strong><em>Hình 9:</em></strong> Bullet sequence.</p>

**Tóm tắt nguyên lý:** Bullet sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Button. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Bullet. Tất cả Bullet vào trạng thái ẩn, không hiển thị trên màn hình.
- **Giai đoạn 2:** Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal di chuyển cho Bullet mỗi 100ms để tăng trạng thái của Bullet tạo sự di chuyển cho Bullet.
  - Hành động: Button gửi Signal bắn đạn cho Bullet mỗi khi nhấn nút. Bullet sẽ sẽ kiểm tra số đạn hiện có và nếu còn thì sẽ cập nhật trạng thái để bắn đạn ra tại vị trí hiện tại của Gun.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Bullet trước khi thoát game.

**Code:** Tương tự Gun ở trên.

### 3.3 Bang.

**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/4caa0f6c-85ab-4e45-9c1d-0ba4429fca3e" alt="bang sequence" width="640"/></p>
<p align="center"><strong><em>Hình 10:</em></strong> Bang sequence.</p>

**Tóm tắt nguyên lý:** Bang sẽ nhận Signal thông được gửi từ Screen. Quá trình xử lý của đối tượng phân làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Bang. Cho tất cả các bang về trạng thái lặn, không xuất hiện trên màn hình.
- **Giai đoạn 2:** Chơi game, Vụ nổ chỉ xuất sau khi Monster bị phá hủy. Vụ nổ bao gồm các hoạt ảnh được cập nhật sau mỗi 100ms sau 3 hoạt ảnh thì sẽ tự reset.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Bullet trước khi thoát game.

**Code:** Tương tự Gun ở trên.

### 3.4 Border.

**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/00231537-dbbb-4ee3-93dd-b7ab88ebbba1" alt="border sequence" width="640"/></p>
<p align="center"><strong><em>Hình 11:</em></strong> Border sequence.</p>

**Tóm tắt nguyên lý:** Border là 1 đối tượng bất động trong game. Có nhiệm vụ update level khi đến mốc điểm quy định và kiểm tra game over.
- **Giai đoạn 1:** Bắt đầu game, cài đặt thông số vị trí và hiển thị của Border.
- **Giai đoạn 2:** Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms
  - Kiểm tra số điểm nếu số điểm thêm 200 thì tăng tốc độ của Monster.
  - Kiểm tra vị trí của các Monster nếu Monster chạm vào Border thì gửi tín hiệu Reset đến Screen.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Border trước khi thoát game.

**Code:** Tương tự Gun ở trên.

###  3.5 Monster.

**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/9f204c40-f40f-4081-bbfc-3737fa821e37" alt="monster sequence" width="640"/></p>
<p align="center"><strong><em>Hình 12:</em></strong> Monster sequence.</p>

**Tóm tắt nguyên lý:** Monster là đối tượng xuất hiện và di chuyển liên tục trong game nhận signal từ Screen. Chia làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt thông số của Monster. Cấp điểm xuất phát ngẫu nghiên cho Monster, hiển thị lên màn hình.
- **Giai đoạn 2:** Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms:
  - Cập nhật vị trí và hoạt ảnh di chuyển cho Monster.
  - Kiểm tra vị trí của các Bullet nếu Bullet chạm vào Monster thì thực hiện reset Bullet và Monster rồi tạo Bang.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Monster trước khi thoát game.

**Code:** Tương tự Gun ở trên.

## IV. Hiển thị và âm thanh trong trò chơi bắn cung
### 4.1 Đồ họa

Trong trò chơi, màn hình hiện thị là 1 màn hình **LCD OLed 1.3"** có kích thước là **128px*64px**. Nên các đối tượng được hiển thị trong game phải có kích thước hiển thị phù hợp với màn hình nên cần được thiết kế riêng. 

Đồ họa được thiết kế từng phần theo từng đối tượng bằng phần mềm [Photopea](https://www.photopea.com/)

#### 4.1.1 Thiết kế đồ họa cho các đối tượng

<p align="center"><img src="https://github.com/user-attachments/assets/a2f97cf8-1666-4ce0-975a-ce995b2f4dc8" alt="kill monster game bitmap" width="720"/></p>
<p align="center"><strong><em>Hình 13:</em></strong> Bitmap của các đối tượng.</p>

**Bitmap** là một cấu trúc dữ liệu được sử dụng để lưu trữ và hiển thị hình ảnh trong game.

**Animation** là ứng dụng việc nối ảnh của của nhiều ảnh liên tiếp tạo thành hoạt ảnh cho đổi tượng muốn miêu tả. Trong game, biến “action_image” trong đối tượng được sử dụng nối các ảnh theo thứ tự tạo thành animation.

**Ghi chú:** Trong thiết kế trên có nhiều ảnh khác nhau cho cùng 1 đối tượng để tạo animation cho đối tượng đó nhằm tăng tính chân thật lúc chơi game.
