# Özgür Yazılım Kampı: GNU/Linux Sistem Yönetimi 1 (2. Gün Notları) 

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin ikinci gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Çevre Değişkenleri (Environment Variables)

Bir değişken hem kullanıcıya hem de genel olarak sisteme tanımlandığında, **kullanıcı ayarları sistemin genel ayarını ezer** (override eder).

- **Sistem geneli ayarları**: `/etc/environment` dosyasında tanımlanır
- **Kullanıcı özel ayarları**: `~/.profile` veya `~/.bashrc` dosyalarında tanımlanır

## 2. Dosya Okuma Komutları

### `cat` Komutu
Dosya içeriğinin tamamını boyutu ve satır sayısı fark etmeksizin ekrana yazdırır. Küçük dosyalar için uygundur.

### `less` Komutu
Dosya içeriğini sayfa sayfa görüntüler. İleri-geri gezinme imkanı sunar:
- **↑↓ ok tuşları**: Satır satır gezinme
- **Space/Page Down**: Sonraki sayfa
- **Page Up**: Önceki sayfa
- **q**: Çıkış

### `more` Komutu
`less` komutunun daha eski versiyonudur. Sadece ileri doğru gezinme imkanı sunar ve daha kısıtlı özelliklidir.

### `head` Komutu
Dosyanın başındaki kısımları yazdırır. Varsayılan olarak ilk 10 satırı gösterir.

**Örnek kullanım**: `head -n 5 dosya_adi` (ilk 5 satırı gösterir)

### `tail` Komutu
Dosyanın sonundaki kısımları yazdırır. Varsayılan olarak son 10 satırı gösterir.

**Örnek kullanım**: `tail -n 5 dosya_adi` (son 5 satırı gösterir)

## 3. I/O Yönlendirmesi (Input/Output Redirection)

Komutların giriş ve çıkış akışlarını yönlendirme işlemidir.

### Standart Akışlar
- **0 (stdin)**: Standart giriş (klavye)
- **1 (stdout)**: Standart çıkış (başarılı sonuçlar)
- **2 (stderr)**: Standart hata (hata mesajları)

### Yönlendirme Operatörleri
- **`>`**: Çıktıyı dosyaya yönlendirir (dosyayı siler ve üzerine yazar)
- **`>>`**: Çıktıyı dosyanın sonuna ekler (append)
- **`1>`**: Sadece stdout'u yönlendirir
- **`2>`**: Sadece stderr'i yönlendirir

**Örnek kullanım**: 
```bash
tail -n 5 dosya.txt > cikti.txt
```

**Dikkat**: Komut hata verirse dosya boşaltılır ve hata mesajı ekrana yazılır.

### `/dev/null` - Çöp Tenekesi
Özel bir aygıt dosyasıdır. Buraya yönlendirilen tüm veriler silinir (kaybolur).

**Kullanım örneği**: 
```bash
find /etc -name "*.conf" 2>/dev/null  # Hata mesajlarını gizle
```

### Karmaşık Yönlendirme
```bash
ls mevcut_dosya olmayan_dosya > basarili.txt 2> hatalar.txt
```
- Başarılı çıktılar `basarili.txt`'ye
- Hata mesajları `hatalar.txt`'ye yönlendirilir

**Önemli**: Komut çıktılarında stderr (hata mesajları) her zaman stdout'tan önce görüntülenir.

## 4. Komut Zincirleme

### `;` (Noktalı Virgül)
Komutları sırayla çalıştırır. Birinci komutun başarı durumu ikincisini etkilemez.
```bash
echo "Birinci"; echo "İkinci"
```

### `&&` (Ve Operatörü)
İlk komut başarılı olursa ikincisini çalıştırır. İlk komut başarısız olursa durur.
```bash
mkdir yeni_dizin && cd yeni_dizin
```

### `||` (Veya Operatörü)
İlk komut başarısız olursa ikincisini çalıştırır. İlk komut başarılı olursa durur.
```bash
cp dosya.txt yedek/ || echo "Kopyalama başarısız!"
```

### `|` (Pipe - Boru)
Bir komutun çıktısını diğer komutun girdisi yapar.
```bash
ls -la | grep ".txt"  # txt dosyalarını filtrele
```

## 5. Metin İşleme Komutları

### `wc` (Word Count)
Kelime, satır, karakter sayısını hesaplar:
- **`-l`**: Satır sayısı
- **`-w`**: Kelime sayısı  
- **`-c`**: Karakter sayısı

### `sort`
Satırları sıralar:
- **Varsayılan**: Alfabetik sıralama
- **`-f`**: Büyük/küçük harf hassasiyetini kaldırır
- **`-r`**: Ters sıralama

### `grep`
Dosya içinde metin arar ve eşleşenleri döndürür:
- **`-i`**: Büyük/küçük harf ayrımı yapmaz
- **`-r`**: Dizinleri recursive olarak arar

### `echo`
Metni ekrana yazdırır veya değişken değerlerini gösterir:
```bash
echo "Merhaba Dünya"
echo $HOME  # Çevre değişkeni değerini göster
```

### RegEx (Regular Expressions)
Düzenli ifadeler Linux'tan bağımsız bir kavramdır. Metin arama, eşleştirme ve değiştirme işlemlerinde kullanılan güçlü pattern matching sistemidir.

## 6. Dosya Sistemi ve inode Yapısı

### inode (Index Node)
Her dosya ve dizinin sahip olduğu, dosya hakkında metadata tutan veri yapısıdır:

**İçerdiği bilgiler:**
- Dosyanın disk üzerindeki konumu
- Dosya boyutu
- Dosya izinleri (permissions)
- Sahiplik bilgileri (owner/group)
- Zaman damgaları (oluşturma, değiştirme, erişim)
- Link sayısı

**Çalışma mantığı:**
1. Dosyaya ismiyle erişilmek istendiğinde
2. Sistem bulunduğu dizinden inode numarasını öğrenir
3. inode'dan dosyanın gerçek konumunu ve metadata'sını alır

### Dosya İşlemleri

#### Dosya Silme
1. Dosyanın bulunduğu dizinden ismi silinir
2. inode bağlantısı koparılır
3. **Data bloğu hemen silinmez**, sadece "boş" olarak işaretlenir
4. Boş işaretli alanların üzerine yeni veriler yazılabilir

**Önemli**: Birden fazla hard link varsa, bir dosya silinse bile data korunur.

#### Dosya Taşıma
1. Kaynak dizinden isim ve inode bağlantısı silinir
2. Hedef dizinde yeni isim ve aynı inode bağlantısı oluşturulur
3. Aynı dosya sistemi içindeyse data taşınmaz

#### Dosya Kopyalama
1. Yeni bir inode oluşturulur
2. Data fiziksel olarak kopyalanır
3. Her kopya farklı inode'a sahiptir

## 7. Link Türleri

Linux'ta iki tür link vardır:

### Symbolic (Soft) Link
```bash
ln -s hedef_dosya link_adi
```

**Özellikleri:**
- Kendi inode'una sahiptir
- Hedef dosyanın yolunu (path) içerir
- Hedef dosya silinirse link "kırılır"
- Farklı dosya sistemleri arasında oluşturulabilir

**Link kırılma durumları:**
- ✅ Hedef dosya silinir/taşınır → Link çalışmaya devam eder
- ❌ Hedef dosyanın ismi değişir → Link kırılır

### Hard Link
```bash
ln hedef_dosya hard_link_adi
```

**Özellikleri:**
- Aynı inode'u paylaşır
- Hedef dosyayla tamamen aynıdır
- Herhangi biri silinse diğeri çalışmaya devam eder
- Sadece aynı dosya sistemi içinde oluşturulabilir
- Dizinler için oluşturulamaz

**Kullanım sebepleri:**
- Bazı uygulamalar symbolic link takip edemez
- Dosya güvenliği (yedekleme)
- Disk alanı tasarrufu

## 8. Dosya Sistemi Türleri

### ext4 (Fourth Extended File System)
**Özellikler:**
- Linux'un geleneksel dosya sistemi
- inode sayısı dosya sistemi oluştururken statik olarak belirlenir
- Maksimum dosya boyutu: 16 TB
- Maksimum dosya sistemi boyutu: 1 EB

**Dezavantajları:**
- inode sayısı sonradan artırılamaz
- inode dolduğunda boş disk alanı olsa bile yeni dosya oluşturulamaz
- Bu durumda sadece mevcut dosyaların üzerine yazarak alan kullanılabilir

### XFS
**Özellikler:**
- Büyük dosya depolama ve performans için optimize edilmiş
- Yüksek bant genişliği (>200MB/s) ve IOPS (>1000) ortamlarında optimal performans
- Dinamik inode tahsisi
- Maksimum dosya boyutu: 8 EB

**Avantajları:**
- inode sayısı dinamik olarak artırılabilir
- Büyük dosyalar için daha hızlı
- Genişletilmiş öznitelikler (xattr) için daha fazla alan (64K'ya kadar)

**Dezavantajları:**
- Metadata işlemleri için daha fazla CPU kaynak tüketir
- Dosya sistemi küçültme desteği yok

## 9. Özel Komutlar

### `debugfs` Komutu
Ext2/ext3/ext4 dosya sistemlerinin durumunu incelemek ve değiştirmek için kullanılan interaktif debugger'dır.

**Kullanım:**
```bash
debugfs /dev/sda1        # Salt-okunur mod
debugfs -w /dev/sda1     # Okuma-yazma modu
```

**Önemli**: Mount edilmiş dosya sistemlerde kullanılmamalıdır.

### `stat` Komutu
Dosya hakkında detaylı metadata bilgisi verir:
- inode numarası
- Hard link sayısı
- Dosya boyutu
- Zaman damgaları
- İzinler ve sahiplik

### `find` Komutu
Güçlü dosya arama aracı:
- **`-mmin n`**: Son n dakikada değiştirilen dosyalar
- **`-mtime n`**: Son n günde değiştirilen dosyalar
- **`-name "pattern"`**: İsim bazlı arama
- **`-samefile dosya`**: Aynı inode'a sahip dosyalar (hard linkler)

### `ls` Komutu Detayları
- **`ls -l`**: Detaylı listeleme (ilk 10 karakter dosya türü ve izinleri gösterir)
  - İlk karakter: dosya türü (`-` dosya, `d` dizin, `l` link)
  - Sonraki 9 karakter: sahip-grup-diğer izinleri (rwx formatında)
- **`ls -1`**: Dosya isimlerini tek sütunda listeler
- **`ls -R`**: Dizinleri listeler ama alt dizinlere recursive olarak inmez

## 10. Praktik Senaryolar

### Dosya Güncelleme Durumları
Bazı durumlarda dosyayı silip aynı isimle yeniden oluşturmak gerekebilir:

**Sebepleri:**
- Uygulama güncellemeleri
- Yapılandırma dosyası yenileme
- Log rotation işlemleri

**Symbolic link avantajı**: Dosya silinip aynı isimle yeniden oluşturulduğunda link otomatik olarak yeni dosyayı gösterir.

### inode Dolması Problemi
ext4'te inode sayısı sınırlı olduğu için, kullanılan diskte yer olsa bile kullanılabilir inode olmadığı için yeni dosyalar oluşturamayız:

**Problem**: Disk alanı varken yeni dosya oluşturulamama

**Çözümler**:
1. Mevcut dosyları silme
2. Büyük dosyaların üzerine yazma
3. Sanal makine kullanarak farklı dosya sistemi oluşturma

---

## Komut Listesi, Parametreler ve Açıklamaları

| Komut | Önemli Parametreler | Açıklama |
|-------|-------------------|----------|
| `less` | `q` (çıkış), `↑↓` (gezinme), `Space` (sayfa) | Dosya içeriğini sayfa sayfa görüntüler |
| `more` | `Space` (sonraki sayfa), `q` (çıkış) | Dosya içeriğini tek yönlü görüntüler |
| `head` | `-n [sayı]` | Dosyanın başından belirtilen satır sayısını gösterir |
| `tail` | `-n [sayı]` | Dosyanın sonundan belirtilen satır sayısını gösterir |
| `cat` | `-n` (satır numarası) | Dosya içeriğinin tamamını yazdırır |
| `echo` | `-n` (yeni satır yok) | Metni ekrana yazdırır veya değişken değerini gösterir |
| `wc` | `-l` (satır), `-w` (kelime), `-c` (karakter) | Sayım işlemleri yapar |
| `sort` | `-f` (case-insensitive), `-r` (reverse) | Satırları sıralar |
| `grep` | `-i` (ignore case), `-r` (recursive) | Metin içinde arama yapar |
| `ls` | `-l` (detaylı), `-1` (tek sütun), `-R` (dizinleri listele) | Dizin içeriğini listeler |
| `find` | `-mmin [n]`, `-mtime [n]`, `-name`, `-samefile` | Dosya ve dizin arama |
| `ln` | `-s` (symbolic link) | Link oluşturur |
| `stat` | - | Dosya metadata bilgilerini gösterir |
| `debugfs` | `-w` (write mode), `-R` (command) | Dosya sistemi debug aracı |
| `>` | - | Çıktıyı dosyaya yönlendirir (overwrite) |
| `>>` | - | Çıktıyı dosyaya ekler (append) |
| `1>` | - | Sadece stdout'u yönlendirir |
| `2>` | - | Sadece stderr'i yönlendirir |
| `\|` | - | Pipe: komut çıktısını başka komuta aktarır |
| `;` | - | Komutları sırayla çalıştırır |
| `&&` | - | İlk komut başarılı olursa ikincisini çalıştırır |
| `\|\|` | - | İlk komut başarısız olursa ikincisini çalıştırır |

## Zorlu Komutlar için Pratik Örnekler

### `find` Komutu Örnekleri
```bash
# Son 5 dakikada değiştirilen dosyaları bul
find /home -mmin -5

# .txt uzantılı dosyaları bul
find . -name "*.txt"

# Aynı inode'a sahip tüm hard linkleri bul
find / -samefile dosya.txt

# 100MB'dan büyük dosyaları bul
find /home -size +100M

# Dosyanın kaç tane sylmlink'i olduğunu bul
find . -type l -lname dosya.txt
```

### I/O Yönlendirme Örnekleri
```bash
# Hem başarılı hem hatalı çıktıları ayrı dosyalara yönlendir
ls mevcut_dosya olmayan_dosya > basarili.txt 2> hatalar.txt

# Hata mesajlarını gizle, sadece başarılı çıktıları göster
find /etc -name "*.conf" 2>/dev/null

# Tüm çıktıyı tek dosyaya yönlendir (stdout ve stderr)
komut > cikti.txt 2>&1

# Başarılı çıktıları gizle, sadece hataları göster
komut 1>/dev/null

# Sessiz çalıştırma (tüm çıktıları gizle)
komut >/dev/null 2>&1
```

### `grep` ve Pipe Zincirleme Örnekleri
```bash
# Log dosyasında error içeren satırları bul
tail -f /var/log/syslog | grep -i error

# Sistem süreçlerinde apache olanları bul
ps aux | grep apache | grep -v grep

# Dizindeki dosya sayısını öğren
ls -1 | wc -l

# En büyük 5 dosyayı bul ve boyutlarını göster
ls -la | sort -k5 -nr | head -5

# Belirli uzantıdaki dosyaları say
find . -name "*.txt" | wc -l
```

### Link İşlemleri Örnekleri
```bash
# Symbolic link oluştur
ln -s /var/log/messages log_link.txt

# Hard link oluştur  
ln important_file.txt backup_hardlink.txt

# Link bilgilerini kontrol et
ls -l link_file.txt
stat original_file.txt

# Bir dosyanın tüm hard linklerini bul
find / -samefile /path/to/file 2>/dev/null
```

### `echo` ve Değişken Örnekleri
```bash
# Basit metin yazdır
echo "Merhaba Dünya"

# Çevre değişkeni değerini göster
echo "Ev dizini: $HOME"
echo "Sistem yolu: $PATH"

# Dosyaya metin ekle
echo "Yeni log girişi: $(date)" >> log_file.txt

# Boş dosya oluştur
echo -n > yeni_dosya.txt

# Çoklu satır yazdırma
echo -e "Birinci satır\nİkinci satır\nÜçüncü satır"
```

### Komut Zincirleme Örnekleri
```bash
# İlk komut başarılı olursa ikincisini çalıştır
mkdir yeni_proje && cd yeni_proje && touch README.md

# İlk komut başarısız olursa ikincisini çalıştır
cp onemli_dosya.txt yedek/ || echo "Yedekleme başarısız!" | mail admin@domain.com

# Komutları sırayla çalıştır (başarı durumundan bağımsız)
echo "İşlem başlıyor"; cp dosya.txt yedek/; echo "İşlem tamamlandı"

# Karmaşık zincirleme örneği
ls -la /tmp && echo "Listeleme başarılı" || echo "Dizin bulunamadı" && date
```

### Dosya Sistemi Analiz Örnekleri
```bash
# inode kullanım durumunu kontrol et
df -i

# Belirli bir dosyanın inode bilgilerini gör
stat dosya.txt

# Debugfs ile dosya sistemi bilgilerini incele (unmount edilmiş sistem için)
sudo debugfs -R "stats" /dev/sda1
```
