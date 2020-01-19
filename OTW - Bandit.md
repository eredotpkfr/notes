## Over The Wire Bandit

### 0

Flag direkt verilmiş 1 den 2 ye geçmek için onu parola olarak kullanacağız.

> boJ9jbbUNNfktd78OOpsqOltutMc3MY1

### 1 - 2

Özel karakter (. ! - , '' ^ % & / ( = ...) adı verilen dosyaları terminalden açarken "./" ile tanıtılması gerekir.

```bash
mkdir ./-   --> "-" # isminde dizin oluşturur.
touch ./-   --> "-" # isminde ascii text oluşturur
rm ./-      --> "-" # yi siler
mkdir "-"   --> "-" # isminde dizin oluşturur.
```

> CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

### 2 - 3

Bandit2 dizinine gidip "spaces in this filename" dosyasını cat ile açtık flag karşımızda. Boşuk karakterini tanıması içi " " içerisine yazılması lazım veya "./" ile yada spaces\ in\ this\ filename\ 

`file "spaces in this filename"` ile dosya nın tipini öğrenebiliriz.

> UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

### 3 -  4

Bandit3 dizinine gidip inhere dizinine girdik dizinde ls yaptık fakat sonuç alamadık daha sonra ls -a diyip gizli dosyalarıda gördük **.hidden** dosyasını cat ile okuttuk flag karsımızda.

> pIwrPrtPN36QITSp3EQaw936yaFoFgAB

### 4 - 5

Birkaç dosya verilmiş içlerinden birinde flag değeri saklı bunu bulabilmek için "cat ./-file00" komutu ile tek tek baktık. "./" dememizin sebebi dosyalar "-" ile başlıyor.

> koReBOKuIDDepwhWk7jZC0RTdopnAYKh

### 5 - 6

bandit5 dizininin içerisinde inhere dizininde maybe isminde bir çok dosya var bizden burada 1033 byte boyutunda insan tarafından okunabilir ve çalıştırılamaz bir dosyayı bulmamız isteniyor `du -a -b` komutuyla bütün dizinlerin boyutlarını byte cinsinden yazdırdık 1033 olan dosyaya gidip ls diyoruz burada yanıltmaca var `ls -a` dediğimizde gizli dosyalarında olduğunu görebiliriz `du -b -a` ile dosya boyutarına bakıyoruz 1033 olan .file2 isminde bir gizli dosya `cat .file2` komutu ile içeriğini okuyup  bayrağı yakalıyoruz.

> DXjZPULLxYr17uwoI01bNLQbtFemEgo7

### 6 - 7

Sunucuda gizlenmiş bir dosya varmış ve bu dosya bandit7 kullanıcısına aitmiş aynı zamanda bandit6 grubundaymış kök dizinine geçip `find . -user bandit7 -group bandit6` şeklinde bir arama yaptım ve birçok sonuç içerisinden **"./var/lib/dpkg/info/bandit7.password"** bu satır dikkatimi çekti dizine gidip `cat bandit7.password` komutunu çalıştırdım ve flag kaşımda.

> HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

### 7 - 8 

data.txt isminde çok uzun bir dosya verilmiş ve flagın millionth kelimesiden sonra olduğu söylenmiş `sort data.txt | grep millionth` komutu ile çok rahatça flagı yakaladık `grep millionth data.txt` komutu ilede bulunabilirdi fakat ben sıralamak istedim.

> cvX2JJa4CFALtqS87jk27qwqGhBM9plV

### 8 - 9

Bu bölümde data.txt dosyası verilmiş ve içerisinde flag değeri var fakat okadar karmaşık değer arasında nasıl bulacağız soruda ipucu olarak şu söylemiş yalnızca 1 defa tekrar ediyor denmiş. burada `uniq` komutunu kullanabiliriz. `cat data.txt | uniq -c` (-c) parametresi kaç defa tekrar ettiğini gösterecek fakat burada şöyle 1 sıkıntı var tekrar edenleri alt alta almamız gerekiyorki `uniq` komutu tekrar ettiğini anlasın eğer bu komutu çalıştırırsak hepsi için 1 kere tekrar ediyor diyecektir. `sort data.txt | uniq -c` komutunu çalıştırdığımızda yalnızca 1 satır 1 kere tekrar ediyor oda bizim flag değerimiz oluyor.

> UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

### 9 - 10

data.txt dosyasını `cat` ile okuttuğumuzda bir sürü anlamsız şeyler çıkıyor `strings` komutu ile okuttuğumuzda yazılabilir karakterleri yazdırıyor `strings data.txt` ile yazdırıyoruz daha sonra `strings data.txt | grep "^="` komutu ile `=` ile başlayan satırları yazdırıyoruz ve flag karşımızda.

> truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

### 10 - 11

data.txt dosyasının içerisinde base64 algoritması ile şifrelenmiş bir veri olduğu söylenmiş 
ipucuda base64 kullanımı:

```bash
  echo "erdogan" | base64 # base64 algoritması ile "erdogan"ı şifreler. 
  echo "ZXJkb2dhbgo=" | base64 -d # "ZXJkb2dhbgo="i base64 ile decode eder. 
```

`base64 -d data.txt` komutu ile flagı yakaladık.

> IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

### 11 - 12 

data.txt dosyası verilmiş içerisinde anlaşılamayan veriler var ipucudada küçük ve büyük harflerin 13 defa kaydırılması gerektiği yazıyordu ("rot13 algoritması ile şifrelenmiş") `tr` komutu ile belirli aralıktaki harfleri başka harflerle değişirebiliyoruz. `tr` komutu kullanımı:

```bash
echo "asd88" | tr -d [:digit:] # Numaraları at gitsin çıktı asd 
echo "/" | tr "/" "+" # Slash yerine + koy çıktı +. 
```

`echo "Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh" | tr 'A-Za-z' 'N-ZA-Mn-za-m'` komutu ile flagı yakaladık. Komutta 'A-Za-z' kısmı ASCII tablosunda A dan Z ye kadar daha sonra a dan z ye kadar olan kısım yani Z den a ya olan kısımdaki "[{}]" gibi karakterleri atlaması için bu şekilde kullanılmış 2. opsiyonda ise "N-ZA-Mn-za-m" N den başlatılarak harflerin ASCII tablosundaki sayısal değerleri 13 arttırılmış("Caesar algoritması").

> 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

### 12 - 13

Soruda data.txt dosyası verilmiş ve hexdump ile sıkıştırıldığı söylenmiş bizde `xxd -r data.txt >> /tmp/dizin/data.txt` şeklinde bir komutla hexdumptan binary haline çevirdik daha sonra /tmp/dizin/ dizinine geçip `file data.txt` komutu ile dosyanın **gzip** ile sıkıştırıldığını ve isminin eskiden data2.bin olduğunu söylüyordu bizde `mv data.txt data.txt.gz` ile **.gz** uzantısı haline çevirdik ve `gunzip data.txt.gz` ile sıkıştırılmış dosyayı çıkarttık daha sonra yeni bir dosya geldi tekrar file çektik **tar** ile sıkıştırıldığı söylenmişti `tar -xvf [dosya_adı]` şeklinde kullanımla tekrardan çıkarttık bu böyle data8.bin olasıya kadar gitti `file data8.bin` diyip dosya tipine baktığımızda **ASCII** karakter dosyası olduğunu anladık daha sonra `cat data8.bin` diyip bayrağı yakaladık.

```bash
bzip2 dosya.txt # bunzip ile sıkıştırma yapar çıktı: dosya.txt.bz2
bunzip2 dosya.txt.bz2 # bz ile sıkıştırılmışı çıkartır
gzip -9 dosya.txt # gzip ile sıkıştırma yapar çıktı: dosya.txt.gz 
gunzip dosya.txt.gz # gzip ile sıkıştırılmışı çıkartır.
tar -cvf dosya.tar dosya # dosyayı depolar sıkıştırır dosya.tar haline getirir.
tar -xvf dosya.tar # tar ile depolanmışı çıkartır.
zip -r dosya.zip dosya_yada_klasor # .zip haline getirir.
unzip dosya.zip # zipi çıkartır.
rar a -ap dosya.rar dosya_yada_klasor # .rar haline getirir.
unrar e dosya.rar # rardan çıkartır.
```

> 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

### 13 - 14

Burada /etc/bandit_pass/ dizininde bandit14 diye bir dosyada parolanın olduğu söyleniyor fakat bandit13 kullanıcısı olarak okuma yetkimiz yok ve bize bir **private_key** verilmiş ssh bağlantısı için bu keyi kullanacağız `ssh bandit14@127.0.0.1 -i sshkey.private` komutu ile bandit14 kullanıcısı olarak kendimize(localhost) ssh bağlantısı kurduk `-i` parametresi ile keyimizin yolunu belirttik. bandit14 kullanıcısı olduğumuzda `cat /etc/bandit_pass/bandit14` komutu ile flagı yakaladık.

> 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

### 14 - 15 

Bu seviyede önceki seviyede aldığımız flagı 30000. porttan veri olarak göndermemiz gerekiyor `nmap 127.0.0.1` taraması yaptığımızda 30000. portta **NDMP (Network Data Management Protocol)** çalışıyor. `netcat 127.0.0.1 30000` komutunu çalıştırdığımızda bizden veri bekledi önceki flagı girdik ve bize bir sonraki seviyenin flagını verdi.
**netcat** kullanımı:

```bash
nc [options] host port # Temel kullanım.
nc -l -p [Local Port] # Port dinler.
nc [Hedef IP adresi] [Port] # Temel bağlantı.
```

> BfMYroe26WYalil77FoDi9qh59eK5xNr

### 15 - 16

Bu seviyede bizden 30001. porta `openssl s_client` ile şifreli bağlantı sağladık daha sonra `openssl s_client -connect 127.0.0.1:30001` koutunu çalştırdık ve bizden bir input bekledi bizde bir önceki seviyedeki flagı girerek bir sonraki seviyenin flagını bulduk.

> cluFn7wTiGryunymYOu4RcffSxQluehd

### 16 - 17

Soruda localhost ta 31000. port ve 32000. portların arasında birkaç portun açık olduğu belirtilmiş bu porların 1 tanesi ssl ile çalışıyor onu bulup open ssl ile bağlantı kurmamız gerekiyor. `nmap -p 31000-32000 127.0.0.1` yada `netcat -vz 127.0.0.1 31000-32000` komutu ile açık portları taradık çıkan portlardan 31790.porta ssl bağlantısı denediğimizde (`openssl s_client -connect 127.0.0.1 31790`) parola girmemiz 31791.için ekran çıktı şuanki bölümün parolasını gidiğimizde RSA sshkey verdi bize onu alıp sshkey.private diye /tmp/ dizininde bir dosyaya kaydettik daha sonra sshkey.private dosyası için 600 yetkileri verdik (`chmod 600 sshkey.private`) sonrasında `ssh -i sshkey.private 127.0.0.1` komutu ile bandit17 kullanıcısı olduk.

### 17 - 18

Bu bölümde password.new ve password.old isminde 2 dosya verilmiş bu 2 dosyanın belli bir kısmı ortakmış belirli bir kısmında da parola gizli imiş burada diff komutunu kullanmamızı istiyorlar. diff komutu kullanımı diff komutu 2 dosya arasındaki farkları buluyor.

```bash
diff a.txt b.txt
<ab # b.txt dosyasında bulunmayan fakat a.txt dosyasında bulunan anlamına gelir.
>cd # a.txt dosyasında bulunmayan fakat a.txt dosyasında bulunan anlamına gelir.
```

> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

### 18 - 19

Bu soruda bandit18 kullanıcısı olarak bağlandığımızda .bashrc dosyası modifiye edilmiş ve ekrana byby ! yazıp çıkıyor. Parola ev dizinimizde readme dosyasında saklı buyüzden bandit18 kullanıcısı olarak su şekilde bağlanmamız lazım. `ssh bandit18@bandit.labs.overthewire.org -p 2220 'cat readme'` flag direkt karşımızda.

> IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

### 19 - 20

Bu soruda bandit20-do diye bir dosya verilmiş ve dosyanın suid biti aktif yani u dosyayı çalıştıran herhangi biri dosya çalıştığı sürece dosyanın sahibinin yetkilerine sahip olur bu dosyayı çalıştırıpda bandit20 nin passwordunu çekebiliriz. **/etc/bandit_pass/dizinine** gidip orada `/home/bandit19/bandit20-do cat bandit20` komutunu çalştırdık ve flag karşımızda.

> GbKksEFF4yrVs6il55v6gwY5aVje5f0j

### 20 - 21

Bu soruda bize suconnect diye çalıştırılabilir bir dosya verilmiş ve suidbiti aktif, dosya argüman olarak verilen porta bağlantı kurup bağlantının ilk satırını okuyor eğer bir önceki flag ile eşleşirse bir sonraki flagı veriyor. Bizde `echo 'GbKksEFF4yrVs6il55v6gwY5aVje5f0j' | netcat -lp 4444 &` 4444. porttu dinlemeye aldık (-l (listen) , -p (port)) ve bunu yaparken ekrana eski flagı yazdırdık ve komutu arkaplanda çalıştırmak için sonuna & ekledik. `jobs` komutu ile arkaplanda çalışan işlemleri görüntüleyebiliriz `fg` komutu ile arkaplandaki işlemi öne alabiliriz `bg` komutu ile de arkaya atabiliriz. İlla işlemi arkaya atmaya gerek yok `tmux` sisteme yüklü onu da kullanabiliriz. Daha sonra `./suconnect 4444` komutunu çalıştırıp bir sonraki flagı yakaladık.

> gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

### 21 - 22

Bu soruda Linuxta belirli zamanlarda belirli scriptleri çalıştırmak için kullanılan `crontab` komutu ve yapılandırılması kullanılmıs "/etc/cron.d/" dizininde yapılandırma dosyaları var `cat /etc/cron.d/cronjob_bandit22` komutu ile okuduğumuzda her dakika `/usr/bin/cronjob_bandit22.sh` scriptinin çalıştırıldığını farkettik ve bu scriptte bir sonraki parolayı /tmp dizini altında bir ASCII Text dosyasına yazdırıyor. Dosyayı cat ile okuduğumuzda Flag karşımıza çıktı.

>  Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

### 22 - 23 

Bu soruda önceki sorunun mantığıyla tamamen aynı bandit23 kullanıcısı her dakika bir komut çalıştırıyor "/usr/bin/cronjob_bandit23.sh" scriptinin içeriğine baktığımızda `mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)` satırı oldukça dikkat çekiyor `mytarget` değişkenine `I am user bandit23` in MD5 hashinin `cut` edilmiş halini atamışlar (algoritmayı bizde uyguladığımızda filename çıkıyor zate) ve /tmp/ dizininde bu isimde bir dosyaya flagı yazıyor cat komutu ile dosyayı okuduğumuzda flag direkt karşımızda.

> jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

### 23 - 24

Bu seviyedede her dakika bandit24 kullanıcısı tarafından çalıştırılan bir script var "cat /usr/bin/cronjob_bandit24.sh" komutu ile scriptin içeriğine baktık ve scipt /var/spool/bandit24/ dizinindeki bütün scriptleri çalıştırıyor ve ardından siliyor. Bizde bi script yazdık yazdığımız script bir sonraki flagı "cat /etc/bandit_pass/bandit24" çekiyor ve /tmp/ de bir dizin oluşturup orada bir dosyaya yazıyor daha sonra yazdığımız scripti "/var/spool/bandit24/" (çalıştırma yetkisi veriyoruz herkes için) dizinine attığımızda scriptimiz bandit24 kullanıcısı tarafından çalıştırılacak ve flagı çekebileceğiz 
(bandit23 ken yetkimiz yok) scriptimizin içeriği aşağıdaki gibi.

```bash
#!/bin/bash
deger=`cat /etc/bandit_pass/bandit24`
mkdir /tmp/dizin3
touch /tmp/dizin3/flag.txt
echo "$deger" > /tmp/dizin3/flag.txt
exit
```



> UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

### 24 - 25



