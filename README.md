# Tugas 1 — Data Wrangling

![Python](https://img.shields.io/badge/python-3.11-blue) ![pandas](https://img.shields.io/badge/pandas-2.x-green) ![SQLite](https://img.shields.io/badge/sqlite-3-informational) ![status](https://img.shields.io/badge/status-lolos_validasi-brightgreen)

> ETL pipeline multi-sumber: CSV + SQL + REST API → `df_master_analisis`

**Muhammad Adam Hasani** · NIM 25200013

---

## Studi Kasus

Sebagai Associate Data Engineer di **PT Nusantara Retail Mandiri**, diminta menganalisis performa kampanye kupon diskon: total transaksi berkupon, profil loyalitas pemakainya, dan status pengirimannya. Data tersebar di tiga departemen dengan tiga format berbeda — persis masalah klasik yang diselesaikan data wrangling.

## Arsitektur Pipeline

```
┌─────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  CSV (POS)  │     │  SQLite (CRM)    │     │  REST API         │
│  transaksi  │     │  pelanggan       │     │  status kirim     │
└──────┬──────┘     └────────┬─────────┘     └─────────┬─────────┘
       │ read_csv            │ sqlalchemy              │ requests +
       │ parse_dates+dtype   │ WHERE is_active=1       │ json_normalize
       ▼                     ▼                         ▼
┌─────────────────────────────────────────────────────────────────┐
│         dedup → MERGE (LEFT JOIN) → df_master_analisis          │
└──────┬──────────────────────┬───────────────────────┬───────────┘
       ▼                      ▼                       ▼
  analisis kupon        generate_data_dictionary   assert × 3
```

## Keputusan Desain

| Keputusan | Alasan |
|---|---|
| **LEFT JOIN** POS→logistik | Order tanpa record kirim (belum diproses) tetap terhitung; INNER JOIN memicu undercount penjualan |
| **LEFT JOIN** →CRM | CRM sudah difilter `is_active=1`; INNER di sini membuang transaksi pelanggan non-aktif yang secara bisnis tetap nyata |
| **Dedup sebelum merge** | API level order vs POS level item — tanpa dedup, join menggandakan baris |
| **SQLite**, bukan server DB | Zero-infra, file ikut deliverable, reproducible di mesin siapa pun |
| **dtype override `InvoiceNo`** | Mencegah pandas mengonversi ke numeric dan merusak leading zero |

## Hasil Analisis

| Metrik | Nilai |
|---|---|
| Total invoice pakai kupon | **203** |
| Total nilai transaksi kupon | **68,949.57** |
| Tingkat terkirim | **75.4%** (153/203) |

```
Loyalitas pemakai kupon        Status pengiriman
Silver     ██████████ 51       Delivered   ██████████████ 153
Bronze     █████████  49       In Transit  ██ 25
Gold       ████████   46       Shipped     █ 21
Platinum   ███████    43       Cancelled   ▏ 4
```

## Struktur Repository

| File | Keterangan |
|---|---|
| `Tugas1_DW_25200013.ipynb` | Notebook utama — full run, outputs lengkap |
| `transaksi_pos_nrm.csv` | 80,000 baris transaksi POS |
| `customers_nrm.db` | SQLite — 1,904 pelanggan CRM |
| `status_pengiriman.json` | Mock REST API logistik — 4,005 shipment |
| `kamus_data_hasil.csv` | Output data dictionary otomatis |

<details>
<summary>Data quality checks (semua lolos)</summary>

```python
assert df.duplicated(subset=['InvoiceNo','StockCode']).sum() == 0   # integritas PK
assert (df['total_amount'] >= 0).all()                              # domain nilai
assert (df['InvoiceDate'] <= pd.Timestamp.now()).all()              # temporal sanity
```
</details>

## Reproducibility

```bash
# upload ke Google Colab / Jupyter, lalu Run All
# notebook fetch data logistik dari raw URL repo ini,
# fallback otomatis ke file lokal jika offline
```

Basis transaksi: [UCI Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail) — data pelanggan & logistik disimulasikan konsisten dengan transaksinya.

---

*Data Wrangling · 25200013 — Muhammad Adam Hasani*
