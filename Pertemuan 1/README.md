1.5.4 Pertanyaan Praktikum
  1. Pada kondisi apa program masuk ke blok if? 
  2. Pada kondisi apa program masuk ke blok else? 
  3. Apa fungsi dari perintah delay(timeDelay)? 
  4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), 
     ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → 
     mati dan berikan penjelasan disetiap baris kode nya

Jawaban:
1. Program akan masuk ke blok if ketika kondisi yang telah ditentukan bernilai benar, yaitu saat parameter atau variabel        sudah memenuhi batas tertentu yang diinginkan dalam program. Pada percobaan ini contohnya if (timeDelay <= 100).
2. Program akan masuk ke blok else ketika kondisi pada if tidak terpenuhi (false), sehingga program menjalankan alternatif      aksi yang telah disediakan. Pada percobaan ini jika timedelay > 100.
3. Perintah delay(timeDelay) berfungsi untuk memberikan jeda waktu dalam eksekusi program sehingga mengatur kecepatan           perubahan kondisi, misalnya memperlambat atau mempercepat nyala LED.
4. 

1.6.2 Pertanyaan Praktikum
  1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
  2. Jelaskan bagaimana program membuat efek LED berjalan dari kiri ke kanan!
  3. Jelaskan bagaimana program membuat LED kembali dari kanan ke kiri!
  4. Buatkan program agar LED menyala tiga LED kanan dan tiga LED kiri secara bergantian dan berikan penjelasan disetiap        baris kode nya dalam bentuk README.md!

Jawaban:
1. https://www.tinkercad.com/things/jt3V7dlmb3V-praktikum-perulangan-?sharecode=MtP5Vuvq2ylu7-gddtWwZruVzZ2iBRxFl79opZxt5jU
2. Efek LED yang terlihat berjalan dari kiri ke kanan dibuat dengan menggunakan perulangan **for** yang melakukan penambahan nilai secara bertahap. Program dimulai dari pin dengan nilai paling kecil, lalu bergerak ke pin dengan nilai yang lebih besar secara berurutan. Pada setiap langkah, LED akan dinyalakan, kemudian diberi jeda waktu, lalu dimatikan kembali. Proses yang berulang ini menghasilkan tampilan seolah-olah LED bergerak dari arah kiri ke kanan.
3. Efek LED yang bergerak dari kanan ke kiri merupakan kebalikan dari pola sebelumnya, yaitu menggunakan perulangan **for** dengan pengurangan nilai secara bertahap. Program diawali dari pin dengan nilai paling tinggi, lalu berpindah ke pin dengan nilai yang lebih rendah secara berurutan. Pada setiap langkah, LED akan dinyalakan, diberi jeda waktu, kemudian dimatikan kembali. Proses ini membuat tampilan LED seolah-olah bergerak dari kanan ke kiri.
4. Program LED menyala tiga LED kanan dan tiga LED kiri secara bergantian
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

  delay(timer); // Memberikan jeda agar perubahan terlihat

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

## Source Code

```cpp
void setup() {
  // Menginisialisasi pin 2 sampai 7 sebagai OUTPUT
  for (int ledPin = 2; ledPin < 8; ledPin++) {
    pinMode(ledPin, OUTPUT);
  }
}

void loop() {

  // --- MENYALAKAN 3 LED KIRI ---
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, HIGH); // LED kiri menyala
  }
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, LOW); // LED kanan mati
  }

  delay(1000); // Jeda 1 detik

  // --- MENYALAKAN 3 LED KANAN ---
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, LOW); // LED kiri mati
  }
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, HIGH); // LED kanan menyala
  }

  delay(1000); // Jeda 1 detik
}
