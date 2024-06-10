# APLIKASI-KAS-RT KELOMPOK 7

| NO |        Anggota        |  Link GitHub  |  Link YouTube  |
|----|-----------------------|---------------|----------------|
| 1  |Ghufron Malik          |[Click](https://github.com/ghufronmalik64)      |[Click](https://www.youtube.com/@massssalek)       |
| 2  |Dilah Adelia Putri     |[Click](https://github.com/fadilah23)      |[Click](https://www.youtube.com/@fadilahputri2855)       |
| 3  |Nova Restina Maharani  |[Click](https://github.com/NovaRestina)      |[Click](https://www.youtube.com/@novarestinamaharani7047)       |

--------------------------------------------------------------------

| NO |        DAFTAR ISI       |   LINK    |
|----|-------------------------|-----------|
| 1  |Penjelasan               |[Click](https://github.com/ghufronmalik64/APLIKASI-KAS-RT?tab=readme-ov-file#1-penjelasan)
| 2  |Teknologi Yang Digunakan |[Click](https://github.com/ghufronmalik64/APLIKASI-KAS-RT?tab=readme-ov-file#ii-teknologi-yang-digunakan)|
| 3  |Install & Runing Project |[Click](https://github.com/ghufronmalik64/APLIKASI-KAS-RT?tab=readme-ov-file#iii-cara-install-dan-menjalankan-proyek)|
| 4  |Kontribusi               |[Click](https://github.com/ghufronmalik64/APLIKASI-KAS-RT?tab=readme-ov-file#iv-kontribusi)|



## I. Penjelasan
Aplikasi Kas RT adalah solusi digital untuk manajemen keuangan dan komunikasi di lingkungan RT. Aplikasi ini menyediakan fitur-fitur lengkap untuk mempermudah pengelolaan keuangan, notifikasi, dan komunikasi antara pengurus RT dan warga. Berikut adalah deskripsi fitur-fitur utama yang tersedia dalam aplikasi ini.

**1. Laporan Keuangan Kas RT Warga** <br>
**Deskripsi:** Menyediakan laporan detail mengenai pemasukan dan pengeluaran kas RT yang bisa diakses oleh warga.

**2. Notifikasi Laporan Kas RT dari Firebase** <br>
**Deskripsi:** Mengirimkan notifikasi kepada warga setiap ada laporan baru atau perubahan dalam kas RT.

**3. Cloud Messaging Kas RT dari Firebase** <br>
**Deskripsi:** Memungkinkan pengurus RT mengirim pesan atau informasi penting kepada warga secara cepat.

**4. Notifikasi bagi warga yang belum membayar** <br>
**Deskripsi:** Mengirimkan pengingat otomatis kepada warga yang belum membayar iuran RT.


## II. Teknologi yang Digunakan

- **Backend**: [APISpreadsheet.com](https://apispreadsheet.com/)
- **Frontend**: Android (Java/Kotlin)
- **Notifikasi & Cloud Messaging**: Firebase

## III. Cara Install dan Menjalankan Proyek

### 3.1 Backend

1. **Buat Spreadsheet di Google Sheets**:
   - Buat spreadsheet baru di Google Sheets dengan kolom yang sesuai untuk mencatat keuangan kas RT.
   - Misalnya, kolom `Date`, `Type`, `Amount`, `Description`, dll.
2. **Dapatkan API Key dari APISpreadsheet.com**:
   - Daftar dan masuk ke [APISpreadsheet.com](https://apispreadsheet.com/).
   - Ikuti panduan untuk menghubungkan Google Sheets Anda dan dapatkan API Key serta URL endpoint.

### 3.2 Frontend

1. **Buat Proyek Baru di Android Studio**:
   - Buka Android Studio dan buat proyek baru dengan template "Empty Activity".
   - Pilih bahasa pemrograman yang diinginkan (Java atau Kotlin).

2. **Clone Repositori**:
    ```bash
    git clone https://github.com/username/kas-rt-android.git
    cd kas-rt-android
    ```

3. **Tambahkan Dependensi untuk HTTP Requests dan Firebase**:
   - Buka `build.gradle` (Module: app) dan tambahkan dependensi berikut: 
   
    ```groovy
    dependencies {
        implementation 'com.squareup.retrofit2:retrofit:2.9.0'
        implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
        implementation 'com.google.firebase:firebase-messaging:23.0.0'
    }
    ```

4. **Konfigurasi Retrofit**:

    ```java
    // ApiClient.java
    package com.example.kasrt;
    import retrofit2.Retrofit;
    import retrofit2.converter.gson.GsonConverterFactory;
    public class ApiClient {
        private static Retrofit retrofit = null;
        public static Retrofit getClient(String baseUrl) {
            if (retrofit == null) {
                retrofit = new Retrofit.Builder()
                        .baseUrl(baseUrl)
                        .addConverterFactory(GsonConverterFactory.create())
                        .build();
            }
            return retrofit;
        }
    }
    ```
    
5. **Buat Interface API**:

    ```java
    // ApiService.java
    package com.example.kasrt;
    import java.util.List;
    import retrofit2.Call;
    import retrofit2.http.GET;
    import retrofit2.http.POST;
    import retrofit2.http.Body;
    public interface ApiService {
        @GET("your-spreadsheet-endpoint")
        Call<List<KasRecord>> getKasRecords();
        @POST("your-spreadsheet-endpoint")
        Call<Void> addKasRecord(@Body KasRecord record);
    }
    ```

6. **Model untuk KasRecord**:
   
    ```java
    // KasRecord.java
    package com.example.kasrt;
    public class KasRecord {
        private String date;
        private String type;
        private double amount;
        private String description;
        // Constructor, getters, and setters
    }
    ```

7. **Activity untuk Menampilkan Laporan Keuangan**:

    ```java
    // KasListActivity.java
    package com.example.kasrt;
    import android.os.Bundle;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.recyclerview.widget.LinearLayoutManager;
    import androidx.recyclerview.widget.RecyclerView;
    import java.util.List;
    import retrofit2.Call;
    import retrofit2.Callback;
    import retrofit2.Response;
    public class KasListActivity extends AppCompatActivity {
        private RecyclerView recyclerView;
        private KasAdapter adapter;
        private ApiService apiService;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_kas_list);
            recyclerView = findViewById(R.id.recyclerView);
            recyclerView.setLayoutManager(new LinearLayoutManager(this));
            apiService = ApiClient.getClient("https://api.apispreadsheet.com/").create(ApiService.class);
            loadKasRecords();
        }
        private void loadKasRecords() {
            Call<List<KasRecord>> call = apiService.getKasRecords();
            call.enqueue(new Callback<List<KasRecord>>() {
                @Override
                public void onResponse(Call<List<KasRecord>> call, Response<List<KasRecord>> response) {
                    List<KasRecord> kasRecords = response.body();
                    adapter = new KasAdapter(kasRecords);
                    recyclerView.setAdapter(adapter);
                }
                @Override
                public void onFailure(Call<List<KasRecord>> call, Throwable t) {
                    // Handle failure
                }
            });
        }
    }
    ```

8. **Layout untuk KasListActivity** (`activity_kas_list.xml`):

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
        <RecyclerView
            android:id="@+id/recyclerView"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>
    ```
    
### 3.3 Integrasi Firebase untuk Notifikasi dan Cloud Messaging

1. **Tambahkan Firebase ke Proyek Android**:
   - Ikuti petunjuk di [Firebase Console](https://console.firebase.google.com/) untuk menambahkan Firebase ke proyek Android Anda.

2. **Tambahkan Firebase Cloud Messaging**:
   - Tambahkan dependensi Firebase Cloud Messaging di `build.gradle` seperti yang sudah ditunjukkan di atas.

3. **Inisialisasi Firebase di `MainActivity.java`**:

    ```java
    // MainActivity.java
    package com.example.kasrt;
    import android.os.Bundle;
    import androidx.appcompat.app.AppCompatActivity;
    import com.google.firebase.messaging.FirebaseMessaging;
    public class MainActivity extends AppCompatActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            FirebaseMessaging.getInstance().subscribeToTopic("kasrt")
                .addOnCompleteListener(task -> {
                    String msg = "Subscribed to kasrt topic";
                    if (!task.isSuccessful()) {
                        msg = "Subscription failed";
                    }
                    // Log or Toast the message
                });
        }
    }
    ```

## IV. Kontribusi
**Kontribusi sangat diterima dan dihargai. Silakan fork repositori ini dan buat pull request Anda.**

Aplikasi Kas RT dirancang untuk mempermudah pengelolaan dan komunikasi di lingkungan RT, serta meningkatkan transparansi dan efisiensi dalam manajemen keuangan RT. Kami berharap aplikasi ini dapat memberikan manfaat yang signifikan bagi pengurus RT dan warga.
