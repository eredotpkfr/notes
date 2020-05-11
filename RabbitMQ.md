## RabbitMQ

### Nedir?
`Erlang` dili ile yazılmış, açık kaynak kodlu bir `Message Queue` mesaj kuyruğu sistemidir, benzerleri olarak `Apache Kafka`, `Msmq`, `Microsoft Azure Service Bus`, `Kestrel`, `ActiveMQ` servisleri bulunmaktadır.

`RabbitMQ` veriyi alır ve bir kuyruğa iletir ardından kuyruğu izleyen bir başka yazılım kuyrultan gelen veriyi bekler `RabbitMQ` kuyruktan veriyi alacak olan yazılım ayağa kalkmadığı sürece verileri kuyrukta tutar bu nedenle oldukça kullanışlıdır. Ayrıca `RabbitMQ` nun kendi içerisinde farklı `exchange` türleri vardır.

`RabbitMQ` kavramları;
* `Producer` Kuyruğa veriyi gönderen uygulama, bir script olabilir.
* `Consumer` Kuyruğu dinleyen uygulama/script.
* `Routing key` veriyi yönlendireceğimiz anahtarımız.
* `Exchange` veriyi ilgili `routing key`e göre ilgili queue’ya yönlendiren bölüm.
* `Queue` verinin son olarak düştüğü kuyruk, kuyrukta çok fazla veri birikirse ve `Consumer` bunlara yanıt veremez ise kuyruk şişer, server restart edilirse kuyruktaki veriler kayıp olabilir.
* `Exchange type` Gelen verinin, `routing key`e göre hangi queue’ya `nasıl` gönderileceğini belirtir.
* `VirtualHost` VirtualHost’lar, genelde yetki yönetimi için kullanılır, Exchange ve Queue’lar virtualhost’lar içinde tanımlanır.

Kullanım alanları;
* Bankaların kullandığı EFT vb. işlemlerde
* Yoğun istek (request) alan e-ticaret sistemlerinde
* E-mail gönderimi işlemlerinde

Avantajları;
* FIFO - First In First Out kuyruk oluşturur.
* OpenSource olduğu için yaygın kullanımı vardır.
* Cross platform destekler, windows ve linux sistemlere kurulabilir.
* Web arayüzü sunarak kolaylık sağlar.
* Birçok dil ile entegre çalışır;
  * Python, Java, Ruby, PHp, C#, JavaScript, Go, Objective-C, Swift
* Asenkron çalışır.
