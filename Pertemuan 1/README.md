1.5.4 Pertanyaan Praktikum
  1. Pada kondisi apa program masuk ke blok if? 
  2. Pada kondisi apa program masuk ke blok else? 
  3. Apa fungsi dari perintah delay(timeDelay)? 
  4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), 
     ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → 
     mati dan berikan penjelasan disetiap baris kode nya

Jawaban:
1. Program akan masuk ke blok if ketika kondisi yang telah ditentukan bernilai benar, yaitu saat parameter atau variabel sudah memenuhi batas tertentu yang diinginkan dalam program. Pada percobaan ini contohnya if (timeDelay <= 100).
2. Program akan masuk ke blok else ketika kondisi pada if tidak terpenuhi (false), sehingga program menjalankan alternatif aksi yang telah disediakan. Pada percobaan ini jika timedelay > 100.
3. Perintah delay(timeDelay) berfungsi untuk memberikan jeda waktu dalam eksekusi program sehingga mengatur kecepatan perubahan kondisi, misalnya memperlambat atau mempercepat nyala LED.
4. LED tidak langsung reset → tetapi berubah dari cepat → sedang → mati
```cpp
const int ledPin = 6;         // Menentukan pin yang digunakan untuk LED
int timeDelay = 1000;         // Nilai awal delay (LED berkedip lambat)
bool fasePerlambatan = false; // false = percepatan, true = perlambatan

void setup() {
  pinMode(ledPin, OUTPUT); // Mengatur pin LED sebagai output
}

void loop() {
  digitalWrite(ledPin, HIGH); // Menyalakan LED
  delay(timeDelay);           // Menunggu sesuai waktu delay

  digitalWrite(ledPin, LOW);  // Mematikan LED
  delay(timeDelay);           // Menunggu lagi

  // Jika masih dalam fase percepatan
  if (!fasePerlambatan) {
    timeDelay -= 100; // Mengurangi delay → LED semakin cepat

    // Jika sudah mencapai batas tercepat
    if (timeDelay <= 100) {
      fasePerlambatan = true; // Berpindah ke fase perlambatan
    }
  } 
  else {
    timeDelay += 200; // Menambah delay → LED semakin lambat

    // Jika sudah mencapai batas tertentu (kecepatan sedang)
    if (timeDelay >= 700) {
      digitalWrite(ledPin, LOW); // Memastikan LED mati
      while(true);               // Menghentikan program (loop berhenti)
    }
  }
}
---



Jawaban:
1. https://www.tinkercad.com/things/jt3V7dlmb3V-praktikum-perulangan-?sharecode=MtP5Vuvq2ylu7-gddtWwZruVzZ2iBRxFl79opZxt5jU
2. Efek LED yang terlihat berjalan dari kiri ke kanan dibuat dengan menggunakan perulangan **for** yang melakukan penambahan nilai secara bertahap. Program dimulai dari pin dengan nilai paling kecil, lalu bergerak ke pin dengan nilai yang lebih besar secara berurutan. Pada setiap langkah, LED akan dinyalakan, kemudian diberi jeda waktu, lalu dimatikan kembali. Proses yang berulang ini menghasilkan tampilan seolah-olah LED bergerak dari arah kiri ke kanan.
3. Efek LED yang bergerak dari kanan ke kiri merupakan kebalikan dari pola sebelumnya, yaitu menggunakan perulangan **for** dengan pengurangan nilai secara bertahap. Program diawali dari pin dengan nilai paling tinggi, lalu berpindah ke pin dengan nilai yang lebih rendah secara berurutan. Pada setiap langkah, LED akan dinyalakan, diberi jeda waktu, kemudian dimatikan kembali. Proses ini membuat tampilan LED seolah-olah bergerak dari kanan ke kiri.
4. Program LED menyala tiga LED kanan dan tiga LED kiri secara bergantian
```cpp
int timer = 300; // Variabel untuk mengatur waktu jeda (delay)
void setup() {
  // Perulangan untuk mengatur pin 2 sampai 7 sebagai output
  for (int i = 2; i <= 7; i++) {
    pinMode(i, OUTPUT); // Set setiap pin sebagai output (untuk LED)
  }
}

void loop() {
  // Menyalakan LED sisi kiri (pin 2, 3, 4)
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, HIGH); // LED menyala
  }

  // Mematikan LED sisi kanan (pin 5, 6, 7)
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, LOW); // LED mati
  }

  delay(timer); // Memberikan jeda

  // Menyalakan LED sisi kanan (pin 5, 6, 7)
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, HIGH); // LED menyala
  }

  // Mematikan LED sisi kiri (pin 2, 3, 4)
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, LOW); // LED mati
  }

  delay(timer); // Jeda sebelum mengulang pola
}
