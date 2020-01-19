## HTTP Metodları

`GET` Bir kaynağı istemek için kullanılır.

`POST ` **(NOT IDEMPOTENT)** Bir kaynağı değiştirmek için kullanılabilir fakat `GET` metodu ilede değiştirilebilir ve `POST` metodu **NOT IDEMPOTENT** dir bu çağrılan bir metodun tekrar tekrar server da aynı etkiyi yaratmasıdır eğer `POST` metodu ile bir kullanıcı oluşturursam ve ardından tekrar kullanıcı oluşturursam 2 defa kullanıcı oluşturmuş olurum. `PUT` metodu ise `IDEMPOTENT` dir yanı `x = 5` dersem `x` her (3 kere de çağırsam 5 kerede çağırsam okuyan taraf için aynı olur.) çağrıldığında 5 olur yani 1 kereliğine etki yaratır ve kalıcıdır.

`PUT` Bir kaynağı kalıcı olarka ve anlamlı olarak değiştirmek için kullanılır.

`DELETE` Bir kaynağı siler.

`HEAD`, `GET` metodu ile aynı fakat `GET` metodunda mesajın bir **"body"** kısmı vardır `HEAD` metodu yalnızca `HEAD` 
başlıkları gönderir **"body"** kısmını göndermez.

### Response CODES

#### 1XX Bilgi Vericidirler

```bash
100 Continue (Devam)
102 İşlem
```

#### 2XX Başarılı

```bash
200 Kaynağı karşıya ilettim 200 OK
201 Yeni kaynak oluşturuldu.
204 İçerik yok.
```

#### 3XX Yönlendirme

```bash
305 Proxy kullan
307 Geçici olarak yeniden gönder.
```

#### 4XX İstemci hatası

```bash
400 Bad request (Kötü istek)
404 Sayfa bulunamadı
451 Yasal nedenlerle gösterilemiyor.
402 Ödeme gerekli
```

#### 5XX Sunucu hatası

```bash
500 Sunucuda bir hata oluştu
503 Hizmet yok
```

### HTTP HEADERS

#### Accept (req)

```http
Syntax : Accept: <MIME_type>/<MIME_subtype>
Example: Accept: image/jpeg,image/tiff
Example: Accept-Language: en-US;
Example: Accept-encoding: gzip
```

#### Content-Type (req/resp)

```http
Example: Content-Type: text/html; charset=utf-8
Example: Content-Type: text/xml; charset="utf-8"
```

#### Host (req)

Domain name içerir.

```http
Example: Host: www.erdoganyoksul.com
```

#### Connection (req/resp)

```http
Example: Connection: close - TCP Bağlantısını kapatır.
Example: Connection: keep-alive - TCP Bağlantısını açık bırak ben tekrardan request atacağım. Tarayıcılar en fazla 6 bağlantı açabilir (RFC 2616).
```

#### Referer (req)

```http
Example: Referer: http://abc.xyz.com
```

#### User-Agent (req)

Tarayıcı bilgisi

```http
Example: User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0
```

#### Cache-Control (req/resp)

```http
Example: Cache-Control: no-cache
Example: Cache-Control: public
Example: Cache-Control: private
Example: Cache-Control: no-store
```

