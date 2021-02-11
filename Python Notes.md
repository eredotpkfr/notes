## Python Notes

### Python simple HTTP Server

`sudo python3 -m http.server  --bind 127.0.0.1 80` 80. porttan python ile bir http server başlatır, iç ağda 
kısa bir test yapılacaksa oldukça kullanışlı `sudo python3 -m http.server  --bind 0.0.0.0 8080`.

### Spawn interactive BASH Shell with Python

`python -c 'import pty; pty.spawn("/bin/bash")'` Python ile interaktif **BASH Shell** spawn eder.

### Python `lambda`

`lambda` tek satırda fonksiyon tanımı yapar.

> fonk = lambda message: print(message)

### Python import all modules in directory

In `__init__.py`

```python
from os.path import dirname, basename, isfile, join
import glob

modules = glob.glob(join(dirname(__file__), "*.py"))
__all__ = [ basename(f)[:-3] for f in modules if isfile(f) and not f.endswith('__init__.py')]
```

### Python import all classes in directory

In `__init__.py`

```python
import os, sys

path = os.path.dirname(os.path.abspath(__file__))

for py in [f[:-3] for f in os.listdir(path) if f.endswith('.py') and f != '__init__.py']:
    mod = __import__('.'.join([__name__, py]), fromlist=[py])
    classes = [getattr(mod, x) for x in dir(mod) if isinstance(getattr(mod, x), type)]
    for cls in classes:
        setattr(sys.modules[__name__], cls.__name__, cls)
```



### Python ignore errors during `decode()` like `UnicodeDecodeError:`

```python
#!/usr/bin/python3

import requests

url = 'https://www.google.com'
response_content = requests.get(url).content.decode('UTF-8')
print(response_content)
```

byte tipinde bir veriyi stringe çevirmek için `decode()` metodunu kullanırız, eğer içerisinde decode edilemeyecek karakter varsa `UnicodeDecodeError:` hatası alırız bu hataları *ignore (susturmak)* istersek `decode()` metodunda `errors ` parametresine `ignore` değerini vermemiz gerekir.

```python
#!/usr/bin/python3

import requests

url = 'https://www.google.com'
response_content = requests.get(url).content.decode('UTF-8', errors = 'ignore')
print(response_content)
```

### Python json

```python
#!/usr/bin/env python3

import json

dict_ = '{"a": 1, "b": 2}'

print(json.loads(dict_)) # Converts string to dict
print(type(json.loads(dict_))) # <class 'dict'>

dict = {'a': 1, 'b': 2}
print(json.dumps(dict_)) # Converts dict to string
print(type(json.dumps(dict_))) # <class 'str'>

x = {'a': 1, 'b': 2}
a = json.dumps(x, indent = 4)
print(a)
# separators = (". ", " = ") you can change default separators like this parameter
# sort_keys = True results sorted with this parameter

```



### Python RepeatedTimer with threading, your code is not blocked during running

```python
#!/usr/bin/python3

from threading import Timer
from time import sleep
import requests

global array
array = []

sendreq = lambda url: array.append(requests.get(url).status_code)

class RepeatedTimer(Timer): # RepeatedTimer class

    def _isSet(self): # Set finished value as False
        self.finished.clear()
        self.finished.isSet()

    def _run(self):
        self._isSet()
        self.start_repeated_timer()
        self.function(*self.args, **self.kwargs)

    def start_repeated_timer(self): # This method starts repeated timer
        if not self.is_running():
            self._timer = Timer(self.interval, self._run)
            self._timer.start()
            self.finished.set()

    def stop_repeated_timer(self): # This method stops repeated timer
        self._timer.cancel()
        self._isSet()

    def is_running(self):
        return self.finished.is_set()


if __name__ == '__main__':
	thread = RepeatedTimer(
		2,
		sendreq,
		args = ['https://www.google.com/']
	)

	thread.start_repeated_timer()

	for _ in range(1, 6):
		print(_, thread.is_running())
		sleep(1)

	thread.stop_repeated_timer()
    print(thread.is_running())

	[print(x) for x in array]
```

Yukarıdaki kod arkaplanda 2 saniyede bir google'a istek atar ve dönen status_code ları bir array'de toplar ve bunu yaparken kodunuz bloke olmaz `for _ in range(1, 6): print(_)`  satırında anlaşılıyor.

### Python threading with queue and working workers :)

```python
#!/usr/bin/python3

import logging
import threading
import time
from queue import Queue

def thread_function(name):
    logging.info("Thread %s: starting", name)
    time.sleep(2)
    logging.info("Thread %s: finishing", name)

if __name__ == "__main__":
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO,
                        datefmt="%H:%M:%S")

    threads = list()
    for index in range(3):
        logging.info("Main    : create and start thread %d.", index)
        x = threading.Thread(target=thread_function, args=(index,))
        threads.append(x)
        x.start()

    for index, thread in enumerate(threads):
        logging.info("Main    : before joining thread %d.", index)
        thread.join()
        logging.info("Main    : thread %d done", index)

# ------------------------- #

def worker(q):
	while True: # Worker looking for new data in queue
		get = q.get() # Oopss, getting some data!
		q.task_done()
		if get == None: break

		work(get)

	return True


def work(param):
	print(param)

queue = Queue() # Create FIFO(FirstInFirstOut) queue

for _ in range(1, 6): # Create 5 worker, workers are working on work :)
	threading.Thread(
		target = worker,
		args = [queue]
	).start()

for _ in range(1, 10):
	queue.put(_)

for _ in range(1, 6):
	queue.put(None)

queue.join()
```

### Python custom formatter for logging :)

```python
#!/usr/bin/python3

from logging import FileHandler, Logger, Formatter, ERROR, DEBUG, INFO

class MyFormatter(Formatter):
    
	FORMATS = {
		DEBUG :"DBG: %(message)s",
		ERROR : "ERROR: %(message)s",
		INFO : "INFO: %(message)s",
		'DEFAULT' : "%(levelname)s: %(message)s"
	}

	def format(self, record):
		log_fmt = self.FORMATS.get(record.levelno)
		formatter = Formatter(log_fmt)
		return formatter.format(record)

class SetupLogger(Logger):
    def __init__(self, name, file):
        super().__init__(name)
        self.set_handler(file)

    def set_handler(self, file):
        handler = FileHandler(file)
        handler.setFormatter(MyFormatter())

        self.addHandler(handler)


logger = SetupLogger(name = 'first_logger', file = 'first.log')
logger.setLevel(INFO)
logger.info('This is info message')
logger.setLevel(ERROR)
logger.error('This is error message!')
```

### Python simple `pymongo`

```python
#!/usr/bin/python3

import pymongo

client = pymongo.MongoClient('mongodb://localhost:27017/')
db = client['veri_tapanı']
collection = db['fucking_collection']
data = {'key': 'value'}
class_ = collection.insert_one(data)

print(client.list_database_names())
print(class_.inserted_id)

for obj in collection.find():
    print(obj)
```

### Python simple `mysql` connection

```python
#! /usr/bin/env python3
#-*- coding:UTF-8 -*-

import mysql.connector

db = mysql.connector.connect(
    host = 'localhost',
    user = 'deneme',
    passwd = 'deneme',
    database = 'deneme'
)

cursor = db.cursor()

while True:
    username = input('Name: ')
    if username.split():
        sql = 'SELECT * FROM table_1 WHERE user_name = %s'
        val = (username,)
        cursor.execute(sql,val)
        result = cursor.fetchall()
        if not result:
            sql = 'INSERT INTO table_1 (login,user_name) VALUES (NOW(),%s);'
            val = (username,)
            cursor.execute(sql,val)
            db.commit()
            while True:
                msg = input('> ')
                sql = 'INSERT INTO table_1 (user_name,message,message_date) VALUES (%s,%s,NOW());'
                val = (username,msg)
                cursor.execute(sql,val)
                db.commit()
            break
        else: print('UserName Is Already Taken {}'.format(username))
    else: pass
    
db.close()
```

### Python means under scores

```python
#!/usr/bin/python3

"""

	Single Leading Underscore: _var
	Single Trailing Underscore: var_
	Double Leading Underscore: __var
	Double Leading and Trailing Underscore: __var__
	Single Underscore: _

"""

class class_:

	def __init__(self):
		self._var = '_var'
		self.__var = '__var'

		return None

class Example(class_):
	def __init__(self):
		super(Example, self).__init__()
		print(self._var)
		print(self.__var)
		return None



object = class_()
print(object._class___var)
print(object._var)
print(dir(object))
# print(object.__var)
Example()


def external_func(): # Simple stupid function
    return False

def _internal_func():
    return 'F*ck off!' # Simple f*cking function

def one_under_score():
    for _ in range(0, 10):
        print(_)
```

Eğer herhangi bir `class` ın içerisindeki değişken `__` ile başlıyor ise (`self.__var`) python interpreter'ı bunu `private` değişken olarak atar ve `_[class_name]__[değişken]` şeklinde bir yapıya dönüştürür (`[obje]._[class_name]__[değişken]` şeklinde de erişilir), başka `class` lardan bu değişkene erişilemez. objenin `dir()` çıktısına baktığımızda `__var` şeklinde bir değişken göremeyiz bunun yerine `_class___var` şeklinde bir değişken vardır. Bu kullanım oldukça zor olduğu için python programcıları kendi aralarında `_var` şeklinde bir takma isim kullanırlar, bir programda `_` ile başlayan bir değişken gördüğünüzde anlamalısınızki bu bir `private` değişken ve yalnızca bu `class` içerisinde kullanılabilir. Aslında `private` variable değildir fakat `__`'nin kullanımı zor olduğu için bu şekilde bir kullanımda vardır. `_var` yaptığınızda aslında başka class'lardan bu değişkene erişebilirsiniz `print(self._var)` satırında görülüyor :)  `print(self.__var)` satırında hata alcağız aynı şekilde yorum satırındaki (`# print(object.__var)`) `#` 'i kaldırırsakta hata alırız.

```python
__var
_var
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_class___var', '_var']
_var
Traceback (most recent call last):
  File "under_scores.py", line 35, in <module>
    Example()
  File "under_scores.py", line 25, in __init__
    print(self.__var)
AttributeError: 'Example' object has no attribute '_Example__var'
```

Eğer herhangi bir fonksiyon `_` ile başlıyor ise (`_my_function()`) bu fonksiyon başka bir python dosyasına `*` ile import edilirken göz ardı edilir ve `import` edilmez. Aşağıda `*` ile fonksiyonları `import ` ettiğimizde `_internal_func()` fonksiyonunun `import` edilmediği görülüyor. Düz bir şekilde `import under_scores` dediğimizde ise bu fonksiyon'a `under_scores._internal_func()` şekilnde erişebiliyoruz.

```shell
Python 3.8.2 (default, Apr 27 2020, 15:53:34) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from under_scores import *
>>> external_func()
False
>>> _internal_func()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name '_internal_func' is not defined
>>> import under_scores
>>> under_scores.external_func()
False
>>> under_scores._internal_func()
'F*ck off!'
>>> 
```

`var_` şeklinde bir yapı şunun için kullanılır, bildiğimiz gibi python programlama diline ait keyword ler vardır ve siz bunlarla aynı isimde değişken atayamazsınız örnek olarak `for`, `in`, `with`, `as`, `not`, `False`, `True` vb.

Eğer bu kelimeleri değişkeninizde kullanmak istiyorsanız kelimenin sonuna `_` koyarak bunu bypass edebilirsiniz ve o kelimeyi değişken olarak kullanabilirsiniz. `class class_:` satırında anlaşılıyorki `class_` isminde bir class oluşturmuşuz ve program hata vermiyor. Her türlü kullanabilirsiniz `for_`, `in_`, `with_`, `not_`, `as_` vb.

Düz bir şekilde `_` görürseniz bu şu anlama gelir, bu değişken benim umurumda değildir, gereksizdir kafama göre değişken oluşturdum şeklinde kullanılır :).

```python
for _ in range(0, 10):
    print(_)
```

Satırında örnek kullanım vardır.

Ayrıca `_` nin python interpreter'ında farklı bir anlamı daha vardır buda son verinizi kendi içerisinde tutar örn;

```python
Python 3.8.2 (default, Apr 27 2020, 15:53:34) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 20 + 20
40
>>> _
40
>>> 30 + 30
60
>>> _
60
>>> 
```

Eğer bir değişkenin sağında ve solunda `__` görürseniz bu şu anlama gelir, initialized değişken yada fonksiyondur, genelde fonksiyonlarda kullanılır. Yukarıdaki örnekte `def __init__(self):` fonksiyonu python tarafından ön tanımlı olarak gelen bir yapıcı (`constructor`) fonksiyondur.

### Python Decorators

```python
#!/usr/bin/env python3

def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

def say_whee():
    print("Whee!")

say_whee = my_decorator(say_whee)

## ------------------------------------------------------- ##

def mydec(func):
    def wrapper(x, y):
        return x+1+y+1

    return wrapper

@mydec
def topla(a, b):
    return a + b

print(topla(1,1))
```

### Python Critical Section and Race Condition

Aşağıdaki örnek bir python kodunu incelediğimizde 2 farklı fonksiyonumuz var, bu fonksiyonlardan `producer` olan `self.__var` değerini 10 arttırıyor, `consumer` ise 10 azaltıyor. Buradaki 2 fonksiyonda tek bir değişken üzerinde değişiklik yapıyor ve biz bu 2 fonksiyonu arkada bir `thread` olarak başlattığımızda bu 2 fonksiyon hiçbir şeye bakmadan sadece kendilerine verilen görevi yapacaktır ve `race condition`'a girecekler `consumer` fonksiyonu `producer`'ın bittiğini beklemeden direkt değeri azaltmaya çalışacak bunu aynı şekilde `producer`da yapacak ve çıktımız çok saça bir hal alacak şöyleki; `50, 49, 48, 51, 52, 53, 54, 47...` fakat biz önce `producer`'ın çalışmasını ve 50 olan değerin 60 olmasını daha sonra ardından `consumer`  çalışmasını istiyoruz ve çıktımız tekrardan 50 ile bitmeli. İşte 2 fonksiyonda aynı değişken üzerinde işlem yaptığı için 2 fonksiyonda `Critical Section` (Kritik Bölge)'ye giriyor, bizim burada kodda dememiz lazımki `producer` sen `Critical Section`'a girdin ve `producer` `Critical Section`'dayken başka kimse `self.__var` değişkeninin değerini değiştirmesin şeklinde belirtmemiz gerekiyor bunu ise `threading.Lock()` class'ı ile yapıyoruz.

```python
#!/usr/bin/python3

from threading import Thread, Lock

class CriticalSection:

	def __init__(self):
		self.__var = 50
		self.__lock = Lock()

		return None

	def producer(self):
		self.__lock.acquire()
		for _ in range(0, 10):
			self.__var += 1
			print(self.__var)
		self.__lock.release()


#		Lock with "with" statement :)

#		with self.__lock:
#			for _ in range(0, 10):
#				self.__var += 1
#				print(self.__var)

	def consumer(self):
		self.__lock.acquire()
		for _ in range(0, 10):
			self.__var -= 1
			print(self.__var)
		self.__lock.release()

#		Lock with "with" statement :)

#		with self.__lock:
#			for _ in range(0, 10):
#				self.__var -= 1
#				print(self.__var)


section = CriticalSection()

Thread(target = section.producer).start()
Thread(target = section.consumer).start()
```

### Python run function every day in specific time

```python
#!/usr/bin/python3

from datetime import datetime
from threading import Timer

class class_:

	def __init__(self):
		pass

	def start(self):
		sec = self.calculate_seconds()
		print('Function starts after {} seconds'.format(sec))

		thread_timer = Timer(
			sec,
			self.work,
			args = ['Hello!']
		)

		thread_timer.setDaemon(False)
		thread_timer.start()
		thread_timer.join()

		self.start()

		"""
					OUTPUT

		Function starts after 2 seconds
		Hello!
		Function starts after 2 seconds
		Hello!
		Function starts after 2 seconds

		"""

		return None


	def work(self, msg):
		print(msg)

	def calculate_seconds(self):
		today = datetime.today()

		tomorrow = today.replace(
			day = today.day, # today.day + 1 // set day tomorrow
			hour = today.hour, # set hour // 12
			minute = today.minute, # set minute // 0
			second = today.second + 2, # set second // 0
			microsecond = 0 # set microseconds // 0

			# This configrations works run `work` method
			# every day at 12:00 clock
		)

		return (tomorrow - today).seconds + 1

if __name__ == '__main__':
	object = class_()
	object.start()
```

Yukarıdaki kod, bügünün tarhini ve saatini esas alarak yeni bir tarih ve saat belirler ve bunu saniye cinsinden belirtir daha sonra `Timer()` objesine saniyeyi vererek o tarihte ve saatte fonksiyonun çalışması sağlanır. `.calculate_seconds()` metodunda gerekli hesaplamalar yapılmış istenilirse her gün saat `14:00` da belirli işlemler yaptırılabilir.

### Python daily logger with custom formatter

```python
#!/usr/bin/python3

from logging import handlers
from logging import Logger, Formatter, ERROR, DEBUG, INFO, WARNING
from time import sleep
from os import path, getcwd, makedirs
import datetime

class MyFormatter(Formatter):

	FORMATS = {
		DEBUG : '%(asctime)s | %(levelname)s, %(status_code)s, %(url)s, %(message)s',
		ERROR : '%(asctime)s | %(levelname)s, %(url)s, %(error)s, %(message)s',
		INFO : '%(asctime)s | %(levelname)s, %(message)s',
#		WARNING: '%(asctime)s | %(levelname)s, %(status_code)s, %(url)s, %(error)s, %(message)s',
        'DEFAULT' : "%(levelname)s: %(message)s"
	}

	def format(self, record):
		log_fmt = self.FORMATS.get(record.levelno)
		formatter = Formatter(log_fmt)

		return formatter.format(record)


class SetupLogger(Logger):

	def __init__(self, name, directory, prefix='log'):
		super().__init__(name)

		if not path.exists(path.join(getcwd(), directory)):
			makedirs(directory)

		self.set_handler(directory, prefix)

	def set_handler(self, direc_, pref):
		handler = DailyRotatingFileHandler(direc_, pfx=pref)
		handler.setFormatter(MyFormatter())

		self.addHandler(handler)

class DailyRotatingFileHandler(handlers.RotatingFileHandler):

	def __init__(self, basedir, mode='a', maxBytes=0, backupCount=0, encoding=None, delay=0, pfx='log'):
		self.prefix = pfx
		self.basedir_ = basedir
		self.baseFilename = self.get_base_filename()

		handlers.RotatingFileHandler.__init__(self, self.baseFilename, mode, maxBytes, backupCount, encoding, delay)

	def get_base_filename(self):
		self.today_ = datetime.date.today()
		basename_ = self.prefix + '.' + self.today_.strftime('%Y-%m-%d') + '.log' # File names

		return path.join(self.basedir_, basename_)

	def shouldRollover(self, record):
		if self.stream is None:
			self.stream = self._open()

		if self.maxBytes > 0 :
			msg = "%s\n" % self.format(record)
			self.stream.seek(0, 2)
			if self.stream.tell() + len(msg) >= self.maxBytes:
				return 1

		if self.today_ != datetime.date.today(): # Check date
			self.baseFilename = self.get_base_filename()
			return 1

		return 0
    
logger = SetupLogger('name', './logs/')
```

### Python auto register class when it's defined and dynamic class creation with metaclass

```python
REGISTRY = {}

def register_class(target_class):
    REGISTRY[target_class.__name__] = target_class


class MetaRegistry(type):

    def __new__(meta, name, bases, class_dict):
        cls = type.__new__(meta, name, bases, class_dict)
        if name not in REGISTRY:
            register_class(cls)
        return cls


class BaseClass(metaclass=MetaRegistry):
    pass
```

### Python get return value from any thread with queue module

```python
from queue import Queue
from threading import Thread

def foo(bar):
    print('hello {0}'.format(bar))
    return 'foo'

que = Queue()

t = Thread(target=lambda q, arg1: q.put(foo(arg1)), args=(que, 'world!'))
t.start()
t.join()
result = que.get()
print(result)
```

### Python call function every spesific time and re-run thread with Event objects

```python
import threading
from time import sleep

class Worker(threading.Thread):
	def __init__(self, interval, *args, **kwargs):
		super().__init__(*args, **kwargs)
		self.keepgoing = threading.Event()
		self.keepgoing.clear()
		self.startprocess = threading.Event()
		self.startprocess.set()
		self._interval = interval

	def run(self):
        self.keepgoing.set()
		while self.keepgoing:
			while self.startprocess.isSet():
				self._target(*self._args, **self._kwargs)
				sleep(self._interval)
			self.startprocess.wait()

	def restart(self):
		self.startprocess.clear()
		self.startprocess.set()

	def stop(self):
		self.startprocess.clear()

	def kill(self):
		self.stop()
		self.keepgoing.clear()


def printf(str):
	print(str)

w = Worker(2, target=printf, args=['<test>'], daemon=True)
print('Started')
w.start()

sleep(5)
w.stop()
print('Stopped')

sleep(10)
w.restart()
print('re-run')

sleep(2)
w.kill()
```

### Python repeated timer without `Timer` object, if `Timer` object not used, you can stop thread any time you want

```python
from threading import Thread, Event
from time import sleep

class RepeatedFunction(Thread):
	def __init__(self, interval, *args, **kwargs):
		super().__init__(*args, **kwargs)
		self.stopped = Event()
		self._interval = interval

	def run(self):
		while not self.stopped.wait(self._interval):
			self._target(*self._args, **self._kwargs)

	def stop(self):
		self.stopped.set()

def printf(str_=False):
	print(str(str_))

t = RepeatedFunction(
	1,
	target=printf,
	kwargs={'str_': True},
	daemon=True
)

t.start()
sleep(2)
t.stop()
```

### Python contex manager

```python
#!/usr/bin/python3

class ContextManager():
	def __init__(self):
		print('init method called')

	def __enter__(self):
		print('enter method called')
		return self

	def __exit__(self, exc_type, exc_value, exc_traceback):
		print('exit method called')

with ContextManager() as manager:
	print('with statement block')
    
# OUTPUT
# init method called
# enter method called
# with statement block
# exit method called
```

### Pymongo simple bulk write 

```python
#!/usr/bin/python3

from pymongo import (MongoClient, InsertOne, UpdateOne)
from datetime import datetime

class MongoDB(MongoClient):

	# DEFAULTS
	HOST = '127.0.0.1'
	PORT = 27017
	DB_NAME = 'test_db'

	def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.db = self[self.DB_NAME]
	
    def insert_collection(self, collection_name, datas):
        self.collection = self.db[collection_name]
        operations = []
        
        for data in datas:
            exists = self.exists(data)
            if not exists:
                now = datetime.now()
                data.update({'first_seen': now, 'last_seen': now, 'count': 1})
                operations.append(InsertOne(data))
         	else:
                operations.append(UpdateOne(
                    {'_id': exists.get('_id')},
                    {'$set': {
                        'last_seen': datetime.now(), 'count': exists.get('count')+1
                    }}
                ))

    	self.collection.bulk_write(operations)
   	
    def exists(self, which_data):
        if self.collection.count_documents(which_data) == 0:
            return False
        return self.collection.find_one(which_data)
```

### Python simple SingleTon structure

```python
#!/usr/bin/python3

class SingleTon(object):

	# __instance__ = None

	def __new__(cls, *args, **kwargs):
		# if cls.__instance__ is None:
		#	cls.__instance__ = super().__new__(cls, *args, **kwargs)
		# return cls.__instance

		if not hasattr(cls, 'instance'):
			cls.instance = super().__new__(cls, *args, **kwargs)

		return cls.instance


	def __init__(self):
		if not hasattr(self, 'x'):
			self.x = 5

	def __call__(self):
		return self.x


a = SingleTon()
print(a.x)
a.x += 5

b = SingleTon()
print(b.x)

print(a, b, sep='\n')
print(a is b)
print(a(), b())
```

### Python changing logging's `basicConfig(` which is already set

```python
# Example to remove all root logger handlers and reconfigure. (UNTESTED)
import logging

# Remove all handlers associated with the root logger object.
for handler in logging.root.handlers[:]:
    logging.root.removeHandler(handler)

# Reconfigure logging again, this time with a file.
logging.basicConfig(filename = 'myfile.log', level=logging.DEBUG, format='%(filename)s:%(lineno)s %(levelname)s:%(message)s')
```
