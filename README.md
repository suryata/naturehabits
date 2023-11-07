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



