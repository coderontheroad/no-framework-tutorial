[<< önceki](01-front-controller.md) | [sonraki >>](03-error-handler.md)

### Composer

[Composer](https://getcomposer.org/) PHP için kullanılan bağımlılık yöneticisidir.

Framework kullanmıyor olmanız demek, her bir şey yapmak istediğinizde amerikayı yeniden keşfetmek zorundasınız demek değildir. Composer kullanarak uygulamanız için üçüncü-parti kütüphaneleri yükleyebilirsiniz.

Eğer Composer zaten yüklü değilse, websitesine giderek yükleyebilirsiniz. Projeniz için composer paketlerini [Packagist](https://packagist.org/) adresinde bulabilirsiniz.

Projenizin kök dizininde 'composer.json' isimli bir dosya oluşturun. Bu projeniz ve projenizin bağımlılıkları için kullanılan, Composer için bir ayar dosyasıdır. Bu dosya doğru yazılmış bir JSON dosyası olmalıdır, yoksa Composer hata verir.

Şu kodları dosyanın içerisine koyun:

```json
{
    "name": "Proje İsmi",
    "description": "Proje Açıklaması",
    "keywords": ["anahtar kelime", "diğer anahtar kelime"],
    "license": "MIT",
    "authors": [
        {
            "name": "İsminiz",
            "email": "isminiz@email.com",
            "role": "Creator / Main Developer"
        }
    ],
    "require": {
        "php": ">=5.5.0"
    },
    "autoload": {
        "psr-4": {
            "Example\\": "src/"
        }
    }
}
```

autoload yazan kısıma bakarsanız, 'Example' namespace'ini kullandığımı görebilirsiniz. Burada projeniz için ne uygunsa onu kullanabilirsiniz, ama şimdiden itibaren ben her zaman 'Example' namespace'ini kullanacağım. Bunu kendi projeniz için kodlarınızda istediğiniz gibi değiştirebileceğinizi unutmayın.

Yeni bir konsol penceresi açın ve projenizin kök dizinine gidin. Burada 'composer update' komutunu çalıştırın.

Composer bağımlılıklarınızı ve vendor klasörünü kilitleyen 'composer.lock' dosyası oluşturacaktır.

'composer.lock' dosyasını versiyon kontrolüne dahil etmek projeniz için iyi bir şeydir. Test araçlarının ([Travis CI](https://travis-ci.org/) gibi) projenizdeki kütüphanelerle aynı versiyonları kullanarak testleri çalıştırmasını sağlar. Aynı zamanda projede çalışan herkesin aynı kütüphane versiyonlarıyla çalışmasını sağlar, böylece 'benim bilgisayarımda çalışıyordu' problemleriyle karşılaşmazsınız.

Şimdi başarılı bir şekilde boş bir proje sistemi kurmuş oldunuz.

[<< önceki](01-front-controller.md) | [sonraki >>](03-error-handler.md)
