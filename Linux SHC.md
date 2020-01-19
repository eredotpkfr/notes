## Linux SHC

`shc` yazılımı scriptleri gizlemeye/bulanıklaştırmaya obfuscate etmeye yarıyor `-f` parametresiyle scriptin yolunu vermeniz yeterli olacaktır.

```bash
sudo apt-get install shc # shc yükler.
shc -f script.sh # obfuscate eder.
shc -r -f script.sh # Relax security özelliğini aktif eder, farklı işletim sistemlerinde scriptin sağlıklı çalışmasını sağlar bu parametre verilmediği sürece script farklı bir işletim sisteminde hata verir ve çalışmaz.
```

`shc` yazılımı 2 farklı dosya çıkartır.

> script.sh.x - dosyası çalıştırılabilir obfuscate edilmiş script
>
> script.sh.c - c kodları.

Obfuscate ettikten sonra **"script.sh" , "script.sh.c"** dosyalarını silebiliriz. Daha sonra ise **"script.sh.x"** isimli dosyanın ismini **"script.sh"** olarak değiştirebiliriz yapılan bu değişiklikler olumsuz etki oluşturmaz.

