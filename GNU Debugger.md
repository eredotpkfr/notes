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

### C programlama dilinde veri boyutları

> 1 Karakter 1 byte dir ve bildiğimiz gibi 1 byte 8 bittir, yaani 1 byte 2^8 = 256 farklı değer alabilir demektir. ASCII tablosunda 1 byte'ın yalnızca 7 biti kullanılır buda 2^7 = 128 farklı sayı demektir ve ASCII tablosunda da 128 karakter vardır.

|      Type      | Storage Size |                     Value Range                      |
| :------------: | :----------: | :--------------------------------------------------: |
|      char      |    1 byte    |               -128 to 127 or 0 to 255                |
| unsigned char  |    1 byte    |                       0 to 255                       |
|  signed char   |    1 byte    |                     -128 to 127                      |
|      int       | 2 or 4 bytes | -32,768 to 32,767 or -2,147,483,648 to 2,147,483,647 |
|  unsigned int  | 2 or 4 bytes |          0 to 65,535 or 0 to 4,294,967,295           |
|     short      |   2 bytes    |                  -32,768 to 32,767                   |
| unsigned short |   2 bytes    |                     0 to 65,535                      |
|      long      |   8 bytes    |     -9223372036854775808 to 9223372036854775807      |
| unsigned long  |   8 bytes    |              0 to 18446744073709551615               |

İnanmıyorsanız deneyin :)

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <float.h>

int main(int argc, char** argv) {

    printf("CHAR_BIT    :   %d\n", CHAR_BIT);
    printf("CHAR_MAX    :   %d\n", CHAR_MAX);
    printf("CHAR_MIN    :   %d\n", CHAR_MIN);
    printf("INT_MAX     :   %d\n", INT_MAX);
    printf("INT_MIN     :   %d\n", INT_MIN);
    printf("LONG_MAX    :   %ld\n", (long) LONG_MAX);
    printf("LONG_MIN    :   %ld\n", (long) LONG_MIN);
    printf("SCHAR_MAX   :   %d\n", SCHAR_MAX);
    printf("SCHAR_MIN   :   %d\n", SCHAR_MIN);
    printf("SHRT_MAX    :   %d\n", SHRT_MAX);
    printf("SHRT_MIN    :   %d\n", SHRT_MIN);
    printf("UCHAR_MAX   :   %d\n", UCHAR_MAX);
    printf("UINT_MAX    :   %u\n", (unsigned int) UINT_MAX);
    printf("ULONG_MAX   :   %lu\n", (unsigned long) ULONG_MAX);
    printf("USHRT_MAX   :   %d\n", (unsigned short) USHRT_MAX);

    return 0;
}
```



Bir integer 4 bytlık yer kaplar, şunu unutmamak lazım bir adres en fazla 1 byte'lık veri tutabilir. Eğer biz 1 sasıyısını RAM de tutacaksak 00000001 şeklinde bir adreste tutabiliriz fakat veri int olarak atandığı için 4 bytlık yer kaplayacaktır. Yaani RAM de 00000000 00000000 00000000 0000001 şeklinde depolanacaktır ve 4 adresi işgal edecektir.

Burada adreste RAM de veriyi tutarken, endianness kavramı devreye giriyor, tutulacak değerin önemli biti en baştada olabilir en sondada.

### Memory Segmentation

#### Text (Code Segment, Fixed Size)

Program çalıştırıldığında oluşturulur, programın assembly kodlarının tutulduğu bölgedir, boyutu runtime sırasında değiştirilemez. Program çalıştığında `rip` bu segmentin en başdaki ilk komutu, instruction'u gösterir, daha sonra bu instruction'un boyutu alınır ve eklenir ve bir sonraki instruction bulunur en son aşamada ise `rip` in gösterdiği instruction çalıştırılır.

#### Data & Bss (Fixed Size)

data ve bss setmentlerinde, program sırasında oluşturulan global ve static değişkenler tutulur. Eğer değişken initialize olarak atanmış ise data da, eğer initialize atanmamış ise bss segmentinde tutulur. data & bss segmentlerinin boyutu runtime sırasında değiştirilemez.

#### Heap (Unfixed Size)

Heap segmenti programcının direkt erişip işlem yapabildiği alandır. Programcı `malloc`, `calloc`, `free` gibi fonksiyonlarla heap alanında işlemler yapabilir. Heap alanında sıralı olarak yer ayırtılır.

#### Stack (Unfixed Size)

Stack veri yapısı **FILO (First In Last Out)** şeklindedir, ilk giren en son çıkar. Heap alanındakinden farklı olarak adresler sıralı değildir.

> Kodu disassemble ettiğimizde `call` komutunu görürüz, `call` komutu fonksiyon çağırmaya yarar. Fonksiyonlar `stack` veri yapısı ile çalışır. Bir C kodu çalışırken baştan aşağıya doğru çalışır ve her komutun adresi ardı ardına sıralanmıştır fakat `printf()` gibi bir fonksiyon çağrılmışsa `printf()` fonksiyonunun başladığı bir adres vardır ve ordan itibaren komutları çalıştırmaya devam eder. `printf()` fonksiyonu sona erdiğinde ise` main()` fonksiyonunda kaldığı yerden program çalışmaya devam eder, peki bu nasıl oluyo? Stack veri yapısı ile.
>
> ESP: Stack Pointer, `push` ekleme yapar `pop` ise çıkarma yapar bu nedenle **stacks are not fixes**. Stack pointer her zaman stack'e son ekleneni gösterir.
>
> EBP: Local Base Pointer, burada stack frame vardır ve 2 tane pointer vardır bu pointerlardan biri SFP Saved Frame Pointer, SFP stack pointer'ın gösterdiğinin bir öncekini tutar. Diğer pointer ise return address tir buda main fonksiyonundaki instruction'a geri döneceği adresi tutar.



