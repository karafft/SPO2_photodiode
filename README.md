# O2 Photodiode

STM32F429I-DISCO ve BPW34 fotodiyot ile iki dalga boylu (kırmızı / kızılötesi) ışık ölçümü. Nabız ve SpO2 hesabı bilgisayardaki Python arayüzünde yapılır.

## Bağlantılar

### Breadboard

| Parça | Bir ucu | Diğer ucu |
|---|---|---|
| R1 (330 Ω) | + hattı (3.3 V) | LED1 uzun bacak (anot) |
| LED1 (kırmızı, 630 nm) | R1 | Kısa bacak (katot) → **PE2** |
| R2 (330 Ω) | + hattı (3.3 V) | LED2 uzun bacak (anot) |
| LED2 (kızılötesi, 850 nm) | R2 | Kısa bacak (katot) → **PE3** |
| BPW34 | Katot (tırnaklı bacak) → + hattı (3.3 V) | Anot → R3 ve **PA5** |
| R3 (10 kΩ) | BPW34 anodu ve PA5 | − hattı (GND) |

- LED katotları sadece pinlere gider, GND'ye değmez.
- BPW34 ters kutupludur (katot +'da); bu bilerek böyle.
- İki ayrı breadboard kullanılıyorsa + hatları birbirine bağlanmalı. LED tarafında GND gerekmez.

### STM32F429I-DISCO

| Kart pini | Görevi | Nereye |
|---|---|---|
| **3V** | Besleme | Breadboard + hattı |
| **GND** | Toprak | Breadboard − hattı |
| **PE2** | GPIO çıkış, LOW = LED1 yanar | LED1 katodu |
| **PE3** | GPIO çıkış, LOW = LED2 yanar | LED2 katodu |
| **PA5** | ADC1_IN5 | BPW34 anodu / R3 birleşimi |
| **PA9** | USART1_TX | USB-seri adaptörün **RX** pini |
| **PA10** | USART1_RX | USB-seri adaptörün **TX** pini |
| **GND** | Toprak | USB-seri adaptörün **GND** pini |

5V pini kullanılmaz; PA5 analog girişi 3.3 V üstünü kaldırmaz.

### USB-seri adaptör (COM6)

| Adaptör pini | Kart pini |
|---|---|
| TX | PA10 |
| RX | PA9 |
| GND | GND |
| VCC | Bağlanmaz |

TX karşı tarafın RX'ine gider. Kart ST-LINK USB kablosundan beslendiği için adaptörün VCC'si boşta kalır. Adaptörde gerilim seçimi varsa 3.3 V'a alınmalı.

Seri port ayarı: 115200 baud, 8N1.

## Çalıştırma

1. `O2_Photodiode` projesini STM32CubeIDE ile derleyip karta yükle.
2. Arayüzü aç ve port olarak COM6'yı seç:

```
python o2_monitor.py
```

Kartı test etmek için (arayüz kapalıyken):

```
python test_board.py COM6
```

## Seri komutlar

| Komut | İşlevi |
|---|---|
| `START` / `STOP` | Veri gönderimini başlatır / durdurur |
| `LED,<maske>` | 0 = hiçbiri, 1 = LED1, 2 = LED2, 3 = ikisi |
| `RATE,<hız>` | Saniyedeki ölçüm sayısı (10–200) |

Kart her ölçümde bir satır yollar: `karanlık,led1,led2`
