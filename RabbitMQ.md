## RabbitMQ

### Nedir?
`Erlang` dili ile yazılmış, açık kaynak kodlu bir `Message Queue` mesaj kuyruğu sistemidir, benzerleri olarak `Apache Kafka`, `Msmq`, `Microsoft Azure Service Bus`, `Kestrel`, `ActiveMQ` servisleri bulunmaktadır.

`RabbitMQ` veriyi alır ve bir kuyruğa iletir ardından kuyruğu izleyen bir başka yazılım kuyruktan gelen veriyi bekler `RabbitMQ` kuyruktan veriyi alacak olan yazılım ayağa kalkmadığı sürece verileri kuyrukta tutar bu nedenle oldukça kullanışlıdır. Ayrıca `RabbitMQ` nun kendi içerisinde farklı `exchange` türleri vardır.

`RabbitMQ` kavramları;
* `Producer` kuyruğa veriyi gönderen uygulama, bir script olabilir.
* `Consumer` kuyruğu dinleyen uygulama/script.
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

### Ubuntu server'a yüklemek
Bunun için `sudo apt-get install rabbitmq-server` şeklinde komut çalıştırmanız yeterli bununla beraber erlang'a olan bağımlı paketlerde yüklenecektir.

Sisteminiz başlatıldığında servisin otomatikman ayağa kalkmasını istemiyorsanız;
```
sudo systemctl disable rabbitmq-server.service
sudo systemctl disable epmd.service
```
şeklinde 2 farklı komut çalıştırmanız yeterli.

Sistem başlatıldığında başlamasını istiyorsanız;
```
sudo systemctl enable rabbitmq-server.service`
sudo systemctl enable epmd.service
```

Servis yönetimi;
```
sudo service rabbitmq-server start # servisi başlatır.
sudo service rabbitmq-server stop # servisi durdurur.
sudo service rabbitmq-server restart # yeniden başlatır.
```

> Config file `/etc/rabbitmq/rabbitmq-env.conf`

### Python ile RabbitMQ entegrasyonu
Python ile `RabbitMQ` servisine bağlanmak için `pika` isminde bir modül kullanmamız gerekiyor, modülü yüklemek için `pip3 install pika --upgrade` şeklinde bir komut çalıştırmak yeterli.

### rabbitmq_producer.py

```python
#!/usr/bin/python3

import pika
import json

data = [
	{'key1': 'value1'},
	{'key2': 'value2'},
	{'key3': 'value3'},
	{'key4': 'value4'},
	{'key5': 'value5'}
]

class RabbitMQ:
    """ Simple RabbitMQ Producer script """
	def __init__(self, host, port, username, password):
		self.host = host
		self.port = port
		self.username = username
		self.password = password

	def connection(self):
		return pika.BlockingConnection(pika.ConnectionParameters(
			host = self.host,
			port = self.port, credentials = pika.PlainCredentials(
				username = self.username,
				password = self.password
				)
			)
		)

	def send(self, data):
		channel = self.connection().channel()
		channel.queue_declare(
			queue = 'kuyruk',
#			durable = True
		)

		channel.basic_publish(
			exchange = '',
			routing_key = 'kuyruk',
			body = data,
#			properties = pika.BasicProperties(
#				delivery_mode = 2
#			)
		)

mq = RabbitMQ('127.0.0.1', 5672, 'guest', 'guest')

for _ in data:
	mq.send(json.dumps(_))
```



### rabbitmq_consumer.py

```python
#!/usr/bin/python3

import pika

class RabbitMQ:
    """ Simple RabbitMQ Consumer scritp """
	def __init__(self, host, port, username, password):
		self.host = host
		self.port = port
		self.username = username
		self.password = password

	def connection(self):
		return pika.BlockingConnection(pika.ConnectionParameters(
			host = self.host,
			port = self.port, credentials = pika.PlainCredentials(
				username = self.username,
				password = self.password
				)
			)
		)

	def get(self):
		channel = self.connection().channel()
		channel.queue_declare(queue = 'kuyruk')
		channel.basic_consume(
			queue = 'kuyruk',
			on_message_callback = self.get_data,
			auto_ack = True
		)
		channel.start_consuming()

	def get_data(self, ch, method, properties, body):
		print('[x] Received %r' % body.decode())


mq = RabbitMQ('127.0.0.1', 5672, 'guest', 'guest')
mq.get()
```

> RabbitMQ default olarak `5672`. portta çalışır.
>
> RabbitMQ'Nun WebUI kısmını aktif etmek istersek `rabbitmq-plugins enable rabbitmq_management` şeklinde komut çalıştırmamız gerekir, WebUI ise default olarak `15672`. portta çalışır. `http://127.0.0.1:15672/`.