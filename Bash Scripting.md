## Bash Scripting

###  Değişken atama

```bash
a="10" # a nın içerisine string 10 değerini ata.
a=10 # a nın içerisine integer 10 değerini ata.
isim="Hamza Tek" # isim değişkeninin içerisine "Hamza Tek" stringini ata.
echo $isim # isim değişkenini ekrana yazdır.
unset isim # isim değişkeninin değerini kaldır.
```



> **'#'** ile başlayan satırlar yorum satırırdır.
>
> `sleep` komutu verilen değer kadar saniye cinsinden bekler.
>
> `sleep 60` 1 dakika bekler.



### declare Komutu

declare komutu herhangi bir veri tipinde değişken atamamızı sağlar.

`declare -a liste` **-a(rray)** liste isminde bir dizi oluşturur.

`liste=(kırmızı elma yedim)` liste listesine **"kırmızı" "elma" "yedim"** 
elemanlarını verir.

```bash
echo ${liste} # listenin ilk elemanını yazdırır.
echo ${liste[1]} # listenin 1. indisini yazdırır.
echo ${liste[*]} # listenin tüm elemanlarını yazdırır.
echo ${#liste[0]} # listenin 0. indisinin length (uzunlugunu) yazdırır.
echo ${#liste[*]} # listenin kaç elemanlı olduğunu yazdırır.
declare -i a # a değişkeninin tipinin integer olacağını belirtir.
```

fazlası için `declare --help`



### Diziler

```bash
renkler=( "kırmızı" "yeşil" "mavi" ) 
echo ${renkler[0]} # 0. indisi yazdırır.
echo ${renkler[@]} # bütün indisleri yazdırır.
echo ${#renkler[@]} # eleman sayısını yazdırır.
echo ${!renkler[@]} # tüm elemanların index sırasını gösterir.
renkler[3]="sarı" # sarı değerini diziye ekler
```



### Regular Expression

`;` tek satırda komutları sonlandırıp başka bir komut girmeye yarar. `ls; whoami`

`*` Her şey olabilir , joker karakter.

```bash
ls a* # a ile başlayan dosyaları listele
ls *.* # dosya uzantısı olanların hepsini listele
ls *.py* # .py den önce ve sonra her şey olabilir (.py uzantılı dosyaları listele)
ls *c # c ile biten dosyaları listele
find . -name A* # A ile başlayan dosyaları bul
rm -rf *clearnote* # simplenote içeren dizinlerin hepsini sil
```



`>` Dosyanın içerisine yaz (üstüne yazma gerçekleşir), touch koutunun kısaltmasıdır dosya yoksa oluşturur varsa içerisine yazar. 

```bash
> script.sh # script.sh dosyası oluşturur.
echo "Merhaba Dünya" > deneme.txt
echo "Selam" > deneme.txt
cat deneme.txt # Çıktısında sadece Selam yazar
```



`>>` Dosyanın içerisine yaz (üstüne yazma gerçekleşmez)

```bash
echo "Selam" > deneme.txt
echo "ben ali" >> deneme.txt
cat deneme.txt  # çıktısında Selam ben ali yazar.
```



`?` Karakteri temsil eder her değeri alabilir

`ls a?` a dan sonra bir karakter olan dosyaları yazdır

`ls di???` di den sonra 3 karakter olan dosyaları yazdır. 



"  " ve '  ' Farkı

```bash
degisken="Selam"
echo "$degisken" # Çıktısında Selam yazar.
echo '$degisken' # Çıktısında $degisken yazar.
```



`&` subshell e atar komutu arkada çalıştırır

`echo "selam" & echo "merhaba"` "&" işaretinden önceki komutları arkada işleyecek aradaki işlemleri görmeyeceğiz daha sonra ekrana merhaba yazacak sonrasında ise subshell den çıktıyı alıp bize gösterecekçıktısı merhaba selam şeklinde olur.



`&&` ve işareti `||` veya işareti

`ls && clear` paralel komut çalıştırır ve işaretidir. `komut1 && komut2` komut2 sadece ve sadece komut1 sıfır çıkış durumu ile dönmüşse çalıştırılır. 

`komut1 || komut2` komut2 sadece ve sadece komut1 sıfırdan farklı bir çıkış durumu ile dönmüşse çalıştırılır. 



`^` ile başlayan demektir (satır başı)

```bash
grep -c "^Birinci" # Birinci ile başlayan kaç değer var.
grep -n "^İkinci" # İkinci ile başlayan satırları satır numarası ile birlikte getir.
grep -v "^Üçüncü" # Üçüncü ile başlamayan (-v) satırları getir.
grep -v -n "^Dördüncü" # Dördüncü ile başlamayan (-v) satırları satır numarası ile birlikte (-n) getir. (-c çıktıda kaç satır varsa satır numarasını yazdırır.)
```



`$` Satır bununla bitenleri getir anlamına gelir.

```bash
echo "12.12.2017" | grep -E '.2017$' # -E, egrep le aynı kullanım için grep yalnızca 1 argüman alır birden fazla vermek için egrep yada grep -E kullanmalıyız
cat /etc/passwd | egrep '^a|^b' # a ve b ile başlayan satırları getir.
cat /etc/passwd | grep -E -v -n '^a|^b|c$' # a ve b ile başlayan ve c ile biten satırları getirme ve bunu satır numaralari ile birlikte yap. (egrep ve grep -E de | işareti ile argümanlar ayrılır).
```



`|` **(pipe)**,  önceki komutun çıktısını sonraki komuta girdi olarak verir

`cat dosya.txt | grep -c "^File"` dosya.txt dosyasının içeriğinde "File" ile başlayan (^) 
kaç değer var.



Matematiksel işlem yapmak için `expr` komutu kullanılır. bc komutu ilede matematiksel işlemler yapılabilir çok ayrıntılıdır. `echo "10.5 * 2" | bc `, bc komutu ile karekök alınabilir.` echo "sqrt(25)" | bc -l` **-l** math library kullanıyorum anlamında,` man bc` ile daha fazla...

`echo "2^3" | bc -l` 2 nin küpünü alır.

```bash
expr 10 + 10
expr 100 / 2
expr 10 * 2 # Burası çok önemli bu komutu girersek hata alacağız expr komutu ile çarpma yapmak istiyorsak escape (backslash) koymamız gerekiyor. Backslash ondan bir sonraki karakterin komut olarak algılanmamasını sağlıyor örn. a=10 && echo "\$a" -> çıktısı "$a" olacaktır.
expr 10 \* 2 # Bu kullanım doğrudur.
```



`[] `İçerisindeki karakterleri içeren

```bash
grep [azk]hmet örnek.txt # ahmet,zahmet,kahmet
grep [a-z]n örnek.txt # an - zan arasındaki bütün kelimeleri arar a dan z ye
grep [1-9]n örnek.txt # 1n - 9n arasındaki bütün kelimeleri arar 1 den 9 a olumsuzluk anlamıda katar aynı zamanda
```



`\\` Bundan sonraki karakteri düz metin olarak algıla
`grep ahmet\\$ dosya.txt`



`.` Herhangi bir karakter olabilir anlamına gelir soru işareti gibi aynı zamanda bulunduğun dizin anlamına gelir 

`grep f.....n dosya.txt # f ile başlıyıp n ile bitenleri ara dosya.txt de`

`(())` Çift parantez içinde yapılan her işlem aritmatik işlem olarak algılanır. Aynı zamanda $ işaretinden sonraki tek parantez içindeki işlemler komut olarak algılanır.

`echo $(grep root /etc/passwd)`



###   Değişken Üzerinde Değişiklik Yapma

```bash
a="nabererdoganyoksul"
echo ${#a} # Uzunluğunu yazdırır (length).
echo ${a:5:7}   # "erdogan" çıktısını verir
echo ${a:5}     # "erdoganyoksul" çıktısını verir ilk değer başlangıç sonraki değer bitiş. Yapılan değişiklikler kalıcı değildir.
echo $a # Çıktısı "nabererdoganyoksul" olacaktır.
echo ${a#naber} # Baştaki naberi yazdırmasın dedik, çıktısı "erdoganyoksul"
dosya=/home/eredot/test.sh
"echo ${dosya##/*/}" # en uzun (baştan başlar) / / arasındakileri siler çıktı -> test.sh
"echo ${dosya#/*/}" # en kısa (baştan başlar) / / arasındakileri siler çıktı -> eredot/test.sh
"echo ${dosya%%/*}" # sondan başlar en uzun çıktı -> 
"echo ${dosya%/*}" # sondan başlar en kısa çıktı -> /home/eredot
a=ankara && b=GAZİANTEP 
echo ${a^*} # ilk harfini büyültür.
echo ${a^^*} # bütün harfi büyültür.
echo ${b,*} # ilk harfi küçültür.
echo ${b,,*} # bütün harfleri küçültür.
deneme=ankara && ankara=baskent
echo ${!deneme} # ! den ki ifade değişken olarak alınır ve değişkenin içerisindeki değeri ekrana basar çıktı -> baskent
echo ${a%yoksul} # "%" işareti değişkenin sonuna bakar "naber" yazsaydık, naberi silmezdi > "nabererdogan"
echo ${a:-bunu_yaz} # a değişkeninde herhangi bir değer atanmazsa içerisine "bunu_yaz" değerini verir. 
echo ${a:=bunu_yaz} # hem ekrana basar hemde değişkene atama yapar ("bunu_yaz değerini verir")
echo ${a:?tanımsız} # a ya değer atanmadı ise hata verdirtir ve scriptten çıkar.
echo ${a:+erdogan} # a nın değeri varsa ekrana "erdogan" yazar.
```



###   export Komutu ve read Komutu

script.sh şeklinde bir shell dosyamız olsun ve dosya içeriğinde echo **$deger** olsundosyayı çalıştırdığımızda çıktı vermez çünkü **"deger"** değişkeni tanımlı değil shellde `export deger="eredot"` şeklinde bir tanımlama yapıp dosyayı 
çalıştrırsak "eredot" çıktısını alırız. **$PATH** şeklinde bir hazır tanımlı değişkenimiz vardır eğer script.sh  dosyasının dizinini bu değişkenin içerisine tanımlarsak biz hangi dizinde olursak olalım ./script.sh yazdığımızda dosya çalışır. script.sh dosyasnın dizini **"/home/eredot/script/script.sh"** olarak varsayalım **"pwd"** çıktısının **/home/eredot/script/** olduğunu varsayalım `export PATH="$PATH:/home/eredot/script/"` şeklinde bir değişken tanımlaması yaparsak biz nerede olursak olalım **./script.sh** yazdığımızda kodumuz çalışacaktır.

Direkt **"/bin"** dizini altına atarsakta bu işlemi gerçekeştirmiş oluruz. Yaptığımız işlem kalcı değildir yalnızca o shell de çalışır. `export PATH=$PATH:~/` ev dizinimizdeki scriptleri **PATH** değişkenimize ekler `export PATH=~/:$PATH` yapmak yanlış olur **PATH** in başına eklemiş oluruz ilk orada arar **cat /etc/environment** dosyasında düzenlemeler yapılabilir. 

Bash her işlemde subprocess oluşturur, çocuk işlemler alt işlemler öyle  çalışır. "source" komutu yeni bir subprocess oluşturmaz direkt o shell de çalışır. çalıştırılabilir dosyayı "strace" ile çalıştırırsak bütün sistem çağrılarını gösterir.



read komutu klavyeden değer okur `-p` parametresi ile ekrana hem 1 şeyler yazar hemde klavyeden değer okur ve değişkene atar.
`read -p "Sayı gir: " sayı` sayı değişkenine atar.
`read -s deger` silent modda çalışır girilen değerler ekranda gözükmez.
`read -ps "Password : " passwd` silent modda ve ekrana yazı yazdırarak girdi alıyor aynı zamanda passwd 
değişkenine atıyor.



### alias Komutu

alias "takma ad" demektir çok sık kullanılan komutlara takma isim vererek o takma isimle verdiğimiz komutu çalıştırabiliriz. `alias pt='cd /scripts/fire_ball/'` komut satırında **"pt"** komutunu çalıştırdığımızda **"cd /scripts/fire_ball/"** komutunu çalıştıracak ve fire_ball dizinine gidecek. Yapılan bu atamalar kalıcı değildir yalnızca o shell de çalışır. İşlemi kalıcı yapmak istersek kullanıcı **/home** dizinimizin altındaki **.bashrc** dosyasında en alt kısımda alias tanımları var oraya kendi tnaımlarımızı ekliyip dosyayı kaydettiğimizde bilgisayarı kapatıp açsak bile yaptığımız tanımıkullanabiliriz. .bashrc dosyasında değişiklik yaptığımızda değişikliklerin algılanıp kaydedilmesi için aşağıdaki komutu çalıştırmak gerekir `. .bashrc`



### awk Komutu ve df Komutu

```bash

isim     soyisim     cinsiyet
hamza    tek         E
ali      keklik      E
ayşe     bahar       K

# dosya.txt nin içeriği yukarıdaki gibi olsun. Stünlar $1 $2 $3 şeklinde gider.
cat dosya.txt | awk '{print $1}' # isimleri yazdırır.
cat dosya.txt | awk '{print $2}' # soyisimleri yazdırır.
cat dosya.txt | awk '{print $3}' # cinsiyetleri yazdırır.
cat dosya.txt | awk '{print $1 $2}' # isim ve soyisim yazdırır fakat araya boşluk koymaz.
cat dosya.txt | awk '{print $1" "$2}' # isim ve soyisim yazdırır fakat araya boşluk koyar.
ls -altr | awk '{print $1}' # dosya izinlerini verir.
ls -altr | awk '{print $1" "$9}' # dosya izinlerini ve isimlerini verir.

# df -h sisteme mount edilen diskleri ve yüzde kaç kullanıldığnı gösterir.


# '\' işareti koyup entera basarsak komut alt kısımdan devam eder komut ekrana sığmadığında kullanılır.


df -h | awk 'BEGIN {print "BAŞLIK YAZAR SONRA İŞLEMİ YAPAR"} {print $1 " --> " $2}'
df -h | awk '{print $1 " --> " $5} END {print "İŞLEM SONU"}'

# Eğer sütünlar boşluklarla değilde ":" ile ayrılmış olsaydı bunu "awk" ya bildirmemiz gerekirdi
cat örnek.txt | awk 'BEGIN {FS=":"} {print $1 $2}'
cat örnek.txt | awk 'BEGIN {FS=":"} {OFS=" --> "} {print $1,$2}' OFS 
# Otomatik olarak aralara " --> " koyar.
cat örnek.txt | awk 'BEGIN {FS=":"} {OFS=" --> "} {ORS="\n---------\n"} {print $1,$2}' ORS # Satırlarn arasına verilen değeri koyar.
awk -f komut.awk dosya.txt # Şeklinde bir kullanımla hazır dosyadan komutta 
çağırabiliriz.
```



### cut ve wc Komutu

Bir dosya içerisindeki satırların içerdiği alanları keserek belirli bir yere kopyalamak için kullanılır.Kesme işlemi bazı ayıraçlara göre olabildiği gibi, sabit uzunluktaki alanlara göre de yapılabilir.

`-b` Karakter konumlarını belirler. Byte tipinde işlem yaparak karakterleri byte birimiyle algılar.
`-c` Karakter konumlarını belirler. Byte tipinde işlem yaparak karakterleri karakter olarak algılar.
`-f` Özel bir ayıraçla ayrılan alanları tanımlamak amacıyla kullanılır.
`-d` Ayıraçları tanımlamak için kullanılır.

`-s`,`-f` seçeneği kullanıldığı durumlarda, ayıraca sahip olmayan satırların atlanmasını sağlar.
`-n` Tek byte’tan fazla olan karakterleri tamamiyle alır.

`cat /etc/passwd | cut -d":" -f1` ':' işaretine göre stünlara ayırır ve 1. stünu yazdırır.

`cat /etc/passwd | cut -d":" -f1 | cut -c3` ':' işaretine göre stünlara ayırır ve 1. stünun ilk 3 karakterini yazdır.



`wc` (word count) komutu ise herhangi bir dosyanın sahip olduğu, "byte sayısı", "satır sayısı" ve "kelime sayısı" bilgilerini gösterir. Bir kaç opsiyon alabilmektedir.

`-w` Kelime sayısı.
`-L` En uzun satır uzunluğu.
`-l` Satır sayısı.
`-c` Byte sayısı, dosyanın kaç Byte'tan oluştuğu.

`cat /etc/passwd | wc -w` kaç kelime içeriyor onu yazdırır.
`cat /etc/passwd | wc -l` kaç satırdan oluşuyor onu yazdırır.



### sed (stream editor) ve du (disk usage) Komutu

**sed (stream editor) "s/a/b/" dosya_adı temel kullanım 1. aralığa değiştirilecek 2. aralığa yerine ne koyulacak, bu örnekte a yerine b yazacaktr (" sed 's///' dosya_adı ").** sed satır satır işlemler yapar örneğin " sed 's/bakkal/shop/' dosya_adı" bakkal yerine shop yazmak istersen ilk satıra bakar bakkal gördüğü anda yerine shop yazar ve os satır için işlemini orada bitirir diğer satıra geçer. İşlemi global hale getirmek için " sed 's/bakkal/shop/g' dosya_adı" şeklinde kullanmalıyız. /usr/bin yerine /home/dizin şeklinde bir değişiklik yapacaksak backslash kullanmamız lazım çünkü sed 
/usr/bin deki slashları ve kendi slashlarını karıştırır buyüzden kaçış ifadesi ile slashları komut olarak alglamamasını sağlamamız lazım.

```bash
sed 's/\/usr\/bin/\/home\/dizin/g' dosya_ad # Şeklinde
sed 's|/usr/bin|/home/dizin|g' dosya_adı # Şeklinde de kullanılabilir
sed 's_/usr/bin_/home/dizin_g' dosya_adı # Şeklinde de kullanılabilir
# Yaptığmız değişiklikler çıktı olarak gelir dosyaya işlemi yapmaz yönlendirme yaparak yada -i parametresi ile ana dosyada değişiklik yapabiliriz.
sed 's/\/usr\/bin/\/home\/dizin/g' dosya_adı > dosya_adı_2
sed -i 's/\/usr\/bin/\/home\/dizin/g' dosya_adı
```



`du` (disk usage) komutu ise dosyaların diskte kapladığı alanı gösterir -a parametresi bütün dosyalar için 
gösterir `-b` parametresi ise byte çinsinden gösterir.

`du deneme.txt` deneme.txt nin diskte kapladığı alanı göster.
`du -b -a` bütün dosyaların kapladığı alanı byte cinsinden göster.
`du -b deneme.txt` deneme.txt nin diskte kapladığı alanı byte cinsinden göster.



### tr (translate) ve diff (different) Komutu

`tr` (translate) komutu `sed` komutu gibi aynı kelime veya karakter değiştirme, çıkartma gibi işlemleri yapar.

```bash
cat /etc/passwd | tr ":" "/" # bütün : ları / ile değiştirir
cat /etc/passwd | tr -d ":" # ":" karakterlerini atar -d (delete) 
cat /etc/passwd | tr "[a-z]" "[A-Z]" # bütün küçük harfleri büyük harflere çevirir.
cat /etc/passwd | tr d "[:space:]" # boşlukları at. ( [:lower:] [:upper:] )
echo 'erdogan' | tr 'A-Za-z' 'N-ZA-Mn-za-m' # erdogan yazısını rot13 algoritması ile şifreler (rot13 sezar gibidir 13 defa harfleri kaydırır.)
```



`diff` komutu 2 dosyayı karşılaştırır ve farklarını ekrana basar.

`diff a.txt b.txt` a.txt ve b.txt yi satır satır karşılaştırır farkları varsa ekrana basar.



### if else Yapısı

```bash
if [ $a -eq 0 ]   # $a değişkeni 0 a eşitse  
then 
echo $a           # ekrana $a değerini yazdır
else              # diğer koşullarda exit komutunu çalıştır.
exit
fi                # if bloğunu kapat
```



> [ -ne ] eşit değilse not equal.
>
> [ -lt ] deger1 deger2 den küçükse (lower than).
>
> [ -le ] küçük ve eşitse.
>
> [ -gt ] büyükse (greater than).
>
> [ -ge ] büyük eşitse.
>
> [ -f dizin/dosya ] dosya kontrolü yapar.



> `if(())` iç içe 2 parentez ile harflerle değilde operatörlerle karşılaştırma yapılabilir.
>
> `while(($a <= 10))` iç içe 2 parentez ile harflerle değilde operatörlerle karşılaştırma yapılabilir.



```bash
if(( "$a" <= "$b"))
if(( "$a" >= "$b"))
if(( "$a" == "$b"))
if[[ "$a" < "$b"]] # alfabetik dizilime göre string karşılaştırır. b harfi a harfinden büyüktür.
if [ $a -gt 10 ] && [ $a -lt 30 ] # a değişkeni 10 dan büyükse ve 30 dan küçükse.
if [[ $a -gt 10 && $a -lt 30 ]] # şeklinde kullanıcaksam iç içe 2 köşeli parantez kullanmam gerekli.
if [ $a -gt 10 ] || [ $a -lt 30 ] # a değişkeni 10 dan büyükse veya 30 dan küçükse.
if [[ $a -gt 10 || $a -lt 30 ]] # şeklinde kullanıcaksam iç içe 2 köşeli parantez kullanmam gerekli.
if [ $a -gt 10 -o $a -lt 30 ] # a değişkeni 10 dan büyükse veya 30 dan küçükse.
```



>  SIK KULLANILAN OPERATÖRLER
>
> ! EXPRESSION 	The EXPRESSION is false.
>
> -n STRING 	The length of STRING is greater than zero.
>
> -z STRING 	The lengh of STRING is zero (ie it is empty).
>
> STRING1 = STRING2 	STRING1 is equal to STRING2
>
> STRING1 != STRING2 	STRING1 is not equal to STRING2
>
> INTEGER1 -eq INTEGER2 	INTEGER1 is numerically equal to INTEGER2
>
> INTEGER1 -gt INTEGER2 	INTEGER1 is numerically greater than INTEGER2
>
> INTEGER1 -lt INTEGER2 	INTEGER1 is numerically less than INTEGER2
>
> -d FILE 	FILE exists and is a directory.
>
> -e FILE 	FILE exists.
>
> -r FILE 	FILE exists and the read permission is granted.
>
> -s FILE 	FILE exists and its size is greater than zero (ie. it is not empty).
>
> -w FILE 	FILE exists and the write permission is granted.
>
> -x FILE 	FILE exists and the execute permission  is granted.
>
> -f FILE   FILE exists and is a regular.



### case Kullanımı

```bash
case $1 in
  "kırmızı")
    echo "kırmızı";;
  "yeşil")
    echo "yeşil";;
  *)
    echo "farklı bir renk";;
esac
```

$1 değişkeni scripte gönderilen 1. argümandır ($1 $2 $3 $4 şeklinde sıralanır). 1. argüman kırmızı ise ekrana kırmızı yazacak, yeşil ise yeşil yazacak hiçbiriyle eşleşmezse "farklı bir renk" yazacak (*).

> $# argüman sayısı
>
> $* bütün argümanları içerir
>
>
> $@ bütün argümanları içerir fakat $* dan farklı olarak dizi halinde tutar ve indis işlemleri yapılabilir.
>
> $0 scriptin ismini tutar 0. argümandır :) 



### for loop Kullanımı

```bash
for((i=1;i<=10;i++))
  do
    echo "Merhaba Dünya"
done
```

Ekrana 10 de "Merhaba Dünya" yazacaktır (değişken atama ve değişkeni arttırma işlemini 2 tane iç içe parantez içerisinde yapmamızın nedeni bash scripting de matematiksel işlem olarak algılanır bu şekilde kullanmamız gerekiyor.)



> for degısken in {00..99} -> bash version 3 den fazla ise bu komut çalışır ("echo $BASH_VERSION")
>
> for degısken_2 in {1..100..2} -> bash version 3 den fazla ise bu komut çalışır ("echo $BASH_VERSION")
>
> for i in 1 2 3 4 5 
>
> for i in $(ls ~/)
>
> for x in `ls /etc`



### while loop Kullanımı

```bash
a=1
while [ $a -le 10 ]
  do
    echo "$a"
    a=`expr $a + 1`
  done
```



>  1 den 10 a kadar yazdırır. ` işareti içerisindekileri komut olarak algılar ve çalıştırır, çıktısı üzerine işlem yapılır.



`ls | while read deger; do echo $deger; done;` while ve read in birlikte kullanımı.

`[ -f "until.txt" ] && rm until.txt; > until.txt` until.txt varsa sil yoksa oluştur :)

> until ve while aynı şeylerdir aynı işlevleri yaparlar



```bash
dosya_boyutu=`du -k until.txt | awk '{print $1}'``
until [ $dosya_boyutu -ge 10 ]
  do
    echo "erdogan" >> until.txt
    dosya_boyutu=`du -k until.txt | awk '{print $1}'``
  done
```



> Dosya boyutu 10 kilobayt olasıya kadar içerisine erdogan yazacaktır.
>
> **break** döngüyü kırar, sonlandırır.
>
> **continue** alt satırları yapmaz döngüyü başa atar.



### Function Kullanımı

```bash
#!/bin/bash
sayi=$1
sonuc=1
fonk(){
  while [ $sayi -gt 0 ]
      do
          sonuc=$(($sonuc * $sayi))
          sayi=$(($sayi - 1))
      done
  echo $sonuc
}
fonk

function yazdır(){
  echo $1
  }
yazdır ekrana_yaz
```



> Yukarıdaki gibi fonksiyonada argüman gönderebiliyoruz. Yukarıdaki fonksiyon faktoriyel hesaplaması yapar. 
>
> `function fonksiyon_adı(){}` şeklindede kullanılabilir.
>
> Çoğu programlama dilinde olduğu gibi global ve local değişkenler buradada geçerli.



### Select Döngüsü

```bash
select degisken in liste
do
  komutlar
done

select selection in ankara izmir istanbul elazığ
do
  echo "$selection seçildi"
done
Genelde select case ile birlikte kullanılır.
select selection in ankara izmir istanbul elazığ
do
  case $selection in 
    ankara )
      echo "ankara seildi";;
    *   )
      echo "-----";;
    esac
done
```