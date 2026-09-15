# 🐛 ch32v003_debug

Thư viện hỗ trợ in dữ liệu gỡ lỗi (`printf`) siêu nhẹ trực tiếp qua chân nạp SWIO (PD1) dành cho dòng vi điều khiển **CH32V003**. Viết dưới dạng wrapper tối ưu trên nền framework **ch32v003fun**, giúp khắc phục hoàn toàn lỗi báo thiếu hàm trong VSCode/IntelliSense mà không cần include `<stdio.h>` gây phình bộ nhớ Flash.

---

## 🚀 Tính năng nổi bật

- **Siêu nhẹ & Tối ưu Flash:** Khai báo prototype `printf` siêu nhẹ của `ch32v003fun`, tránh kéo theo thư viện C tiêu chuẩn (`stdio.h`).
- **Không cần dây UART phụ:** Truyền dữ liệu debug trực tiếp qua chân SWIO (PD1) dùng chung với mạch nạp WCH-LinkE.
- **Tương thích IDE tốt:** Xóa bỏ hoàn toàn gạch đỏ báo lỗi hàm `printf()` trên VSCode / PlatformIO.
- **Định dạng cơ bản:** Hỗ trợ đầy đủ các tả định dạng thông dụng: `%d`, `%x`, `%c`, `%s`.

---

## 📑 Cấu trúc hàm (API Reference)

### **`int printf(const char* format, ...)`**

Hàm xuất dữ liệu định dạng ra cổng debug SWIO.

| Tham số  | Kiểu dữ liệu  | Mô tả                                                    |
| -------- | ------------- | -------------------------------------------------------- |
| `format` | `const char*` | Chuỗi định dạng dữ liệu (Hỗ trợ `%d`, `%x`, `%c`, `%s`). |
| `...`    | `va_list`     | Danh sách các biến tương ứng với chuỗi định dạng.        |

- **Giá trị trả về:** Số lượng ký tự đã xuất ra thành công (`int`).
- **Lưu ý:** Không hỗ trợ in số thực (`%f`) để tối ưu hóa triệt để dung lượng bộ nhớ.

---

## ⚙️ Cấu hình & Phụ thuộc (`funconfig.h`)

Để tính năng in debug qua chân nạp hoạt động, bạn cần đảm bảo cờ macro sau được bật trong file `include/funconfig.h` của dự án:

```c
// Bật tính năng in debug printf qua giao tiếp 1-wire SWIO
#define FUNCONF_USE_DEBUGPRINTF 1

```

_(Nếu đặt cờ này bằng `0`, các lệnh `printf` sẽ tự động bị loại bỏ để tiết kiệm dung lượng khi build bản thương mại)._

---

## 📝 Code mẫu sử dụng

```c
#include "ch32fun.h"
#include <ch32v003_debug.h>
#include <ch32v003_delay.h>

void setup() {
    int counter = 100;
    // In chuỗi và giá trị biến ra Terminal
    printf("Vọc Vạch IoT: Debug System Ready!\n");
    printf("Gia tri khoi tao: %d (0x%x)\n", counter, counter);
}

void loop() {
    static int count = 0;
    printf("Loop count: %d\n", count++);
    delayMs(1000);
}

int main() {
    SystemInit();
    delayMs(1000); // Khoảng chờ an toàn tránh brick chip
    setup();
    while (1) {
        loop();
    }
}

```

---
