## Supervisor: A Process Control System

### Nedir?

Supervisor `client/server` sistemidir, `Linux/Unix` gibi işletim sistemlerinde process'leri control etmek ve izlemek için kullanılır. `launchd`, `daemontools`, ve `runit`  gibi yazılımlarla aynı amaca sahiptir.

### Bazı Özellikleri

#### Kolaylık

Her process için `rc.d` dosyası yazmak oldukça zahmetlidir, ayrıca `rc.d` dosyası program crash verdiğinde yada çöktüğünde otomatik olarak tekrardan başlatmaz. Supervisor process leri subprocess olarak başlatır ve crash verdiğinde otomatik olarak yeniden başlaması için konfigre eder.

#### Doğruluk/Kesinlik

Unix teki processlerin durumunu saptamak oldukça zordur, `PID` dosyaları gerçeği içermeyebilir, supervisor processleri subprocess olarak başlattığı için subprocess'in durumunu daha doğru bir şekilde saptayabilir.

#### Process Grupları

Process ler bazen belirli gruplarla başlatılması yada durdurulması gerekebilir, supervisor Supervisor işlemlerinize öncelik ekleyerek bunu yapmanıza imkan tanır.

### Bileşenleri

#### supervisord

Supervisor'un sunucu tarafındaki bileşenine `supervisord` denir. Alt programları başlatmak, istemcilerden gelen  komutlara yanıt vermek, çökmüş veya çıkmış alt süreçleri yeniden  başlatmak, alt işlem `stdout` ve `stderr` çıktılarını kaydetmek ve alt süreç yaşamlarındaki noktalara karşılık gelen “olayları” oluşturmak ve  işlemekten sorumludur.

`supervisord` bir konfigrasyon dosyası kullanır `Linux` OS larda bu `/etc/supervisord.conf` dizinindedir.

#### supervisorctl

Supervisor'un komut satırı istemci `supervisorctl` olarak adlandırılır.

#### Web Server

Supervisor `9001`. porttan hizmet veren bir arayüze sahiptir. `http://localhost:9001/`

### Kurulum

`sudo apt-cache show supervisor` komutu ile paket ayrıntılarını ve bağımlı olduğu diğer paketleri görebiliriz. `sudo apt-get install supervisor` komutu ile `apt` paket yöneticisinden kolaylıkla sistemimize kurabiliriz.

`sudo systemctl disable supervisor.service` komutu ile bilgisayarımız başlatıldığı anda `supervisor`un başlatılmamasını sağlayabiliriz.

`sudo systemctl enable supervisor.service` komutu ile bilgisayarımız açıldığında `supervisor` serviside otomatik olarak başlatılacaktır.

`sudo service supervisor status` servis durumu.

`sudo service supervisor start` servisi başlatır.

`sudo service supervisor stop` servisi durdurur.

#### Konfigrasyon dosyanız yoksa!

`echo_supervisord_conf` komutu ile `supervisor` konfigrasyonlarınızı ekrana basabilirsiniz, eğerki `/etc/supervisor/supervisord.conf` dosyanız eksik ise `echo_supervisord_conf > /etc/supervisord.conf` komutu ile telafi edebilirsiniz.

### WebUI Aktif Etmek İstersek

Supervisor'un WebUI kısmını aktif etmek için `/etc/supervisor/supervisord.conf` konfigrasyon dosyasına aşağıdaki satırları eklememiz yeter, `9001`. porttan hizmet verecektir. 

```shell
[inet_http_server]
port=127.0.0.1:9001
```

