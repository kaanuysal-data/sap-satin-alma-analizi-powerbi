# SAP Satın Alma Süreç Analizi — Power BI Dashboard

SAP HANA üzerinden çekilen satın alma verilerini (SAT → SAS → Mal Girişi → Fatura döngüsü) Star Schema veri modeliyle yapılandırıp, uçtan uca süreç görünürlüğü sağlayan Power BI raporu.

## İş Problemi

Satın alma süreci (Satın Alma Talebi → Satın Alma Siparişi → Mal Girişi → Fatura) çok adımlı ve farklı SAP modüllerine (MM, FI) yayılmış durumda. Bu, aşağıdaki soruların yanıtlanmasını zorlaştırıyordu:

- Hangi siparişlerde teslimat/faturalama gecikmesi var?
- Hangi tedarikçiler/depolar performans sorunları yaşıyor?
- Açık (henüz teslim/faturalanmamış) tutar ne kadar?

## Veri Mimarisi

SAP HANA Studio üzerinden SAT/SAS/MIRO/MIGO verileri çekilip Python ile batch olarak aktarıldı, ardından Star Schema ile modellendi:

```
                    dim_Date
                       │
   d_PurchGroup ───┐   │   ┌─── d_Plant
                    │   │   │
   d_MatGroup  ─────┤   │   ├───── d_Slov
                    │   │   │
                  f_ALL (fact tablo)
                    │   │   │
   d_Material ──────┤   │   ├───── d_Vendor
                    │   │   │
   d_PR        ─────┘   │   └───── d_Company
                       d_PO
```

Merkezdeki `f_ALL` fact tablosu, `PO_Key` ve `PR_Key` üzerinden tüm boyut tablolarına bağlanıyor — bu yapı, farklı kırılım seviyelerinde (tedarikçi, malzeme grubu, satın alma grubu, şirket) hızlı ve tutarlı analiz yapılmasını sağlıyor.

## Dashboard Özellikleri

**Genel Bakış Sayfası**
- Toplam SAS/MG/FG tutarları, teslimat ve faturalama oranları
- Yıllara göre açık tutar trendi
- En düşük teslimat performansına sahip tedarikçiler
- Malzeme / Malzeme Grubu / Şirket kırılımında Top 5 analizleri
- Depo bazlı çift yönlü açıklık (MG-FG) analizi

**Sipariş Hareket Döngüsü Sayfası**
- Sipariş bazlı detay: SAT → SAS → Mal Girişi → Fatura zaman çizelgesi
- Sipariş kalemi seviyesinde açık miktar/tutar takibi
- SAT→SAS, SAS→MG, SAS→FG gün bazlı süre analizleri

## Kullanılan Teknolojiler

- **Veri Kaynağı**: SAP HANA (SAT/SAS/MIRO/MIGO tabloları)
- **ETL**: Python (batch veri aktarımı)
- **Modelleme**: Star Schema (fact-dimension), Power BI Data Model
- **Görselleştirme**: Power BI (DAX ölçüleri, drill-through, dinamik filtreler)

## İş Değeri

- SAP BW gibi yüksek ücretli raporlama araç ihtiyaçlarını azaltarak raporlama maliyetlerinin düşürülmesine katkı sağladı
- Satın alma sürecindeki gecikme noktalarının (SAT→SAS, SAS→MG, SAS→FG) görünür hale gelmesini sağladı
- Tedarikçi/depo bazlı performans karşılaştırmasını tek ekranda mümkün kıldı

> **Not:** Bu repodaki dashboard'da kullanılan tüm veriler (tedarikçi isimleri, tutarlar, şirket bilgileri) gizlilik nedeniyle anonimleştirilmiştir. Veri modeli ve analiz yaklaşımı gerçek projeyle birebir aynıdır.

## Yazar

Kaan Uysal
