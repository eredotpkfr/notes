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

