# 📘 Praktikum Sistem Mikrokontroler

## IV. Pertanyaan Praktikum

---

### 1) Jelaskan proses dari input keyboard hingga LED menyala/mati!

**Jawaban:**
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

### 2) Mengapa digunakan `Serial.available()`?

**Jawaban:**
Digunakan untuk memastikan data sudah tersedia sebelum dibaca.

Jika dihapus:
- Arduino tetap membaca walaupun tidak ada data
- Bisa menghasilkan nilai acak
- Program menjadi tidak stabil

---

### 3) Program LED Blink saat input '2'

```cpp
int led = 13;
char data;
bool blinking = false;

void setup() {
  pinMode(led, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  if (Serial.available() > 0) {
    data = Serial.read();

    if (data == '1') {
      digitalWrite(led, HIGH);
      blinking = false;
    }
    else if (data == '0') {
      digitalWrite(led, LOW);
      blinking = false;
    }
    else if (data == '2') {
      blinking = true;
    }
  }

  if (blinking) {
    digitalWrite(led, HIGH);
    delay(500);
    digitalWrite(led, LOW);
    delay(500);
  }
}
```

**Penjelasan:**
- `pinMode()` → set pin LED
- `Serial.begin()` → mulai komunikasi
- `Serial.read()` → baca input
- `blinking` → kontrol mode kedip
- `delay()` → jeda

---

### 4) delay() atau millis()?

**Jawaban:**
- `delay()` → menghentikan program (blocking)
- `millis()` → tidak menghentikan program (non-blocking)

**Kesimpulan:**
- delay cocok untuk sederhana
- millis lebih baik untuk sistem kompleks

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
