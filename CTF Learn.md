## CTF Learn

### BASIC INJECTION

Bu aşamada başlıkta temel bir injection açığı olduğu belirtilmiş ve bize **[](https://web.ctflearn.com/web4/)** şeklinde bir link verilmiş. Linke gittiğimizde bizden input bekleyen bir alan var, direkt kaynak kodlarına bakıyoruz form kısmında **action = "."** satırını görüyoruz buradaki **.** şu işe yarar aynı **URL** yi döndürür fakat farklı parametrelerle. Alt kısımlarda `<!-- Try some names like Hiroki, Noah, Luke -->` şeklinde bir yorum satırı var buradali isimleri tek tek denediğimizde Luke isminin çıktısında **Name: Luke Data: I made this problem**. Şeklinde bir ifade görüyoruz Aklımıza hemen **SQL Injection** açığı geliyor sistem veri tabanında isim sorguluyor eğer isim veri tabanında varsa column ları çekip getiriyor. **SQL** Sorgusunu kabaca yazdığımızda şu şekilde bir şey olması gerekiyor. 

`SELECT * FROM users WHERE username = 'Luke';` burada username kısmına bizden alınan değer giriliyor biz input 
olarak `' or '1' = '1` Şeklinde bir veri girersek direkt tablodaki bütün columnları ve verileri çekeriz biz bu veriyi 
girdiğimizde sorgu aşağıdaki gibi olacak. `SELECT * FROM users WHERE username = '' or '1' = '1';` Bu sorgu şunu ifade eder **users** tablosundan **username** i '' (Boş) yada `'1' = '1'` (True döndürür burası) olan ları getir burada **WHERE** İfadesinden sonra **username**='' kısmı **False** döndürse bile or (yada) dediğimiz için ve **'1' = '1'** Herzaman **True** 
döndüreceği için direkt verileri çekeriz. `' or '5' = '5  Yada ' or 'a' = 'a` gibi verilerde girilebilir. 

> flag{th4t_is_why_you_n33d_to_sanitiz3_inputs}

### FORENSICS 101

Burada bize bir .jpg uzantılı fotoğraf verilmiş ve bayrağın içinde bir yerde olduğunu söylemişler. Linux ta string komutu ile dosyayı okuttuğumuzda **flag{wow!_data_is_cool}** şeklinde bayrağı direkt yakalıyoruz.

> flag{wow!_data_is_cool}

### IP TRACER

Bob un amatör bir hacker olduğunu ve 159.167.16.5 IP adresinin lokasyonunu öğrenmeye çalıştığı söylenmiş IP location finder dan hemen lokasyonuna bakıyoruz.

> London

### INJ3CTION TIME

Bu aşama çok mükemmel bir SQL Injection zafiyeti barındırıyor çözerken çok muhteşem bir haz aldım :). Burada bizden input olarak ID alınyor ve veri tabanında bir sorgu gerçekleştiriliyor ve kullanıcı bilgileri bize getiriliyor ve bu işlemler GET metodu ile yapılıyor bu şekilde URL den ilerleyebiliriz. **https://web.ctflearn.com/web8/?id=1** şeklinde bir url miz var burada ID değerini değiştirerek farklı sonuçlar alabilriiz fakat hiç bulunmayan bir id değeri girdiğimizde sonuç almayız **"-1"** gibi :). Burada yapmamız gereken rastgele bir ID değeri girmek ve ardından **or 1 = 1** gibi bir sorgu gerçekleitirmek bu sayede ID değeri eşleşsede eşleşmesede (True da dönse False de dönse) or (yada) dediğimiz için ve 1 sürekli 1 e eşit olduğu için bize bütün sonuçları döndürecektir **(https://web.ctflearn.com/web8/?id=-1 or 1 = 1)**. Bu aşamadan sonra **ORDER BY** diyerek  herhangi bir column a özgü sıralama ile sonuç alabiliriz bu Komutu kullanarak aslında column sayısını öğrenebiliriz. **(https://web.ctflearn.com/web8/?id=-1 or 1 = 1 order by 1,2,3,4,5)** Gibi bir sorgu gerçekleştirdiğimizde sonuç alamayız fakat **(https://web.ctflearn.com/web8/?id=-1 or 1 = 1 order by 1,2,3,4)** şeklinde bir sorgu gerçekleştirirsek sonuç alırız buda demek oluyorki 4 tane column (stun) a sahibiz. Bütün bu aşamalardan sonra union komutu ile komutları birleştirerek bir çok bilgiye ulaşabiliriz. **(https://web.ctflearn.com/web8/?id=-1 or 1 = 1 union select 1,2,3,4)** Şeklinde sorgu ile columnları getirebiliriz buradaki 1,2,3,4 sayıları column isimleri değildir sadece semboliktir yani 1,2,3,4 sıradaki columnları getir gibi. Burada 1 yerine `NOW()`, 2 yerine `VERSION()`, 3 yerine `DATABASE()` yazarak version seçili olan database ve tarih ve saat gibi bilgilere erişebiliriz yani SQL Fonksiyonları çağırabiliriz `(DATABASE() = webeight)`. Bu aşamalardan sonra tablolara ve columnlara ve verilere erişmemiz gerekecek bunun için `group_concat()` Fonksiyonu kullanılabilir.

`https://web.ctflearn.com/web8/?id=-1 or 1 = 1 union select group_concat(schema_name),2,3,4 from 
information_schema.schemata` Veri tabanlarını getiren sorgu. 
`https://web.ctflearn.com/web8/?id=-1 or 1 = 1 union select group_concat(table_name),2,3,4 from 
information_schema.tables where table_schema = database() `Şeklinde bir sorgu ile tabloları çekebiliriz. **where** 
**table_schema = database()** şeklinde bir filtreleme yapmasa idil bütün tabloları getirirdi bu filtre ile birlikte 
yalnızca webeight databasesine ait tablolar getirilir.) **Tablolar: w0w_y0u_f0und_m3 , webeight (w0w_y0u_f0und_m3** 
**isimli tablo dikkatimizi çekiyor.)**
`https://web.ctflearn.com/web8/?id=-1 or 1 = 1 union select group_concat(column_name),2,3,4 from 
information_schema.columns where table_schema = database()` Columnları çekmek için bir üst sorguya benzer bir 
sorgu kullanıyoruz **(Columns : f0und_m3 , breed , name , color , id)**. f0und_m3 dikkatimizi çekiyor hemen.
`https://web.ctflearn.com/web8/?id=-1 or 1 = 1 union select group_concat(f0und_m3),2,3,4 from w0w_y0u_f0und_m3) `Şeklinde bir sorgu ile bayrağı yakalıyoruz.

> abctf{uni0n_1s_4_gr34t_c0mm4nd}

> %23 - # anlamına gelmektedir.
>
> %09 - tab ifadesi (4 boşluk) 

### SIMPLE PROGRAMMING

Bu aşamada bize data.dat diye bir dosya verilmiş ve içerisi full 0 ve 1 lerden oluşan verilerle dolu. Soruda ise şöyle söylenmiş 0 ların sayılarının 3 ün katı olan veya 1 lerin sayılarının 2 nin katı olan kaç satır vardır demiş bizde hemen python3 ile ufak bir kod yazdık ve cevabı bulduk code aşağıdaki gibi.

```python
#! /usr/bin/env/python3
line = 0
with open('./data.dat','r') as file:
    for x in file:
        zero = x.count('0')
        one = x.count('1')
        if zero % 3 == 0 or one % 2 == 0:
            line += 1
        else: pass
print(line)
```

> 6662

### PASTE THOSE BINARIES

**r44LxiXq** şeklinde bir veri verilmiş başlıktan direkt aklımıza pastebin.com geliyor ve **pastebin.com/r44LxiXq** şeklinde linke gitmeyi deniyoruz ve karşımızda flag.

> ABCTF{past3_that_b1n}

### LAZY GAME CHALLENGE

Burada netcat ile bir sunucaya 10001. portta bağlantı kuruyoruz ve orada terminal üzerinde çalışan bir oyun var 500$ parayla başlıyoruz ve para yatırıyoruz daha sonra bilgisayarın tahmin ettiği sayıyı tahmin etmeye çalışıyoruz 1-10 arasında.

Bizim 500$ paramız var eğer biz para yatırırken olmayan bir değer girersek -9223372036854775808 gibi çünkü python da integer değerler -9223372036854775808 ile 9223372036854775807 arasındadır. Oyunu oynadığımızda bayrak karşımıza çıkıyor.

> CTFlearn{d9029a08c55b936cbc9a30_i_wish_real_betting_games_were_like_this!}



