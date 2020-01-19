## Linux Links

Linuxta dosyaları bağlayabiliyoruz yani aslında hiç orda olmayan bir dosyayı ordaymış gibi gösterebiliyoruz bir örnekle anlatırsak daha iyi olur.

Örneğin bir program yüklediniz ve programın çalışması için "/etc/program/" dizininde olması gerekiyor ama sizin "/etc" dizini altında programı yükleyecek boş alanınız yok sizde programı "/home/ev/" dizinine yükleyip "/etc/program/" dizinine bağlarsanız program sanki oradaymış gibi görünecektir 2 dizindende çalıştırılacaktır.

```bash
ln -s /home/ev /etc/program # Şeklinde bir kullanımla bunu yapabilirsiniz.
ln ./deneme.txt ./deneme_2.txt # Şeklinde bir kullanımla farklı isimde bir bağlantı oluşturabilirsiniz.
```

> deneme.txt de yapılacak olan değişiklik deneme_2.txt de de yapılır çünkü bu 2 dosya birbirlerine artık bağlıdır. "deneme_2.txt diskte hiç yer kaplamaz"

`ls -l` komutu ile link olan dosyaları saptayabiliriz örn.

> `lrwxr-xr-x  root  root  deneme.txt `En baştaki "l" harfi bir link olduğunu belirtiyor.

* Eğer `ln` komutuyla birbirlerine bağlanacak olan dosya sistemi elamanları birer dizinse; `-s` parametresini kullanmak zorundasınız.

* Eğer `ln` komutuyla birbirlerine bağlanacak olanlar birer dosyaysa ama farklı dosya sistemlerindeyse (örneğin, farklı disklerdeyse), gene `-s` parametresini kullanmak zorundasınız.

* `ln` komutuyla, bir dizini ve bir dosyayı birbirlerine bağlayamazsınız. Bağlanacak olanların ikisi de dizin, ya da ikisi de dosya olmalıdır.

	

Aynı dosya sisteminde yer alan ve `-s` kullanılmadan bağlanmış olan dosyalardan birini silmeniz diğerini etkilemez. Asıl dosyayı silseniz bile, LINUX bağlantıyı fark edip dosyayı diskten gerçekten silmeyecektir. LINUX her dosya için yapılmış bağlantıları sayar ve her silme işleminde bağlantı sayısını bir azaltır. Gerçek silme işi bu bağlantı sayısı sıfırlanınca yapılır.

> **NOT:** Farklı dosya sistemlerinde yer alan bağlantılar için bu bağlantı sayma işine güvenmeyiniz. Farklı dosya sisteminde bağlantısı olan bir dosyayı silerseniz bşınız derde girer. Asıl dosya silinir ve diğer sistemde gerçekte varolmayan bir dosyayı gösteren bir bağlantınız kalır.