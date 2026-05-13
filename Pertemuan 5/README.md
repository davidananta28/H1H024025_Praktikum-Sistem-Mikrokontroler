# Pertanyaan Praktikum 5.5.4

## 1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!

Ketiga task pada program RTOS tidak benar-benar berjalan secara bersamaan, melainkan berjalan secara bergantian (concurrent). Mekanisme yang digunakan adalah scheduler pada RTOS yang membagi waktu eksekusi CPU ke setiap task dengan sangat cepat sehingga terlihat seperti berjalan bersamaan.

Setiap task akan dieksekusi sesuai prioritas dan status task tersebut. Ketika sebuah task memasuki delay menggunakan `vTaskDelay()`, task tersebut akan masuk ke keadaan blocked sehingga CPU dapat digunakan oleh task lain. Dengan cara ini, beberapa task dapat berjalan secara efisien tanpa saling menghambat.

Pada praktikum ini, ketiga task memperoleh giliran eksekusi secara terus-menerus sehingga LED, serial monitor, maupun proses lainnya dapat berjalan secara paralel.

---

## 2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!

Untuk menambahkan task keempat pada program RTOS, langkah-langkahnya adalah sebagai berikut:

### Membuat fungsi task baru

```cpp
void Task4(void *pvParameters) {
    while(1) {
        Serial.println("Task 4 berjalan");
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}
```

### Menambahkan task pada setup()

```cpp
xTaskCreate(
    Task4,
    "Task4",
    1000,
    NULL,
    1,
    NULL
);
```

### Langkah tambahan
- Mengatur prioritas task sesuai kebutuhan
- Menentukan stack size yang cukup
- Meng-upload ulang program ke board

Setelah ditambahkan, scheduler RTOS akan otomatis mengatur eksekusi task keempat bersama task lainnya.

---

## 3. Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya?

```cpp
#include <Arduino_FreeRTOS.h>     // Import FreeRTOS untuk multitasking di Arduino
#include <semphr.h>               // Import semaphore (buat ngatur rebutan data)

void TaskBlink1(void *pvParameters);   // Deklarasi task kedip LED 1
void TaskBlink2(void *pvParameters);   // Deklarasi task kedip LED 2
void TaskReadPot(void *pvParameters);  // Deklarasi task pembaca potensiometer

SemaphoreHandle_t xMutex;          // Mutex dipakai agar variabel delayValue tidak dipakai barengan oleh banyak task
volatile int delayValue = 200;     // Nilai delay awal untuk kedip LED (boleh berubah)

void setup() {
  Serial.begin(9600);              // Nyalain serial monitor buat debugging
  xMutex = xSemaphoreCreateMutex(); // Bikin mutex

  // Membuat task pembaca potensiometer (prioritas 2)
  xTaskCreate(TaskReadPot, "ReadPot", 128, NULL, 2, NULL);

  // Membuat task LED pin 8 (prioritas 1)
  xTaskCreate(TaskBlink1, "Blink1", 128, NULL, 1, NULL);

  // Membuat task LED pin 7 (prioritas 1)
  xTaskCreate(TaskBlink2, "Blink2", 128, NULL, 1, NULL);

  vTaskStartScheduler();           // Start RTOS, program tidak lagi menggunakan loop()
}

void loop() {}                     // Tidak dipakai karena semua dijalankan oleh FreeRTOS

// Task 1: Membaca potensiometer lalu mengubah delay LED
void TaskReadPot(void *pvParameters) {
  while(1) {
    int potValue = analogRead(A0);        // Baca nilai pot 0-1023
    int mappedDelay = map(potValue, 0, 1023, 50, 1000); // Konversi ke delay 50-1000 ms

    // Mengunci mutex agar hanya task ini yang bisa mengubah delayValue
    if (xSemaphoreTake(xMutex, portMAX_DELAY)) {
      delayValue = mappedDelay;           // Simpan delay baru
      xSemaphoreGive(xMutex);             // Lepaskan mutex
    }

    // Tampilkan nilai pot dan delay hasil mapping
    Serial.print("Pot: ");
    Serial.print(potValue);
    Serial.print(" | Delay: ");
    Serial.println(mappedDelay);

    vTaskDelay(100 / portTICK_PERIOD_MS); // Baca pot setiap 100 ms
  }
}

// Task 2: Kedip LED di pin 8 dengan delay berdasarkan potensiometer
void TaskBlink1(void *pvParameters) {
  pinMode(8, OUTPUT);            // LED pertama
  while(1) {
    int d;
    if (xSemaphoreTake(xMutex, portMAX_DELAY)) {
      d = delayValue;            // Ambil nilai delay yang terbaru
      xSemaphoreGive(xMutex);
    }

    digitalWrite(8, HIGH);       // LED ON
    vTaskDelay(d / portTICK_PERIOD_MS);
    digitalWrite(8, LOW);        // LED OFF
    vTaskDelay(d / portTICK_PERIOD_MS);
  }
}

// Task 3: Kedip LED di pin 7 dengan delay lebih lambat (1.5x dari LED 1)
void TaskBlink2(void *pvParameters) {
  pinMode(7, OUTPUT);            // LED kedua
  while(1) {
    int d;
    if (xSemaphoreTake(xMutex, portMAX_DELAY)) {
      d = delayValue * 1.5;      // Buat delay LED 2 lebih lambat
      xSemaphoreGive(xMutex);
    }

    digitalWrite(7, HIGH);
    vTaskDelay(d / portTICK_PERIOD_MS);
    digitalWrite(7, LOW);
    vTaskDelay(d / portTICK_PERIOD_MS);
  }
}
```

Setelah ditambahkan sensor potensiometer, nilai analog dari potensiometer digunakan untuk mengatur delay kedipan LED. Semakin besar nilai potensiometer, maka LED akan berkedip semakin lambat. Sebaliknya, jika nilai potensiometer kecil, LED akan berkedip lebih cepat.

Sensor dibaca menggunakan `analogRead()`, kemudian nilainya dipetakan menjadi delay menggunakan fungsi `map()`. Delay tersebut digunakan pada `vTaskDelay()` sehingga kecepatan blinking LED dapat berubah secara dinamis sesuai posisi potensiometer.

Hasil pengujian menunjukkan bahwa perubahan putaran potensiometer dapat mengontrol kecepatan LED secara real-time tanpa mengganggu task lain karena sistem RTOS menjalankan task secara concurrent.

---

# Pertanyaan Praktikum 5.6.4

## 1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!

Kedua task berjalan secara bergantian menggunakan mekanisme scheduler pada RTOS. Walaupun terlihat berjalan bersamaan, sebenarnya CPU mengeksekusi task satu per satu dengan pergantian yang sangat cepat.

Scheduler akan memberikan jatah waktu eksekusi kepada masing-masing task berdasarkan prioritas dan kondisi task. Jika suatu task melakukan `vTaskDelay()`, maka task lain akan memperoleh kesempatan menggunakan CPU.

Karena pergantian terjadi sangat cepat, pengguna melihat kedua task seperti berjalan paralel secara bersamaan.

---

## 2. Apakah program ini berpotensi mengalami race condition? Jelaskan!

Ya, program berpotensi mengalami race condition apabila dua task mengakses variabel atau resource yang sama secara bersamaan tanpa mekanisme sinkronisasi.

Contohnya jika dua task membaca dan menulis variabel global secara bersamaan, data dapat berubah secara tidak terduga sehingga menghasilkan output yang salah atau tidak konsisten.

Untuk mencegah race condition, RTOS menyediakan mekanisme seperti:
- Mutex
- Semaphore
- Critical Section

Dengan mekanisme tersebut, akses resource dapat dilakukan secara bergantian sehingga data tetap aman dan konsisten.

---

## 3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya?

```cpp
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <DHT22.h>

#define DHT22_PIN 2

// Membuat objek DHT22
DHT22 dht22(DHT22_PIN);

struct readings {
  float temp;
  float hum;
};

QueueHandle_t my_queue;

void read_data(void *pvParameters);
void display_data(void *pvParameters);

void setup() {

  Serial.begin(9600);

  // Membuat queue
  my_queue = xQueueCreate(5, sizeof(struct readings));

  // Membuat task membaca sensor
  xTaskCreate(
    read_data,
    "Read Sensor",
    128,
    NULL,
    1,
    NULL
  );

  // Membuat task menampilkan data
  xTaskCreate(
    display_data,
    "Display",
    128,
    NULL,
    1,
    NULL
  );

  // Menjalankan scheduler
  vTaskStartScheduler();
}

void loop() {
  // kosong karena menggunakan FreeRTOS
}

// ==================== TASK MEMBACA SENSOR ====================
void read_data(void *pvParameters) {

  struct readings data;

  while (1) {

    // Membaca data dari DHT22
    float temperature = dht22.getTemperature();
    float humidity = dht22.getHumidity();

    // Mengecek apakah data valid
    if (dht22.getLastError() != dht22.OK) {

      Serial.print("DHT22 error: ");
      Serial.println(dht22.getLastError());

    } else {

      data.temp = temperature;
      data.hum = humidity;

      // Mengirim data ke queue
      xQueueSend(my_queue, &data, portMAX_DELAY);
    }

    // Delay 2 detik
    vTaskDelay(2000 / portTICK_PERIOD_MS);
  }
}

// ==================== TASK MENAMPILKAN DATA ====================
void display_data(void *pvParameters) {

  struct readings data;

  while (1) {

    // Menerima data dari queue
    if (xQueueReceive(my_queue, &data, portMAX_DELAY) == pdPASS) {

      Serial.print("Temperature = ");
      Serial.print(data.temp);
      Serial.println(" C");

      Serial.print("Humidity = ");
      Serial.print(data.hum);
      Serial.println(" %");

      Serial.println("-------------------");
    }
  }
}
```

Setelah menggunakan sensor DHT asli, program dapat membaca suhu dan kelembapan lingkungan secara real-time. Data sensor berubah secara dinamis sesuai kondisi sekitar dan ditampilkan pada Serial Monitor atau LCD.

Task pembacaan sensor berjalan secara periodik menggunakan `vTaskDelay()`, sedangkan task lain tetap dapat berjalan tanpa terganggu karena RTOS mengatur pembagian waktu eksekusi.

Hasil pengujian menunjukkan bahwa nilai suhu dan kelembapan berubah sesuai kondisi ruangan, misalnya ketika sensor disentuh atau didekatkan ke sumber panas.
