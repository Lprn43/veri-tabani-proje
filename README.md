# 2026-2027 Veritabanı Otel Rezervasyon ve Konaklama Yönetim Sistemi
Bu proje, Karadeniz Teknik Üniversitesi - Veritabanı Yönetim Sistemleri dersi kapsamında geliştirilmiştir.

---

## Grup Üyeleri

- **Alperen Berat Baram** - 455121
- **Mirali Teymur** - 455132

---

## Proje Hakkında ve Amaç

Bu projede bir otel işletmesinin rezervasyon, konaklama, ödeme ve personel süreçlerini yöneten bir sistem geliştirerek ilişkisel veritabanı tasarımı, normalizasyon, SQL sorguları (CRUD, çok tablolu JOIN, gruplama), tarih aralığı kontrolü, tetikleyici (TRIGGER), saklı prosedür (STORED PROCEDURE), görünüm (VIEW), işlem (TRANSACTION) ve indeksleme kavramlarının uygulanması; bunun üzerine REST API ve kullanıcı arayüzü geliştirilmesi hedeflenmektedir.

Sistem; bir otelde yer alan odaların, oda tiplerinin, misafirlerin, rezervasyonların, konaklamaların, ödemelerin, ek hizmetlerin ve personelin yönetilmesini sağlayan bir otomasyon sistemidir. 
- **Misafirler:** Sisteme kayıt olup giriş yaparak tarih aralığı ve oda tipine göre boş oda arayabilir, rezervasyon yapabilir, rezervasyonunu iptal edebilir ve geçmiş konaklamalarını görebilir.
- **Resepsiyon Personeli:** Misafirin giriş (check-in) ve çıkış (check-out) işlemlerini yapar, konaklama sırasında alınan ek hizmetleri (kahvaltı, spa, otopark, oda servisi vb.) konaklama hesabına ekler ve çıkışta toplam tutarı hesaplayıp ödeme alır.
- **Yönetici:** Oda, oda tipi, sezon fiyatı, ek hizmet ve personel verilerini ekleyebilir, güncelleyebilir, silebilir ve raporları görüntüleyebilir.

Sistemdeki en önemli iş kuralı, aynı odanın çakışan tarihlerde iki kez rezerve edilememesidir ve bu kural veritabanı seviyesinde uygulanmaktadır. Oda fiyatları sezona göre değişir; bir rezervasyonun toplam tutarı, konaklanan her gece için o geceye ait sezon fiyatı toplanarak hesaplanır.

---

## Kullanılan Teknolojiler

- **Veritabanı:** Microsoft SQL Server (MSSQL)
- **Backend:** .NET (C#) - REST API (Katmanlı mimari: Entity, Repository, Service, Controller ve JWT tabanlı kimlik doğrulama)
- **Frontend:** HTML, CSS (Değişebilir)
- **Test Araçları:** Postman / Swagger

---

## ER Diyagramı

EKLENECEK

---

## Veritabanı Mimarisi ve SQL Betiği

### 1. Temel Tablolar (3NF Düzeyinde)
- `OdaTipi`: Tip adı (standart, deluxe, suit), kapasite, açıklama, temel fiyat
- `Oda`: Oda numarası, kat, oda tipi, durum (boş, dolu, temizlikte, bakımda)
- `SezonFiyati`: Oda tipi, başlangıç-bitiş tarihi, gecelik fiyat
- `Misafir`: Ad, soyad, TC/pasaport no, telefon, e-posta, doğum tarihi
- `Kullanici`: Giriş bilgileri, rol (misafir, resepsiyon, yönetici), ilişkili misafir veya personel
- `Personel`: Ad, soyad, görev (resepsiyon, kat görevlisi, yönetici), işe giriş tarihi
- `Rezervasyon`: Misafir, oda, giriş-çıkış tarihi, kişi sayısı, durum (beklemede, onaylı, iptal, tamamlandı), oluşturma tarihi
- `Konaklama`: Rezervasyon, gerçek giriş-çıkış zamanı, işlemi yapan personel
- `EkHizmet`: Hizmet adı, birim fiyat
- `KonaklamaHizmeti`: Konaklama, ek hizmet, adet, tarih
- `Odeme`: Konaklama, tutar, ödeme türü (nakit, kart), tarih
- `TemizlikKaydi`: Oda, personel, tarih, durum
- `IslemLog`: Tetikleyiciler tarafından doldurulan işlem geçmişi (hangi tabloda ne zaman ne değişti)

### 2. Zorunlu Veritabanı Nesneleri
- **Trigger (Tetikleyiciler):**
  1. Çakışan tarihli rezervasyonu engelleyen tetikleyici.
  2. Check-in yapıldığında oda durumunu "dolu", check-out yapıldığında "temizlikte" yapan tetikleyici.
  3. Rezervasyon ve ödeme tablolarındaki değişiklikleri `IslemLog` tablosuna yazan tetikleyici.
- **Stored Procedure (Saklı Prosedürler):**
  1. Verilen tarih aralığı ve oda tipi için boş odaları listeleyen prosedür.
  2. Bir konaklamanın toplam tutarını (sezon fiyatları + ek hizmetler) hesaplayan prosedür.
  3. Aylık doluluk oranı ve gelir raporu üreten prosedür.
- **View (Görünümler):**
  1. Bugün giriş ve çıkış yapacak misafirler listesi.
  2. Oda bazında güncel durum ve son temizlik tarihi.
- **Transaction (İşlem):**
  - Check-out işlemi tek bir transaction içinde yapılır: konaklama kapatılır, ödeme kaydedilir, oda durumu güncellenir, herhangi bir adım başarısız olursa tümü geri alınır .
- **İndeksleme ve Örnek Veri:**
  - Sık sorgulanan sütunlara (rezervasyon tarihleri, oda numarası, misafir e-postası) indeks tanımlanmıştır.
  - En az 20 oda, 50 misafir ve 200 rezervasyon içeren test verisi bulunmaktadır.

---
