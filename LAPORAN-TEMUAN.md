# Temuan-bug1

saya menemukan pada kode main.js itu terdapat parseInt() membaca jumlah barang jika kita input manual misal ya 1.9 tiba tiba kepotong sendiri jadi 1 kita gatau nih kenapa bisa kepotong sendiri tanpa adanya peringatan dan input yang di masukan pengguna tidak diproses dengan hasilnya

- untuk cara buktiinnya
bisa masukin kedalam keranjang dulu nah disitu ada yang bisa untuk input manual, masukin angka 1.9 ternyata malah kepotong otomatis jadi 1 sehingga tidak sesuai dengan input pengguna 

- kenapa ini bahaya / ga adil
pengguna dapat bingung dengan angka yang dimasukan kenapa bisa berbeda tanpa adanya peringatan atau notifikasi bukan mengubah nilainya itu diam diam

- cara betulin
Jangan menggunakan parseInt() untuk membaca input quantity tapi kita dapat menggunakan Number() kemudian divalidasi dengan Number.isInteger() nah jika pengguna memasukkan angka desimal, tampilkan pesan seperti "Jumlah barang harus berupa bilangan bulat." Selain itu, tambahkan atribut step="1" pada input agar browser hanya menerima angka bulat

# temuan bug 2

saya melihat saat saya masukin keranjang dan mengosongkan bukan mengnolkan ya tapi mengosongkan saya melihat item tersebut tidak kehapus dan data yang ada di stokpun tidak berkurang dan saya nyoba untuk pembayaran itu masih bisa terdeteksi 1 sedangkan saya itu mengkosongkan input stok tersebut

- cara buktiinnya
saya masukin ke + nah disitu ada di bagian keranjnag jumlah input manual saat saya hapus tidak angka sama sekali masih terdeteksi 1 untuk pembayaran dan stok pun juga sama

- kenapa ini bahaya / ga adil
karena pengguna akan mengira bahwa input ttersebut sudah di hapus dan barang sudah berubah, padahal sistem masih menggunakan jumlah lama untuk menghitung total pembayaran dan membuat tampilan tidak sesuai dengan data

- cara betulin
Saat input kosong, tampilkan pesan bahwa jumlah harus diisi atau kembalikan nilai sebelumnya. Jangan membiarkan tampilan input kosong sementara data di keranjang masih menggunakan nilai lama.

# temuan 3 KEAMANAN
bisa dilihat dalam validasi kupon itu masih dilakukan di sisi browser atau kita bisa sebut (client-side) yang artinya seluruh logika diskon, termasuk hash kupon dan besar potongan, berada di file javaScript yang bisa dilihat atau dimodifikasi 

- cara buktiinya
kita dapat lihat di tab f12 selanjutnya bisa membuka devtools lanjut ada tab sources dan file main.js 

const KUPON_HASH = "...";
const DISKON_KUPON = 0.9;
nah dibagian situ bisa dilihat diskon tersimpan langsung di browser

- kenapa ini bahaya / ga adil
Yang dirugikan adalah pemilik toko, karena potongan harga dapat disalahgunakan apabila logika bisnis hanya mengandalkan kode di browser

- cara betulin
untuk solusinya vlidasi kupon itu harus dipindahkan ke server (backend). Browser tersebut mengirim kode kupon, lalu server memeriksa apakah kupon valid dan mengembalikan besarnya diskonnah dengan begitu pengguna tidak dapat melihat ataupun mengubah aturan diskon

# temuan KEAMANAN
dapat dilihat dalam main.js terdapat fungsi placeOrder() langsung menghapus keranjang dan menampilkan pesan "Pesanan masuk!" tanpa mengirim data ke server atau melakukan validasi transaksi


- cara buktiinya
pertama kita jalankan dulu aplikasi lokalnya nah terus matikan koneksi internet kita tambahkan nih beberapa produk dan lanjut Klik Continue to Checkout lalu Confirm namun aplikasi tetap menampilkan pesan "Pesanan masuk!" meskipun tidak pernah berkomunikasi dengan server

- kenapa ini bahaya / ga adil
Pengguna mendapatkan konfirmasi yang tidak benar pada aplikasi nyata, hal ini dapat menyebabkan pengguna mengira pesanannya berhasil diproses padahal belum pernah diterima sistem

- cara betulin
Sebelum menampilkan pesan sukses, aplikasi harus mengirim data pesanan ke backend menggunakan API, lalu hanya menampilkan pesan berhasil apabila server mengembalikan respons sukses

# temuan KEAMANAN
untuk temuan keamanan selanjutnya terdapat aplikasi masih menggunakan innerHTML untuk menampilkan data produk dan keranjang nah saat ini data memang berasal dari array yang ditulis langsung di dalam kode sehingga belum dapat dieksploitasi oleh pengguna biasa, namun apabila di masa depan data produk berasal dari API, database, atau CMS tanpa proses sanitasi, penggunaan innerHTML dapat membuka peluang DOM XSS

- cara buktiinya
awalnya kita buka devtools terlebih dahulu dilanjutkan Pasang breakpoint sebelum renderProducts() nah ini kita ubah datanya jadi seperti ini 
products[0].name =
'<img src=x onerror=alert(1)>'

kita lanjutkan untuk eksekusi dan payload akan dimasukkan ke dalam innerHTML

- kenapa ini bahaya / ga adil
Jika data produk berasal dari sumber yang tidak terpercaya, penyerang dapat menyisipkan JavaScript berbahaya yang mencuri data pengguna atau mengubah tampilan halaman

- cara betulin
Gunakan textContent atau buat elemen DOM dengan createElement() daripada memasukkan data langsung ke innerHTML

# temuan ETIKA
sekarang bagian temuan etika terdapat aplikasi langsung menampilkan pesan "Pesanan masuk!" setelah tombol konfirmasi ditekan, padahal tidak ada proses pengiriman data ke server. Pesan tersebut dapat membuat pengguna percaya bahwa pesanannya berhasil diproses, padahal sebenarnya hanya simulasi

- cara buktiinya
untuk pertama jalankan dulu aplikasi secara lokal lalu kita tambahkan beberapa produk ke keranjang kita klik Continue to Checkout lalu Confirm lanjut aplikasi langsung menampilkan toast "Pesanan masuk!" tanpa melakukan komunikasi ke server

- kenapa ini bahaya / ga adil
Pengguna mendapatkan informasi yang menyesatkan di dalam aplikasi belanja nyata, konfirmasi keberhasilan seharusnya hanya diberikan setelah sistem benar-benar menerima dan memproses pesanan

- cara betulin
Tampilkan pesan sukses hanya setelah server mengembalikan status berhasil. Jika aplikasi hanya demo, gunakan pesan yang jelas

# temuan ETIKA

pada temuan etika selanjutnya aplikasi menggunakan istilah seperti "Catatan buat petani" dan "dikirim ke petani", padahal aplikasi tidak benar-benar mengirim catatan tersebut ke server atau kepada pihak lain. Hal ini dapat menimbulkan ekspektasi yang tidak sesuai terhadap proses yang sebenarnya

- cara buktiinya
di keranjang kita isi kolom catatan selanjutnya buka DevTools lalu kearah Network kita klik Confirm nah disini tidak ada request yang mengirim isi catatan ke server, setelah checkout selesai, catatan hanya hilang dari halaman

- kenapa ini bahaya / ga adil
Pengguna dapat mengira bahwa pesan mereka benar-benar diterima oleh petani atau penjual, padahal aplikasi tidak melakukan proses tersebut yang menyebabkan ini mengurangi transparansi terhadap pengguna

- cara betulin
Gunakan label yang lebih netral, misalnya "Catatan Pesanan", atau benar-benar kirim catatan tersebut ke backend jika memang akan diproses