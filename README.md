Tugas 1 Data Wrangling - Integrasi Data Multi-Sumber

Muhammad Adam Hasani - 25200013

Studi kasus PT Nusantara Retail Mandiri. Menggabungkan tiga sumber data jadi satu dataframe untuk analisis performa kampanye kupon diskon.

Sumber data:
- transaksi_pos_nrm.csv : log transaksi POS (basis: dataset UCI Online Retail + kolom kupon simulasi)
- customers_nrm.db      : SQLite, data pelanggan & membership (CRM)
- status_pengiriman.json: mock REST API logistik (JSON bersarang)

Isi notebook (Tugas1_DW_25200013.ipynb):
- Bagian A : pemetaan kebutuhan bisnis ke teknis + matriks RACI
- Bagian B : load CSV, query SQL via sqlalchemy, GET API + json_normalize, merge left join
- Bagian C : fungsi kamus data otomatis + assertions validasi

Hasil: 203 invoice pakai kupon, 75.4% terkirim.

Cara jalankan: upload semua file ke Colab/Jupyter, run all.

Output tambahan: kamus_data_hasil.csv (metadata dataframe master).
