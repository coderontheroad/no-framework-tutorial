[<< önceki](06-dispatching-to-a-class.md) | [sonraki >>](08-dependency-injector.md)

### Inversion of Control

Son bölümde bir controller sınıfı oluşturduk ve `echo` ile bir çıktı verdik. Ama şunu unutmayalım object oriented güzel bir HTTP soyutlama biçimimiz var. Ama şuan sınıfımız içerisinden kullanılabilir değil.

En makul kullanım [inversion of control](http://en.wikipedia.org/wiki/Inversion_of_control) kullanmak. Bu, sınıfa ihtiyacı olan objeyi oluşturma sorumluluğu vermek yerine, sadece objeyi sormak demek. Bunu da [dependency injection](http://en.wikipedia.org/wiki/Dependency_injection) ile yapabiliriz.

Eğer bu söylediklerim size şuan biraz karmaşık geliyorsa endişelenmenize gerek yok. Sadece dersi takip edin ve işlemleri yaptıkça bu şeyler size çok daha mantıklı gelmeye başlayacak.

`Homepage` controller dosyanızı bu şekilde değiştirin:

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

use Http\Response;

class Homepage
{
    private $response;

    public function __construct(Response $response)
    {
        $this->response = $response;
    }

    public function show()
    {
        $this->response->setContent('Hello World');
    }
}
```

Unutmayın ki `Http\Response` u dosyanın en üst kısmında [import](http://php.net/manual/en/language.namespaces.importing.php) ediyoruz. Bu da dosyanın herhangi bir yerinde `Response` kullandığımız zaman tam yolu çözümleyebilmesini sağlıyor.

Artık constructor içerisinde `Http\Response` u açıkca sorabiliriz. Bizim durumumuzda `Http\Response` bir interface. Yani herhangi bir sınıf buradaki kuralları uygulayarak inject edilebilir. Daha fazla bilgi ve ayrıntalar için [type hinting](http://php.net/manual/en/language.oop5.typehinting.php) ve [interfaces](http://php.net/manual/en/language.oop5.interfaces.php) kavramlarını inceleyebilirsiniz.

Şimdi kodlarımız hata döndürecektir, çünkü gerçekte henüz hiçbir şey inject etmedik. Hadi bunu adresleri çözümlediğimiz `Bootstrap.php` dosyası içerisinde çözelim:

```php
$class = new $className($response);
$class->$method($vars);
```

`Http\HttpResponse` objesi `Http\Response` interface'ini implement ediyor, yani bizim anlaşmamıza uyuyor ve kullanabiliriz.

Şimdi herşey yine çalışmaya başladı. Ama bu örneği takip ederseniz, bu yolla oluşturulan tüm objeleriniz aynı objeleri inject edecektir. Bu tabiki iyi bir şey değil, sonraki bölümde bunu düzeltelim.

[<< önceki](06-dispatching-to-a-class.md) | [sonraki >>](08-dependency-injector.md)
