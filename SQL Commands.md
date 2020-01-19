## SQL Commands

```mysql
CREATE DATABASE [db_name]; # DataBase Oluşturur.
DROP DATABASE [db_name]; # DataBase Siler.
DELETE FROM uyeler WHERE yas < 18; # yaşı 18 den Küçük Olanları Sil uyeler Tablosundan.
DELETE FROM uyeler; # uyeler Tablosunu Siler.
DROP TABLES tablo_adı # Tablo Siler.
USE [db_name]; Yada CONNECT [db_name]; # DataBaseye Bağlantı Sağlar, Kullanıma Alır Ondan Sonra Gireceğimiz Her Sorgu Seçilen DataBase İçin Çalışır. Eğer Seçilip Seçilmediğinden Emin Değilsek İlerleyen Sorgularda DataBase Belirterek Tablo Belirtmekte Fayda Var Örn.
SELECT * FROM data_base.table; # Seçili Olan DataBaseyi Görmek "SELECT DATABASE();"
CREATE TABLE yeni_tablo (id INT,name VARCHAR(25),surname VARCHAR(25),message VARCHAR(255)); # yeni_tablo Adında Tablo Oluşturur ve İçerisinde Hangi Stunların Yer Alacağını İfade Eder ve Değişkenlerin Tiplerini (INT,VARCHAR,vb.)
```

> INT - Veri sayısal bir değerdir.
>
> VARCHAR(n) - Veri n kadar harf içeren bir alfasayısal değerdir.
>
> UNSIGNED - Sayı pozitif bir tamsayı olmak zorundadır.
>
> NOT NULL - Veri tanımsız (null) olamaz.
>
> AUTO_INCREMENT - Yeni veri eklendiğinde artar.
>
> PRIMARY KEY - Tablonun ana sütunudur ve her zaman farklıdır.



```mysql
SELECT matematiksel_işlem # İşlemin Sonucunu Döndürür.
SELECT 87 % 9; # Mod Alır.
SELECT MOD(37, 13); # Mod Alır.
SELECT ABS(-4.5); # Mutlak Değer Sorgusu Yapar.
SELECT POWER(4, 3); # Üs Hesaplaması Yapar. (4 Üssü 3)
SELECT SQRT(9); # KareKök Alır.
```



```mysql
SHOW TABLES; # Tabloları Görüntüler.
DESCRIBE [tablo_adı] # Tablo Hakkında Detaylı Bilgi Getirir.
INSERT INTO yeni_tablo (id,name,surname,message) VALUES ("1","Jack","Borrison","Heeey !"); # Tabloyu Belirttikten Sonra (id,name,surname,message) Şeklinde Column İsimlerini Girdik Daha Sonra VALUES("1","Jack","Borrison","Heeey !") Şeklinde Verileri EKledik Burada Girilen Column Namelerin ve Girilen Değerlerin Aynı Sırada Olması Gerekmektedir.
```



```mysql
SELECT sütunlar FROM tablo_adı WHERE koşullar; # Temel Sorgu Yapısıdır. (*) Karakteri Bütün Stunları Getirir, Joker Bir Karakterdir.
SELECT * FROM yeni_tablo; # yeni_tablo daki Bütün Verileri (Stunları) Getir.
SELECT name,surname FROM yeni_tablo; # yeni_tablodan Column Name i "name" ve "surname" Olan Stunları Getir.
ORDER BY # Verilen Değere Göre Sıralama Yapar.
LIMIT baslangıc,adet # Şeklinde Sonuçların Belli Sayıda Çıkmasını İsteyebiliriz.
SELECT * FROM yeni_tablo WHERE name = 'Jack' LIMIT 0,3; # İsmi Jack Olanlardan 3 Tane Getir.
SELECT * FROM yeni_tablo ORDER BY id; # id ye Göre Sıralayıpta Çıktı Üretir Bütün Verileri Getirir.

WHERE koşullar # Koşula Bağlı Sorgu İçin Kullanılır (Satır Seçmek İçin Kullanılır SELECT İse Stun Seçmek İçin Kullanılır Yani SELECTTEN Sonra Stun İsmi Girersek O Stunları Getirir WHERE Den Sonra Koşul Girersek O Koşula Uyan Satırları Getirir.).

SELECT * FROM yeni_tablo WHERE id = '1'; # id Si 1 Olan Bütün Kullanıcıları Getir(yeni_tablo dan).
SELECT * FROM DENEME.yeni_tablo WHERE surname = 'Borrison'; # surname'i 'Borrison' Olan Bütün Kullanıcıları DENEME VeriTabanının Altındaki yeni_tablo İsimli Tablodan Getir.
SELECT isim, yas, email FROM tablom WHERE yas < '30'; # tablom İsimli Tablodaki "isim","yas" ve "email" Stunlarını Getir Fakat Yaşı '30' dan Küçük Olan Satırları Getir (">=" , "<=" , "=" Gibi Operatörler Kullanılabilir (Eşitsizlik durumunu ise "<>" ile belirtiriz).).
SELECT isim, yas, email FROM tablom WHERE yas <> '24'; # tablom dan Yaşı 24 Ten Farklı Olanların isim,yas,email Stunlarını Getir.
```



```mysql
sütun_adı LIKE kalıp # Eğer Bir Sözel Verinin Sadece Belli Kriterlere Uyanlarını Seçmek İçin Kullanılır.
SELECT isim, yas, email FROM tablom WHERE isim LIKE 'a%'; # a% a Harfi İle Başlayanları Getir Demek (isim Columnuna Göre).
SELECT * FROM yeni_tablo WHERE name LIKE 'J%'; # İsmi 'J' İle Başlayanların Bütün Columnlarını Getir.
SELECT * FROM yeni_tablo WHERE name LIKE 'k%'; # İsmi 'k' İle Biten Sonuçların Bütün COlumnlarını Getir.
SELECT * FROM yeni_tablo WHERE surname LIKE '%rri%'; # Soyisminde 'rri' Geçen Bütün Sonuçların Bütün Columnlarını Getir.
```



```mysql
SELECT name FROM yeni_tablo WHERE surname = "Borrison" AND message = 'Heeey !'; # AND (ve) 2 Koşulada Uyan Sonuçları Getirir Burada surname si 'Borrison' Olan ve message si 'Heeey !' Olan Satırın name Stununu Getirmesini Söyledik Yanı Soyismi 'Borrison' Olan ve Mesajı 'Heeey !' Olan Kişinin Adını Getirdik Birden Fazla Sonuç Olsaydı Hepsini Getirirdi.
SELECT id,name,message FROM yeni_tablo WHERE surname = 'Clark' OR name = 'Rick'; # Soyismi 'Clark' Olan Yada İsmi 'Rick' Olan Her Satırın id,name,message Stunlarını Getir.
SELECT * FROM yeni_tablo WHERE name NOT LIKE 'J%'; # İsmi 'J' İle Başlamayanları Getir (NOT Eğer Koşul Gerçekleimiyorsa Yap Anlamında).
```



```mysql
SELECT isim, yas, email FROM tablom WHERE ( isim = 'Ali' OR isim = 'Sevim' ) AND yas < 30; # İsmi Ali Yada Sevim Olan ve Aynı Zamanda Yaşı 30 Dan Küçük Olanların isim,tas,email Stunlarını Getir.
SELECT * FROM uyeler WHERE id = 3 OR id = 6 OR id = 7; # Şeklinde Bir Sorgu Yazmak Yerine IN İfadesini Kullanarak Yazabiliriz "SELECT * FROM uyeler WHERE id IN(3, 6, 7);" Bir Başka Kullanım "SELECT * FROM uyeler WHERE id NOT IN(3, 6, 7);"

SELECT * FROM uyeler WHERE id >= 60 AND id <= 100; # Şeklinde Bİr Kullanım Yerine BETWEEN İfadesi İle Kullanılabilri "SELECT * FROM uyeler WHERE id BETWEEN 60 AND 100;" Bir Başka Kullanım "SELECT * FROM uyeler WHERE id NOT BETWEEN 60 AND 100;"

SELECT * FROM uyeler WHERE email IS NULL; # email Değeri NULL Olanları Getir.
SELECT * FROM uyeler WHERE email IS NOT NULL; # email Değeri NULL Olmayanları Getir.

SELECT DISTINCT meslek FROM uyeler; # uyeler Tablosundaki meslek Stunundaki Mesleklerden Benzer Olanları Çıkartır Sonuç Olarak Kaç Çeşit Meslek Olduğunu Öğrenebiliriz.

SELECT MIN(yas) FROM uyeler; # uyeler Tablosundaki yas Stunundaki En Küçük Değeri Getirir.
SELECT MAX(yas) FROM uyeler; # Tablosundaki yas Stunundaki En Büyük Değeri Getirir.
SELECT SUM(yas) FROM uyeler; # uyeler Tablosundaki yas Stunundaki Verilerin Toplamını Getirir.
SELECT AVG(yas) FROM uyeler; # uyeler Tablosundaki yas Stunundaki Verilerin Ortalamasını Getirir.
SELECT SUM(yas) / AVG(yas) FROM uyeler; # uyeler Tablosundaki yas Stunundaki Verilerin Toplamının Ortalamalarına Bölümünün Sonucunu Döndürür.

SELECT COUNT(id) FROM uyeler; # uyeler Tablosundaki id Stunundaki Verileri Sayar.
SELECT CONCAT(isim, ' ', soyad) FROM uyeler; # uyeler Tablosundaki Verileri (isim , ' ' ,soyad) Formatında Getir. Çıktı ÖRN. "Jack Morrison"

SELECT AVG(sinav1) as ortalama FROM notlar; # as (gibi) Çıktıdaki Tabloyu Adlandırır.

SELECT isim, sinav1, sinav2,(40 / 100 * sinav1) + (60 / 100 * sinav2) as final_notu FROM kullanicilar ORDER BY final_notu DESC;

DELETE FROM users WHERE name = "erdogan"; # Satır siler
UPDATE tablo SET stun = deger WHERE satır = deger; # Tablodanki verileri değiştirir.
UPDATE uyeler SET yas = 40 WHERE isim = 'Ali';
UPDATE uyeler SET yas = yas + 1 WHERE isim = 'Ali';
ALTER TABLE users ADD COLUMN age INT; # INT tipinde age diye bir column oluşturur users tablosunda.
INSERT INTO users (name,age) VALUES("46","Hakan"); # Tabloya veri ekler.
```



