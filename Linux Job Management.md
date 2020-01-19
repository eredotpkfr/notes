## Linux Job Management

Arkaplanda çalıştırılan işlemleri `jobs` komutu ile listeleyebiliriz. İşlemi arkaplanda çalıştırmayı unuttuk ve işlem beklenenden uzun sürecek gibi bu durumda bulunduğumuz işlemdeyken `CTRL + Z` kombinasyonuna basarak. Aktif işlemi durdurabiliriz. İşlem durdurulduğunda arkaplanda işlem tekrar başlatılmayı bekler.

`bg` komutu ile durdurulmuş ve arkaplanda çalıştırılmayı bekleyen son processi arkaplanda çalıştırır. Yani durdurulmuş işlemin sonuna `&` ekleyerek işlemi arkaplanda çalıştırır. Normalde `bg` komutu bu `jobs` listesindeki en son işlemi arkaplanda çalışır hale getirecektir. Ancak listeden istediğimiz işlemi belirterek istediğimiz processin çalışmasını sağlayabiliriz (başlarındaki 1-2-3 ... gibi tam sayılarla).

`+` işareti ile belirtilen işlem son aktif işlemi temsil eder. Eğer `bg` komutuna işlem belirtmeden kullanırsak `+` işaretinin olduğu process çalışacaktır.

Herhangi bir işlemi arkaplanda çalıştırdık veya işlemi durdurarak arkaplana attık. Bu işlemleri tekrar önplanda getirerek çalıştırmak için `fg` komutu kullanılabilir.

> bg - background
>
> fg - foreground

Yine `bg` komutunda olduğu gibi jobs listesindeki `+` işareti hangi işlemin devreye sokulacağını belirtiyor.