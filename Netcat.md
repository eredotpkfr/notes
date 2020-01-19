## Netcat

telnet ve netcat neredeyse tamamen aynı şeylerdir. netcat komutu herhangi bir IP ye yada domaine verilen porttan bağlantı kurmaya çalışır. `netcat` yada `nc` ile çalıştırırlır.

`netcat 127.0.0.1 80` loopback e 80. porttan bağlantı kurmayı dener.

`netcat www.google.com 443` www.google.com a 443. porttan bağlandık ve bizden request(istek) bekleyen ekran geldi `GET / HTTP/1.1` ile `HTTP` isteği attık ve olumlu sonuç döndü `echo $?` komutundan anlaşılıyor.

`netcat -vz 127.0.0.1 80` network bağlantım varmı yokmu onu test eder.
`netcat -vz www.google.com 443 `



netcat in 2 tane modu vardır client ve server
`netcat -l 3000` 3000. portu dinler / server
`netcat -vz 127.0.0.1 3000` 3000. porttan bağlanır / client
`netstat -plnt` dinlenen bütün portları gösterir hangi servisler dinliyor fakat burada önemli olan hangi interface den dinliyor. 

`netstat -plnt | grep 8080`



