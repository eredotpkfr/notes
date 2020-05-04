## Python Notes

### Python simple HTTP Server

`sudo python3 -m http.server  --bind 127.0.0.1 80` 80. porttan python ile bir http server başlatır, iç ağda 
kısa bir test yapılacaksa oldukça kullanışlı `sudo python3 -m http.server  --bind 0.0.0.0 8080`.

### Spawn interactive BASH Shell with Python

`python -c 'import pty; pty.spawn("/bin/bash")'` Python ile interaktif **BASH Shell** spawn eder.

### Python `lambda`

`lambda` tek satırda fonksiyon tanımı yapar.

> fonk = lambda message: print(message)

### Python ignore errors during `decode()` like `UnicodeDecodeError:`

```python
import requests
url = 'https://www.google.com'
response_content = requests.get(url).content.decode('UTF-8')
print(response_content)
```

byte tipinde bir veriyi stringe çevirmek için `decode()` metodunu kullanırız, eğer içerisinde decode edilemeyecek karakter varsa `UnicodeDecodeError:` hatası alırız bu hataları *ignore (susturmak)* istersek `decode()` metodunda `errors ` parametresine `ignore` değerini vermemiz gerekir.

```python
import requests
url = 'https://www.google.com'
response_content = requests.get(url).content.decode('UTF-8', errors = 'ignore')
print(response_content)
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

