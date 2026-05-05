# Praktikum Sistem Mikrokontroler

## Percobaan 1: Analog to Digital Converter (ADC)
### IV. Pertanyaan Praktikum

1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?
2. Mengapa diperlukan fungsi map() dalam program tersebut?
3. Modifikasi program agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun potensiometer tetap memiliki rentang ADC 0–1023!

---

### 1. Fungsi analogRead()

Fungsi `analogRead()` digunakan untuk membaca nilai tegangan analog dari potensiometer yang terhubung ke pin analog Arduino. Nilai tersebut dikonversi menjadi data digital dengan rentang 0–1023. Nilai ini merepresentasikan posisi potensiometer yang kemudian digunakan sebagai input untuk mengatur pergerakan servo motor.

---

### 2. Fungsi map()

Fungsi `map()` digunakan untuk mengubah rentang nilai dari satu skala ke skala lain. Pada percobaan ini, nilai ADC (0–1023) diubah menjadi sudut servo (0–180 derajat). Hal ini diperlukan karena servo hanya dapat menerima nilai sudut tertentu, sehingga nilai ADC harus disesuaikan terlebih dahulu agar dapat digunakan.

---

### 3. Modifikasi Servo (30° – 150°)

```cpp
#include <Servo.h>

Servo myservo;

const int potensioPin = A0;
const int servoPin = 9;

int pos = 0;
int val = 0;

void setup() {
  myservo.attach(servoPin);
  Serial.begin(9600);
}

void loop() {
  val = analogRead(potensioPin);
  pos = map(val, 0, 1023, 30, 150);
  myservo.write(pos);

  Serial.print("ADC Potensio: ");
  Serial.print(val);
  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  delay(20);
}
```

---

## Percobaan 2: Pulse Width Modulation (PWM)
### IV. Pertanyaan Praktikum

1. Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!
2. Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?
3. Modifikasilah program agar LED hanya menyala pada rentang PWM 50 sampai 200!

---

### 1. Fungsi analogWrite()

Fungsi `analogWrite()` digunakan untuk menghasilkan sinyal PWM (Pulse Width Modulation) pada pin digital Arduino. PWM bekerja dengan cara mengatur lebar pulsa (duty cycle) sehingga menghasilkan tegangan rata-rata. Semakin besar duty cycle, maka LED akan semakin terang, dan jika kecil maka LED akan redup.

---

### 2. Hubungan ADC dan PWM

Nilai ADC memiliki rentang 0–1023, sedangkan PWM memiliki rentang 0–255. Oleh karena itu, digunakan fungsi `map()` untuk mengubah skala nilai ADC menjadi PWM. Secara umum, nilai PWM merupakan hasil pembagian skala dari ADC (sekitar 1/4 dari nilai ADC).

---

### 3. Modifikasi PWM (50 – 200)

```cpp
#include <Arduino.h>

const int potPin = A0;
const int ledPin = 9;

int nilaiADC = 0;
int pwm = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  nilaiADC = analogRead(potPin);
  pwm = map(nilaiADC, 0, 1023, 50, 200);
  analogWrite(ledPin, pwm);

  Serial.print("ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" | PWM: ");
  Serial.println(pwm);

  delay(50);
}
```
