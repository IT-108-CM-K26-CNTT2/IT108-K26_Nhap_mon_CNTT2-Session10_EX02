# 🌱 Cài đặt cảm biến độ ẩm đất – Trang trại Lan

> Văn bản thường là *giải thích*; nội dung trong `khung code` hoặc khối code là **lệnh cần gõ nguyên văn**.

## 1. Chuẩn bị

Cập nhật hệ thống bằng `sudo apt update`, cài thư viện, rồi bật SPI qua `sudo raspi-config` (**Interface Options → SPI**):

```bash
sudo apt install -y python3-gpiozero python3-spidev
ls /dev/spidev*   # kết quả mong đợi: /dev/spidev0.0
```

Nối dây: `VCC` → 3.3V, `GND` → GND, `AOUT` → `CH0` của MCP3008, rơ-le `IN` → `GPIO17`.

## 2. Ký tự đặc biệt (Escape)

Dùng `\` để hiển thị nguyên ký tự mà không bị biến thành định dạng:

- Độ ẩm \*\*dưới 30%\*\* (dấu sao hiển thị đúng, không in đậm)
- \# Dòng này không phải tiêu đề
- 1\. Dòng này không thành danh sách đánh số

Trong khối code không cần escape.

## 3. Mã Python điều khiển máy bơm

Lưu thành `pump_control.py`, chạy bằng `python3 pump_control.py`. Biến `THRESHOLD` là ngưỡng bật bơm, cần chỉnh theo giá trị đo thực tế của cảm biến.

```python
import time
from gpiozero import MCP3008, OutputDevice

sensor = MCP3008(channel=0)
pump = OutputDevice(17)
THRESHOLD = 0.6   # trên mức này = đất khô

while True:
    if sensor.value > THRESHOLD:
        pump.on(); time.sleep(10); pump.off()
    time.sleep(60)
```

## 4. So sánh cảm biến

| Tiêu chí | Loại cũ (Điện trở) | Loại mới (Điện dung) |
|:--|:--:|:--:|
| Độ bền | ⚠️ Bị ăn mòn sau vài tuần | ✅ Chống ăn mòn |
| Độ chính xác | Trung bình | Cao |
| Giá tham khảo | ~15.000 đ | ~45.000 đ |
| Tuổi thọ | 1 – 3 tháng | 1 – 2 năm |

## 5. Danh sách kiểm tra thiết bị

- [x] Raspberry Pi bật nguồn bình thường
- [ ] Cài `gpiozero`, `spidev` và bật SPI
- [ ] Nối dây đúng sơ đồ, bọc kín mối nối
- [ ] Đo giá trị cảm biến khi khô/ướt để chỉnh `THRESHOLD`
- [ ] Chạy thử: đất khô → bơm tự bật, hết 10 giây tự tắt

