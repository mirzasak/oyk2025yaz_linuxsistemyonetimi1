# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (4. Gün Notları - **Linux Dosya Sistemi ve Disk Yönetimi Rehberi**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin dördüncü gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Linux Dosya Sistem Hiyerarşisi (FHS)

Linux sistemlerde "**Her şey bir dosyadır**" felsefesi geçerlidir. FHS (Filesystem Hierarchy Standard), Linux dağıtımlarında dosya sisteminin nasıl organize edileceğini belirleyen standarttır. Her dağıtım %100 uymak zorunda olmasa da, genellikle bu standarda uygun yapı kullanılır.

### 1.1 Temel Dizinler ve İşlevleri

#### `/` (Root Dizin)
- Dosya sisteminin kök dizini
- Tüm diğer dizinlerin başlangıç noktası

#### `/boot` - Boot Dizini
- Sistem başlangıcında gereken dosyaları içerir (kernel, bootloader, initramfs)
- Genellikle ayrı bir partition olarak yapılandırılır
- **Neden ayrı partition?**: 
  - Sistem kurtarma durumlarında erişilebilirlik
  - Boot loader'ın disk sınırlamaları (eski sistemlerde ilk 1024 silindir sınırı)
  - Root partition'ı şifrelendiğinde boot dosyalarına erişim

#### `/tmp` - Geçici Dosyalar
- Geçici dosyaların depolandığı dizin
- Genellikle noexec (çalıştırılamaz) olarak mount edilir
- Sistem yeniden başlatıldığında temizlenebilir

#### `/var` - Değişken Veriler
- Uygulamaların çalışma sırasında oluşturduğu dinamik verilerin tutulduğu dizin
- Log dosyaları, cache, spool dosyaları burada bulunur
- Mail kuyrukları, print kuyrukları gibi sistem servisleri verileri

#### `/srv` - Servis Verileri
- Web sunucu dosyaları, FTP dosyaları gibi servis verilerinin tutulduğu dizin
- `/var` içindeki kritik verilerin ayrılması için önerilir
- Her dağıtım bu dizini sunmaz

#### `/run` - Runtime Verileri
- Sistem çalışma zamanında oluşturulan geçici veriler
- PID dosyaları, socket dosyaları gibi runtime bilgileri
- Sistem kapatıldığında otomatik olarak temizlenir

#### `/opt` - İsteğe Bağlı Uygulamalar
- Linux dosya sistemi standardına uymayan uygulamalar için
- Uygulamaların tüm bağımlılıklarını kendi içinde tutması
- **Dezavantajları**:
  - Ortak kütüphanelerin ayrı tutulması
  - Güvenlik güncellemelerinin zorlaşması
  - Disk alanının verimsiz kullanılması

#### `/media` ve `/mnt` - Mount Noktaları
- **`/media`**: Tak/çıkar (removable) cihazlar için (USB, CD/DVD)
- **`/mnt`**: Kalıcı disk bölümlerinin geçici mount edilmesi için
- Her iki dizin de mount işlemleri için kullanılır

#### `/lost+found` - Kayıp Dosyalar
- Dosya sistemi tutarsızlıkları sonucu oluşan yetim dosyalar için
- **Çalışma Prensibi**:
  - Dosya silindiğinde inode hemen silinmezse ortaya çıkar
  - fsck (file system check) bu durumu tespit eder
  - İnode'a otomatik isim verir ve lost+found altında tutar
- Her disk bölümünün kendi lost+found dizini vardır
- **Teorik olarak** sistemde birden fazla lost+found olabilir, **pratikte** her partition'ın kendi dizini bulunur

## 2. Özel Dosya Sistemleri

### 2.1 `/proc` - Process Dosya Sistemi
- **Sanal dosya sistemi** - fiziksel olarak diskte bulunmaz
- Kernel space ile user space arasındaki köprü görevi görür
- Process bilgileri, sistem durumu, donanım bilgileri sağlar
- Dosyalar 0 byte olarak görünür ama içerik barındırır
- Komut satırının aslında `/proc`'un arayüzü olduğu söylenebilir

### 2.2 `/sys` - Sistem Dosya Sistemi
- `/proc`'a benzer sanal dosya sistemi
- Device driver'lar ve kernel modülleri ile etkileşim sağlar
- `/proc`'un yalnızca processler için tasarlanması nedeniyle ortaya çıkmış
- Donanım aygıtları, sürücü parametreleri, sistem ayarları için kullanılır

## 3. Paket Yönetimi

### 3.1 Paket Yöneticisi Nedir?
Paket yöneticisi, Linux sistemlerde yazılım kurulum, güncelleme ve kaldırma işlemlerini yöneten araçtır. Linux'taki dağıtık dosya yapısı nedeniyle bu yönetim kritik önem taşır.

### 3.2 Paket Yöneticisinin İşlevleri
- **Sürüm Takibi**: Yazılım sürümlerini takip eder ve güncellemeleri yönetir
- **Bağımlılık Yönetimi**: Yazılımların ihtiyaç duyduğu kütüphaneleri otomatik yönetir
- **Kurulum/Kaldırma**: Yazılımları sistemden temiz kurma ve kaldırma
- **Güvenlik Kontrolü**: Paketlerin güvenilirliğini ve bütünlüğünü kontrol eder
- **Post-Install İşlemler**: Kurulum sonrası gerekli yapılandırmaları yapar

### 3.3 Yaygın Paket Yöneticileri
- **APT** (Debian, Ubuntu): Advanced Package Tool
- **YUM/DNF** (Red Hat, Fedora, CentOS): Yellowdog Updater Modified / Dandified YUM
- **DPKG** (Debian): Debian Package Manager
- **Pacman** (Arch Linux): Package Manager

### 3.4 Paket Deposu (Repository)
Paket deposu, yazılım paketlerinin merkezi olarak depolandığı ve dağıtıldığı sunuculardır. Paket yöneticileri bu depolardan yazılımları indirir ve kurar.

## 4. Disk Yönetimi ve Partitioning

### 4.1 MBR (Master Boot Record) Sınırlamaları
MBR formatında bir disk **maksimum 4 primary partition**'a sahip olabilir. Bu sınırlama MBR'ın yapısından kaynaklanır:

- **MBR Yapısı**: 512 byte boyutundadır
- **Partition Tablosu**: 64 byte (4 × 16 byte partition girişi)
- **Sınırlama Nedeni**: Sabit boyutlu partition tablosu sadece 4 giriş içerebilir

### 4.2 Extended Partition Çözümü
- Primary partition'lardan **yalnızca 1 tanesi Extended** olabilir
- Extended partition içinde **Logical partition**'lar oluşturulabilir
- Logical partition numaralandırması **5'ten başlar** (/dev/sdb5, /dev/sdb6...)
- Bu yöntemle teorik olarak sınırsız partition oluşturulabilir

## 5. VirtualBox ile Pratik Uygulama

### 5.1 Sanal Disk Ekleme
1. **VirtualBox Settings** → **Storage** → **Controller: SATA**
2. **Add Hard Disk** (sağdaki disk ikonu)
3. **Create** ile yeni disk oluştur
4. Diski sanal makineye attach et

### 5.2 Disk Partitioning İşlemi

```bash
# Disk durumunu kontrol et
lsblk

# Partitioning işlemini başlat
fdisk /dev/sdb

# Partition tiplerini seç:
# - Primary: Doğrudan kullanılabilir partition
# - Extended: Logical partition'lar için container
# - Logical: Extended içinde oluşturulan partition
```

### 5.3 Dosya Sistemi Oluşturma

```bash
# EXT4 dosya sistemi oluştur
mkfs -t ext4 /dev/sdb1

# XFS dosya sistemi oluştur
mkfs -t xfs /dev/sdb5
```

### 5.4 Mount İşlemleri

```bash
# Geçici mount
sudo mount /dev/sdb1 /mnt/ext4

# Kalıcı mount için /etc/fstab düzenle
echo '/dev/disk/by-uuid/9420a4b3-99b4-47a2-845f-cfcf1f70adc9 /mnt/ext4 ext4 defaults 0 0' >> /etc/fstab
```

## 6. Komut Referansı

### 6.1 Disk ve Dosya Sistemi Komutları

| Komut | Parametreler | Açıklama |
|-------|-------------|----------|
| `lsblk` | `-f` | Block device'ları listeler, -f dosya sistemini ve UUID'yi gösterir |
| `lscpu` | - | CPU bilgilerini /proc/cpuinfo'dan okuyarak görüntüler |
| `fdisk` | `/dev/sdX` | Disk partitioning aracı, interaktif mod |
| `mkfs` | `-t <filesystem_type> /dev/sdXN` | Belirtilen partition'da dosya sistemi oluşturur |
| `mount` | `<device> <mount_point>` | Dosya sistemini belirtilen noktaya bağlar |
| `umount` | `<mount_point>` | Mount edilmiş dosya sistemini ayırır |
| `fsck` | `/dev/sdXN` | Dosya sistemi tutarlılık kontrolü ve onarımı yapar |

### 6.2 Sistem Bilgi Komutları

| Komut | Açıklama |
|-------|----------|
| `df -h` | Disk kullanım bilgilerini human-readable formatta gösterir |
| `du -sh` | Dizin boyutunu özetler |
| `free -h` | Bellek kullanım bilgilerini gösterir |
| `cat /proc/meminfo` | Detaylı bellek bilgilerini gösterir |
| `cat /proc/cpuinfo` | CPU detaylarını gösterir |

### 6.3 /etc/fstab Formatı
```
<device> <mount_point> <filesystem_type> <options> <dump> <pass>
```

- **device**: UUID veya device path
- **mount_point**: Mount edilecek dizin
- **filesystem_type**: ext4, xfs, vfat vb.
- **options**: defaults, noexec, ro vb.
- **dump**: Backup için (genellikle 0)
- **pass**: fsck sıralaması (0=kontrol etme, 1=root, 2=diğer)
