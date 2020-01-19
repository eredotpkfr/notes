## Linux MAChanger

`sudo apt-get install macchanger` macchanger yükler

`macchanger --help` help sayfası

```bash
ifconfig wlan0 down # wlan0 adaptörünü devre dışı bırakır, bağlantısını keser bi nevi.
macchanger --random wlan0 # wlan0 arayüzündeki mac adresini rastgele olarak manipüle eder.
ifconfig wlan0 up # wlan0 arayüzünü tekrar aktif eder, ayağa kaldırır :).
```

 