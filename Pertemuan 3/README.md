# Praktikum Sistem Mikrokontroler


## IV. Pertanyaan Praktikum
1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan?
3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi  jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap sistem

---

### 1. Proses input dari input keyboard hingga LED menyala/mati
   1. User mengetik input pada Serial Monitor.
   2. Data dikirim melalui komunikasi UART ke Arduino.
   3. Arduino mengecek data dengan `Serial.available()`.
   4. Data dibaca menggunakan `Serial.read()`.
   5. Program membandingkan input:
      - '1' → LED menyala
      - '0' → LED mati
   6. Arduino mengirim sinyal HIGH/LOW ke LED.
   7. LED menyala atau mati sesuai perintah.

---

### 2. Mengapa digunakan Serial.available()
Fungsi Serial.available() digunakan untuk memeriksa apakah terdapat data yang tersedia pada buffer serial sebelum dilakukan pembacaan. Hal ini penting untuk mencegah program membaca data yang belum tersedia, yang dapat menyebabkan error atau pembacaan data yang tidak valid. Jika fungsi ini dihilangkan, maka program akan tetap mencoba membaca data meskipun tidak ada data yang masuk, sehingga dapat menghasilkan nilai yang tidak sesuai atau menyebabkan perilaku sistem menjadi tidak stabil.

---

### 3) Program LED Blink saat input '2'

```cpp
#include <Arduino.h>

const int PIN_LED = 12;
char mode = '0'; 

void setup() {
  Serial.begin(9600);
  Serial.println("Ketik '1' untuk menyalakan LED, '0' untuk mematikan LED, '2' untuk LED blink");
  pinMode(PIN_LED, OUTPUT);
}

void loop() {
  
  if (Serial.available() > 0) {
    char inputBaru = Serial.read();    
    if (inputBaru != '\n' && inputBaru != '\r') {
      mode = inputBaru; 
      if (mode == '1') {
        digitalWrite(PIN_LED, HIGH);
        Serial.println("Mode: LED ON");
      } 
      else if (mode == '0') {
        digitalWrite(PIN_LED, LOW);
        Serial.println("Mode: LED OFF");
      } 
      else if (mode == '2') {
        Serial.println("Mode: LED BLINKING");
      } 
      else {
        Serial.println("Perintah tidak dikenal");
      }
    }
  }
  if (mode == '2') {
    digitalWrite(PIN_LED, HIGH);
    delay(100);
    digitalWrite(PIN_LED, LOW);
    delay(500);
  }
}
```

---

### 4) Menggunakan delay() atau millis()

Penggunaan delay() akan menghentikan seluruh proses pada mikrokontroler selama waktu tertentu, sehingga sistem tidak dapat menjalankan tugas lain secara bersamaan. Hal ini dapat menyebabkan sistem menjadi kurang responsif, terutama jika digunakan dalam aplikasi yang membutuhkan multitasking. Sebaliknya, millis() memungkinkan pengaturan waktu tanpa menghentikan proses utama, sehingga sistem tetap dapat menjalankan fungsi lain secara bersamaan. Penggunaan millis() lebih disarankan dalam sistem yang membutuhkan respons cepat dan efisiensi waktu

---

## I2C dan LCD

### 1) Cara kerja I2C

**Jawaban:**
- Menggunakan 2 jalur: SDA dan SCL
- Arduino sebagai master
- LCD sebagai slave
- Data dikirim lalu ditampilkan di LCD

---

### 2) Potensiometer

**Jawaban:**
Konfigurasi:
- Kiri → GND
- Tengah → Output
- Kanan → VCC

Jika tertukar:
- Nilai tetap berubah
- Arah putaran terbalik

---

### 3) Program UART + I2C

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
int pot = A0;
int value;

void setup() {
  Serial.begin(9600);
  lcd.init();
  lcd.backlight();
}

void loop() {
  value = analogRead(pot);

  float volt = value * (5.0 / 1023.0);
  int persen = map(value, 0, 1023, 0, 100);

  Serial.print("ADC: ");
  Serial.print(value);
  Serial.print("  ");
  Serial.print(persen);
  Serial.println("%");

  lcd.setCursor(0, 0);
  lcd.print("ADC:");
  lcd.print(value);
  lcd.print(" ");
  lcd.print(persen);
  lcd.print("%   ");

  lcd.setCursor(0, 1);

  int bar = map(value, 0, 1023, 0, 16);

  for (int i = 0; i < bar; i++) {
    lcd.print((char)255);
  }
  for (int i = bar; i < 16; i++) {
    lcd.print(" ");
  }

  delay(500);
}
```

---

### 4) Tabel Pengamatan

| ADC | Volt (V) | Persen (%) |
|-----|----------|------------|
| 1   | 0.00     | 0%         |
| 21  | 0.10     | 2%         |
| 49  | 0.24     | 5%         |
| 74  | 0.36     | 7%         |
| 96  | 0.47     | 9%         |

---

## Kesimpulan

- Input diproses melalui UART
- Output ditampilkan ke LED dan LCD
- I2C digunakan untuk komunikasi LCD
- Sistem berjalan sesuai perintah
