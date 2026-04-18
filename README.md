# 🎯 HeadHunter Dashboard

> **İleri Excel ve Veri Görselleştirme** dersi dönem projesi

---

## 📌 Proje Hakkında

HeadHunter Dashboard, 50.000 kişilik sentetik bir iş gücü veri seti üzerinde kapsamlı veri ön işleme, istatistiksel analiz ve görselleştirme işlemlerinin gerçekleştirildiği bir **İleri Excel** projesidir.

Projenin temel hedefi; nitelikli adayları çok boyutlu CV puanlama sistemiyle değerlendirmek, mesleğe özel uygunluk sorgulama mekanizması kurmak ve tüm bu süreci interaktif bir dashboard üzerinden yönetilebilir kılmaktır.

---

## 📁 Proje Dosyaları

| Dosya | Boyut | Açıklama |
|---|---|---|
| `Veri İşleme.xlsx` | **~80 MB** | Tüm veri ön işleme adımlarının yürütüldüğü ana çalışma dosyası (27 sayfa) |
| `Dashboard.xlsm` | ~700 KB | Makro destekli interaktif dashboard ve pivot tablolar |
| `Sunum.pptx` | ~1.5 MB | Proje sunum dosyası |
| `Rapor.docx` | ~1.2 MB | Detaylı proje raporu |

---

## ⚙️ Veri Ön İşleme

> Projenin en kritik ve en kapsamlı aşaması olan veri ön işleme süreci, yaklaşık **80 MB** büyüklüğündeki `Veri İşleme.xlsx` dosyasında **27 ayrı sayfa** üzerinde yürütülmüştür. 50.000 satırlık ham veri seti, çok aşamalı bir filtreleme, hesaplama ve istatistiksel eleme sürecinden geçirilerek **1.640 nitelikli adaya** indirgenmiştir.

### 1. 🗃️ Veri Seti Oluşturma (`DATA` Sayfası)

Hazır bir veri setine (ID, İsim, Soyisim, Cinsiyet alanları) aşağıdaki nitelikler `RANDBETWEEN` fonksiyonu kullanılarak yapay olarak eklendi:

- **Yabancı Dil Bilgisi** → 0–5 arası rastgele
- **Askerlik Durumu** → `EĞER` fonksiyonu ile erkeklere 0/1, kadınlara boş
- **Deneyim (Yıl)** → 0–20 arası rastgele
- **Programlama Dili Sayısı** → 0–8 arası rastgele (maks. 8 dil)
- **İkili (0/1) Nitelikler:** Ehliyet, Veritabanı bilgisi, MS Office, Adobe, ISO17001, SDLC, Donanım, Sunucu sistemleri, Takım çalışması, Yapay zeka kullanımı, Seyahat engeli
- **Mezun Olunan Üniversite** → 1–130 arasında kodlanmış 130 üniversite tablosundan `DÜŞEYARA` + `RANDBETWEEN` ile rastgele atandı

### 2. 🏫 Referans Tabloları

| Sayfa | İçerik |
|---|---|
| `ÜNİVERSİTELER` | 130 üniversitenin sayısal kodlarla eşleştirildiği arama tablosu |
| `TOP50` | Bonus puan almaya hak kazanan prestijli 50 üniversite listesi |
| `MESLEK` | Her meslek için zorunlu ve opsiyonel niteliklerin tanımlandığı şart tablosu (1 = Gerekli, 0 = Gerekmiyor) |

### 3. 🧹 Veri Temizleme — Power Query & Formül Tabanlı

Ham veri `KİRLİ VERİ` sayfasından alınarak sırasıyla şu temizleme adımları uygulandı:

- **Power Query** üzerinden cinsiyeti `NULL` olan tüm kayıtlar silindi
- Cinsiyeti **erkek** olup askerlik durumu **0** olan kişiler elendi (`cns_tmz` sayfası)
- Temizlenmiş veri `UYGUNLUK SORGULAMA` sayfasına aktarıldı

### 4. 🔍 Mesleğe Özel Uygunluk Sorgulama (`UYGUNLUK SORGULAMA` Sayfası)

Her kişinin her meslek için uygunluğu, `MESLEK` sayfasındaki şart tablosu referans alınarak zincirleme `EĞER` formülleriyle sorgulandı. Örnek formül yapısı:

```excel
=EĞER(G2<1;"UYGUN DEĞİL";EĞER(J2<2;"UYGUN DEĞİL";EĞER(K2<5;"UYGUN DEĞİL";
  EĞER(L2<1;"UYGUN DEĞİL";EĞER(O2<1;"UYGUN DEĞİL";EĞER(P2<1;"UYGUN DEĞİL";
  EĞER(R2<1;"UYGUN DEĞİL";EĞER(S2<1;"UYGUN DEĞİL";EĞER(T2<1;"UYGUN DEĞİL";
  EĞER(U2<1;"UYGUN DEĞİL";"UYGUN"))))))))))
```

**18 farklı meslek** için bu sorgulama ayrı ayrı yürütüldü:
Grafik Tasarım Uzmanı, UX Uzmanı, Endüstri Mühendisi, Bilgisayar Mühendisi, Yazılım Mühendisi, Web Tasarım Uzmanı, Veri Analisti, Bilgi İşlem Uzmanı, Dijital Pazarlama Uzmanı, Sistem Analisti, Beyaz Şapkalı Hacker, Ağ Güvenliği Uzmanı, İşletim Sistemi Yöneticisi, TD Uzmanı, TD Personeli, Veritabanı Yöneticisi, Mobil Yazılım Uzmanı, Proje Yönetimi, Yazılım Geliştirme

### 5. 📊 CV Puanı Hesaplama

Her meslekle uyumlu adaylar ayrı sayfalara aktarıldıktan sonra niteliklerin ağırlıklı toplamından CV puanı hesaplandı. Katsayılar `nitkatsayı` adlı arama tablosundan `DÜŞEYARA` ile dinamik olarak çekildi:

```excel
=TOPLA(
  DÜŞEYARA(G$2;nitkatsayı;2;YANLIŞ)*G7;
  DÜŞEYARA(H$2;nitkatsayı;2;YANLIŞ)*H7;
  ...
  DÜŞEYARA(U$2;nitkatsayı;2;YANLIŞ)*U7
)
```

### 6. 🏆 Üniversite Bonus Puanı

`TOP50` tablosundaki prestijli üniversitelerden mezun adaylara CV puanına otomatik olarak **+5 puan** eklendi. `EĞERHATA` ile `#YOK` hataları temizlendi:

```excel
=EĞERHATA(EĞER(F5=DÜŞEYARA(F5;TOP50UNI;1;YANLIŞ);V5+5;V5);V5)
```

### 7. 📉 İstatistiksel Eleme & Uç Değer Temizleme

Her meslek için ayrı ayrı hesaplandı:

- Ortalama (ORTALAMA)
- Standart sapma (STDSAPMA)
- Alt uç sınır ve üst uç sınır
- Z-skorları

**Alt uç sınırın altında kalan adaylar** elendiğinde üst sınır değerlendirmeye alınmadı; CV puanı yükseldikçe aday daha nitelikli sayıldığı için yalnızca alt sınır kriteri uygulandı. Tüm temizlenmiş veriler `TEMİZLENMİŞ TÜMÜ` sayfasında birleştirildi.

### 📦 Sonuç

| Aşama | Kayıt Sayısı |
|---|---|
| Ham veri seti | 50.000 |
| Cinsiyet & askerlik temizleme sonrası | ~45.000 |
| Mesleğe göre uygunluk filtresi sonrası | ~10.000 |
| İstatistiksel uç değer eleme sonrası | **~1.640** |

---

## 📊 Dashboard

`Dashboard.xlsm` dosyası 5 sayfa içermektedir:

- **DASHBOARD** — Dilimleyicilerle (slicer) kontrol edilen interaktif görsel ekran
- **PİVOT** — Ham pivot tablolar
- **VERİ** — Pivota beslenen veri
- **DÜZENLENMİŞ PİVOT** — Görselleştirme için optimize edilmiş pivot tablolar
- **DÜZENLENMİŞ VERİ** — Temizlenmiş ve dashboard'a uyarlanmış final veri seti

Dashboard'u dinamik hale getirmek için **Power Query** kullanılarak meslek dışındaki sütunlar sabitlendi (unpivot işlemi), ardından oluşan `Değer` sütunu üzerinden dilimleyiciye bağlı filtreleme sağlandı.

---

## 🛠️ Kullanılan Araç ve Teknikler

- **Microsoft Excel** (XLSM, XLSX)
- **Power Query** — Veri temizleme ve dönüştürme
- **Excel Formülleri** — `EĞER`, `EĞERHATA`, `DÜŞEYARA`, `RANDBETWEEN`, `TOPLA`, `ORTALAMA`, `STDSAPMA`
- **Pivot Table & Pivot Chart**
- **Dilimleyici (Slicer)**
- **VBA Makro**

---

## 🚀 Nasıl Kullanılır?

1. `Veri İşleme.xlsx` dosyasını Microsoft Excel ile açın (büyük dosya olduğu için birkaç dakika sürebilir)
2. `Dashboard.xlsm` dosyasını açarken **makroları etkinleştirin**
3. Dashboard üzerindeki dilimleyicilerden meslek seçimi yaparak filtrelenmiş adayları inceleyin


