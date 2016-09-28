[sonraki >>](02-composer.md)

### Ön Controller

[Ön controller](http://en.wikipedia.org/wiki/Front_Controller_pattern) uygulamanıza giriş için bir tek nokta oluşturmakdır.

Başlarken, projeniz için boş bir klasör oluşturun. Aynı zamanda tüm istekleri karşılayacak bir giriş noktasına ihtiyacınız var. Bu da demek oluyor ki 'index.php' dosyasını oluşturmak zorundayız.

Bunu yapmak için genelde kullanılan yol projenin kök dizinine bir 'index.php' dosyası koymaktır. Bazı frameworkler de aynen bunu kullanır. Şimdi bunun neden yanlış olduğunu açıklayayım.

'index.php' bir başlangıç noktasıdır, yani web sunucu klasörünün içerisinde olması gerekir. Bu da demek oluyor ki web sunucusu tüm alt klasörlere ulaşabilir. Eğer gerekli ayarları yapabilirseniz, hala projeniz içerisinde bulunan alt klasörlere ulaşılmasını engelleyebilirsiniz.

Ama bazen herşey planlandığı gibi gitmez. Birşeyler ters giderse ve dosyalarınız yukarıdaki gibi ayarlanmışsa, uygulamanızın bütün kodları ziyaretçilere açık hale gelebilir. Bunun neden kötü olduğunu açıklamama gerek yok sanırım.

Sonuç olarak bunu yapmak yerine proje klasöründe 'public' isimli bir klasör oluşturun. Aynı zamanda uygulamanız için 'src' klasörünü projenizin kök dizinine oluşturmanın tam zamanı.

'public' klasörünüzün içerisinde şimdi 'index.php' dosyasını oluşturabiliriz. Şunu aklınızdan çıkarmayın; burada hiç bir açık vermek istemezsiniz, yani sadece şu kodu koymamız yeterlidir:

```php
<?php 

require __DIR__ . '/../src/Bootstrap.php';
```

`__DIR__` klasörün yolunu tutan bir [sihirli sabittir](http://php.net/manual/tr/language.constants.predefined.php). Bunu kullanarak, 'require' fonksiyonunun her zaman aynı yolu kullanarak çalışmasını sağlayabilirsiniz. Aksi takdirde 'index.php' dosyasını başka bir klasörden çalıştırırsanız dosyayı bulamayacaktır.

'Bootstrap.php' dosyası uygulamayı bir araya getiren dosyamız olacak. Az sonra buna geleceğiz.

Public klasörünün geri kalanında diğer statik dosyalarımız (JavaScript ve CSS dosyaları gibi) bulunacak.

Şimdi 'src' klasörünün içerisine girin ve içerisine şu kodları yazarak 'Bootstrap.php' dosyasını oluşturun:

```php
<?php 

echo 'Hello World!';
```

Şimdi herşey doğru mu bir kontrol edelim. Konsolu açın (terminal, komut satırı vs.) ve projenizin 'public' klasörüne gidin. Bu klasördeyken `php -S localhost:8000` komutunu çalıştırın. Bu PHP ile hazırda gelen websunucusunu çalıştırır ve sayfaya tarayıcınızdan `http://localhost:8000` adresine giderek ulaşabilirsiniz. Şuan 'hello world' yazısını görüyor olmalısınız.

Eğer bir hata varsa, geriye gidin ve düzeltmeye çalışın. Eğer sadece boş sayfa görüyorsanız, console penceresini sunucu hatası varmı diye kontrol edebilirsiniz.

Şimdi ilerlemeyi kayıt altına almanın tam zamanı. Eğer zaten Git kullanmıyorsanız, yeni bir repo oluşturun. Bu bir Git dersi olmadığı için detaylara girmeyeceğim. Ama versiyon kontrol sistemi kullanmak sadece öğrenmek için proje yapıyorsanız bile bir alışkanlık olmalı.

[sonraki >>](02-composer.md)
