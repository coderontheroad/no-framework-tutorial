[<< önceki](08-dependency-injector.md) | [sonraki >>](10-dynamic-pages.md)

### Şablonlar

Template engine PHP ile gerekli değildir çünkü dilin kendisi bunu halledebilir. Ama değerleri filtrelemenizi daha kolay yapmayı sağlayabilir. Ayrıca uygulamanızın mantığı ve değişkenleri HTML kodlarına koyduğunuz şablon dosyaları arasında açık bir çizgi oluşturmanızı kolaylaştırır.

Bunu okumak için bir kaynak [ircmaxell on templating](http://blog.ircmaxell.com/2012/12/on-templating.html). Lütfen konu ile ilgili farklı bir görüş için [bunuda](http://chadminick.com/articles/simple-php-template-engine.html) okuyun. Kişisel olarak konu üstünde güçlü bir görüşüm yok, yani hangi yaklaşımın sizin için daha iyi olduğuna kendiniz karar verin.

Bu ders için [Mustache](https://github.com/bobthecow/mustache.php)'in PHP portunu kullanacağız. Yani devam etmeden önce o paketi yükleyin (`composer require mustache/mustache`).

Bilinen diğer bir alternatif de [Twig](http://twig.sensiolabs.org/).

Şimdi lütfen [engine sınıfının](https://github.com/bobthecow/mustache.php/blob/master/src/Mustache/Engine.php) kaynak kodlarına gidin ve inceleyin. Görebileceğiniz gibi, sınıf interface sağlamıyor.

Somut sınıfa karşı bir ipucu yazabilirsiniz. Ama bu yaklaşımın sorunu sıkı bir bağlantı oluşturursunuz.

Diğer bir deyişle, motoru kullanan tüm kodlarınız bu mustache paketine bağlanacaktır. Eğer entegrasyonu değiştirmek isterseniz probleminiz olacak. Belki Twig'e geçmek isteyeceksiniz, belki kendi sınıfınızı yazmak isteyeceksiniz yada motora bir özellik eklemek isteyeceksiniz. Bunu tekrar geri dönüp sıkı bağlantı oluşturmuş tüm kodları değiştirmeden yapamayacaksınız.

Yapmak istediğimiz zayıf bağlantı. Arayüze bir ipucu yazacağız ve sınıf/entegrasyona değil. Böylece farklı bir entegrasyona ihtiyacınız olursa, sadece o arayüzü yeni sınıfınızda entegre ederek ve yeni sınıfı inject ederek yapabilirsiniz.

Paketin kodlarını düzenlemek yerine [adapter pattern](http://en.wikipedia.org/wiki/Adapter_pattern) kullanacağız. Bu olduğundan daha karmaşık geliyor, sadece takip edin.

Önce istediğimiz interface'i tanımlayalım. [interface segregation principle](http://en.wikipedia.org/wiki/Interface_segregation_principle)'ı hatırlayın. Bu bir çok method ile beraber büyük interface yerine her interface'i olabildiğince küçük yapmaya çalışacağız anlamına geliyor. Eğer gerekirse bir sınıf birden fazla interface extend edebilir.

Yani bizim şablon motorumuz aslında ne yapmalı? Şimdilik sadece basit bir `render` methoduna ihtiyacımız var. `src/` klasöründe tüm şablonla ilgili şeyleri koyabileceğiniz `Template` isimli yeni bir klasör oluşturun.

Bu klasörde `Renderer.php` isimli yeni bir interface oluşturun:

```php
<?php declare(strict_types = 1);

namespace Example\Template;

interface Renderer
{
    public function render($template, $data = []) : string;
}
```

Şimdi bu çözüldü, hadi mustache için entegrasyonu oluşturalım. Aynı klasörde, şu içerikle beraber `MustacheRenderer.php` dosyası oluşturalım:

```php
<?php declare(strict_types = 1);

namespace Example\Template;

use Mustache_Engine;

class MustacheRenderer implements Renderer
{
    private $engine;

    public function __construct(Mustache_Engine $engine)
    {
        $this->engine = $engine;
    }

    public function render($template, $data = []) : string
    {
        return $this->engine->render($template, $data);
    }
}
```

Görebileceğiniz gibi adaptör oldukça basit. Orjinal sınıf bir çok method içerirken, bizim adaptörümüz gerçekten basit ve sadece interface'i oluşturuyor.

Elbette `Dependencies.php` dosyamıza tanımlamayı eklememiz gerekiyor çünkü diğer türlü injector arayüz için ipucunda hangi entegrasyonu entegre etmesi gerektiğini bilemez. Şu satırı ekleyin:

`$injector->alias('Example\Template\Renderer', 'Example\Template\MustacheRenderer');`

Şimdi `Homepage` controllerda, şunun gibi yeni bir bağlılık ekleyin:

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

use Http\Request;
use Http\Response;
use Example\Template\Renderer;

class Homepage
{
    private $request;
    private $response;
    private $renderer;

    public function __construct(
        Request $request, 
        Response $response,
        Renderer $renderer
    ) {
        $this->request = $request;
        $this->response = $response;
        $this->renderer = $renderer;
    }

...
```

Ayrıca `show` methodunu yeniden yazmalıyız. Lütfen not edin basit bir dizi göndermemize rağmen, Mustache ayrıca view context objesi gönderme seçeneği veriyor. Bunun üstünden daha sonra geçeceğiz, şimdilik olabildiğince basit tutalım.

```php
    public function show()
    {
        $data = [
            'name' => $this->request->getParameter('name', 'stranger'),
        ];
        $html = $this->renderer->render('Hello {{name}}', $data);
        $this->response->setContent($html);
    }
```

Şimdi herşeyin çalıştığını tarayıcınıza giderek kontrol edin. Varsayılan olarak Mustache basit string kullanır. Ama biz şablon dosyaları istiyoruz, bu yüzden geri dönelim ve değiştirelim.

Bu değişikliği yapmak için `Mustache_Engine` constructor'a seçenekler dizisi göndermeliyiz. Bu yüzden `Dependencies.php` dosyasına geri dönün ve şu kodları ekleyin:

```php
$injector->define('Mustache_Engine', [
    ':options' => [
        'loader' => new Mustache_Loader_FilesystemLoader(dirname(__DIR__) . '/templates', [
            'extension' => '.html',
        ]),
    ],
]);
```

Seçenekler dizisi gönderiyoruz çünkü varsayılan `.mustache` uzantısı yerine `.html` uzantısını kullanmak istiyoruz. Neden? Diğer şablon dilleri benzer yazım şekli kullanır ve eğer başka bir şeye değiştirmek istersek tüm şablon dosyalarını yeniden isimlendirmek zorunda kalmayız.

Projenizin kök dizininde `templates` klasörü oluşturun. Orada, `Homepage.html` dosyası oluşturun. Dosyanın içeriği şöyle görünmeli:

```
<h1>Hello World</h1>
Hello {{ name }}
```

Şimdi `Homepage` controller'a geri dönebilir ve render satırını `$html = $this->renderer->render('Homepage', $data);` a değiştirebilirsiniz.

Tarayıcınızdan hello sayfasına gidin ve herşeyin çalıştığından emin olun. Her zamanki gibi değişikliklerinizi commit etmeyi unutmayın.

[<< önceki](08-dependency-injector.md) | [sonraki >>](10-dynamic-pages.md)
