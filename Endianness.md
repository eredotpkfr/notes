## Endianness

İnsanların soldan sağa veya sağdan sola alfabelere sahip olmaları gibi işlemciler de baytları saklarken önemli baytın solda veya sağda olmasına göre sınıflandırılır. Buna sıralılık veya bitimlilik (endianness) da denir. Diğer bir deyişle endianness bayt sıralamasıdır. Kıymetli baytın solda olduğu sıralamaya **big-endian** (düz sıralı), en sağda olduğu sıralamaya da **little-endian** (ters sıralı) denir. Bu kavramları tam olarak anlamak için öncelikle hızlı bir şekilde hafızayı inceleyelim.

Hafızayı en basit ve kaba şekliyle baytların saklandığı uzun bir dizin şeklinde ifade edebiliriz. Normalde dizindeki herhangi bir yeri ifade etmek için **“indis”** terimi kullanılırken, bilgisayar bilimcileri hafızadan bahsederken **“adres”** terimini daha çok kullanılırlar. Her bir adres hafıza dizininin bir elemanını tutar. Bu eleman genelde bir bayt olur ama bu bir zorunluluk değildir. Diyelim ki bir kutucuk **(word) 32 bit yani 4 bayt** olsun. Tam sayılar, tek duyarlı ondalık sayılar, hepsi 32 bit uzunluğunda yani bir kutu uzunluğundadır. Peki biz bunları hafızayı nasıl koyacağız? Sonuçta her bir hafıza adresi tek bir bayt depolayabilir. Sorumuzun cevabı basit: 32 bit uzunluğundaki sayımızı 4 bayta bölücez. Mesela sayımız 16 tabanında şu şekilde ifade edilsin **12CD34AB16** .16 tabanında her basamak 4 bit ile ifade edildiğinden sayımızı ifade edebilmek için 8 tane 16 tabanında basamağa ihtiyacımız olacaktır. Böylece sayımızı şu şekilde ayırabiliriz: **12 CD 34 AB**. Ayrılmış bölümlerin her biri birer bayt oldu böylece. Şimdi gelelim sayımızı hafızada depolamaya. Bunun iki yöntemi vardır.

### Big - Endian

Bu yöntemde önemli bayt en küçük adreste depolanır. Bu durumda sayımız aşağıdaki gibi olur.

>  0A0B0C0D - 0A 0B 0C 0D
>
> Hafızada 0A 0B 0C 0D şeklinde tutulur

### Little - Endian

Bu yöntemde ise tam tersi bir yol izleriz. Aynı şekilde sayımızı bölümleriz. Bu sefer ise önemli bayt en büyük 
adreste depolanır. İlk sayımızı (12CD34AB) tabloda görelim.

> 12CD34AB - 12 CD 34 AB
>
> Hafızada AB 34 CD 12 şeklinde tutulur