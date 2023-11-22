# **Tugas 9 - Pemrograman Berbasis Platform**

**I Made Surya Anahata Putra**<br/>
**2206081370**<br/>
**PBP A**<br/>

## **Apakah bisa kita melakukan pengambilan data JSON tanpa membuat model terlebih dahulu? Jika iya, apakah hal tersebut lebih baik daripada membuat model sebelum melakukan pengambilan data JSON?**

Ya, Mengambil data JSON tanpa membuat model terlebih dahulu memungkinkan fleksibilitas dan kecepatan implementasi yang lebih tinggi, terutama bermanfaat ketika struktur JSON tidak terlalu kompleks atau hanya beberapa elemen tertentu yang diperlukan. Namun, pendekatan ini cenderung kurang terorganisir dan lebih rentan terhadap kesalahan tipe data, karena tidak ada struktur validasi yang ketat. Sebaliknya, mengambil data JSON dengan membuat model terlebih dahulu menjamin konsistensi dan integritas data, memudahkan proses validasi, dan lebih efektif dalam pengelolaan data untuk skala besar. Pendekatan ini memerlukan waktu pengembangan yang lebih lama dan kurang fleksibel terhadap perubahan struktur JSON, namun lebih cocok untuk aplikasi yang lebih kompleks dengan kebutuhan validasi dan konsistensi data yang ketat.  <br>

## **Jelaskan fungsi dari CookieRequest dan jelaskan mengapa instance CookieRequest perlu untuk dibagikan ke semua komponen di aplikasi Flutter.**

Dalam pengembangan aplikasi Flutter, CookieRequest berfungsi sebagai mekanisme untuk menangani cookie saat melakukan permintaan HTTP. Ini penting untuk memelihara sesi pengguna, menyediakan pengalaman pengguna yang konsisten, mengimplementasikan fitur keamanan seperti token dan proteksi anti-CSRF, serta mengoptimalkan kinerja jaringan. Alasan mengapa instance CookieRequest perlu dibagikan ke semua komponen di aplikasi adalah untuk memastikan konsistensi data di seluruh aplikasi, mengelola sumber daya secara lebih efisien dengan menghindari pembuatan instance berulang, memudahkan pembaruan dan pemeliharaan melalui titik pengelolaan terpusat, serta memfasilitasi integrasi dan modularitas. Dengan demikian, penggunaan instance CookieRequest yang dibagikan ini membantu menjaga keamanan dan integritas data pengguna, sekaligus memastikan pengalaman pengguna yang lancar dan efisien dalam aplikasi Flutter. <br>

## Jelaskan mekanisme pengambilan data dari JSON hingga dapat ditampilkan pada Flutter.
  + Pertama-tama membuat model item. Disini saya mengambil endpoint json dari tugas 6 lalu menggunakan website `quicktype` untuk diubah menjadi model Item. 
  + Menambahkan dependensi http dengan run perintah dibawah ini:
  ```
  flutter pub add http
  ```
  + Pada file `android/app/src/main/AndroidManifest.xml` tambahkan kode berikut:
  ```
  ...
    <application>
    ...
    </application>
    <!-- Required to fetch data from the Internet. -->
    <uses-permission android:name="android.permission.INTERNET" />
  ...
  ```
  + Membuat berkas `list_product.dart` pada folder `lib/screens`
  + Pada file `list_item.dart` tambahkan kode berikut:
  ```
  import 'package:flutter/material.dart';
  import 'package:http/http.dart' as http;
  import 'dart:convert';
  import 'package:<APP_NAME>/models/product.dart';
  ```
  ```
  class ProductPage extends StatefulWidget {
    const ProductPage({Key? key}) : super(key: key);

    @override
    _ProductPageState createState() => _ProductPageState();
  }

  class _ProductPageState extends State<ProductPage> {
  Future<List<Item>> fetchProduct() async {
      var url = Uri.parse(
          'http://127.0.0.1:8000/main/get-item/');
      var response = await http.get(
          url,
          headers: {"Content-Type": "application/json"},
      );

      // melakukan decode response menjadi bentuk json
      var data = jsonDecode(utf8.decode(response.bodyBytes));

      // melakukan konversi data json menjadi object Product
      List<Item> list_product = [];
      for (var d in data) {
          if (d != null) {
              list_product.add(Item.fromJson(d));
          }
      }
      return list_product;
  }

  @override
  Widget build(BuildContext context) {
      return Scaffold(
            appBar: AppBar(
              title: const Center(
                child: Text(
                  'List Item',
                ),
              ),
              backgroundColor: Colors.indigo,
              foregroundColor: Colors.white,
            ),
          drawer: const LeftDrawer(),
          body: FutureBuilder(
              future: fetchProduct(),
              builder: (context, AsyncSnapshot snapshot) {
                  if (snapshot.data == null) {
                      return const Center(child: CircularProgressIndicator());
                  } else {
                      if (!snapshot.hasData) {
                      return const Column(
                          children: [
                          Text(
                              "Tidak ada data Item.",
                              style:
                                  TextStyle(color: Color(0xff59A5D8), fontSize: 20),
                          ),
                          SizedBox(height: 8),
                          ],
                      );
                  } else {
                      return ListView.builder(  
                          itemCount: snapshot.data!.length,
                          itemBuilder: (_, index) => Container(
                                  margin: const EdgeInsets.symmetric(
                                      horizontal: 16, vertical: 12),
                                  padding: const EdgeInsets.all(20.0),
                                  child: InkWell(
                                    child: Column(
                                    mainAxisAlignment: MainAxisAlignment.start,
                                    crossAxisAlignment: CrossAxisAlignment.start,
                                    children: [
                                        Text(
                                        "${index + 1}. ${snapshot.data![index].fields.name}",
                                        style: const TextStyle(
                                            fontSize: 18.0,
                                            fontWeight: FontWeight.bold,
                                        ),
                                        ),
                                        const SizedBox(height: 10),
                                        Text("${snapshot.data![index].fields.amount}"),
                                        const SizedBox(height: 10),
                                        Text("${snapshot.data![index].fields.description}"),
                                        const SizedBox(height: 10),
                                        Text("${snapshot.data![index].fields.price}"),
                                          ElevatedButton(
                                            onPressed: () {
                                            Navigator.push(
                                              context,
                                              MaterialPageRoute(
                                                builder: (context) => DetailItemPage(item: snapshot.data![index]),
                                              ),
                                            );
                                        },
                                        child: const Text('Detail Item'),
                                      ),
                                    ],
                                    ),
                                  )
                              ));
                      }
                  }
              }));
      }
  }
  ```

## **Jelaskan mekanisme autentikasi dari input data akun pada Flutter ke Django hingga selesainya proses autentikasi oleh Django dan tampilnya menu pada Flutter.**

  + Meminta input mengenai username dan password di Flutter.
  + Menggunakan metode http.post untuk mengirim informasi data login user ke server Django
  + Setelah django mendapatkan data informasi login, django akan memverifikasi informasi login dan jika valid akan dapat login.
  + Setelah autentikasi berhasil, tampilan menu atau akses ke fitur akan di tampilkan di aplikasi flutter.

## Sebutkan seluruh widget yang kamu pakai pada tugas ini dan jelaskan fungsinya masing-masing.
  + `ListView`: Digunakan untuk loop item
  + `http`: Ditambahkan sebagai dependensi untuk membuat permintaan HTTP
  + `Elevated Button`: Untuk membuat button
  + `Text`: Menampilkan teks
  + `FutureBuilder`: Digunakan untuk membangun widget secara asinkron berdasarkan snapshot terbaru dari data.
  + `Provider` : Digunakan untuk mengelola dan menyediakan state dari kelas `CookieRequest` ke semua widget anak.
  + `SizedBox`: Memberikan space diantara widget

## Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step! (bukan hanya sekadar mengikuti tutorial).

- [x] Memastikan deployment proyek tugas Django kamu telah berjalan dengan baik. <br>
  + Mencoba redeploy tetapi css masih belum bisa, masih menanyakan ke asdos

- [x] Membuat halaman login pada proyek tugas Flutter. <br>
  + Buat `django-app` bernama authentication
  + Menambahkan `authentication` ke `INSTALLED APPS` pada `settings.py`
  + Menjalankan `pip install django-cors-headers`
  + Menambahkan `corsheaders` ke `INSTALLED APPS` pada `settings.py`
  + Menambahkan `corsheaders.middleware.CorsMiddleware` pada `settings.py`
  + Menambahkan variabel dibawah ini pada `settings.py`:
  ```
  CORS_ALLOW_ALL_ORIGINS = True
  CORS_ALLOW_CREDENTIALS = True
  CSRF_COOKIE_SECURE = True
  SESSION_COOKIE_SECURE = True
  CSRF_COOKIE_SAMESITE = 'None'
  SESSION_COOKIE_SAMESITE = 'None'
  ```
  + Membuat methode view untuk login pada berkas `authentication/views.py`:
  ```
  from django.shortcuts import render
  from django.contrib.auth import authenticate, login as auth_login
  from django.http import JsonResponse
  from django.views.decorators.csrf import csrf_exempt

  @csrf_exempt
  def login(request):
      username = request.POST['username']
      password = request.POST['password']
      user = authenticate(username=username, password=password)
      if user is not None:
          if user.is_active:
              auth_login(request, user)
              # Status login sukses.
              return JsonResponse({
                  "username": user.username,
                  "status": True,
                  "message": "Login sukses!"
                  # Tambahkan data lainnya jika ingin mengirim data ke Flutter.
              }, status=200)
          else:
              return JsonResponse({
                  "status": False,
                  "message": "Login gagal, akun dinonaktifkan."
              }, status=401)

      else:
          return JsonResponse({
              "status": False,
              "message": "Login gagal, periksa kembali email atau kata sandi."
          }, status=401)
  ```
  + Tambahkan kode berikut pada `urlpatterns` di `urls.py` aplikasi authentication:
  ```
  path('login/', login, name='login'),
  ``` 
  + Tambahkan kode berikut pada `urlpatterns` di `urls.py` pada berkas `home_shop/urls.py`:
  ```
  path('auth/', include('authentication.urls')),
  ```
  + Install beberapa package berikut:
  ```
  flutter pub add provider
  flutter pub add pbp_django_auth
  ```
  + Pada berkas `main.dart` ubahlah kode sebelumnya menjadi seperti ini untuk menyediakan `CookieRequest` library ke semua child widgets dengan menggunakan `provider`:
  ```
    class MyApp extends StatelessWidget {
      const MyApp({Key? key}) : super(key: key);

      @override
      Widget build(BuildContext context) {
          return Provider(
              create: (_) {
                  CookieRequest request = CookieRequest();
                  return request;
              },
              child: MaterialApp(
                  title: 'Flutter App',
                  theme: ThemeData(
                      colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
                      useMaterial3: true,
                  ),
                  home: MyHomePage()
              ),
          );
      }
  }
  ```
  + Buat berkas baru dengan nama `login.dart` pada folder `screens`
  + Isilah dengan kode dibawah ini
  ```
  import 'package:home_shop/screens/menu.dart';
  import 'package:flutter/material.dart';
  import 'package:pbp_django_auth/pbp_django_auth.dart';
  import 'package:provider/provider.dart';

  void main() {
      runApp(const LoginApp());
  }

  class LoginApp extends StatelessWidget {
  const LoginApp({super.key});

  @override
  Widget build(BuildContext context) {
      return MaterialApp(
          title: 'Login',
          theme: ThemeData(
              primarySwatch: Colors.blue,
      ),
      home: const LoginPage(),
      );
      }
  }

  class LoginPage extends StatefulWidget {
      const LoginPage({super.key});

      @override
      _LoginPageState createState() => _LoginPageState();
  }

  class _LoginPageState extends State<LoginPage> {
      final TextEditingController _usernameController = TextEditingController();
      final TextEditingController _passwordController = TextEditingController();

      @override
      Widget build(BuildContext context) {
          final request = context.watch<CookieRequest>();
          return Scaffold(
              appBar: AppBar(
                  title: const Text('Login'),
              ),
              body: Container(
                  padding: const EdgeInsets.all(16.0),
                  child: Column(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: [
                          TextField(
                              controller: _usernameController,
                              decoration: const InputDecoration(
                                  labelText: 'Username',
                              ),
                          ),
                          const SizedBox(height: 12.0),
                          TextField(
                              controller: _passwordController,
                              decoration: const InputDecoration(
                                  labelText: 'Password',
                              ),
                              obscureText: true,
                          ),
                          const SizedBox(height: 24.0),
                          ElevatedButton(
                              onPressed: () async {
                                  String username = _usernameController.text;
                                  String password = _passwordController.text;

                                  // Cek kredensial
                                  // TODO: Ganti URL dan jangan lupa tambahkan trailing slash (/) di akhir URL!
                                  // Untuk menyambungkan Android emulator dengan Django pada localhost,
                                  // gunakan URL http://10.0.2.2/
                                  final response = await request.login("http://<APP_URL_KAMU>/auth/login/", {
                                  'username': username,
                                  'password': password,
                                  });
                      
                                  if (request.loggedIn) {
                                      String message = response['message'];
                                      String uname = response['username'];
                                      Navigator.pushReplacement(
                                          context,
                                          MaterialPageRoute(builder: (context) => MyHomePage()),
                                      );
                                      ScaffoldMessenger.of(context)
                                          ..hideCurrentSnackBar()
                                          ..showSnackBar(
                                              SnackBar(content: Text("$message Selamat datang, $uname.")));
                                      } else {
                                      showDialog(
                                          context: context,
                                          builder: (context) => AlertDialog(
                                              title: const Text('Login Gagal'),
                                              content:
                                                  Text(response['message']),
                                              actions: [
                                                  TextButton(
                                                      child: const Text('OK'),
                                                      onPressed: () {
                                                          Navigator.pop(context);
                                                      },
                                                  ),
                                              ],
                                          ),
                                      );
                                  }
                              },
                              child: const Text('Login'),
                          ),
                      ],
                  ),
              ),
          );
      }
  }
  ```
  + Pada berkas `main.dart` ubah `home: MyHomePage()` menjadi `home: LoginPage()`
  
- [x] Mengintegrasikan sistem autentikasi Django dengan proyek tugas Flutter. <br>
  + Pada bagian login diatas sudah disertakan juga cara untuk autentikasi saat login.

- [x] Membuat model kustom sesuai dengan proyek aplikasi Django. <br>
  + Membuka endpoint JSON pada tugas sebelumnya
  + Lalu salin dan copy paste di website `quicktype`
  + Pada situs web Quicktype, ubahlah setup name menjadi Product, source type menjadi JSON, dan language menjadi Dart
  + Tempel data JSON yang telah disalin sebelumnya ke dalam textbox yang tersedia pada Quicktype.
  + Lalu click copy code dan salin pada berkas `models/item.dart`

- [x] Membuat halaman yang berisi daftar semua item yang terdapat pada endpoint JSON di Django yang telah kamu deploy. <br>
  - [x] Tampilkan name, amount, dan description dari masing-masing item pada halaman ini. <br>
    + Membuat berkas `list_product.dart` pada folder `lib/screens`
    + Pada file `list_item.dart` tambahkan kode berikut:
    ```dart
    import 'package:flutter/material.dart';
    import 'package:http/http.dart' as http;
    import 'dart:convert';
    import 'package:<APP_NAME>/models/product.dart';
    ```
    ```dart
    class ProductPage extends StatefulWidget {
      const ProductPage({Key? key}) : super(key: key);

      @override
      _ProductPageState createState() => _ProductPageState();
    }

    class _ProductPageState extends State<ProductPage> {
    Future<List<Item>> fetchProduct() async {
        var url = Uri.parse(
            'https://i-made213-tugas.pbp.cs.ui.ac.id/get-item/');
        var response = await http.get(
            url,
            headers: {"Content-Type": "application/json"},
        );

        // melakukan decode response menjadi bentuk json
        var data = jsonDecode(utf8.decode(response.bodyBytes));

        // melakukan konversi data json menjadi object Product
        List<Item> list_product = [];
        for (var d in data) {
            if (d != null) {
                list_product.add(Item.fromJson(d));
            }
        }
        return list_product;
    }

    @override
    Widget build(BuildContext context) {
        return Scaffold(
              appBar: AppBar(
                title: const Center(
                  child: Text(
                    'List Item',
                  ),
                ),
                backgroundColor: Colors.indigo,
                foregroundColor: Colors.white,
              ),
            drawer: const LeftDrawer(),
            body: FutureBuilder(
                future: fetchProduct(),
                builder: (context, AsyncSnapshot snapshot) {
                    if (snapshot.data == null) {
                        return const Center(child: CircularProgressIndicator());
                    } else {
                        if (!snapshot.hasData) {
                        return const Column(
                            children: [
                            Text(
                                "Tidak ada data Item.",
                                style:
                                    TextStyle(color: Color(0xff59A5D8), fontSize: 20),
                            ),
                            SizedBox(height: 8),
                            ],
                        );
                    } else {
                        return ListView.builder(  
                            itemCount: snapshot.data!.length,
                            itemBuilder: (_, index) => Container(
                                    margin: const EdgeInsets.symmetric(
                                        horizontal: 16, vertical: 12),
                                    padding: const EdgeInsets.all(20.0),
                                    child: InkWell(
                                      child: Column(
                                      mainAxisAlignment: MainAxisAlignment.start,
                                      crossAxisAlignment: CrossAxisAlignment.start,
                                      children: [
                                          Text(
                                          "${index + 1}. ${snapshot.data![index].fields.name}",
                                          style: const TextStyle(
                                              fontSize: 18.0,
                                              fontWeight: FontWeight.bold,
                                          ),
                                          ),
                                          const SizedBox(height: 10),
                                          Text("${snapshot.data![index].fields.amount}"),
                                          const SizedBox(height: 10),
                                          Text("${snapshot.data![index].fields.description}"),
                                          const SizedBox(height: 10),
                                          Text("${snapshot.data![index].fields.price}"),
                                            ElevatedButton(
                                              onPressed: () {
                                              Navigator.push(
                                                context,
                                                MaterialPageRoute(
                                                  builder: (context) => DetailItemPage(item: snapshot.data![index]),
                                                ),
                                              );
                                          },
                                          child: const Text('Detail Item'),
                                        ),
                                      ],
                                      ),
                                    )
                                ));
                        }
                    }
                }));
        }
    }
    ```
- [x] Membuat halaman detail untuk setiap item yang terdapat pada halaman daftar Item. <br>
  - [x] Halaman ini dapat diakses dengan menekan salah satu item pada halaman daftar Item. <br>
    + Pada berkas `list_item.html` tambahkan kode berikut untuk membuat button ke setiap detail item:
    ```dart
      ElevatedButton(
        onPressed: () {
        Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => DetailItemPage(item: snapshot.data![index]),
          ),
        );
      },
      child: const Text('Detail Item'),
    ),
    ```
  - [x] Tampilkan seluruh atribut pada model item kamu pada halaman ini. <br>
  - [x] Tambahkan tombol untuk kembali ke halaman daftar item. <br>
  + Untuk menambahkan atribut model dan tombol, buatlah berkas `detail_item.dart` lalu tambahkan kode berikut ini:
  ```dart
  import 'package:flutter/material.dart';
  import 'package:home_shop/models/item.dart';
  import 'package:home_shop/widgets/left_drawer.dart';

  class DetailItemPage extends StatefulWidget {
      final Item item;

      const DetailItemPage({super.key, required this.item});

      @override
      State<DetailItemPage> createState() => _DetailItemPageState();
  }

  class _DetailItemPageState extends State<DetailItemPage> {
    @override
    Widget build(BuildContext context) {
      return Scaffold(
            appBar: AppBar(
              title: const Center(
                child: Text(
                  'Detail Item',
                ),
              ),
              backgroundColor: Colors.indigo,
              foregroundColor: Colors.white,
            ),
        drawer: const LeftDrawer(),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              const Text(
                'Item Name:',
                style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
              ),
              Text(
                widget.item.fields.name,
                style: const TextStyle(fontSize: 36, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 20),
              Text(
                'Amount: ${widget.item.fields.amount}',
                style: const TextStyle(fontSize: 18),
              ),
              Text(
                'Description: ${widget.item.fields.description}',
                style: const TextStyle(fontSize: 18),
              ),
              Text(
                'Price: ${widget.item.fields.price}',
                style: const TextStyle(fontSize: 18),
              ),
              ElevatedButton(
                onPressed: () {
                  // Navigasi kembali ketika tombol ditekan
                  Navigator.pop(context);
                },
                child: const Text('Kembali'),
              ),            
            ],
          ),
        ),
      );
    }
  }
  ```

# **Tugas 8 - Pemrograman Berbasis Platform**

**I Made Surya Anahata Putra**<br/>
**2206081370**<br/>
**PBP A**<br/>

## **Jelaskan perbedaan antara Navigator.push() dan Navigator.pushReplacement(), disertai dengan contoh mengenai penggunaan kedua metode tersebut yang tepat!**

`Navigator.push()` dan `Navigator.pushReplacement()` adalah dua metode yang digunakan dalam Flutter untuk navigasi antar halaman, tetapi mereka bekerja dengan cara yang berbeda:

**1. Navigator.push():**
+ Fungsi ini digunakan untuk menavigasi ke halaman baru tanpa menghapus halaman saat ini dari stack navigasi.
+ Hal ini berarti ketika menekan tombol kembali, akan kembali ke halaman sebelumnya.
+ Ini berguna ketika ingin mempertahankan riwayat navigasi, seperti dalam kasus sebuah aplikasi dengan banyak halaman yang penggunanya mungkin ingin kembali ke halaman sebelumnya.

**Contoh Penggunaan:**
Misalkan kita memiliki aplikasi dengan dua halaman, Halaman A (Homepage) dan Halaman B (Detail Page). Jika Anda ingin berpindah dari Halaman A ke Halaman B dan masih ingin bisa kembali ke Halaman A, akan menggunakan Navigator.push().

**Contoh kode:**
```dart
    Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => HalamanB()),
    );
```

**2. Navigator.pushReplacement():**
+ Metode ini digunakan untuk menavigasi ke halaman baru dengan mengganti halaman saat ini di stack navigasi.
+ Halaman saat ini dihapus dari stack, dan tidak bisa diakses kembali dengan tombol kembali.
+ Ini berguna dalam kasus seperti proses login, di mana setelah berhasil masuk, pengguna kembali ke halaman login.
    
**Contoh kode:**
```dart
    Navigator.pushReplacement(
    context,
    MaterialPageRoute(builder: (context) => HalamanDashboard()),
    );
```

## **Jelaskan masing-masing layout widget pada Flutter dan konteks penggunaannya masing-masing!**
1. Column dan Row:

+ Column: Mengatur child widgets secara vertikal. Cocok digunakan untuk layout seperti formulir atau daftar vertikal.
+ Row: Mengatur child widgets secara horizontal. Ideal untuk layout seperti toolbar atau baris dengan beberapa elemen berdampingan.

2. Container:

Widget yang menggabungkan desain (seperti padding, margin, borders) dengan layout (seperti width, height). Cocok digunakan untuk membungkus widget lain dengan dekorasi tertentu atau untuk memberikan dimensi spesifik.

3. Stack:

Memungkinkan widget ditumpuk di atas satu sama lain. Ideal untuk kasus seperti overlay, jika ingin menempatkan satu widget di atas widget lain (misalnya, teks di atas gambar).

4. Wrap:

Mirip dengan Row atau Column, tetapi secara otomatis akan berpindah ke baris atau kolom baru ketika tidak ada ruang. Berguna untuk layout yang memerlukan responsivitas, seperti chip tag atau galeri foto.
ListView:

Digunakan untuk membuat daftar scrollable. Sangat berguna untuk menampilkan daftar item yang panjang, seperti daftar pesan atau hasil pencarian.

5. GridView:

Menampilkan child widgets dalam grid. Cocok untuk kasus seperti galeri gambar atau pilihan menu.

6. Expanded dan Flexible:

+ Expanded: Mengambil ruang yang tersedia di Row atau Column.
+ Flexible: Mirip dengan Expanded tetapi memberikan kontrol lebih lanjut atas bagaimana child widget menyesuaikan diri dengan ruang yang tersedia.

7. Padding:

Memberikan padding pada widget lain. Sangat berguna untuk memberikan ruang di sekitar widget lain tanpa mengubah isi widget tersebut.

8. Align dan Center:

+ Align: Mengatur posisi child widget di dalam dirinya.
+ Center: Kasus khusus dari Align yang menengahkan child widget.

9. ConstrainedBox dan SizedBox:

+ ConstrainedBox: Menerapkan batasan tambahan ke child widget, seperti batas minimum atau maksimum untuk lebar dan tinggi.
+ SizedBox: Versi spesifik dari ConstrainedBox yang memberikan ukuran tetap.

10. AspectRatio:

Memaksa child widget memiliki rasio aspek tertentu. Berguna untuk media atau widget lain yang dimensi dan proporsinya penting.

11. FittedBox:

Memastikan bahwa child widget pas dengan ruang yang tersedia, mengatur skala atau memotong jika perlu.
## **Sebutkan apa saja elemen input pada form yang kamu pakai pada tugas kali ini dan jelaskan mengapa kamu menggunakan elemen input tersebut!**
1. TextFormField untuk Nama Item <br>

```dart
TextFormField(
  decoration: InputDecoration(
    hintText: "Nama Item",
    labelText: "Nama Item",
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(5.0),
    ),
  ),
  onChanged: (String? value) {
    setState(() {
      _name = value!;
    });
  },
  validator: (String? value) {
    if (value == null || value.isEmpty) {
      return "Nama tidak boleh kosong!";
    }
    return null;
  },
)
```
2. TextFormField untuk Jumlah <br>

```dart
TextFormField(
  decoration: InputDecoration(
    hintText: "Jumlah",
    labelText: "Jumlah",
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(5.0),
    ),
  ),
  onChanged: (String? value) {
    setState(() {
      _amount = int.parse(value!);
    });
  },
  validator: (String? value) {
    if (value == null || value.isEmpty) {
      return "Jumlah tidak boleh kosong!";
    }
    if (int.tryParse(value) == null) {
      return "Jumlah harus berupa angka!";
    }
    return null;
  },
)
```

3. TextFormField untuk Deskripsi

```dart
TextFormField(
  decoration: InputDecoration(
    hintText: "Deskripsi",
    labelText: "Deskripsi",
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(5.0),
    ),
  ),
  onChanged: (String? value) {
    setState(() {
      _description = value!;
    });
  },
  validator: (String? value) {
    if (value == null || value.isEmpty) {
      return "Deskripsi tidak boleh kosong!";
    }
    return null;
  },
)
```

TextFormField untuk Calories

```dart
TextFormField(
  decoration: InputDecoration(
    hintText: "Calories",
    labelText: "Calories",
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(5.0),
    ),
  ),
  onChanged: (String? value) {
    setState(() {
      _kalori = value!;
    });
  },
  validator: (String? value) {
    if (value == null || value.isEmpty) {
      return "Calories tidak boleh kosong!";
    }
    return null;
  },
)
```

Memilih elemen input yang dilakukan sesuai berdasarkan tipe data yang harus diambil dari pengguna (seperti nama item, kuantitas, deskripsi, dan kandungan kalori). Selanjutnya, validasi data juga diimplementasikan untuk menjamin kesesuaian informasi yang diinput oleh pengguna dengan standar yang ditetapkan.

## **Bagaimana penerapan clean architecture pada aplikasi Flutter?**

Penerapan Clean Architecture dalam pengembangan aplikasi Flutter melibatkan strukturisasi kode dan arsitektur dengan cara yang memisahkan kepentingan (separation of concerns), memudahkan pengujian, dan meningkatkan pemeliharaan kode. Berikut adalah langkah-langkah umum untuk menerapkan Clean Architecture pada aplikasi Flutter:

**1. Pembagian Lapisan Aplikasi:**

+ Presentation Layer: Lapisan ini berisi UI dan logika presentasi. Ini termasuk widget Flutter, screens, dan controllers yang mengelola state. Pola yang sering digunakan di sini adalah Model-View-ViewModel (MVVM) atau similar.
+ Domain Layer: Lapisan ini berfungsi sebagai jantung dari aplikasi. Ini berisi entitas bisnis, use cases, dan abstraksi repository. Lapisan ini harus bebas dari ketergantungan pada framework dan library eksternal.
+ Data Layer: Lapisan ini bertanggung jawab atas akses data, seperti panggilan API, operasi database, dan penyimpanan cache. Ini termasuk repository implementations, data models, dan data sources.

**2. Prinsip SOLID:**

Penerapan prinsip SOLID (Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion) penting untuk memastikan kode yang bersih, modular, dan mudah di-maintain.

**3. Dependency Injection (DI):**

DI digunakan untuk mengurangi ketergantungan langsung antara kelas dan modul, yang mempermudah pengujian dan pemeliharaan. Framework seperti GetIt atau provider dapat digunakan untuk DI di Flutter.

**4. Pengujian:**

Clean Architecture memudahkan pengujian unit dan integrasi karena setiap lapisan dapat diuji secara terpisah. Lapisan presentasi dapat diuji dengan widget testing, domain layer dengan pengujian unit logika bisnis, dan data layer dengan mock atau fake objects.

**5. Manajemen State:**

Penerapan Clean Architecture seringkali diiringi dengan manajemen state yang baik, seperti menggunakan Provider, Riverpod, Bloc, atau Redux untuk mengelola state aplikasi secara efektif.

**6. Route Management:**

Manajemen route yang terorganisir, misalnya dengan menggunakan Navigator 2.0 atau library seperti auto_route, juga penting untuk menjaga navigasi yang terstruktur dan terpisah dari logika UI.

**7. Abstraksi dan Modularitas:**

Mendefinisikan interface untuk repository di domain layer dan implementasinya di data layer memudahkan pergantian sumber data tanpa mempengaruhi lapisan lain.

Contoh pembagiannya sebagai berikut: <br>

+ Domain
  + Entities
  + Usecases
  + Repositories

+ App
  + View
  + Controller
  + Presenter
  + Extra

+ Data
  + Repositories
  + Models
  + Mappers
  + Extra

+ Device
  + Devices
  + Extra

## **Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step!**
- [x] Membuat minimal satu halaman baru pada aplikasi, yaitu halaman formulir tambah item baru dengan ketentuan sebagai berikut: <br>
  + Pada folder `lib` buatlah file dengan nama `itemlist_form.dart`.
    - [x] Memakai minimal tiga elemen input, yaitu name, amount, description. Tambahkan elemen input sesuai dengan model pada aplikasi tugas Django yang telah kamu buat. <br>
    - [x] Memiliki sebuah tombol Save. <br>
    - [x] Setiap elemen input di formulir juga harus divalidasi dengan ketentuan sebagai berikut: <br>
    - [x] Setiap elemen input tidak boleh kosong. <br>
    - [x] Setiap elemen input harus berisi data dengan tipe data atribut modelnya. <br>
    + Pada file tersebut tambahkan kode dibawah ini
    ```dart
    import 'package:flutter/material.dart';
    import 'package:nature_habits/widgets/left_drawer.dart';
    import 'package:nature_habits/widgets/item_card.dart';

    class InventoryFormPage extends StatefulWidget {
    const InventoryFormPage({super.key});

    @override
    State<InventoryFormPage> createState() => _InventoryFormPageState();
    }

    class _InventoryFormPageState extends State<InventoryFormPage> {
    final _formKey = GlobalKey<FormState>();
    String _name = "";
    int _amount = 0;
    String _description = "";
    String _kalori = "";

    @override
    Widget build(BuildContext context) {
        return Scaffold(
        appBar: AppBar(
            title: const Center(
            child: Text(
                'Form Tambah Item',
            ),
            ),
            backgroundColor: Colors.indigo,
            foregroundColor: Colors.white,
        ),
        drawer: const LeftDrawer(),
        body: Form(
            key: _formKey,
            child: SingleChildScrollView(
                child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
                Padding(
                padding: const EdgeInsets.all(8.0),
                child: TextFormField(
                    decoration: InputDecoration(
                    hintText: "Nama Item",
                    labelText: "Nama Item",
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(5.0),
                    ),
                    ),
                    onChanged: (String? value) {
                    setState(() {
                        _name = value!;
                    });
                    },
                    validator: (String? value) {
                    if (value == null || value.isEmpty) {
                        return "Nama tidak boleh kosong!";
                    }
                    return null;
                    },
                ),
                ),
                Padding(
                padding: const EdgeInsets.all(8.0),
                child: TextFormField(
                    decoration: InputDecoration(
                    hintText: "Jumlah",
                    labelText: "Jumlah",
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(5.0),
                    ),
                    ),
                    onChanged: (String? value) {
                    setState(() {
                        _amount = int.parse(value!);
                    });
                    },
                    validator: (String? value) {
                    if (value == null || value.isEmpty) {
                        return "Jumlah tidak boleh kosong!";
                    }
                    if (int.tryParse(value) == null) {
                        return "Jumlah harus berupa angka!";
                    }
                    return null;
                    },
                ),
                ),
                Padding(
                padding: const EdgeInsets.all(8.0),
                child: TextFormField(
                    decoration: InputDecoration(
                    hintText: "Deskripsi",
                    labelText: "Deskripsi",
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(5.0),
                    ),
                    ),
                    onChanged: (String? value) {
                    setState(() {
                        _description = value!;
                    });
                    },
                    validator: (String? value) {
                    if (value == null || value.isEmpty) {
                        return "Deskripsi tidak boleh kosong!";
                    }
                    return null;
                    },
                ),
                ),
                Padding(
                padding: const EdgeInsets.all(8.0),
                child: TextFormField(
                    decoration: InputDecoration(
                    hintText: "Calories",
                    labelText: "Calories",
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(5.0),
                    ),
                    ),
                    onChanged: (String? value) {
                    setState(() {
                        _kalori = value!;
                    });
                    },
                    validator: (String? value) {
                    if (value == null || value.isEmpty) {
                        return "Calories tidak boleh kosong!";
                    }
                    return null;
                    },
                ),
                ),
                Align(
                alignment: Alignment.bottomCenter,
                child: Padding(
                    padding: const EdgeInsets.all(8.0),
                    child: ElevatedButton(
                    style: ButtonStyle(
                        backgroundColor: MaterialStateProperty.all(Colors.indigo),
                    ),
                    onPressed: () {
                        if (_formKey.currentState!.validate()) {
                        itemList.add(Item(_name, _amount, _description, _kalori));
                        showDialog(
                            context: context,
                            builder: (context) {
                            return AlertDialog(
                                title: const Text('Item berhasil tersimpan'),
                                content: SingleChildScrollView(
                                child: Column(
                                    crossAxisAlignment: CrossAxisAlignment.start,
                                    children: [
                                    Text('Nama: $_name'),
                                    Text('Jumlah: $_amount'),
                                    Text('Deskripsi: $_description'),
                                    Text('Calories: $_kalori'),
                                    ],
                                ),
                                ),
                                actions: [
                                TextButton(
                                    child: const Text('OK'),
                                    onPressed: () {
                                    Navigator.pop(context);
                                    },
                                ),
                                ],
                            );
                            },
                        );
                        _formKey.currentState!.reset();
                        }
                    },
                    child: const Text(
                        "Save",
                        style: TextStyle(color: Colors.white),
                    ),
                    ),
                ),
                ),
            ],
            )),
        ),
        );
    }
    }

    ```
- [x] Mengarahkan pengguna ke halaman form tambah item baru ketika menekan tombol Tambah Item pada halaman utama. <br>
  + Pada card pada halaman utama saya menambahkan Navigator untuk push page InventoryFormPage kedalam stack. Berikut kodenya: 
  ```dart
    if (item.name == "Tambah Item") {
    Navigator.push(
    context,
    MaterialPageRoute(
      builder: (context) => const InventoryFormPage(),
    ));
  }
  ```

- [x] Memunculkan data sesuai isi dari formulir yang diisi dalam sebuah pop-up setelah menekan tombol Save pada halaman formulir tambah item baru. <br>
+ Tambahkan fungsi `showDialog()` pada bagian `onPressed()` dan munculkan widget `AlertDialog` pada fungsi tersebut. Berikut kodenya:
```dart
    onPressed: () {
    if (_formKey.currentState!.validate()) {
      showDialog(
        context: context,
        builder: (context) {
          return AlertDialog(
            title: const Text('Item berhasil tersimpan'),
            content: SingleChildScrollView(
              child: Column(
                crossAxisAlignment:
                    CrossAxisAlignment.start,
                children: [
                  Text('Nama: $_name'),
                  Text('Jumlah: $_amount'),
                  Text('Deskripsi: $_description'),
                  Text('Calories: $_kalori'),
                ],
              ),
            ),
            actions: [
              TextButton(
                child: const Text('OK'),
                onPressed: () {
                  Navigator.pop(context);
                },
              ),
            ],
          );
        },
      );
    _formKey.currentState!.reset();
    }
  },
  ```

- [x] Membuat sebuah drawer pada aplikasi dengan ketentuan <br>
  + Pada folder `lib` buatlah folder `widgets` yang bernama `left_drawer.dart`. Lalu tambahkan kode berikut:
  ```dart
    import 'package:flutter/material.dart';
    import 'package:nature_habits/screens/itemlist.dart';
    import 'package:nature_habits/screens/itemlist_form.dart';
    import 'package:nature_habits/screens/menu.dart';

    class LeftDrawer extends StatelessWidget {
    const LeftDrawer({super.key});

    @override
    Widget build(BuildContext context) {
        return Drawer(
        child: ListView(
            children: [
            const DrawerHeader(
                decoration: BoxDecoration(
                color: Colors.indigo,
                ),
                child: Column(
                children: [
                    Text(
                    'Nature Habits',
                    textAlign: TextAlign.center,
                    style: TextStyle(
                        fontSize: 30,
                        fontWeight: FontWeight.bold,
                        color: Colors.white,
                    ),
                    ),
                    Padding(padding: EdgeInsets.all(10)),
                    Text(
                    "Catat seluruh keperluan di sini!",
                    style: TextStyle(
                        fontSize: 15.0,
                        fontWeight: FontWeight.normal,
                        color: Colors.white),
                    ),
                ],
                ),
            ),
            ListTile(
                leading: const Icon(Icons.home_outlined),
                title: const Text('Halaman Utama'),
                // Bagian redirection ke MyHomePage
                onTap: () {
                Navigator.pushReplacement(
                    context,
                    MaterialPageRoute(
                        builder: (context) => MyHomePage(),
                    ));
                },
            ),
            ListTile(
                leading: const Icon(Icons.add_shopping_cart),
                title: const Text('Tambah Item'),
                // Bagian redirection ke ItemFormPage
                onTap: () {
                Navigator.pushReplacement(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const InventoryFormPage(),
                    ));
                },
            ),
            ListTile(
                leading: const Icon(Icons.checklist),
                title: const Text('Lihat Item'),
                // Bagian redirection ke ItemFormPage
                onTap: () {
                Navigator.pushReplacement(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const ItemListPage(),
                    ));
                },
            ),
            ],
        ),
        );
    }
    }

  ```

## **Melakukan add-commit-push ke GitHub.**


# **Tugas 7 - Pemrograman Berbasis Platform**

**I Made Surya Anahata Putra**<br/>
**2206081370**<br/>
**PBP A**<br/>

## **Apa perbedaan utama antara stateless dan stateful widget dalam konteks pengembangan aplikasi Flutter?**
> Stateless Widget:

+ Tidak Berubah <br>
Stateless widget tidak bisa berubah, mereka tidak dapat memiliki keadaan yang berubah setelah dibuat. Ini berarti bahwa nilai dan konfigurasi mereka bersifat final dan tidak berubah sepanjang waktu hidup widget.
+ Render UI <br>
Stateless widget digunakan untuk merender bagian UI yang tidak perlu diperbarui dengan data yang berubah. Contohnya adalah icon atau teks yang tidak berubah.
+ Lifecycle <br>
Stateless widget memiliki lifecycle yang sederhana. Mereka memiliki fungsi build yang dipanggil ketika widget tersebut harus digambar di layar.
+ Contoh: Text, Icon, dan FlatButton.
> Stateful Widget:

+ Dinamis dan Berubah<br>
Stateful widget bisa berubah. Ini berarti mereka dapat mempertahankan keadaan yang mungkin berubah selama waktu hidup widget. Misalnya, stateful widget dapat melacak apakah tombol telah ditekan atau tidak, atau memperbarui tampilan setelah data dari jaringan diterima.
+ Menyimpan Keadaan<br>
Stateful widget memisahkan objek state dari objek widget. Objek state (State) adalah tempat untuk variabel-variabel yang dapat berubah dan mempengaruhi bagaimana widget terlihat atau berfungsi.
+ Lifecycle yang Kompleks<br>
Stateful widget memiliki lifecycle yang lebih kompleks yang mencakup createState(), initState(), setState(), dispose(), dan lain-lain, yang memungkinkan pengembang untuk mengontrol siklus hidup dari keadaan widget.
+ Contoh: Checkbox, Slider, dan Form.
## **Sebutkan seluruh widget yang kamu gunakan untuk menyelesaikan tugas ini dan jelaskan fungsinya masing-masing**
+ MaterialApp: Merupakan widget paling atas yang mengatur aplikasi Flutter, dengan fungsinya meliputi penerapan sistem navigasi dan tema.

+ Scaffold: Widget ini memberikan kerangka dasar sesuai dengan prinsip Material Design, termasuk struktur untuk AppBar, Drawer, dan BottomNavigationBar.

+ AppBar: Diletakkan pada bagian atas dari Scaffold, widget ini kebanyakan digunakan untuk menampilkan judul dan aksi aplikasi.

+ ThemeData: Widget ini mengatur tema untuk aplikasi, termasuk warna, tipografi, dan elemen visual lainnya.

+ ColorScheme: Dengan widget ini, skema warna yang konsisten dapat diterapkan melalui ThemeData di seluruh aplikasi.

+ Text: Digunakan untuk menampilkan teks dalam aplikasi dengan opsi untuk mengatur gaya dan penampilannya.

+ SnackBar: Menampilkan pesan sementara di bagian bawah layar sebagai respons terhadap tindakan pengguna.

+ Padding: Widget ini memberikan ruang di sekitar komponen lain, memungkinkan untuk mengatur tata letak dengan lebih baik.

+ Column: Widget ini mengatur anak-anak widget secara vertikal.

+ GridView.count: Mengatur item-item dalam bentuk grid dengan jumlah kolom yang sudah ditetapkan.

+ Center: Widget ini mengatur posisi anak-anak widget di tengah-tengah widget induknya.

+ Container: Merupakan widget yang menggabungkan berbagai fungsi, termasuk pembatasan ukuran, padding, margin, dan dekorasi.

+ Material: Menyediakan efek visual Material Design untuk sebuah widget.

+ InkWell: Widget ini tidak hanya memberikan efek visual pada material tetapi juga menangani interaksi tap dari pengguna.

+ Icon: Widget untuk menampilkan ikon dengan berbagai pilihan gaya dan ukuran.

## **Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step (bukan hanya sekadar mengikuti tutorial)**
-   [x] Membuat sebuah program Flutter baru dengan tema inventory seperti tugas-tugas sebelumnya.
- [x] Membuat tiga tombol sederhana dengan ikon dan teks untuk:
    - [x] Melihat daftar item (Lihat Item)
    - [x] Menambah item (Tambah Item)
    - [x] Logout (Logout)
- [x] Memunculkan Snackbar dengan tulisan:
    - [x] "Kamu telah menekan tombol Lihat Item" ketika tombol Lihat Item ditekan.
    - [x] "Kamu telah menekan tombol Tambah Item" ketika tombol Tambah Item ditekan.
    - [x] "Kamu telah menekan tombol Logout" ketika tombol Logout ditekan.

  >Berikut langkah-langkah melakukan checklist tersebut:<br>

    1. *Generate* proyek Flutter baru dengan nama `nature_habits` dengan menjalankan kode berikut.
        ```
        flutter create nature_habits
        cd nature_habits
        ```
    2. Membuat file `menu.dart` pada `nature_habits/lib` dan menambahkan import material flutter.
        ```dart
        import 'package:flutter/material.dart';
        ```
    3. Membuat class ShopItem dengan atribut yang sesuai
    ```dart
        class ShopItem {
        final String name;
        final IconData icon;
        final Color color;

        ShopItem(this.name, this.icon, this.color);
    }
    ```
    4. Meng-*cut* line ke-39 sampai line akhir pada `main.dart` ke `menu.dart` serta mengganti properti `home` sebagai berikut.
        ```dart
        home: MyHomePage(),
        ```
    5. Mengubah MyHomePage pada `menu.dart` sebagai statelessWidget sebagai berikut.
        ```dart
        class MyHomePage extends StatelessWidget {
            MyHomePage({Key? key}) : super(key: key);
        }
        ```
    6. Menambahkan variable list yang menampung object item pada class MyHomePage sebagai berikut.
        ```dart
        ...
        final List<ShopItem> items = [
            ShopItem("Lihat Item", Icons.checklist,
                const Color.fromARGB(255, 100, 152, 242)),
            ShopItem("Tambah Item", Icons.add_shopping_cart,
                const Color.fromARGB(255, 254, 134, 98)),
            ShopItem("Logout", Icons.logout, const Color.fromARGB(255, 116, 244, 120)),
        ];
        ```
    7. Menambahkan widget build yang melakukan return `Scaffold`
    ```dart
        @override
        Widget build(BuildContext context) {
            return Scaffold(
            appBar: AppBar(
                title: const Text(
                'Nature Habits',
                ),
            ),
            body: SingleChildScrollView(
                // Widget wrapper yang dapat discroll
                child: Padding(
                padding: const EdgeInsets.all(10.0), // Set padding dari halaman
                child: Column(
                    // Widget untuk menampilkan children secara vertikal
                    children: <Widget>[
                    const Padding(
                        padding: EdgeInsets.only(top: 10.0, bottom: 10.0),
                        // Widget Text untuk menampilkan tulisan dengan alignment center dan style yang sesuai
                        child: Text(
                        'Nature Gifts', // Text yang menandakan toko
                        textAlign: TextAlign.center,
                        style: TextStyle(
                            fontSize: 30,
                            fontWeight: FontWeight.bold,
                        ),
                        ),
                    ),
                    // Grid layout
                    GridView.count(
                        // Container pada card kita.
                        primary: true,
                        padding: const EdgeInsets.all(20),
                        crossAxisSpacing: 10,
                        mainAxisSpacing: 10,
                        crossAxisCount: 3,
                        shrinkWrap: true,
                        children: items.map((ShopItem item) {
                        // Iterasi untuk setiap item
                        return ShopCard(item);
                        }).toList(),
                    ),
                    ],
                ),
                ),
            ),
            );
        }
    ```
    8. Menambahkan class ShopCard sebagai stateless widget untuk menampilkan card sebagai berikut.
    ```dart
        class ShopCard extends StatelessWidget {
        final ShopItem item;

        const ShopCard(this.item, {super.key}); // Constructor

        @override
        Widget build(BuildContext context) {
            return Material(
            color: item.color,
            child: InkWell(
                // Area responsive terhadap sentuhan
                onTap: () {
                // Memunculkan SnackBar ketika diklik
                ScaffoldMessenger.of(context)
                    ..hideCurrentSnackBar()
                    ..showSnackBar(SnackBar(
                        content: Text("Kamu telah menekan tombol ${item.name}!")));
                },
                child: Container(
                // Container untuk menyimpan Icon dan Text
                padding: const EdgeInsets.all(8),
                child: Center(
                    child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                        Icon(
                        item.icon,
                        color: Colors.white,
                        size: 30.0,
                        ),
                        const Padding(padding: EdgeInsets.all(3)),
                        Text(
                        item.name,
                        textAlign: TextAlign.center,
                        style: const TextStyle(color: Colors.white),
                        ),
                    ],
                    ),
                ),
                ),
            ),
            );
        }
    ```
 ## **Melakukan add-commit-push ke GitHub**



