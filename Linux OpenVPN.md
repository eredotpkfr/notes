## Linux OpenVPN

Firefox'a gir ve url kısmına `about:config` yaz daha sonra arama kısmına `media.peerconnection.enabled` yaz çıkan ayardaki değere çift tıklayarak `true` den `false` ye çek.

googleda free vpnbook diye aratıp free vpn elde ediyoruz. "https://www.vpnbook.com/" adresinden openvpn bulabiliriz. indirdikten sonra unzip komutu ile zipten çıkartıyoruz.

```bash
sudo openvpn <kullanılacak_vpn> 
# kullanıcı adı ve parola soracak websitesinde veriyor.
# "initialization squence completed" yazısını gördüğümüzde aktif oluyor

# "https://wtfismyip.com" dan external ip adresini görebilirsin.
```

