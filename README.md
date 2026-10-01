# Tugas 1 — Data Wrangling

> Integrasi data multi-sumber: CSV + SQL + REST API menjadi satu dataframe analisis

**Muhammad Adam Hasani** · NIM 25200013

---

## Studi Kasus

Sebagai Associate Data Engineer di **PT Nusantara Retail Mandiri**, diminta menganalisis performa kampanye kupon diskon: berapa total transaksi berkupon, bagaimana profil loyalitas pemakainya, dan apakah pesanannya sudah terkirim. Datanya tersebar di tiga departemen dengan tiga format berbeda.

## Arsitektur Pipeline

```
┌─────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  CSV (POS)  │     │  SQLite (CRM)    │     │  REST API         │
│  transaksi  │     │  pelanggan       │     │  status kirim     │
└──────┬──────┘     └────────┬─────────┘     └─────────┬─────────┘
       │ pandas.read_csv     │ sqlalchemy              │ requests + json_normalize
       ▼                     ▼                         ▼
┌─────────────────────────────────────────────────────────────────┐
│              MERGE (LEFT JOIN) → df_master_analisis             │
└──────┬──────────────────────┬───────────────────────┬───────────┘
       ▼                      ▼                       ▼
  analisis kupon        kamus data otomatis     assertions validasi
```

## Hasil Analisis

| Metrik | Nilai |
|---|---|
| Total invoice pakai kupon | **203** |
| Total nilai transaksi kupon | **68,949.57** |
| Tingkat terkirim | **75.4%** (153 dari 203) |

Profil loyalitas pemakai kupon: Silver 51 · Bronze 49 · Gold 46 · Platinum 43

Status pengiriman: Delivered 153 · In Transit 25 · Shipped 21 · Cancelled 4

## Struktur Repository

| File | Keterangan |
|---|---|
| `Tugas1_DW_25200013.ipynb` | Notebook utama (sudah full run, outputs lengkap) |
| `transaksi_pos_nrm.csv` | 80,000 baris transaksi POS |
| `customers_nrm.db` | SQLite — 1,904 pelanggan CRM |
| `status_pengiriman.json` | Mock REST API logistik (4,005 shipment) |
| `kamus_data_hasil.csv` | Output kamus data otomatis |

> Basis data transaksi: [UCI Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail) — data pelanggan & logistik disimulasikan konsisten dengan transaksinya.

## Fitur Teknis

- `pd.read_csv` dengan `parse_dates` + `dtype` override untuk tipe data aman
- Query SQL terfilter via `sqlalchemy` (`WHERE is_active = 1`)
- Flatten JSON bersarang dengan `pd.json_normalize()`
- Left join dengan justifikasi eksplisit di notebook
- Automated data dictionary generator → CSV
- Quality gate: 3 assertions (duplikasi PK, nilai negatif, future date)

## Menjalankan

Upload semua file ke [Google Colab](https://colab.research.google.com) atau Jupyter, lalu **Run All**. Notebook mengambil data logistik langsung dari repo ini via `requests`, dengan fallback otomatis ke file lokal.

---

*Data Wrangling · 25200013 — Muhammad Adam Hasani*
