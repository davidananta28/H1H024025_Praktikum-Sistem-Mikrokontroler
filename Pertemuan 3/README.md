# Praktikum Sistem Mikrokontroler

## Percobaan 3A: Komunikasi Serial (UART) 
### IV. Pertanyaan Praktikum
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

## Percobaan 3B: Inter-Integrated Circuit (I2C)
### Pertanyaan Praktikum
1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian tersebut!
2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar!
3. Modifikasi program dengan menggabungkan antara UART dan I2C (keduanya sebagai output) sehingga:
    - Data tidak hanya ditampilkan di LCD tetapi juga di Serial Monitor
    - Adapun data yang ditampilkan pada Serial Monitor sesuai dengan table berikut:
    - ADC: 0  0% | setCursor(0, 0) dan Bar (level) | setCursor(0, 1)
    - Berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4.  Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor 

---

### 1. Cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian

Komunikasi I2C antara Arduino dan LCD dilakukan melalui dua jalur utama, yaitu SDA (data) dan SCL (clock). Arduino berperan sebagai master yang mengontrol jalur clock dan mengirimkan data ke LCD sebagai slave. Setiap perangkat pada jaringan I2C memiliki alamat unik, sehingga Arduino dapat memilih perangkat tujuan dengan mengirimkan alamat tersebut sebelum mengirim data. Setelah alamat dikenali, data dikirim secara serial melalui jalur SDA dan disinkronkan menggunakan sinyal clock pada SCL. LCD kemudian menerima data tersebut dan menampilkannya sesuai dengan perintah yang diberikan.
---

### 2. Apakah pin potensiometer harus seperti itu

Potensiometer memiliki tiga kaki, yaitu dua kaki sebagai sumber tegangan (VCC dan GND) serta satu kaki sebagai output yang terhubung ke pin analog Arduino. Jika posisi kaki kiri dan kanan (VCC dan GND) tertukar, maka arah perubahan nilai tegangan akan terbalik. Artinya, ketika potensiometer diputar ke arah tertentu, nilai yang terbaca akan berlawanan dengan kondisi normal.

---

### 3. Modifikasi program dengan menggabungkan antara UART dan I2C

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Arduino.h>

// Inisialisasi LCD I2C (Alamat 0x27, 16 kolom, 2 baris)
LiquidCrystal_I2C lcd(0x27, 16, 2);

const int pinPot = A0; 

void setup() {
  Serial.begin(9600);
  lcd.init();
  lcd.backlight();
}

void loop() {
  int nilaiADC = analogRead(pinPot);

  // Kalkulasi Volt (0.00 - 5.00V) dan Persentase
  float volt = (nilaiADC / 1023.0) * 5.0;
  int persen = map(nilaiADC, 0, 1023, 0, 100);

  // 1. Output ke Serial Monitor (Sesuai format tabel yang diminta)
  Serial.print("ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" Volt: ");
  Serial.print(volt);
  Serial.print(" V Persen: ");
  Serial.print(persen);
  Serial.println("%");

  // 2. Output ke LCD (I2C)
  // Baris 1: ADC, Volt, dan Persen
  lcd.setCursor(0, 0);
  lcd.print(nilaiADC);
  lcd.print(" ");
  lcd.print(volt, 1); // Menampilkan 1 angka di belakang koma (misal 5.0)
  lcd.print("V ");
  lcd.print(persen);
  lcd.print("%   "); // Spasi untuk clear sisa karakter

  // Baris 2: Progress Bar (Level)
  lcd.setCursor(0, 1);
  int panjangBar = map(nilaiADC, 0, 1023, 0, 16);
  for (int i = 0; i < 16; i++) {
    if (i < panjangBar) {
      lcd.print((char)255); 
    } else {
      lcd.print(" "); 
    }
  }

  delay(200); 
}
```

---

### 4) Table pengamatan pada Serial Monitor

| ADC | Volt (V) | Persen (%) |
|-----|----------|------------|
| 1   | 0.       | 0%         |
| 21  | 0.1      | 2%         |
| 49  | 0.2      | 5%         |
| 74  | 0.4      | 7%         |
| 96  | 0.5      | 9%         |

