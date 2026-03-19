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

### 2. STM32 CubeMX Ayarları 
- **USART1 (Asynchronous):** HC-05 ile ortak bir clock hattı olmadığı için standart asenkron haberleşme seçilmiştir.
- **NVIC Interrupt:** Veri akışı sırasında display tarama (multiplexing) işleminin kesintiye uğramaması ve ekranda titreme (flickering) oluşmaması için UART kesmesi (Interrupt) aktif edilmiştir.
- **GPIO Pull-Down:** HC-05 bağlantısı kesildiğinde veya kapandığında, yüzen (boşta kalan) sinyallerin hatalı UART kesmelerine yol açmasını önlemek için RX pini dahili Pull-down direnci ile yapılandırılmıştır.
- **Baud Rate:** Veri çerçevelerinin senkronize edilmesi ve hatasız seri haberleşme sağlanması için, STM32 USART1 baud hızı HC-05 modülünün varsayılan çalışma frekansı olan 9600 bps ile eşleşecek şekilde yapılandırılmıştır.

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

## Donanım Notları

### 1. Direnç Kullanımı
STM32 GPIO pinlerini ve 5461BS segmentlerini yüksek akımdan korumak için her segment hattına seri olarak 220 ohm akım sınırlama direnci bağlanmıştır.

### 2. USB-TTL ile HC-05 Yapılandırması 
HC-05 modülü, STM32 sistemine entegre edilmeden önce bir USB-TTL dönüştürücü kullanılarak AT modunda yapılandırılmıştır.

### 3. RX-TX Çapraz Bağlantı
Başarılı bir seri haberleşme için, verinin vericiden alıcıya akmasını sağlamak amacıyla TX/RX pinleri çapraz (STM32 TX -> HC-05 RX ve tersi) bağlanmalıdır.
