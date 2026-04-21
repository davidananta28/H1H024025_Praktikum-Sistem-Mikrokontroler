iv.Pertanyaan Praktikum 
1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan?
3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap sistem

Jawaban:
1. Proses kerja sistem adalah sebagai berikut:
User mengetik perintah pada Serial Monitor (keyboard)
Data dikirim melalui komunikasi UART (Serial) ke Arduino
Arduino membaca data menggunakan Serial.read()
Data dibandingkan:
'1' → LED menyala
'0' → LED mati
Arduino mengirim sinyal ke pin output
LED merespon sesuai perintah
2. 



3.6.4 Pertanyaan Praktikum 
1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian tersebut! 
2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar! 
3. Modifikasi program dengan menggabungkan antara UART dan I2C (keduanya sebagai output) sehingga:
   - Data tidak hanya ditampilkan di LCD tetapi juga di Serial Monitor
   - Adapun data yang ditampilkan pada Serial Monitor sesuai dengan table berikut:
     | ADC | Volt (V) | Persen (%) |
     Tampilan jika potensiometer dalam kondisi diputar paling kiri
   - ADC: 0  0% | setCursor(0, 0) dan Bar (level) | setCursor(0, 1)
   - Berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4. Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor 
 
Jawaban 
