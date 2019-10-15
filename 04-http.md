[<< önceki](03-error-handler.md) | [sonraki >>](05-router.md)

### HTTP

PHP, HTTP ile kolayca çalışmak için zaten hali hazırda bir çok şeyle birlikte geliyor. Örneğin [superglobals](http://php.net/manual/en/language.variables.superglobals.php) yapılan istekle ilgili bir çok bilgiyi sağlıyor.

Eğer sadece ufak bir script yazmak istiyorsanız ve sürekli çalışır tutmak önemli değilse bunlar işinize yarayabilir. Fakat temiz, sürdürülebilir, [SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29) kod yazmak istiyorsanız, uygulamanız içinde kullanabileceğiz object-oriented ile yazılmış iyi bir sınıfa ihtiyacınız var.

Bir kez daha, amerikayı yeniden keşfetmek zorunda değiliz ve hazır paketlerden birini kullanabiliriz. Ben kendi [HTTP component](https://github.com/PatrickLouys/http)'imi yazdım çünkü var olan componentleri beğenmedim, ama siz bunu yapmak zorunda değilsiniz.

Bazı alternatifler: [Symfony HttpFoundation](https://github.com/symfony/HttpFoundation), [Nette HTTP Component](https://github.com/nette/http), [Aura Web](https://github.com/auraphp/Aura.Web), [sabre/http](https://github.com/fruux/sabre-http)

Bu derste ben kendi HTTP componentimi kullanacağım, ama elbette hangi paketi severseniz onu kullanabilirsiniz. Sadece kodu uygun şekilde değiştirmelisiniz.

Tekrar, `composer.json` dosyasını aşağıdaki şekilde düzenleyip `composer update` kodumuzu çalıştırmalıyız:

```json
"require": {
    "php": ">=7.0.0",
    "filp/whoops": "~2.1",
    "patricklouys/http": "~1.4"
},
```

Şimdi aşağıdaki kodları hata işleme kodlarımızın altına `Bootstrap.php` dosyasına ekleyelim (ve exception'ı kaldırmayı unutmayın):

```php
$request = new \Http\HttpRequest($_GET, $_POST, $_COOKIE, $_FILES, $_SERVER);
$response = new \Http\HttpResponse;
```

Bu diğer sınıflarınızda request bilgilerini almak ve browser'a cevap göndermek için kullanabileceğiz `Request` ve `Response` objelerini oluşturur.

Gerçekten bir şey geri gönderebilmek için, ayrıca şu kodları 'Bootstrap.php' dosyasının en sonuna eklememiz gerekiyor:

```php
foreach ($response->getHeaders() as $header) {
    header($header, false);
}

echo $response->getContent();
```

Bu cevap bilgilerini tarayıcıya gönderir. Eğer bunu yapmazsanız, hiç birşey olmaz ve `Response` objesi sadece bilgileri tutar. Bu işlemler farklı HTTP componentlerde farklı şekillerde halledilebilir, bazılarında sınıflar verileri tarayıcıya yan etki olarak gönderir, eğer farklı componentler kullanırsanız farkları göz önünde bulundurun.

`header()` fonksiyonunun ikinci parametresini false olarak ayarladık aksi takdirde var olan headerların üzerine yazacaktır.

Şu anda tarayıcıya '200' durum koduyla boş bir sayfayı geri gönderiyoruz; bunu değiştirmek için şu kodları yukarıdaki kodların arasına koymanız gerekiyor:

```php
$content = '<h1>Hello World</h1>';
$response->setContent($content);
```

Eğer 404 hatasını denemek isterseniz, aşağıdaki kodları kullanabilirsiniz:

```php
$response->setContent('404 - Page not found');
$response->setStatusCode(404);
```

Şunu unutmayın objeler sadece veriyi tutar, yani cevabı göndermeden önce eğer bir den fazla durum kodu kullanırsanız, sadece en son ayarlanan gönderilecektir.

Sonraki partlarda componentlerin farklı özelliklerini nasıl kullanacağınızı göstereceğim. Bu arada [dökümantasyonu](https://github.com/PatrickLouys/http) okuyabilir ya da kaynak kodlarının nasıl çalıştığını inceleyebilirsiniz.

[<< önceki](03-error-handler.md) | [sonraki >>](05-router.md)
