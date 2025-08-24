# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (1. Gün Notları ve Özgür Yazılım Felsefesi)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin ilk gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## Konu-1: Özgür Yazılım (Free Software) Nedir? ve Özgür Yazılım Felsefesi

Özgür yazılım yalnızca teknik bir konu değil, aynı zamanda bir özgürlük meselesidir. Özgür bir yazılıma sahip olmak beraberinde yazılımı kopyalama, değiştirme ve hatta kopyalarını satma özgürlüğüne sahiptir.

### Özgür Yazılımın 4 Temel İlkesi

**1. Kullanma**  
İstediğimiz yazılımı herhangi bir amaç için çalıştırma örneğini ifade eder. Örneğin bir çekiçle çivi çakabiliyorken aynı zamanda kapı da kırabiliriz, ikinci yöntemi tavsiye etmiyoruz :)

**2. Çoğaltma**  
Yazılımı dilediğimiz şekilde/sayıda kopyalayabilir ve bunu başkalarıyla da paylaşabiliriz.

**3. İnceleme**  
Yazılımın nasıl çalıştığını veya herhangi bir zaafiyeti olup olmadığını anlamak için kaynak kodlarını incelemek yazılımın gelişimine katkı sağlayabileceği gibi kullanıcıya da kullandığı yazılım hakkında bilgi sağlaycaktır.

**4. Değiştirme ve Değiştirdiğini Dağıtma**  
Yazılım üzerinde yapılan incelemelerden sonra değiştirmek ve ortaya çıkan yeni sürümün kopyalarını dağıtmak, yapılan değişikliklerden toplumun da faydalanmasını sağlayacaktır.

Bu felsefe, yazılımı sadece tüketmek yerine üzerinde düşünmeyi, geliştirmeyi ve paylaşmayı teşvik ediyor. Özgür Yazılım Kampı'nda aldığımız her ders de aslında bu bakış açısını güçlendiriyor.

## Konu-2: Linux Nedir? Linux'ta Komutlar Nasıl Çalışır?

Linux, 1991 yılında Linus Torvalds tarafından geliştirilmeye başlanan ve bugün milyonlarca kişi tarafından kullanılan özgür ve açık kaynaklı bir işletim sistemi çekirdeğidir.

Birçok insan "Linux" derken aslında Linux çekirdeği üzerine inşa edilmiş işletim sistemlerini (örneğin Ubuntu, Debian, Fedora) kasteder. Bu dağıtımların ortak noktası: özgür yazılım felsefesiyle geliştirilmiş olmalarıdır.

### Linux'un öne çıkan özellikleri:
1. Açık kaynaklı olması
2. Özgür olması
3. Topluluk odaklı olması

Kısacası Linux, sadece bir işletim sistemi değil; aynı zamanda özgür yazılım felsefesinin en güçlü temsilcilerinden biridir.

Linux'u başlattığımızda aslında ilk aşamada yalnızca tek bir komut çalıştırılır. Bu komut, sistemin kullanıcıyla etkileşime geçmesini sağlar.

Varsayılan olarak bu komut, kullanıcı için bir kabuk (shell) başlatır. En yaygın kullanılan kabuklardan biri bash (Bourne Again Shell)'dir.

Kullanıcı bu shell üzerinden `ls`, `cd`, `pwd`, `rm` gibi komutlar çalıştırarak sistemi yönetir.

Dersin tam da bu kısmında sınıfımızdan bir arkadaş "Lamba'dan cin çıkıyor ve tek dilek hakkın var diyor ama sen sonsuz dilek hakkı istiyorsun" şeklinde benim de çok ilgimi çeken bir benzetme yapmıştı.

## Sık Kullanılan Komutlar ve Parametreler

Linux'ta bir komutun nasıl çalıştığını ve hangi parametreleri alabildiğini görmek için kullanabileceğimiz temelde 3 farklı komut var, bunlar sırasıyla `man`, `help` ve `info`. Kullanım şekilleri ise:

```bash
man ls
ls --help
info ls
```

Linux komutları genellikle parametrelerle çalışır, kullanılan parametreler kodun davranışını değiştirir. Parametreler `-` veya `--` ile başlayabilir.

**`-` şeklinde kullanıldığında** arkasına gelen her değeri ayrı bir parametre olarak değerlendirir. Örneğin `ls` komutunda `-la` şeklinde kullandığımızda gizli klasörleri de kapsayacak şeklide bulunduğumuz dizindeki tüm gizli dosya ve dizinleri listeler.

`ls -la` ile `ls -l -a` aynı şeyi ifade eder.
- `-l` (listelemek için kullanılır)
- `-a` (gizli klasörleri göstermek için kullanılır)
- `-h` (dosya boyutlarını okunabilir halde gösterir)

**`--` şeklinde kullanıldığında** ise arkasına gelen parametreyi tek bir değer olarak alır. Örneğin `ls --help` şeklinde kullanılabilir.
- `--help` (komut hakkında bilgiler verir)

## Dosya ve Dizin İşlemleri

Linux'ta bulunan tüm dizinler ve dosyalar tek bir root (`/`) dizininden başlar. Bu yapıya hiyerarşik dosya sistemi denir. Herşey kök dizini altında dallanarak alt dizinlere ayrılır.

### `rm` (remove) / `rmdir` (remove directory)
Dosya/dizin silmek için kullanılır. Çoğunlukla birlikte kullanılan parametreleri `-r` (recursive) `-f` (force) bunlardır.

- `rm` komutu tek başına kullanıldığında dizinleri silemez, yalnızca dosyaları silebilir.
- `rm -r` dizini içerikleriyle birlikte silmek için kullanılır.
- `rm -rf` dizini ve alt dizinde yer alanları onay almadan silmeye zorlar, Linux'ta bulunan en tehlikeli komutlardan biridir. Çalıştırıldığında verilen dizini onay almadan sileceği için yanlış kullanımda geri dönüşü olmayan sonuçlara yol açabilir.
- `rmdir` ise yalnızca **boş** dizinleri silmek için kullanılır, eğer dizin içinde dosya/dizin bulunuyorsa hata verir. İçinde dizin/dosya bulunan bir dizini silmek istiyorsak `rm -rf` kombinasyonunu kullanabiliriz.

### `cd` (change directory)
Bulunduğumuz dizini değiştirmek için kullanılır ve farklı kullanım şekilleri vardır.

- `cd ..` (bir üst dizine çıkar)
- `cd -` (bir önceki dizine geri döner)

**Örnek kullanım:**  
`/usr/lib` klasöründeyken `cd /home/ozguryazilim` dizinine geçtiğinizi varsayalım, bu durumda çalıştıracağınız ilk komut `cd -` olursa sizi bulunduğunuz son konum olan `/usr/lib` dizine götürecektir. Eğer çalıştıracağınız ilk komut `cd ..` olursa bir üst dizininiz olan `/home` dizinine götürecektir.

### `pwd` (print working directory)
Bulunduğumuz dizini gösterir. Bu bilgiyi environment variables'da bulunan `PWD` isimli değişkenin değerinden alır.

`cd` komutu kullanıldığında ise aslında arka planda gerçekleşen işlem `PWD` değişkeninin içeriğini, girdiğimiz parametre ile güncelleyerek bulunduğumuz dosya dizinini değiştirmemizi sağlar. `PWD` değişkeninin değeri değiştiğinde, eski değer `OLDPWD` adında farklı bir değişkene tanımlanır.

`cd` komutu ile birlikte `-` parametresi `cd -` kullanıldığında ise environment variables'daki `OLDPWD` değerinde bulunan dizine yönlendirir bizi.

### `cp` (copy)
Dosya/dizinleri kopyalamak için kullanılır. Kopyalamak istediğimiz dosya/dizini kopyalamak istediğimiz yolu belirterek kopyasını oluşturabiliriz.

Eğer kopyalanmak istenen dizinin içi boş değilse `-r` (recursive) parametresiyle birlikte altındaki dizin/dosya'lar ile birlikte kopyalama gerçekleştirilebilir.

### `mv` (move)
Dosya/dizinleri farklı bir dizine taşımak veya ismini değiştirmek için kullanılır.

### `file`
Dosyanın türünü ve içeriğini analiz etmek için kullanılır. Dosyanın header (başlık) kısmını inceleyerek uzantısına bakmadan gerçek dosya tipini söyler.

### `diff` (difference)
İki dosya arasındaki farkları karşılaştırmak ve göstermek için kullanılır. Hangi satırların farklı olduğunu detaylı şekilde listeler. 

### Hash Komutları (`md5sum`, `sha256sum`)
Dosya bütünlüğünü kontrol etmek için kullanılır. Bu komutlar dosyanın benzersiz bir hash değerini hesaplar:
- `md5sum`: Dosyanın MD5 hash değerini hesaplar
- `sha256sum`: Dosyanın SHA256 hash değerini hesaplar (daha güvenli)

Hash değerleri, dosyanın değişip değişmediğini anlamak veya iki dosyanın aynı olup olmadığını kontrol etmek için kullanılır.

## Ortam Değişkenleri

Linux'ta her kullanıcı kendi ortamına (environment) sahiptir. Bu ortam ise ortam değişkenlerinden (environment variables) oluşur. Linux'ta kullanıcı değiştirip farklı bir kullanıcıya geçiş yaptığınızda environment variables'da da farklılıklar gözlemlenebilir. Çünkü her kullanıcı kendi ev dizinine, kendi PATH ayarına ve kendi konfigürasyon dosyalarına sahiptir.

Sistemde tanımlı ortam değişkenlerini görüntülemek için `set` veya `printenv` komutları kullanılır. Bir değişkeni kullanmak içinse `$DEGISKEN_ADI` şeklinde çağırılabilir.

Örneğin, `PWD` değişkeninin içeriğini görmek için `echo $PWD` şeklinde kullanılabilir.

Kendimiz ortam değişkeni tanımlamak istediğimizde veya varolan bir ortam değişkeninin değerini değiştirmek istediğimizde `export` komutunu kullanabiliriz.

Örneğin, `export LANG="tr_TR.UTF-8"` komutu ile sistem dilini tanımlayan değişkenin içeriğini ve aynı zamanda sistem dilini de değiştirebiliriz.

---

## Komut Referansı

Bu derste öğrendiğimiz tüm komutlar ve parametreler alfabetik sıraya göre listelenmiştir:

### Temel Komutlar
- **`cd`**: Dizin değiştirir
- **`cd -`**: Önceki dizine gider
- **`cd ..`**: Bir üst dizine çıkar
- **`cp`**: Dosya/dizin kopyalar
- **`diff`**: İki dosya arasındaki farkları gösterir
- **`echo`**: Metin yazdırır veya değişken değerini gösterir
- **`export`**: Ortam değişkeni tanımlar veya değiştirir
- **`file`**: Dosya türünü ve içeriğini analiz eder
- **`info`**: Komut hakkında detaylı bilgi verir
- **`ls`**: Dizin içeriğini listeler
- **`man`**: Komut kılavuz sayfasını gösterir
- **`md5sum`**: Dosyanın MD5 hash değerini hesaplar
- **`mkdir`**: Yeni dizin oluşturur
- **`mv`**: Dosya/dizin taşır veya isim değiştirir
- **`nano`**: Nano metin editörünü açar
- **`printenv`**: Ortam değişkenlerini görüntüler
- **`pwd`**: Bulunduğun dizini gösterir
- **`rm`**: Dosya siler
- **`rmdir`**: Boş dizin siler
- **`set`**: Ortam değişkenlerini görüntüler
- **`sha256sum`**: Dosyanın SHA256 hash değerini hesaplar
- **`touch`**: Boş dosya oluşturur veya dosya tarihini günceller
- **`vi`**: Vi metin editörünü açar

### Parametreler
- **`-`**: Tek karakterli parametreler için kullanılır
- **`--`**: Uzun parametreler için kullanılır
- **`-a`**: Gizli dosyaları da gösterir (ls ile)
- **`-f`**: Zorla işlem yapar (rm ile)
- **`-h`**: Dosya boyutlarını okunabilir formatta gösterir (ls ile)
- **`--help`**: Komut hakkında yardım bilgisi verir
- **`-l`**: Detaylı liste formatında gösterir (ls ile)
- **`-r`**: Alt dizinleri dahil ederek işlem yapar (recursive)
- **`-rf`**: Alt dizinleri dahil ederek zorla siler (tehlikeli!)
- **`-v`**: Yapılan işlemleri detaylı gösterir (verbose)

### Örnek Komutlar
- **`export LANG="tr_TR.UTF-8"`**: Sistem dilini Türkçe yapar
- **`info ls`**: ls komutu hakkında detaylı bilgi
- **`ls --help`**: ls komutu yardım sayfası
- **`ls -la`**: Gizli dosyalar dahil detaylı liste
- **`ls -l -a`**: Yukarıdaki komutun alternatif yazımı
- **`man ls`**: ls komutu kılavuz sayfası
- **`rm -r`**: Dizini içindekilerle birlikte siler
- **`rm -rf`**: Dizini onay almadan zorla siler
