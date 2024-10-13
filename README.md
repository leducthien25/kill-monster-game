# Kill Monsters Game - Build on AK Embedded Base Kit

## I. Giới thiệu

Kill monsters game là một tựa game chạy trên AK Embedded Base Kit. Được xây dựng nhằm mục đích giúp ta có thể tìm hiểu và thực hành về lập trình event – driven. Trong quá trình xây dựng nên archery game, ta sẽ hiểu thêm về cách thiết kế và ứng dụng UML, Task, Signal, Timer, Message, State-machine, …


### 1.1 Phần cứng.

<p align="center"><img src="https://github.com/user-attachments/assets/e028e082-bb9b-48f5-b66c-7f73ef28f6ae" alt="AK Embedded Base Kit - STM32L151"></p>
<p align="center"><strong><em>Hình 1:</em></strong> AK Embedded Base Kit - STM32L151.</p>

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

Sau khi xác định được các đối tượng trong game, tiếp theo liệt kê ra các thuộc tính, các task, các signal và bitmap mà trong game sẽ sử dụng tới.
Việc liệt kê càng chi tiết thì việc làm game diễn ra càng nhanh và tạo tình rõ ràng minh bạch cho phần tài nguyên giúp phần code game diễn ra suông sẻ hơn.

#### 2.2.1 Thuộc tính đối tượng
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
        } ar_game_meteoroid_t;
        extern ar_game_meteoroid_t meteoroid[NUM_METEOROIDS + NUM_METEOROIDS_III + GIFT + BOSS];

**Áp dụng struct cho các đối tượng:**
|struct|Các biến|
|------|--------|
|ar_game_archery_t|archery|
|ar_game_arrow_t|arrow[MAX_NUM_ARROW]|
|ar_game_bang_t|bang[NUM_BANG]|
|ar_game_border_t|border|
|ar_game_meteoroid_t|meteoroid[NUM_METEOROIDS]|

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
  - settingsetup.meteoroid_speed : Cấu hình tốc độ của quái vật.

#### 2.2.2 Task
Trong lập trình event-driven, task là một yếu tố quan trọng giúp quản lý các sự kiện, luồng điều khiển, và đồng bộ hóa trong các hệ thống xử lý theo sự kiện, là một đơn vị độc lập đảm nhiệm một nhóm công việc nhất định. Khi hệ thống scheduler tìm thấy message liên quan đến task trong hàng đợi, hệ thống sẽ gọi hàm thực thi của task để xử lý message được gửi đến. Một số tác dụng quan trọng của task:
- **Xử lý sự kiện:** Task được sử dụng để xử lý các message được bắn đến khi có sự kiện xảy ra. Mỗi task có thể được liên kết với một sự kiện cụ thể, và khi hệ thống phát hiện sự kiện này, nó sẽ kích hoạt task tương ứng để xử lý sự kiện đó.
- **Đồng bộ hóa:** Task đóng vai trò quan trọng trong việc đồng bộ hóa hoạt động của hệ thống, đảm bảo rằng các tác vụ được thực thi theo trình tự hợp lý và không gây ra xung đột tài nguyên. Cơ chế này giúp việc xử lý sự kiện diễn ra tuần tự, tránh tình trạng tranh chấp tài nguyên hoặc xử lý không đồng bộ, từ đó duy trì sự ổn định và hiệu quả của hệ thống.
- **Quản lý luồng điều khiển:** Task giúp quản lý luồng sự kiện trong một hệ thống phức tạp. Khi có nhiều sự kiện xảy ra, task sẽ xác định thứ tự và cách thức xử lý các sự kiện này, giúp hệ thống hoạt động mượt mà và có tổ chức hơn.
- **Tách biệt logic:** Task giúp tách biệt rõ ràng các phần logic xử lý sự kiện, điều này làm cho mã nguồn dễ hiểu và dễ bảo trì hơn. Mỗi task chỉ đảm nhận một nhóm công việc nhất định, giúp cho việc quản lý sự kiện trở nên trực quan, điều này giúp Source code rõ ràng, dễ đọc hơn.
- **Phân cấp nhiệm vụ:** Các task trong hệ thống có thể được phân cấp theo mức độ ưu tiên, gọi là task level, giúp sắp xếp thứ tự xử lý các message trong hàng đợi một cách hợp lý. Nhờ đó, những công việc quan trọng sẽ được xử lý trước, trong khi các công việc ít quan trọng hơn sẽ được thực hiện sau. Trong game các task level của game đều là 4 và task nào được gọi trước sẽ được ưu tiên xử lý trước, đảm bảo trải nghiệm mượt mà.

<p align="center"><img src="https://github.com/user-attachments/assets/5511c4dc-5600-4790-be8d-822d12999d89" alt="archery tasks design" width="720"/></p>
<p align="center"><strong><em>Hình 6:</em></strong> Bảng Task của các đối tượng</p>

#### 2.2.3 Message & Signal
**Message** được chia làm 2 loại chính, Message chỉ chứa Signal và Message vừa chứa Signal và Data. **Message** tương đương với **Signal**

<p align="center"><img src="https://github.com/user-attachments/assets/e80ddfa8-6541-4788-b156-caec8412bdb3" alt="archery signals design" width="720"/></p>
<p align="center"><strong><em>Hình 7:</em></strong> Bảng Signal của từng Task</p>

**(*)** Tác dụng của các Signal trong game: xem tại Ghi chú - Hình 5

## III. Hướng dẫn chi tiết code trong đối tượng
### 3.1 Archery
**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/36db629f-1e23-4da3-a1bf-73c4c5cd00eb" width="640"/></p>
<p align="center"><strong><em>Hình 8:</em></strong> Archery sequence</p>

**Tóm tắt nguyên lý:** Archery sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Button. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Archery như vị trí và hình ảnh.
- **Giai đoạn 2:** Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal cập nhật cho Archery mỗi 100ms để cập nhật trạng thái hiện tại của Archery.
  - Hành động: Button gửi Signal di chuyển lên/xuống cho Archery mỗi khi nhấn nút.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Archery trước khi thoát game.

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

AR_GAME_ARCHERY_SETUP() là một macro được dùng định nghĩa để cài đặt trạng thái ban đầu của trò chơi tiêu diệt quái vật. Nó đặt các giá trị của biến archery và sử dụng các hằng số được định nghĩa trước đó để thiết lập tọa độ, màu sắc và hình ảnh của súng.

    #define AR_GAME_ARCHERY_SETUP() \
    do { \
        archery.x = AXIS_X_ARCHERY; \
        archery.y = AXIS_Y_ARCHERY; \
        archery.visible = WHITE; \
        archery.action_image = 1; \
    } while (0);

AR_GAME_ARCHERY_UP() là một macro được sử dụng để di chuyển súng lên trên. Nó giảm giá trị của archery_y bằng một giá trị STEP_ARCHERY_AXIS_Y và kiểm tra nếu giá trị mới bằng 0, nó được gán lại là 10.

    #define AR_GAME_ARCHERY_UP() \
    do { \
        archery_y -= STEP_ARCHERY_AXIS_Y; \
        if (archery_y == 0) {archery_y = 10;} \
    } while(0);

AR_GAME_ARCHERY_DOWN() là một macro được sử dụng để di chuyển súng xuống dưới. Nó tăng giá trị của archery_y bằng một giá trị STEP_ARCHERY_AXIS_Y và kiểm tra nếu giá trị mới vượt quá 50, nó được gán lại là 50.

    #define AR_GAME_ARCHERY_DOWN() \
    do { \
        archery_y += STEP_ARCHERY_AXIS_Y; \
        if (archery_y > 50) {archery_y = 50;} \
    } while(0);

AR_GAME_ARCHERY_RESET() là một macro được sử dụng để đặt lại trạng thái ban đầu của trò chơi tiêu diệt quái vật. Nó đặt lại giá trị của archery, archery_y và làm cho súng trở nên không hiển thị.

    #define AR_GAME_ARCHERY_RESET() \
    do { \
        archery.x = AXIS_X_ARCHERY; \
        archery.y = AXIS_Y_ARCHERY; \
        archery.visible = BLACK; \
        archery_y = AXIS_Y_ARCHERY; \
    } while(0);

Hàm ar_game_archery_handle() là một hàm xử lý các thông điệp (messages) liên quan đến trò chơi cung bắn. Nó chứa một câu lệnh switch-case để xử lý các thông điệp khác nhau. Các thông điệp được gửi đến hàm này thông qua một tham số msg có kiểu dữ liệu ak_msg_t. Mỗi case trong switch-case xử lý một thông điệp cụ thể.

    void ar_game_archery_handle(ak_msg_t* msg) {
        switch (msg->sig) {
        case AR_GAME_ARCHERY_SETUP: {
            APP_DBG_SIG("AR_GAME_ARCHERY_SETUP\n");
            AR_GAME_ARCHERY_SETUP();
        }
            break;

        case AR_GAME_ARCHERY_UPDATE: {
            APP_DBG_SIG("AR_GAME_ARCHERY_UPDATE\n");
            archery.y = archery_y;
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

### 3.2 Arrow

**Sequence diagram:**

<p align="center"><img src="https://github.com/user-attachments/assets/8537eaa6-7710-4f27-9e94-c7f55bb2ab7c" alt="arrow sequence" width="640"/></p>
<p align="center"><strong><em>Hình 9:</em></strong> Arrow sequence</p>

**Tóm tắt nguyên lý:** Arrow sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Button. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Arrow. Tất cả Arrow vào trạng thái lặn, không hiển thị trên màn hình.
- **Giai đoạn 2:** Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal di chuyển cho Arrow mỗi 100ms để tăng trạng thái của Arrow tạo sự di chuyển cho Arrow.
  - Hành động: Button gửi Signal bắn tên cho Arrow mỗi khi nhấn nút. Arrow sẽ sẽ kiểm tra số mũi tên và nếu còn thì sẽ cập nhật trạng thái để bắn mũi tên ra tại vị trí hiện tại của Archery
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Arrow trước khi thoát game.

**Code:** Tương tự Archery ở trên.

### 3.3 Bang

**Sequence diagram:**

<p align="center"><img src="https://github.com/epcbtech/archery-game/blob/main/resources/images/sequence_object/bang_sequence.webp" alt="bang sequence" width="640"/></p>
<p align="center"><strong><em>Hình 10:</em></strong> Bang sequence</p>

**Tóm tắt nguyên lý:** Bang sẽ nhận Signal thông được gửi từ Screen. Quá trình xử lý của đối tượng phân làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt các thông số của Bang. Cho tất cả các bang về trạng thái lặn, không xuất hiện trên màn hình.
- **Giai đoạn 2:** Chơi game, Vụ nổ chỉ xuất sau khi Monsters bị phá hủy. Vụ nổ bao gồm các hoạt ảnh được cập nhật sau mỗi 100ms sau 3 hoạt ảnh thì sẽ tự reset.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Bullet trước khi thoát game.

**Code:** Tương tự Archery ở trên.

### 3.4 Border

**Sequence diagram:**

<p align="center"><img src="https://github.com/epcbtech/archery-game/blob/main/resources/images/sequence_object/border_sequence.webp" alt="border sequence" width="640"/></p>
<p align="center"><strong><em>Hình 11:</em></strong> Border sequence</p>

**Tóm tắt nguyên lý:** Border là 1 đối tượng bất động trong game. Có nhiệm vụ update level khi đến mốc điểm quy định và kiểm tra game over.
- **Giai đoạn 1:** Bắt đầu game, cài đặt thông số vị trí và hiển thị của Border.
- **Giai đoạn 2:** Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms
  - Kiểm tra số điểm nếu số điểm thêm 200 thì tăng tốc độ của Monsters.
  - Kiểm tra vị trí của các Monsters nếu Monsters chạm vào Border thì gửi tín hiệu Reset đến Screen
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Border trước khi thoát game.

**Code:** Tương tự Archery ở trên.

###  3.5 Monsters.

**Sequence diagram:**

<p align="center"><img src="https://github.com/epcbtech/archery-game/blob/main/resources/images/sequence_object/meteoroid_sequence.webp" alt="meteoroid sequence" width="640"/></p>
<p align="center"><strong><em>Hình 12:</em></strong> Meteoroid sequence</p>

**Tóm tắt nguyên lý:** Monsters là đối tượng xuất hiện và di chuyển liên tục trong game nhận signal từ Screen. Chia làm 3 giai đoạn:
- **Giai đoạn 1:** Bắt đầu game, cài đặt thông số của Monsters. Cấp điểm xuất phát ngẫu nghiên cho Monsters, hiển thị lên màn hình.
- **Giai đoạn 2:** Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms
  - Cập nhật vị trí và hoạt ảnh di chuyển cho Meteoroid
  - Kiểm tra vị trí của các Bullet nếu Arrow chạm vào Monsters thì thực hiện reset Bullet và Monsters rồi tạo Bang.
- **Giai đoạn 3:** Kết thúc game, thực hiện cài đặt lại trạng thái của Monsters trước khi thoát game.

**Code:** Tương tự Archery ở trên.
