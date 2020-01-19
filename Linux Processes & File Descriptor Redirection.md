## Linux Processes & File Descriptor Redirection

**Process:** İşletim sistemi tarafından çalıştırılabilir ünite. Eğer bir processin sonucu 0 dönüyor ise başarılı bir şekilde işleme alınmıştır(hatasız) anlamına gelir 0 dan farklı bir değer dönüyorsa başarısız hata alındı anlamına gelir. GNU/Linux ta en son çalıştırılan komutun hangi sonucu döndürdüğünü görmek için. `echo $?` komutu çalıştırılabilir. 

**File Descriptor:** İşletim sistemindeki açık bir dosyayı yada process'i eksiksiz bir şekilde ifade eden bir takım tam sayılardır(0,12,567 gibi negatif olmayan tamsayılar) ve dosyanın hangi kaynağa nasıl erişileceğini ifade eder. Bir process bir kaynağa erişmeye çalıştığında işletim sisteminin kernel(çekirdek) i erişim sağlar. Her process in temelde 3 adet file descriptor (dosya tanımlayıcısı) vardır, istesenizde istemesenizde oluşturulurlar. Bunlar; `STDIN,STDOUT,SDTERR`

`0 STDIN (Standart Input)`  0 değeri ile ifade edilir, standart inputtur o anda bilgisayara takılan klavye,mikrofon... (terminade çalıştırılan bir yazılıma klavyeden input girilmesi bir örnektir.)

`1 STDOUT (Standard Output)` 1 değeri ile ifade edilir, standart çıktı biçimidir (terminalde çalıştırılıp bize gösterilen çıktılar örnektir.)

`2 STDERR (Standard Error)` 2 değeri ile ifade edilir, standart errordur process çalışması sırasında çıkan hataları içerir. İşletim sistemi ilk `STDERR` kısmını kontrol eder eğer hata alınmazsa program başarılı bir şekilde çalışmıştır ve `STDOUT` a bakar.

### File Descriptor Redirecting

GNU/Linuxta herhangi bir process in çıktısını `>` işareti kullanarak yönlendirebiliyoruz eğer `>` işaretini hiçbir file descriptor belirtmede kullanırsak o anki process in `STDOUT` yani standart çıktısını işleme alacaktır. `find /etc -name "python"` gibi bir komut çalıştırdığımızda permission denied hataları alacağız hataları şu şekilde bir komutla `/dev/null` dosyasına aktarabiliriz ve çıktıda hatalar gözükmez. `find /etc -name "python" 2> /dev/null` 2 sayısı `STDERR` i ifade ediyor hataları `/dev/null` dosyasına göderiyo null dosyazı zaaten karadelik ne verirsek yok olup gidiyor.

`tail -f /dev/null`, `-f` argümanı takip eder oraya gelen son verileri gösterir ama null a bir şey gelmeyeceği için sonsuz döngüye girer kapatmanın tek yolu sinyal göndermek.

`find /etc -name "python" | grep "/etc"` gibi bir komut çalştırırsam pipe işareti yalnızca `STDOUT` (standart output) kısmını girdi olarak alıcak ve o çıktı üzerinde yalnızca "/etc" yi arayacak.

`find /etc -name "python" 2>&1 | grep "/etc" ` gibi bir kullanımla `STDERR` ları `STDOUT` a yönlendirdik ve `STDERR` larda `STDOUT` oldu ve artık errorlardada "/etc" yi arayabiliriz.

### Processes

GNU/Linux ta işlemleri görüntülemek için `ps aux` komutu yada `top` komutu kullanılabilir.
`kill` komutu herhangi bir process e sinyal göndermeye yarar. `kill -l` komutu ile tüm sinyalleri görebiliriz.

```bash
2] SIGINT # İşleme interrupt gönderir, kapatır. Önemli bir işlem sırasında durmayabilir. Programı yazana adam ignore edebilir.
15] SIGTERM # Dükkanı kapatıyoruz toparlanması sistem kapatılacak sinyali gönderir ve bütün işlemler son işleri yaparlar başka request almazlar.
1] SIGHUP # configrasyonu değiştirilmiş bir process i kapatıp yeniden başlatmak yerine configrasyonunu update et sinali gönderir. Programa göre değişir configrasyonu update ederken yeniden başlatması gerekebilir.
9] SIGKILL # Direkt işlemi öldürür.
```

`ps aux` komutunun çıktısındaki bazı kısaltmaların anlamları/açıklamaları;

```bash
UID  -> Sürecin sahibinin sayısal kullanıcı kodu.
PID  -> (Process ID) Süreç tanıtım numaras›.
PPID -> (Parent Process ID) Süreci bir başka süreç başlattıysa o sürecin numaras›.
TTY  -> (Teletype) Sürecin hangi terminalden başlatıldığını belirtir.

?: Herhangi bir teminalden değil, sistemin kendisinin
ptsN: (pts1, pts2 gibi) Ağ üzerinden bağlanmış kullanıcılar tarafından başlatılmış süreçleri gösterir.

ttyN: (tty1, tty2 gibi) Seri arabirim üzerinden (modemle bağlantı gibi) bağlı kullanıcılar tarafından başlatılmış süreçleri gösterir.

STAT -> (Status) Sürecin bulunduğu duruma ilişkin bir kod.
R (Runnable): Çalışabilir durumda, sırasını bekliyor.
S (Sleeping): Uyuyor. Bir şeylerin olmasını bekliyor
Z (Zombie): Bu süreç ile bağlantılı tüm diğer süreçler bitmiş veya ölmüş; bunun da bitmiş olması gerekirdi ama bir nedenle ölememiş. ps listesinde hâlâ görünüyor olması zararsızdır.

TIME -> Sürecin ne kadar zamandır çalıştığını gösterir.
COMMAND -> Süreci başlatan komut satırıdır. (Varsa)
```

