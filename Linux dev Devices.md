## Linux /dev Devices

### /dev/null

`find /etc -name "python" 2> /dev/null` 2 sayısı STDERR i ifade ediyor hataları /dev/null dosyasına gönderiyo null dosyazı zaaten karadelik ne verirsek yok olup gidiyor.

`tail -f /dev/null`, `-f` argümanı takip eder oraya gelen son verileri gösterir ama null a bir şey gelmeyeceği için sonsuz döngüye girer kapatmanın tek yolu sinyal göndermek.

### /dev/zero

`dd if=/dev/zero of=/home/eredot_pkfr/Desktop/outputfile.txt bs=512 count=2` 1024 byte lık dosya oluşturur. Dosya içeriğini okuduğumuzda içeriği boş gözükür çünkü içerisi 0 larla doludur 0 okuyulup ekrana basılacak 1 değer 
değildir. `hexdump outputfile.txt` ile içeriğini görebiliriz ayrıntı için `hexdump -v outputfile.txt` Nerede kullanılır? diskte yer ayırtmak için kullanılabilir.

### /dev/urandom

`dd if=/dev/urandom of=/home/eredot_pkfr/Desktop/random.txt bs=512 count=4` İçerisi full random verilerle dolu bir dosya oluşturur ne avantajı vardır?  **zip** ile sıkıştırdığında zero ya göre daha fazla boyut elde edersin. `hexdump random.txt`

> dd if=/dev/zero of=/dev/null bs=512 
>
> Sonsuz bir kaynaktan sonsuz bir yokluğa full veri aktarır ve CPU kitler (100%)

### /dev/tcp

`echo "HEAD / HTTP/1.1" < /dev/tcp/www.google.com/80` 80.porttan bağlantı var mı? kontrol eder echo $? ile başarılı olup olmadığı görülebilir.



