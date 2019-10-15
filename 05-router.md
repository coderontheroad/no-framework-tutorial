[<< önceki](04-http.md) | [sonraki >>](06-dispatching-to-a-class.md)

### Yönlendirme

Yönlendirici belirlediğiniz kurallara göre farklı işlemleri çalıştırmanızı sağlar.

Şuanki yüklememiz için uygulamaya hangi URL'yi kullanarak erişirsek erişelim geri dönen cevap hep aynı şekilde olmaktadır. Hadi bunu düzeltelim.

Ben bu ders için [FastRoute](https://github.com/nikic/FastRoute) kullanacağım. Ama her zamanki gibi, isteğiniz paketi seçmekte özgürsünüz.

Alternatif paketler: [symfony/Routing](https://github.com/symfony/Routing), [Aura.Router](https://github.com/auraphp/Aura.Router), [fuelphp/routing](https://github.com/fuelphp/routing), [Klein](https://github.com/chriso/klein.php)

Şimdiye kadar Composer paketlerinin nasıl yüklendiğini öğrendiniz, yani bu kısmı size bırakıyorum.

Şimdi şu kodları 'Bootstrap.php' dosyamızda 'hello world' mesajını yazdığımız yere koyalım.

```php
$dispatcher = \FastRoute\simpleDispatcher(function (\FastRoute\RouteCollector $r) {
    $r->addRoute('GET', '/hello-world', function () {
        echo 'Hello World';
    });
    $r->addRoute('GET', '/another-route', function () {
        echo 'This works too';
    });
});

$routeInfo = $dispatcher->dispatch($request->getMethod(), $request->getPath());
switch ($routeInfo[0]) {
    case \FastRoute\Dispatcher::NOT_FOUND:
        $response->setContent('404 - Page not found');
        $response->setStatusCode(404);
        break;
    case \FastRoute\Dispatcher::METHOD_NOT_ALLOWED:
        $response->setContent('405 - Method not allowed');
        $response->setStatusCode(405);
        break;
    case \FastRoute\Dispatcher::FOUND:
        $handler = $routeInfo[1];
        $vars = $routeInfo[2];
        call_user_func($handler, $vars);
        break;
}
```

Kodun ilk bölümünde, uygulamamız için mümkün olan adresleri yönlendiriciye bildiriyoruz. İkinci bölümde dispatcher'ı çağırıyoruz ve uygun duruma ait bölüm çalıştırılıyor. Eğer uygun yönlendirici bulunursa çağırılabilir işlem çalıştırılır.

Bu sistem küçük uygulamalar için çalışabilir, ama bir kaç adresle beraber bootstrap dosyanız büyümeye başladığında çok çabuk bir şekilde karmaşık hale gelebilir. Yani bunları ayrı bir dosyayı taşımalıyız.

`src/` klasörünün içerisinde `Routes.php` dosyası oluşturalım. Şu şekilde görünüyor olmalı:

```php
<?php declare(strict_types = 1);

return [
    ['GET', '/hello-world', function () {
        echo 'Hello World';
    }],
    ['GET', '/another-route', function () {
        echo 'This works too';
    }],
];
```

Şimdi route bölümünü `Routes.php` dosyasıyla çalışması için yeniden yazalım.

```php
$routeDefinitionCallback = function (\FastRoute\RouteCollector $r) {
    $routes = include('Routes.php');
    foreach ($routes as $route) {
        $r->addRoute($route[0], $route[1], $route[2]);
    }
};

$dispatcher = \FastRoute\simpleDispatcher($routeDefinitionCallback);
```

Bu zaten bir gelişme, ama artık tüm yönlendirme işlemi kodlarımız `Routes.php` dosyamızda. Bu en uygun çözüm değil, hadi bunu bir sonraki bölümde düzeltelim.

[<< önceki](04-http.md) | [sonraki >>](06-dispatching-to-a-class.md)
