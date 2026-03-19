# STM32-HC05-7Segment-Controller
STM32 microcontroller based project to control a 5461BS 4-digit 7-segment display via HC-05 Bluetooth module.

Bu proje, bir bilgisayardan Python scripti aracılığıyla gönderilen verilerin, HC-05 Bluetooth modülü üzerinden STM32'ye iletilmesini ve 5461BS (4 haneli 7 segment) display üzerinde görüntülenmesini sağlar.

## Donanım Bileşenleri
- STM32 Mikrodenetleyici
- HC-05 Bluetooth Modülü
- 5461BS 7-Segment Display (4 Haneli)

## Yapılandırma ve Kurulum

### 1. HC-05 AT Komutları
Modülü bilgisayarla eşleşecek şekilde yapılandırmak için şu adımlar izlenmiştir:
- `AT+ROLE=0` (Slave mod)
- `AT+UART=9600,0,0` (Baud rate ayarı)
- `AT+NAME=STM32_Display` (İsteğe bağlı isim)

### 2. STM32 CubeMX Ayarları (Nedenleri ile)
- **USART1 (Asynchronous):** HC-05 ile ortak bir clock hattı olmadığı için standart asenkron haberleşme seçilmiştir.
- **NVIC Interrupt:** Veri akışı sırasında display tarama (multiplexing) işleminin kesintiye uğramaması ve ekranda titreme (flickering) oluşmaması için UART kesmesi (Interrupt) aktif edilmiştir.

## Yazılım Yapısı

### Gömülü Kod (STM32)
Tüm kontrol mantığı `main.c` içerisindedir:
- **Multiplexing:** 4 haneli ekranı sürmek için hızlı bir tarama algoritması kullanılmıştır.
- **UART Callback:** Gelen veriler kesme fonksiyonu ile yakalanıp display değişkenlerine aktarılır.

### Python Script (`serial_sender.py`)
Bilgisayar tarafında veriyi seri porta (Bluetooth üzerinden oluşturulan sanal port) yönlendirmek için `pyserial` kütüphanesi kullanılmıştır.

## Nasıl Çalıştırılır?
1. HC-05 modülünü yukarıdaki komutlarla hazırlayın.
2. STM32 kodunu yükleyin.
3. Bilgisayardan terminal üzerinden Bluetooth bağlantısını gerçekleştirin.
4. `python serial_sender.py` komutu ile veri gönderimini başlatın.
