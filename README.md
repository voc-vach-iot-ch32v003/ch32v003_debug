# 🐛 CH32V003 Debug Printf Library

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![PlatformIO](https://img.shields.io/badge/PlatformIO-Supported-orange.svg)
![Framework](https://img.shields.io/badge/Framework-ch32v003fun-green.svg)
![MCU](https://img.shields.io/badge/MCU-CH32V003-red.svg)

Thư viện hỗ trợ in dữ liệu gỡ lỗi (`printf`) siêu nhẹ qua chân nạp **SWIO (PD1)** cho dòng vi điều khiển **CH32V003** trên nền framework `ch32v003fun`. Thư viện xuất dữ liệu trực tiếp lên máy tính thông qua mạch nạp WCH-LinkE mà **không tốn thêm chân UART** phần cứng.

---

## 🚀 Tính năng nổi bật

- **Tối ưu dung lượng Flash:** Không cần include thư viện chuẩn `<stdio.h>`, tiết kiệm tối đa dung lượng bộ nhớ Flash cho vi điều khiển.
- **Không tốn chân UART:** Tận dụng ngay chân Single Wire (SWIO / PD1) dùng để nạp code để truyền log SDI ngược lên máy tính.
- **Hỗ trợ định dạng cơ bản:** Hỗ trợ in chuỗi định dạng qua các đặc tả `%d`, `%x`, `%c`, `%s` (không hỗ trợ `%f` để tránh phình to bộ nhớ).
- **Tương thích toàn bộ gói vỏ:** Tự động tương thích với các dòng CH32V003J4M6 (SOP8), CH32V003A4M6 (SOP16) và CH32V003F4P6 (TSSOP20).

---

## 📦 Cài đặt

Tích hợp thư viện vào dự án PlatformIO bằng cách thêm URL Git vào mục `lib_deps` trong file `platformio.ini`:

```ini
lib_deps =
    https://github.com/voc-vach-iot-ch32v003/ch32v003_debug.git

```

---

## ⚙️ Cấu hình môi trường (`funconfig.h`)

Để tính năng Debug Printf và hàm `delayMs()` hoạt động chính xác, tệp `funconfig.h` trong thư mục `include/` của dự án phải bật 2 cờ cấu hình bắt buộc sau:

```c
#ifndef FUNCONFIG_H
#define FUNCONFIG_H

/**
 * @brief Cho phép SysTick sử dụng trực tiếp tần số của lõi HCLK (48MHz)
 *        giúp hàm delayMs() đạt độ chính xác cao.
 */
#define FUNCONF_SYSTICK_USE_HCLK 1

/**
 * @brief Kích hoạt tính năng in dữ liệu gỡ lỗi (Debug Printf) qua SWIO.
 */
#define FUNCONF_USE_DEBUGPRINTF  1

#endif // FUNCONFIG_H

```

---

## 📝 Quick Start

Dưới đây là đoạn code mẫu nhanh minh họa cách dùng `printf()` để in log ra màn hình Serial Monitor qua chân SWIO:

```c
#include "ch32fun.h"
#include "ch32v003_delay.h"
#include "ch32v003_debug.h"

int main(void)
{
    SystemInit();
    delayMs(1000); // Khoảng chờ an toàn tránh kẹt chip khi nạp

    printf("CH32V003 Debug Printf Initialized!\r\n");

    uint32_t count = 0;
    while (1)
    {
        printf("Count: %ld | Hex: 0x%lx\r\n", count, count);
        count++;
        delayMs(1000);
    }
}

```

---

## 📑 Tra cứu API (API Reference)

### Cụm hàm gỡ lỗi (Debug Log)

| Hàm xử lý                         | Tham số                                                                                             | Giá trị trả về                          | Mô tả                                                                                      |
| --------------------------------- | --------------------------------------------------------------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------ |
| `printf(const char* format, ...)` | `format`: Chuỗi định dạng dữ liệu.<br> `...`: Danh sách tham số tương ứng (`%d`, `%x`, `%c`, `%s`). | `int`: Số lượng ký tự đã in thành công. | In chuỗi dữ liệu đã được định dạng qua chân SWIO (PD1) lên máy tính qua mạch nạp WCH-Link. |

> **⚠️ Lưu ý:** Để tránh phình to bộ nhớ Flash, thư viện **không hỗ trợ** in số thực `%f`. Hãy đổi số thực sang số nguyên trước khi in (ví dụ: nhân với 100 hoặc 1000).

---

## 📄 Giấy phép (License) & Tác giả

- **Tác giả:** [Vọc Vạch IoT](https://github.com/voc-vach-iot)
- **Giấy phép:** Phát hành theo giấy phép [MIT License](LICENSE).
