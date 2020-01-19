## GNU Debugger

`objdump -M intel -d a.out` 
> `-d` object dump ile disassemble eder, tamamını disassemble etmek için `-D` ,
> `-M` ile intel işlemci mimarisine göre çıktı almak istediğimizi belirtiyoruz. Bu şekilde daha okunabilir.

GNU Debugger da bunu yapmak için ise gdb yi açıyoruz ve aşağıdaki komutu giriyoruz çalışmaz ise alttakileri.
```shell
(gdb) set dis intel
(gdb) set disassembly intel
(gdb) set disassembly-flavor intel
```

<u>Programdan çıktığımızda ve tekrar girdiğimizde her seferinde bu işlemi yapmak zorundayız</u>, bunu istemiyorsak.

```shell
echo "set dis intel" > ~/.gdbinit
echo "set disassembly intel" > ~/.gdbinit
echo "set disassembly-flavor intel" > ~/gdbinit
```

gcc/g++ derleyicisinde bir  C programını derlerken genelde yazılımcılar kaynak kodu okunamaz olsun isterler, eğer derlediğimizde halen kaynak kodunun okunmasını istiyorsak `-g` parametresini vermemiz gerekiyor.

`g++ -g -o deneme deneme.c` bu şekilde derlersek gdb programın kaynak kodunu okuyabilir. `gdb -q deneme` diyerek programı gdb de açıyoruz.

`(gdb) list` programın kaynak kodunu ekrana basar.

`(gdb) disasseble main` dediğimizde main fonksiyonunu disassemble eder.

`(gdb) break main` **main** fonksiyonuna bir **breakpoint** koyuyoruz, program main fonksiyonuna gelesiye kadar çalışır, **main** fonksiyonuna geldiğinde durur. Komut bize bir adres döndürür buradan anlıyoruz ki **main** fonksiyonu çalıştıktan sonra çalışan komut o adreste.

`(gdb) run` dediğimizde programı çalıştırır break point varsa durdurur.

`(gdb) info register rip` rip (register instruction pointer) için bilgi verir, hangi adresi işaret ediyor. `(gdb) i r rip ` şeklinde de çalışır.



> o/ octal notation (8'lik taban, gösterim)
>
> x/ hexadecimal (16'lık taban, 0-9 A-F)
>
> u/ unsigned notation 10 base (10'luk taban)
>
> t/ binary notation (2'lik taban)



`(gdb) x 0x555555555149` / `(gdb) x [adres]` verirsek bize o adreste bulunan instruction'u gösterir.

`(gdb) x/o $rip` register instruction pointer'ın içinde bulunan adresi getirir.

`(gdb) x/u $rip`  register instruction pointer'ın içindeki adresi 10'luk tabana çeviripte gösterir.



> /b single byte (1 byte)
>
> /h halfword (2 byte - WORD olarak bilinir)
>
> /w word (4 byte - DWORD denir (double word) - 4 byte'ı ifade eder)
>
> /g giant (8 byte)

```shell
(gdb) x/2x $rip # register instruction pointer'ın adresi ile birlikte 2 adresi daha hexadecimal olarak göster.
(gdb) x/xb $rip # register instruction pointer'ın adresinden başla 1 byte göster hex olarak.
(gdb) x/xh $rip # register instruction pointer'ın adresinden başla 2 byte göster hex olarak.
(gdb) x/xw $rip # register instruction pointer'ın adresinden başla 4 byte göster hex olarak.
(gdb) x/tw $rip # register instruction pointer'ın adresinden başla 4 byte göster binary olarak.
(gdb) x/uw $rip # register instruction pointer'ın adresinden başla 4 byte göster decimal olarak.
(gdb) x/ow $rip # register instruction pointer'ın adresinden başla 4 byte göster octal olarak.
```



<u>Platformdan platforma registerların isimleri değişebilir,</u> registerları görmek için `(gdb) info registers`

`(gdb) info register eip/rip`  register'ın ismi eip olabilir, ripte olabilir.

`(gdb) nexti` next instruction, bir sonraki instruction'u yapar, yaani kodu bir adım daha ilerletir ve çalıştırır.

`(gdb) x/s 0x80484e0`  **0x80484e0** adresinden başlayarak string okur, adresteki stringi getirir.

`(gdb) x/20i 0x80484e0` Adresindeki 20 instruction'u göster.