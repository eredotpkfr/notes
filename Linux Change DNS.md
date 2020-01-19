## Linux Change DNS

```bash
sudo cat /etc/resolv.conf # Nameserver'ları gösterir şuanki. 
sudo nano /etc/dhcp/dhclient.conf # nameserver'ları değiştirmek için konfigrasyon dosyası.
prepend domain-name-servers # DNS ekleyeceğimiz kısım, baştaki "#" kaldır ve ekle. Birden fazla eklenebilir.

# ÖRN.
# prepend domain-name-servers 9.9.9.9, 1.1.1.1, 8.8.8.8;

service network-manager restart # network manager yeniden başlat 
sudo cat /etc/resolv.conf # Kontrol et ve görki dns ler eklenmiş/değişmiş.
```

