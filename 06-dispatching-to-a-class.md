[<< önceki](05-router.md) | [sonraki >>](07-inversion-of-control.md)

### Sınıflara Dağıtmak

Bu derste [MVC (Model-View-Controller)](http://martinfowler.com/eaaCatalog/modelViewController.html) sistemini yapmaya çalışmayacağız. MVC PHP'de olması gerektiği gibi entegre edilemiyor, en azından orjinal tasarlanış şekliyle, her neyse. Eğer bunun hakkında daha fazlasını öğrenmek isterseniz [A Beginner's Guide To MVC](http://blog.ircmaxell.com/2014/11/a-beginners-guide-to-mvc-for-web.html) burayı okuyabilirsiniz.

MVC'yi unutalım ve [separation of concerns](http://en.wikipedia.org/wiki/Separation_of_concerns) üzerinde yoğunlaşalım.

İstekleri halleden sınıflar için açıklayıcı isimlere ihtiyacımız var. Bu ders için ben `Controllers` olarak isimlendireceğim çünkü framework temelinden gelen insanlar için bu tanıdık gelecektir. Ayrıca bunları `Handlers` olarak da isimlendirebilirsiniz.

`src/` klasörümüzün içerisine `Controllers` isimli yeni bir klasör oluşturalım. Bu klasörde tüm controller sınıflarımızı oluşturacağız. Burada, Homepage.php dosyasını oluşturalım.

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

class Homepage
{
    public function show()
    {
        echo 'Hello World';
    }
}
```

Autoloader sadece namespace'i dosya yolu ile ve dosya ismiyle ortak olan sınıflar için çalışır. Uygulamam için başlangıçta `Example` isimli `src/` klasörünü niteleyen bir kök namespace tanımlamıştım.

Şimdi hello world adresini değiştirelim böylece yeni sınıfımızın metodlarıyla çalışabiliriz. Şimdi `Routes.php` dosyasını şu şekilde değiştirelim:

```php
return [
    ['GET', '/', ['Example\Controllers\Homepage', 'show']],
];
```

Sadece çağrılabilir yapmak yerine şimdi array gönderiyoruz. İlk değer namespace tabanlı sınıf ismimiz, ikinci değer ise bu sınıfın hangi methodunu çağırmak istediğimiz.

Bunu çalışır yapmak için, 'Bootstrap.php' dosyamızın router bölümünde ufak refactor yapmamız gerekiyor:

```php
case \FastRoute\Dispatcher::FOUND:
    $className = $routeInfo[1][0];
    $method = $routeInfo[1][1];
    $vars = $routeInfo[2];
    
    $class = new $className;
    $class->$method($vars);
    break;
```

Artık sadece method çağırmak yerine bir obje çalıştırıyoruz ve onun içindeki gerekli methodu çağırıyoruz.

Şimdi `http://localhost:8000/` adresine gittiğinizde herşey çalışıyor olmalı. Eğer çalışmıyorsa, geri dönün ve debug yapın. Ve değişikliklerinizi commit etmeyi unutmayın.

[<< önceki](05-router.md) | [sonraki >>](07-inversion-of-control.md)
