[<< önceki](02-composer.md) | [sonraki >>](04-http.md)

### Hata İşlemek

Hata işlemek kodlarınızda bir hata oluşursa sonuçta ne olacağına karar vermenizi sağlar.

Hata ayıklamaya yardımcı güzel bir hata sayfası yapmak geliştirme yaparken vakit alan bir işlem. Yani kullanabileceğimiz ilk paket bunu bizim yerimize halledebilir.

Ben [filp/whoops](https://github.com/filp/whoops) aracını seviyorum, yani ben bu paketi projeniz için nasıl yükleyebileceğinizi göstereceğim. Eğer başka bir paketi tercih ediyorsanız, onu yüklemekte tamamen serbestsiniz. İşte framework kullanmadan bir şey yapmanın güzelliği burada, projeniz üzerinde tam bir kontrole sahipsiniz.

Diğer bir alternatif paket: [PHP-Error](https://github.com/JosephLenton/PHP-Error)

Yeni bir paket yüklemek için, 'composer.json' dosyanızı açın ve require bölümüne paketinizi ekleyin. Şuna benzer bir şey olmalı:

```php
"require": {
    "php": ">=5.5.0",
    "filp/whoops": ">=1.1.2"
},
```

Şimdi 'composer update' kodunu konsolunuzda çalıştırın ve herşey yüklenmiş olacaktır.

Ama henüz kullanamazsınız. PHP dosyaları nerede bulabileceğini bilmeyecektir. Bunun için autoloader'a ihtiyacınız var, en mantıklı olanı [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloader. Ama Composer zaten bizim yerimize bunu hallediyor, ve yek yapmamız gereken 'Bootstrap.php' dosyasına `require __DIR__ . '/../vendor/autoload.php';` kodunu eklemek.

**Önemli:** production bölümünde herhangi bir hata göstermemelisiniz. Ayrıntılı hatalar ya da sadece basit hata mesajları bile birinin sisteminize sızmasına yardımcı olabilir. Her zaman kullanıcı odaklı hata sayfaları gösterin ve kendinize mail atmak, log tutmak veya benzeri seçenekleri kullanın. Yani production da hataları yalnızca siz görmelisiniz.

Development için bilgilendirici ve güzel bir hata sayfası istiyoruz. Çözüm, nerede olduğumuza göre kodlar arasında geçiş yapmak. Şimdilik sadece 'development' olarak ayarlayabilirsiniz.

Hata işlemeyi hallettikten sonra, bir 'Exception' oluşturup herşeyin düzgün çalışıp çalışmadığına bakmalıyız. 'Bootstrap.php' dosyanız şuna benzer bir şekilde gözükmeli:

```php
<?php

namespace Example;

require __DIR__ . '/../vendor/autoload.php';

error_reporting(E_ALL);

$environment = 'development';

/**
* Register the error handler
*/
$whoops = new \Whoops\Run;
if ($environment !== 'production') {
    $whoops->pushHandler(new \Whoops\Handler\PrettyPageHandler);
} else {
    $whoops->pushHandler(function($e){
        echo 'Friendly error page and send an email to the developer';
    });
}
$whoops->register();

throw new \Exception;

```

Şuan satırları belirtilmiş güzel bir hata sayfasıyla karşılaşıyor olmalısınız. Eğer değilse, geri dönüp çalışana kadar hata ayıklamalısınız. Ayrıca başka bir commit yapmak için şuan iyi bir zaman.

[<< önceki](02-composer.md) | [sonraki >>](04-http.md)
