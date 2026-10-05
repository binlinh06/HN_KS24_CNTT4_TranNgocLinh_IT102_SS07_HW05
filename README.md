# SMART HOME IOT - ENVIRONMENT SENSOR MONITORING

## 1. Giới thiệu

Chương trình mô phỏng một node cảm biến môi trường trong hệ thống Smart Home IoT.

Chương trình sử dụng `struct EnvironmentSensor` để quản lý tập trung các thông tin:

* Mã cảm biến.
* Tên phòng.
* Nhiệt độ.
* Độ ẩm.
* Dung lượng pin.

Sau khi nhận dữ liệu từ bàn phím, chương trình kiểm tra tính hợp lệ của pin và đánh giá trạng thái cảm biến theo ba mức:

* `SAFE`
* `WARNING`
* `CRITICAL`

---

## 2. Cấu trúc project

```text
SMART_HOME_IOT_SESSION05_ENVIRONMENT_SENSOR/
├── main.c
└── README.md
```

---

## 3. Data Model

Chương trình định nghĩa:

```c
struct EnvironmentSensor {
    int sensor_id;
    char room_name[51];
    float temperature;
    float humidity;
    int battery_level;
};
```

### Ý nghĩa các trường

| Trường          | Kiểu dữ liệu | Ý nghĩa               |
| --------------- | ------------ | --------------------- |
| `sensor_id`     | `int`        | Mã định danh cảm biến |
| `room_name`     | `char[]`     | Tên phòng             |
| `temperature`   | `float`      | Nhiệt độ °C           |
| `humidity`      | `float`      | Độ ẩm %               |
| `battery_level` | `int`        | Dung lượng pin %      |

---

## 4. Nhập dữ liệu

Chương trình nhập:

```text
Sensor ID
Room name
Temperature
Humidity
Battery level
```

Tên phòng sử dụng:

```c
scanf("%50s", sensor.room_name);
```

giúp giới hạn tối đa 50 ký tự được nhập vào.

---

## 5. Kiểm tra dữ liệu pin

Dung lượng pin hợp lệ khi:

```text
0 <= battery_level <= 100
```

Nếu dữ liệu nằm ngoài khoảng trên:

```text
Loi: Dung luong pin khong hop le!
```

Sau đó chương trình kết thúc.

Điều kiện:

```c
if (sensor.battery_level < 0 ||
    sensor.battery_level > 100)
```

---

## 6. Phân loại trạng thái

### CRITICAL

Cảm biến ở trạng thái `CRITICAL` khi:

```text
battery_level < 10
```

hoặc:

```text
temperature > 60.0
```

Code:

```c
if (sensor.battery_level < 10 ||
    sensor.temperature > 60.0f)
```

---

### WARNING

Nếu không thuộc `CRITICAL`, cảm biến ở trạng thái `WARNING` khi có ít nhất một điều kiện:

```text
10 <= battery_level <= 20
```

hoặc:

```text
40.0 <= temperature <= 60.0
```

hoặc:

```text
humidity > 85.0
```

Code:

```c
else if (
    (sensor.battery_level >= 10 &&
     sensor.battery_level <= 20) ||

    (sensor.temperature >= 40.0f &&
     sensor.temperature <= 60.0f) ||

    sensor.humidity > 85.0f
)
```

---

### SAFE

Nếu không thuộc hai trường hợp trên:

```text
SAFE
```

---

## 7. Thứ tự xử lý

Luồng chương trình:

```text
Nhập dữ liệu
     |
     v
Kiểm tra battery_level
     |
     +---- Không hợp lệ ----> In lỗi và kết thúc
     |
     v
Kiểm tra CRITICAL
     |
     +---- Đúng ----> CRITICAL
     |
     v
Kiểm tra WARNING
     |
     +---- Đúng ----> WARNING
     |
     v
SAFE
```

Việc kiểm tra `CRITICAL` trước `WARNING` đảm bảo trường hợp nguy hiểm luôn được ưu tiên.

---

## 8. Ví dụ kiểm thử

### Test 1 - SAFE

```text
Input:
101
LivingRoom
28.5
65.0
85
```

Expected:

```text
Trang thai: SAFE
```

---

### Test 2 - WARNING

```text
Input:
102
Kitchen
42.0
88.0
15
```

Expected:

```text
Trang thai: WARNING
```

---

### Test 3 - CRITICAL

```text
Input:
103
ServerRoom
62.5
50.0
5
```

Expected:

```text
Trang thai: CRITICAL
```

---

### Test 4 - Invalid Battery

```text
Input:
104
Bedroom
25.0
50.0
120
```

Expected:

```text
Loi: Dung luong pin khong hop le!
```

---

## 9. Biên dịch

Sử dụng GCC:

```bash
gcc -Wall -Wextra -std=c11 main.c -o main
```

Chạy chương trình trên Windows:

```cmd
main.exe
```

Hoặc PowerShell:

```powershell
.\main.exe
```

---

## 10. Các quy định đã tuân thủ

Chương trình:

* Sử dụng `struct EnvironmentSensor`.
* Chỉ sử dụng một biến cấu trúc.
* Không sử dụng mảng các `struct`.
* Không định nghĩa function tự tạo.
* Toàn bộ xử lý nằm trong `main()`.
* Truy xuất member bằng toán tử `.`
* Sử dụng tên biến theo `snake_case`.
* Kiểm tra dữ liệu pin trong khoảng `[0, 100]`.
* Hiển thị số thực với đúng 2 chữ số thập phân.
* Giới hạn độ dài chuỗi tên phòng.
* Biên dịch bằng chuẩn C11.

---

## 11. Kết luận

Bài tập áp dụng `struct` để thay thế cách quản lý dữ liệu bằng nhiều biến rời rạc.

Thông qua bài thực hành, chương trình có thể:

1. Khai báo và khởi tạo `struct`.
2. Nhập dữ liệu vào các member.
3. Truy xuất member bằng toán tử `.`.
4. Kiểm tra dữ liệu biên.
5. Xử lý logic nhiều mức độ ưu tiên.
6. Xuất báo cáo giám sát cảm biến Smart Home.
