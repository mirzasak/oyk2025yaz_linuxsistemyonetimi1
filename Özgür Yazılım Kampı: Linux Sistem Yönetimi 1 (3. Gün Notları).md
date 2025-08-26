# Özgür Yazılım Kampı: GNU/Linux Sistem Yönetimi 1 (3. Gün Notları) 

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin üçüncü gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Dizin Hard Link Yapısı

### Dizin Oluşturma ve Hard Link Sayıları

Boş bir dizin oluşturduğumuzda **varsayılan olarak 2 tane hard link** ile gelir:

1. **Dizinin kendisi**: Dosya sistemindeki direkt referans
2. **`.` (nokta)**: Dizin içinden kendi kendisine referans

**Dizin hard link sayısı artışı:**
- Ana dizin içinde yeni bir alt dizin oluştururken, ana dizinin hard link sayısı artar
- Bunun sebebi alt dizin içindeki `..` (çift nokta) ifadesinin ana dizini göstermesidir

**Örnek:**
```bash
mkdir ana_dizin           # ana_dizin hard link sayısı: 2
cd ana_dizin
mkdir alt_dizin          # ana_dizin hard link sayısı: 3 olur
```

### Özel Dizin Referansları

- **`.`**: Bulunduğun dizinin kendisini ifade eder
- **`..`**: Bir üst dizini ifade eder

`cd ..` komutunun çalışma mantığı: `..` ifadesinin üst dizine hard link ile bağlanmış olmasından kaynaklanır.

### Dizin Hard Link Kısıtlamaları

**Önemli Kural**: 
- Kullanıcılar dizinler arası hard link **oluşturamazlar**
- Dizinlerde sadece **symbolic link** yapılabilir
- Hard link ile kullanıcılar yalnızca **dosyaları** linkleyebilir
- Dizinlere ait hard linkler yalnızca **root** tarafından otomatik olarak oluşturulur (`.` ve `..`)

## 2. Kullanıcı Home Dizini

### Home Dizini Tanımı
Kullanıcının kişisel alan alanıdır ve şunları barındırır:
- Kullanıcıya özel dosya ve dizinler
- Kullanıcı yapılandırma dosyaları
- Çevresel değişken tanımları
- Uygulama ayarları

**Konumu**: Genellikle `/home/kullanici_adi` şeklindedir.

## 3. Gizli Dosyalar

### Gizli Dosya Tanımı
Dosya veya dizin isminin başında **`.`** (nokta) varsa o dosya/dizin gizli kabul edilir.

### Gizli Dosya Özellikleri
- **Güvenlikle alakası yoktur** - sadece görsel düzen için
- Amaç: Görsel kalabalık oluşturup kafa karışıklığına sebep olmamak
- Dosya gizliliği **yetkilendirmeden bağımsız** bir durumdur
- `ls -a` komutu ile görülebilirler

**Örnekler:**
- `.bashrc` (bash yapılandırması)
- `.profile` (kullanıcı profili)
- `.ssh/` (SSH anahtarları)

## 4. PATH Çevre Değişkeni ve Komut Çalıştırma

### PATH Değişkeni Çalışma Mantığı

Bir komut çalıştırdığımızda sistem şu sırayı takip eder:
1. **Alias kontrolü**: İlk önce tanımlı alias'ları kontrol eder
2. **PATH araması**: Alias bulunamazsa PATH'teki dizinleri sırasıyla tarar
3. **İlk bulma**: PATH'te bulunan ilk uygun komutu çalıştırır

### Komut Bulunamama Sorunu

**Problem**: Komut sisteme yüklenmiş ama çalışmıyor
**Sebep**: Komut sistemde yüklü olmayabilir ya da komutun bulunduğu dizin PATH'te tanımlı olmayabilir.

**Çözümler:**
1. Komutun tam yolunu kullanmak: `/usr/local/bin/komut`
2. Dizini PATH'e eklemek: `export PATH=$PATH:/new/command/path`

### Kullanıcılar Arası PATH Farklılıkları

**Problem**: Komut root'ta çalışıyor ama normal kullanıcıda çalışmıyor
**Sebep**: PATH çevre değişkeni kullanıcıya özeldir
**Çözüm**: 
- Root olarak çalıştırmak 
- Normal kullanıcının PATH'ine gerekli dizini eklemek

### `which` Komutu
Bir komutun sistemde hangi dizinde bulunduğunu gösterir:
```bash
which ls        # /bin/ls
which python3   # /usr/bin/python3
```

## 5. Alias Sistemi

### Alias Tanımı ve Kullanımı
Komutlara kısayol isimleri tanımlamanızı sağlar.
Kendi eklediğimiz Alias'lar sistemi reboot ettiğimizde de kalıcı olmasını sağlamak için kullanıcının /home dizini altındaki .bashrc dosyasına ekleriz.

**Kullanım:**
```bash
alias k="kubectl"          # k yazınca kubectl çalışır
alias ll="ls -la"          # ll yazınca ls -la çalışır
alias ..="cd .."           # .. yazınca cd .. çalışır
```

### Alias Öncelik Sırası
Komut çalıştırma öncelik sırası:
1. **Alias** (en öncelikli)
2. **PATH'teki komutlar**
3. **Built-in komutlar**

### Alias Yönetimi
```bash
alias                      # Tüm alias'ları listele
unalias k                  # k alias'ını kaldır
unalias -a                 # Tüm alias'ları kaldır
```

## 6. History (Komut Geçmişi)

### History Çalışma Mantığı
Yazılan komutların tutulmasını sağlar ve oturum yönetimi ile çalışır.

### History Dosya Konumu
- **Varsayılan**: `~/.bash_history`
- **Özelleştirme**: `HISTFILE` çevre değişkenini değiştirerek farklı konuma ayarlanabilir

### History Oturum Yönetimi
1. **Oturum açıkken**: `history` komutu o oturumdaki tüm komutları gösterir
2. **Oturum kapandığında**: Komutlar history dosyasına yazılır
3. **Dosya okuma**: Açık oturumda dosyayı okursak bir önceki kapalı oturumun komutlarını görürüz

**Önemli**: Açık oturumdaki komutlar henüz dosyaya yazılmamıştır.

## 7. Dosya Sıkıştırma Algoritması

### Sıkıştırma Çalışma Mantığı
1. **Analiz**: Dosya içeriğindeki tekrarlayan ifadeleri tespit eder
2. **İndeksleme**: Tekrarlayan ifadelerin ilk konumlarını kaydeder
3. **Tablo oluşturma**: Tekrarların bulunduğu yerlerin listesini tutar
4. **Boyut azaltma**: Tekrarlı içeriği bir kez kaydederek boyut küçültür

### Sıkıştırma Verimliliği

**Etkili olduğu durumlar:**
- Büyük dosyalar
- Tekrarlı içerik (metin, log dosyaları)
- Benzer pattern'ler

**Etkisiz olduğu durumlar:**
- Çok küçük dosyalar
- Az tekrarlı içerik
- Zaten sıkıştırılmış dosyalar (video, resim)

**Not**: Küçük ve az tekrarlı dosyalarda sıkıştırma sonrası boyut daha büyük olabilir.

## 8. Sistem Dizin Yapısı

### `/bin` vs `/sbin` Farkı

- **`/bin`**: Normal kullanıcıların erişebildiği temel komutlar
  - `ls`, `cp`, `mv`, `cat` gibi günlük komutlar
- **`/sbin`**: Sistem yöneticisi (root) komutları
  - `mount`, `umount`, `fsck` gibi sistem komutları

### PATH'te Bulunma Durumu
- **Normal kullanıcı PATH'i**: genelde `/bin` dahil, `/sbin` dahil değil fakat bu durum kullanılan dağıtıma göre farklılık gösterebilir.
- **Root PATH'i**: Hem `/bin` hem `/sbin` dahil

### Legacy Sembolik Linkler

**Modern sistem yapısı:**
- `/bin` → `/usr/bin` (sembolik link)
- `/sbin` → `/usr/sbin` (sembolik link)

**Sebep**: Tarihsel nedenlerle `/usr` dizini artık ayrı bir bölüm olarak mount edilmez ama uyumluluk için linkler korunur.

## 9. Disk ve Dosya Sistemi Komutları

### `lsblk` (List Block Devices)
Tüm blok aygıtlarını (diskler ve bölümler) tree formatında listeler.

**Çıktı örneği:**
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk 
├─sda1   8:1    0    1G  0 part /boot
└─sda2   8:2    0   19G  0 part /
```

### `df` (Disk Free)
Mount edilmiş dosya sistemlerinin disk kullanım bilgilerini gösterir.

**Önemli parametreler:**
- `-h`: Human readable format
- `-T`: Dosya sistemi türünü göster
- `-i`: inode kullanım bilgisi

## 10. Mount/Umount İşlemleri

### Mount Kavramı
Bir dosya sistemini (disk, bölüm) sistemin dizin ağacına bağlama işlemidir.

**Temel kullanım:**
```bash
mount /dev/sda1 /mnt/disk          # Disk mount etme
umount /mnt/disk                   # Disk unmount etme
```

### Mount Edilen Dizindeki Veri Sorunu

**Senaryo**: İçinde veri olan bir dizin üzerine başka bir disk mount edersek?
**Sonuç**: Mount edilen diskin içeriği görünür, altındaki orijinal veriler gizlenir

**Çözüm - Orijinal veriyi görmek için:**
1. Orijinal dosyanın bulunduğu diski başka bir noktaya mount et
2. Yeni mount noktasından orijinal veriyi görüntüle
3. Mount etmek istediğin diski dosyanın bulunduğu dizine mount et
4. Orijinal dosyanın içeriğini o diskin mount noktasından okuyabilirsin
**ÖNEMLİ NOT-1**: Mount ettiğin dizini unmount işlemi gerçekleştirmeden sildiğimizde orijinal dosya içeriğini de silmiş oluruz.
**ÖNEMLİ NOT-2**: Aşağıdaki komut ekranı bilgilendirme amaçlı eklenmiştir, olduğu gibi uygulanması tavsiye edilmez.

```bash
# /home/mirzasak dizinini /mnt/user dizinine mount edelim
mount /home/mirzasak /mnt/user

# /mnt/user dizinini silelim
rm -rf /mnt/user

# /home/mirzasak dizini de /mnt/user ile birlikte silinmiş oldu
ls -l /home/mirzasak
ls: cannot access '/home/mirzasak': No such file or directory
```

**Hard link durumu**: Mount işleminden önce oluşturulan hard linkler çalışmaya devam eder ancak sembolik linkler kırılır. Mount ettiğin diskin üzerinde sembolik linkin işaret ettiği isimde bir dosya varsa link tekrar çalışır hale gelir ve mount edilen diskin işaret ettiği içeriği gösterir.

### Cross-Device Link Sorunu

**Hard Link Kısıtı**: Hard linkler farklı dosya sistemleri ve farklı diskler arasında oluşturulamaz.
- Hata: `cross-device link invalid`
- Sebep: Hard linkler aynı inode numarasını paylaşır, farklı cihazlarda aynı inode olamaz.

**Symbolic Link**: Farklı dosya sistemleri arasında oluşturulabilir.
- Sebep: Dosya yolu (path) saklar, inode numarası değil. Sembolik linkin kendi inode'u olur ve linklenen dosyanın ismine işaret eder.

## 11. Disk Tanımlama ve Dosya Sistemi

### UUID (Universally Unique Identifier)
Her disk bölümü için üretilen benzersiz kimlik numarasıdır.

**UUID'leri görüntüleme:**
```bash
blkid                              # Tüm blok cihazların UUID'leri
blkid /dev/sda1                    # Belirli bölümün UUID'si
```

**Avantajları:**
- Disk sırası ve ismi değişse bile tanınır
- `/etc/fstab`'da güvenli referans
- Dinamik disk ortamları için ideal

### `fsck` (File System Check)
Linux dosya sistemlerini kontrol etmek ve hataları onarmak için kullanılan komut satırı aracıdır.

**Temel özellikler:**
- Mount edilmiş bölümlerde çalıştırılmamalıdır, dosya sistemine zarar verebilir
- Sistem önyüklemesi sırasında `/etc/fstab` dosyasındaki ayarlara göre otomatik çalışır
- Hardware arızalarını tespit edebilir
- Performansı artırır ve veri kaybı riskini azaltır

**Kullanım:**
```bash
fsck /dev/sda1                     # Belirli bölümü kontrol et
fsck -A                            # fstab'daki tüm sistemleri kontrol et
fsck -f /dev/sda1                  # Zorla tam kontrol
```

### `/etc/fstab` Dosyası
Disk bölümlerinin, blok cihazlarının veya uzak dosya sistemlerinin nasıl mount edileceğini tanımlar. Her dosya sistemi ayrı bir satırda tanımlanır ve bu tanımlar boot sırasında systemd mount unitleri olarak dönüştürülür.

**Fstab sütun yapısı:**
1. **Device/UUID**: Cihaz adı veya UUID
2. **Mount Point**: Mount edilecek dizin
3. **File System Type**: ext4, xfs, ntfs vs.
4. **Options**: rw, ro, noexec vs.
5. **Dump**: Yedekleme (0=hayır, 1=evet)
6. **Pass**: fsck kontrol sırası (0=atla, 1=root, 2+=diğer)

**Örnek fstab girişi:**
```
UUID=12345-abcde /home ext4 defaults 0 2
```

### `lsof` (List Open Files)
Sistemde açık olan dosyaları ve bunları kullanan süreçleri gösterir.

**Temel kullanım:**
```bash
lsof /home                         # /home dizinindeki açık dosyaları göster
lsof -u kullanici                  # Belirli kullanıcının açık dosyaları
lsof -p 1234                       # Belirli sürecin açık dosyaları
lsof -i :80                        # 80 portunu kullanan süreçler
```

**Umount sorunları için:**
```bash
lsof /mnt/disk                     # Mount noktasını kullanan süreçleri bul
# Süreçleri sonlandırıp umount yapabilirsin
```

---

## Komut Listesi, Parametreler ve Açıklamaları

| Komut | Önemli Parametreler | Açıklama |
|-------|-------------------|----------|
| `which` | - | Komutun dosya sistemindeki konumunu gösterir |
| `alias` | `unalias [alias_adı]` | Komut kısayolları oluşturur/kaldırır |
| `history` | `-c` (temizle), `-d [satır]` (sil) | Komut geçmişini gösterir |
| `lsblk` | `-f` (dosya sistemi), `-a` (tüm cihazlar) | Blok cihazları tree formatında listeler |
| `df` | `-h` (human readable), `-T` (fs türü), `-i` (inode) | Disk kullanım bilgilerini gösterir |
| `mount` | `--bind`, `-t [fstype]`, `-o [options]` | Dosya sistemlerini mount eder |
| `umount` | `-f` (force), `-l` (lazy) | Dosya sistemlerini unmount eder |
| `blkid` | - | Blok cihazların UUID ve dosya sistemi bilgilerini gösterir |
| `fsck` | `-A` (tümü), `-f` (force), `-y` (otomatik evet) | Dosya sistemi kontrolü ve onarımı |
| `lsof` | `-u [user]`, `-p [pid]`, `-i [port]` | Açık dosyaları ve süreçleri listeler |

## Zorlu Komutlar için Praktik Örnekler

### Mount/Umount İşlemleri
```bash
# USB disk mount etme
sudo mount /dev/sdb1 /mnt/usb

# Mount bilgilerini kontrol etme
mount | grep /home
df -h /home

# Güvenli unmount (açık dosyaları kontrol et)
lsof /mnt/usb
sudo umount /mnt/usb
```

### fstab Yapılandırması
```bash
# UUID öğrenme
sudo blkid /dev/sda2

# fstab'a kalıcı mount ekleme
echo "UUID=12345-67890 /backup ext4 defaults 0 2" | sudo tee -a /etc/fstab

# fstab'ı test etme (unmount edip tekrar mount et)
sudo umount /backup
sudo mount -a

# Mount sırasında fsck kontrolü
# fstab'da 6. sütun: 0=atla, 1=root(öncelik), 2+=normal sıra
```

### lsof ile Problem Çözme
```bash
# Silinmeyen dosyaları bul (açık handle'lar)
lsof | grep deleted

# Ağ portunu kullanan süreci bul
lsof -i :22    # SSH bağlantıları
lsof -i :80    # HTTP bağlantıları

# Belirli kullanıcının açık dosyaları
lsof -u mirzasak
```

### PATH ve Alias Yönetimi
```bash
# PATH'e yeni dizin ekleme
export PATH=$PATH:/usr/local/bin

# Kalıcı PATH değişikliği (.bashrc'ye ekle)
echo 'export PATH=$PATH:/opt/custom/bin' >> ~/.bashrc

# Alias'ları görüntüle ve yönet
alias                              # Tüm alias'ları listele
alias ll='ls -alF'                 # Yeni alias tanımla
unalias ll                         # Alias'ı kaldır

# Komut tipini öğren
type ll                            # Alias, function, builtin, file?
which -a python                    # Python'un tüm konumları
```

### Dizin Hard Link Analizi
```bash
# Dizin hard link sayısını kontrol et
stat dizin_adi | grep Links

# . ve .. referanslarını test et
ls -la                             # . ve .. görünür
cd /home && pwd                    # .. test
cd . && pwd                        # . test

# Hard link'i olmayan dosyaları bul (orphan)
find / -type f -links 0 2>/dev/null
```

### History Optimizasyonu
```bash
# History boyutunu ayarla
export HISTSIZE=1000               # Bellekteki history
export HISTFILESIZE=2000           # Dosyadaki history

# History'yi hemen dosyaya yaz
history -a

# History dosyasını değiştir
export HISTFILE=~/.my_custom_history

# History'den arama
history | grep mount
# veya Ctrl+R ile interaktif arama
```
