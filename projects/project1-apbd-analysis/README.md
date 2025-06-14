### 1. 🏛️ Analisis Efektivitas Belanja Pemerintah Daerah
**Status**: 🚧 In Progress | **Timeline**: 4-6 weeks

## 📋 Project Overview
Analisis komprehensif untuk mengukur efektivitas belanja APBD terhadap indikator kesejahteraan masyarakat di 34 provinsi Indonesia periode 2019-2023.

## 🎯 Business Problem
Pemerintah daerah seringkali menghadapi tantangan dalam mengalokasikan anggaran secara efektif. Project ini bertujuan untuk:
- Mengidentifikasi pola belanja yang berkorelasi dengan peningkatan kesejahteraan
- Menemukan inefficiency dalam alokasi anggaran
- Memberikan rekomendasi evidence-based untuk optimalisasi APBD

## 📊 Dataset
| Dataset | Source | Period | Size |
|---------|--------|--------|------|
| Data APBD | DJPK Kemenkeu | 2019-2023 | ~170k rows |
| PDRB Regional | BPS | 2019-2023 | ~3.4k rows |
| Indeks Pembangunan Manusia | BPS | 2019-2023 | ~1.7k rows |
| Data Kemiskinan | BPS | 2019-2023 | ~1.7k rows |

## 🛠️ Technical Implementation
### Data Collection & Preparation
-- Example: Aggregating APBD data by function
```sql
SELECT 
    tahun,
    provinsi,
    fungsi_belanja,
    SUM(realisasi) as total_realisasi,
    SUM(anggaran) as total_anggaran,
    (SUM(realisasi)/SUM(anggaran)*100) as realisasi_pct
FROM apbd_data
WHERE tahun BETWEEN 2019 AND 2023
GROUP BY tahun, provinsi, fungsi_belanja
ORDER BY tahun, provinsi;
```

## Key Analysis Performed
1. Correlation Analysis: Belanja per fungsi vs IPM
2. Efficiency Score: DEA (Data Envelopment Analysis)
3. Time Series: Trend analysis belanja vs outcomes
4. Clustering: Grouping daerah by spending patterns

## 📈 Key Findings
📊 Finding 1: Belanja pendidikan menunjukkan korelasi tertinggi (r=0.78) dengan peningkatan IPM
💡 Finding 2: 40% daerah menunjukkan inefficiency dalam belanja kesehatan
🎯 Finding 3: Optimal allocation ratio: Pendidikan (25%), Kesehatan (15%), Infrastruktur (20%)
