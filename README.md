# Querying with SQL & Python

Menghubungkan Python ke database SQLite (Chinook) dan menulis query SQL untuk menjawab pertanyaan bisnis, lalu memuat hasilnya ke Pandas dan memvisualisasikannya dengan Matplotlib. Proyek ini merupakan bagian dari latihan Data Analyst di [roadmap.sh](https://roadmap.sh/projects/querying-sql-python).

## Ringkasan

Proyek ini menjawab tiga pertanyaan bisnis dari database toko musik digital: lagu terlaris, negara dengan pendapatan terbesar, dan karyawan penjualan terbaik. Seluruh query ditulis dengan SQL (`SELECT`, `JOIN`, `GROUP BY`, `ORDER BY`), dijalankan dari Python lewat `sqlite3`, lalu hasilnya dimuat ke DataFrame Pandas.

## Database

| Aspek | Keterangan |
|---|---|
| Sumber | [Chinook database (SQLite)](https://github.com/lerocha/chinook-database) |
| Ukuran file | sekitar 984 KB |
| Jumlah tabel | 11 |
| Isi | Artis, album, lagu, pelanggan, faktur, dan karyawan sebuah toko musik digital |
| Total pendapatan | 2.328,60 dari 412 faktur di 24 negara |

File database diunduh otomatis lewat sel pertama notebook, sehingga tidak perlu disimpan di repositori.

## Teknologi

| Alat | Kegunaan |
|---|---|
| Python | Bahasa pemrograman |
| sqlite3 | Menghubungkan Python ke database SQLite |
| SQL | Menanyai data |
| Pandas | Memuat hasil query ke DataFrame |
| Matplotlib | Visualisasi |
| Google Colab | Lingkungan kerja |

## Relasi Antartabel

Relasi dibaca langsung dari database dengan `PRAGMA foreign_key_list`.

| Dari (tabel.kolom) | Ke (tabel.kolom) | Artinya |
|---|---|---|
| InvoiceLine.TrackId | Track.TrackId | Setiap baris faktur menunjuk ke satu lagu |
| InvoiceLine.InvoiceId | Invoice.InvoiceId | Setiap baris faktur milik satu faktur |
| Invoice.CustomerId | Customer.CustomerId | Setiap faktur milik satu pelanggan |
| Customer.SupportRepId | Employee.EmployeeId | Setiap pelanggan ditangani satu karyawan |
| Employee.ReportsTo | Employee.EmployeeId | Karyawan melapor ke karyawan lain |
| Track.AlbumId, GenreId, MediaTypeId | Album, Genre, MediaType | Lagu memiliki album, genre, dan jenis media |
| Album.ArtistId | Artist.ArtistId | Album dimiliki satu artis |
| PlaylistTrack.PlaylistId, TrackId | Playlist, Track | Menghubungkan daftar putar dengan lagu |

## Pertanyaan dan Temuan Utama

| No | Pertanyaan | Tabel yang dipakai | Temuan |
|---|---|---|---|
| 1 | 10 lagu terlaris | `InvoiceLine`, `Track` | Tidak ada lagu yang terjual lebih dari 2 kali. Sebanyak 256 lagu terjual 2 kali dan 1.728 lagu terjual 1 kali, sehingga peringkat teratas berupa hasil seri |
| 2 | Negara dengan pendapatan terbesar | `Invoice` | USA (523,06 dari 91 faktur), sekitar 22,5% dari total, disusul Canada (303,96) dan France (195,10) |
| 3 | Karyawan penjualan terbaik | `Employee`, `Customer`, `Invoice` | Jane Peacock (833,04 dari 146 faktur dan 21 pelanggan), disusul Margaret Park (775,40) dan Steve Johnson (720,16) |

## Temuan Penting

- **Query 1 berupa hasil seri.** Karena banyak lagu terjual dalam jumlah yang sama, 10 besar ditentukan dengan aturan tambahan: jumlah terjual terbanyak, lalu pendapatan terbesar, lalu urutan abjad. Peringkat 9 dan 10 tidak dapat dianggap sebagai yang "terbaik" secara mutlak.
- **USA unggul karena jumlah pelanggannya.** USA memiliki 13 pelanggan, jauh lebih banyak dari Canada (8). Rata-rata nilai per faktur relatif sama pada negara dengan banyak pelanggan (sekitar 5,4 sampai 5,8).
- **Jane Peacock unggul karena menangani pelanggan terbanyak.** Per pelanggan, Steve Johnson sedikit lebih tinggi (sekitar 40,0 dibanding sekitar 39,7).
- Hasil SQL dan Pandas untuk pendapatan per negara identik.

## Keputusan Analisis

| Hal | Keputusan | Alasan |
|---|---|---|
| Arti "lagu terlaris" | Jumlah terjual terbanyak, lalu pendapatan, lalu abjad | Banyak lagu seri pada jumlah terjual |
| Arti "negara" | Negara penagihan faktur (`BillingCountry`) | Kolom ini langsung tersedia di tabel `Invoice`, tanpa `JOIN` |
| Arti "penjualan karyawan" | Total faktur dari pelanggan yang ditangani karyawan | Database tidak punya kolom penjualan per karyawan, hubungannya lewat `Customer.SupportRepId` |

## SQL dan Pandas

| Aspek | SQL | Pandas |
|---|---|---|
| Penggabungan beberapa tabel | Ringkas dengan `JOIN` | Perlu `merge` berulang |
| Ukuran data | Dihitung di database | Seluruh data dimuat ke memori |
| Pengolahan lanjutan dan grafik | Terbatas | Lebih fleksibel |

Dalam proyek ini keduanya dipakai bersama: SQL untuk query, Pandas untuk grafik.

## Contoh Hasil

![10 negara dengan pendapatan terbesar](images/pendapatan_negara.png)

## Keterbatasan

- Chinook adalah database contoh, sehingga temuan tidak mewakili toko musik sungguhan.
- Pendapatan per pelanggan dan per faktur relatif seragam, jadi peringkat lebih ditentukan oleh jumlah pelanggan daripada besarnya belanja.
- Banyak negara di peringkat bawah memiliki pendapatan dan jumlah faktur yang sama, sehingga urutannya di sana tidak bermakna.
- Rata-rata per faktur pada negara dengan satu atau dua pelanggan sedikit lebih tinggi (hingga 6,66) tetapi dihitung dari sangat sedikit data.

## Struktur Repositori

```
Querying-with-SQL-and-Python/
├── images/
│   └── pendapatan_negara.png
├── querying-with-sql-and-python.ipynb
└── README.md
```

## Cara Menjalankan

| Langkah | Yang dilakukan |
|---|---|
| 1 | Instal library: `pip install pandas matplotlib jupyter` |
| 2 | Unduh database: `wget -O Chinook.sqlite https://raw.githubusercontent.com/lerocha/chinook-database/master/ChinookDatabase/DataSources/Chinook_Sqlite.sqlite` |
| 3 | Buka notebook dengan `jupyter notebook` |
| 4 | Jalankan semua sel dari atas ke bawah |

Di Google Colab, langkah 1 dan 2 sudah ditangani oleh dua sel pertama notebook.

## Tautan

| Item | Tautan |
|---|---|
| Notebook di Colab | [isi tautan notebook Colab] |
| Halaman proyek | https://roadmap.sh/projects/querying-sql-python |

## Penulis

[Nama kamu] · [tautan GitHub atau LinkedIn]
