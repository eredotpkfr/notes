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

`RabbitMQ` default olarak `5672`. portta çalışır. `RabbitMQ`'nun `WebUI` kısmını aktif etmek istersek `rabbitmq-plugins enable rabbitmq_management` şeklinde komut çalıştırmamız gerekir, `WebUI` ise default olarak `15672`. portta çalışır. `http://127.0.0.1:15672/` - `guest:guest` credentials ile giriş yapabilirsiniz.

Python ile `RabbitMQ` servisine bağlanmak için `pika` isminde bir modül kullanmamız gerekiyor, modülü yüklemek için `pip3 install pika --upgrade` şeklinde bir komut çalıştırmak yeterli.

#### rabbitmq_producer.py

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
		self.conn = self.connection()

	def connection(self):
		return pika.BlockingConnection(pika.ConnectionParameters(
			host = self.host,
			port = self.port, credentials = pika.PlainCredentials(
				username = self.username,
				password = self.password
				)
			)
		)

	def close(self):
		self.conn.close()

	def send(self, data):
		channel = self.conn.channel()
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

mq.close()
```



#### rabbitmq_consumer.py

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
		self.conn = self.connection()

	def connection(self):
		return pika.BlockingConnection(pika.ConnectionParameters(
			host = self.host,
			port = self.port, credentials = pika.PlainCredentials(
				username = self.username,
				password = self.password
				)
			)
		)
	
	def close(self):
		self.conn.close()

	def get(self):
		channel = self.conn.channel()
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
mq.close()
```

### Birden fazla worker çalıştırmak

Eğer `RabbitMQ` servisini yeniden başlatırsanız kuyruğunuzdaki veriler kaybolur. Kaybolmamasını istiyorsanız kuyruğunuza `durable` özelliği eklemelisiniz, `durable` özelliği eklediğinizde `producer` ve `consumer` kodlarınızda değişecektir. Aynı zamanda veriyi `publish` ederken `delivery_mode` değerini `2` olarak atayıp mesajın kalıcı olmasını sağlamanız gerekir. Bunu yaptığınızda verinizi `RabbitMQ` diske yazar yapmazsanız `RAM`'e kayıt eder.

> #### Not
>
> `RabbitMQ` ya kuyruğu `durable` yapmasını söylemeniz hiç veri kaybolmayacağı anlamına gelmez verileriniz diske yazılma sırasında da kayıp olabilir. Tamamen güvenli olmasını istiyorsanız `Publisher Confirms`  konusuna bakınız.

Bazen worker ların yaptığı işler uzayabiliryor ve işin bitmesini beklemek gerekebiliyor fakat `RabbitMQ` bundan hiç anlamaz `RabbitMQ` kuyruğa düşen mesajları workerlara anında iletir ve workerlar gereken işi yapamayabilir. Bunun olmaması için `RabbitMQ` ya bunu söylemeniz gerekiyor bunu yapmak için ise `basic.qos` protokolünü kullanmasını söylemelisiniz aşağıdaki örnekte `channel.basic_qos(prefetch_count = 1)` satırında bu işlem yapılmış, burada `prefetch_count = 1` denilerek 1 worker'a 1 den fazla mesaj gitmesin  yaani bir diğer mesajın gitmesi için öncekinin işinin bitmesini bekle şeklinde uyarılmıştır.

Bütün bunları yaptıktan sonra `RabbitMQ` nun mesajların doğru iletilip iletilmediğini anlaması için basic `Acknowledgment` ekleyebiliriz worker kısmında `ch.basic_ack(delivery_tag = method.delivery_tag)` satırında bu işlem yapılmış. En son aşamada ise kodlarımız aşağıdaki gibi olacaktır.

#### task.py

```python
#!/usr/bin/env python

import pika
import sys

connection = pika.BlockingConnection(
	pika.ConnectionParameters(
        	host = 'localhost',
        	heartbeat = 0
	)
)

channel = connection.channel()

channel.queue_declare(
	queue = 'task_queue',
	durable=True
)

message = ' '.join(sys.argv[1:]) or "Hello World!"

channel.basic_publish(
    exchange = '',
    routing_key = 'task_queue',
    body = message,
    properties = pika.BasicProperties(
        delivery_mode = 2,  # make message persistent
    ))

print(" [x] Sent %r" % message)

connection.close()
```

#### worker.py

```python
#!/usr/bin/env python

import pika
import time

connection = pika.BlockingConnection(
	pika.ConnectionParameters(
        	host = 'localhost',
        	heartbeat = 0
	)
)

channel = connection.channel()

channel.queue_declare(
	queue = 'task_queue',
	durable = True
)

print(' [*] Waiting for messages. To exit press CTRL+C')


def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)
    time.sleep(body.count(b'.'))
    print(" [x] Done")
    ch.basic_ack(delivery_tag = method.delivery_tag)


channel.basic_qos(prefetch_count = 1)
channel.basic_consume(queue = 'task_queue', on_message_callback = callback)

channel.start_consuming()
connection.close()
```

> #### NOT
>
> RabbitMQ'da producer ve consumer birbirleri ile haberleşirler ve belirli aralıklarla consumer producer'a `heartbeat (kalp atışı)` yollar. Eğer bu aralığı geçecek sürede bir işlem yapılıyor ise RabbitMQ worker'ınızı öldürür ve bağlantıyı sonlandırır. Bu aşamada `heartbeat` değerini arttırmak bağlantının sonlanmaması için iyi bir çözümdür. Python da `heartbeat = 0` şeklinde connection'a değer verebiliyoruz. Default olarak `60` değerini alır, eğer sizin worker'ınız `60` saniyeyi aşacak bir işlem yapıyor ise muhtemelen RabbitMQ bağlantıyı sonlandırıp `timeout` atacaktır, `heartbeat = 1800` gibi yüksek değerler vermek bu problemin önüne geçecektir. Tabi eğer isterseniz benim yukarıda yaptığım gibi `0` değerini verebilirsiniz, `0` değerini vermek `heartbeat`i devre dışı bırakır ve TCP bağlantısı sürekli açık kalır.

### RabbitMQ da öncelik tanımlama 

#### producer.py

```python
from json import dumps
import pika

class RabbitMQ: # RabbitMQ class_
	### Initialized function for RabbitMQ class_ ###

	def __init__(self, host = '127.0.0.1', port = 5672, username = 'guest', password = 'guest'):
		self.host = host
		self.port = port
		self.username = username
		self.password = password
		self.conn = self.connection()

		return None

	def close(self):
		self.conn.close()

	def connection(self): # Returns RabbitMQ connection
		return pika.BlockingConnection(
            pika.ConnectionParameters(
				host = self.host,
				port = self.port,
				heartbeat = 0, # Set disable Heartbeats for TCP connection's keepalive
				credentials = pika.PlainCredentials(
					username = self.username,
					password = self.password
				)
			)
        )

""" Send data with one connection

    def my_queue_declare(self, send_queue):
        self.send_channel = self.conn.channel()
        self.send_channel.queue_declare(
            queue = send_queue,
            durable = True,
            arguments = {'x-max-priority': 10}
        )
        
        return True

	def send(self, message, priority):      
        self.send_channel.basic_publish(
            exchange = '',
            routing_key = send_queue,
            body = message,
            properties = pika.BasicProperties(
                priority = priority,
                delivery_mode = 2
            )
        )
        
        return True
"""

	def send(self, send_queue, message, priority): # Basic producer method
		send_channel = self.connection().channel()
		send_channel.queue_declare(
			queue = send_queue,
			durable = True,
			arguments = {'x-max-priority': 10}
		)
		send_channel.basic_publish(
			exchange = '',
			routing_key = send_queue,
			body = message,
			properties = pika.BasicProperties(
				priority = priority,
				delivery_mode = 2
			)
		)

		return True


mq = RabbitMQ()
mq.send('kuyruk', 'naber', 9)
mq.close()
```

RabbitMQ da herhangi bir kuyruğa öncelik `priority` eklemek istersek bunu kuyruk tanımlama aşamasında yapmamız gerekiyor (`.queue_declare()`). Öncelik tanımlamak için `0-255` arasında `int` tam sayılar kullanılır fakat resmi dökümanda `0-10` arasında verilmesi öneriliyor bizde yukarıdaki kodda `kuyruk` tanımlaması yapar iken `arguments = {'x-max-priority': 10}` satırında maksimum önceliğin `10` değerine sahip olmasını istedik. Kuyruğa veri `publish` ederken ise bu önceliği belirtmemiz gerekiyor çünkü bizim kuyruğumuz artık bir öncelik `priority` özelliği alıyor. `send()` metodu içerisinde `.basic_publish()` metodu kullanılır iken `properties` (özellikler) kısmında `priority = priority` şeklinde belirtilmiştir. Producer'da yaptığımız bu değişiklikler consumer tarafında da değişime yol açacaktır, önceliğe `priority` sahip bir kuyruğun consumer kodları aşağıdaki gibi olacaktır.

#### consumer.py

```python
from json import dumps
import pika

class RabbitMQ: # RabbitMQ class_

	### Initialized function for RabbitMQ class_ ###

	def __init__(self, host = '127.0.0.1', port = 5672, username = 'guest', password = 'guest'):
		self.host = host
		self.port = port
		self.username = username
		self.password = password
		self.conn = self.connection()

		return None

	def close(self):
		self.conn.close()

	def connection(self): # Returns RabbitMQ connection
		return pika.BlockingConnection(
            pika.ConnectionParameters(
				host = self.host,
				port = self.port,
				heartbeat = 0, # Set disable Heartbeats for TCP connection's keepalive
				credentials = pika.PlainCredentials(
					username = self.username,
					password = self.password
				)
			)
        )

	def recv(self, recv_queue, func):
		recv_channel = self.conn.channel()
		recv_channel.queue_declare(
			queue = recv_queue,
			durable = True,
			arguments = {'x-max-priority': 10}
		)

		recv_channel.basic_qos(prefetch_count = 1)

		recv_channel.basic_consume(
			queue = recv_queue,
			on_message_callback = func
		)

		recv_channel.start_consuming()

		return True

	def work(self, ch, method, properties, body):
		print(body.decode())
		print(properties.priority)
		ch.basic_ack(delivery_tag = method.delivery_tag)
		return True

mq = RabbitMQ()
mq.recv('kuyruk', mq.work)
mq.close()
```

Burada yapılan işte, alınan veri ekrana basılmış hemen ardından `properties.priority` şeklinde öncelik değeri ekrana basılmış.
