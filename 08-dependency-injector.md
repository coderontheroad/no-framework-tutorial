[<< önceki](07-inversion-of-control.md) | [sonraki >>](09-templating.md)

### Bağımlılıklar

Dependency injector sınıfınızın bağımlılıklarını çözer ve sınıf başlatıldığında doğru objelerin oluşturulduğundan emin olur.

Burada önerebileceğim sadece bir tane injector var: [Auryn](https://github.com/rdlowrey/Auryn). Ne yazıkki baktığım tüm alternatifler dökümantasyonlarında ve örneklerinde [service locator antipattern](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/) kullanıyorlar.

Auryn paketini yükleyin ve `src/` klasöründe `Dependencies.php` diye bir dosya oluşturun. Orada şu içeriği ekleyebilirsiniz:

```php
<?php declare(strict_types = 1);

$injector = new \Auryn\Injector;

$injector->alias('Http\Request', 'Http\HttpRequest');
$injector->share('Http\HttpRequest');
$injector->define('Http\HttpRequest', [
    ':get' => $_GET,
    ':post' => $_POST,
    ':cookies' => $_COOKIE,
    ':files' => $_FILES,
    ':server' => $_SERVER,
]);

$injector->alias('Http\Response', 'Http\HttpResponse');
$injector->share('Http\HttpResponse');

return $injector;
```

Devam etmeden önce `alias`, `share` ve `define` ın ne yaptığını anladığınızdan emin olun. Hakkında daha fazlasını [Auryn dökümantasyonlarından](https://github.com/rdlowrey/Auryn) okuyabilirsiniz.

HTTP objelerini paylaşıyorsunuz çünkü bir objeye içeriği eklerken başka bir objeyi geri döndürmek anlamsız olur. Bu yüzden paylaşarak her zaman aynı objeyi alırsınız.

Alias arayüze sınıf ismi yerine ipucu yazmanıza izin verir. Bu eski uygulama yöntemini kullanan sınıflara geri dönüp tek tek düzenlemeden yeni uygulama yöntemine geçirmenize yardımcı olur.

Elbette `Bootstrap.php` dosyanızında değişmesi gerekiyor. `new` fonksiyon ile birlikte `$request` ve `$response` ayarlıyordunuz. Şimdi onu injector'a değiştirin böylece o objeleri her yerde aynı örnekten kullanabiliriz.

```php
$injector = include('Dependencies.php');

$request = $injector->make('Http\HttpRequest');
$response = $injector->make('Http\HttpResponse');
```

Değişmesi gereken bir diğer bölümde route'ın yönlendirilmesi. Önceden olan şu kodunuzu:

```php
$class = new $className($response);
$class->$method($vars);
```

Şu koda çevirin:

```php
$class = $injector->make($className);
$class->$method($vars);
```

Şimdi tüm controller constructor bağımlılıkları Auryn ile otomatik olarak çözülecek.

`Homepage` controller'a geri dönün ve şu şekilde değiştirin:

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

use Http\Request;
use Http\Response;

class Homepage
{
    private $request;
    private $response;

    public function __construct(Request $request, Response $response)
    {
        $this->request = $request;
        $this->response = $response;
    }

    public function show()
    {
        $content = '<h1>Hello World</h1>';
        $content .= 'Hello ' . $this->request->getParameter('name', 'stranger');
        $this->response->setContent($content);
    }
}
```

Şimdi görebildiğiniz gibi sınıfın iki bağımlılığı var. `http://localhost:8000/?name=Arthur%20Dent` bunun gibi sayfaya GET parametresi ile erişmeye çalışın.

Tebrikler, şimdi başarıyla uygulamanız için temeli oluşturdunuz.

[<< önceki](07-inversion-of-control.md) | [sonraki >>](09-templating.md)
