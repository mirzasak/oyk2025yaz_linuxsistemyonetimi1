# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (6. Gün Notları - **Linux İleri Sistem Yönetimi: Kullanıcılar, Yetkiler ve Sistem Araçları**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin altıncı gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Sürüm Yönetimi ve Depolar

### Sürüm Kavramları
- **Major Sürüm (Ana Sürüm):** Büyük değişikliklerin yapıldığı sürümler (örn: 2.x → 3.x)
- **Minor Sürüm (Alt Sürüm):** Küçük özellik eklemeleri ve iyileştirmeler (örn: 3.1 → 3.2)

### Depo Yönetimi

#### Depo Ekleme
Yeni paket depolarını `/etc/apt/sources.list` dosyasına veya `/etc/apt/sources.list.d/` dizini altına `.list` uzantılı dosyalar olarak ekleyebilirsiniz.

#### Depo Kaldırma/Pasifleştirme
1. **Pasifleştirme:** `sources.list` dosyasında ilgili satırın başına `#` işareti eklemek
2. **Tamamen Kaldırma:** Paket ile gelen dosyayı sistemden silmek

#### Depo Önceliği
Bir uygulamanın paket dosyası birden fazla depoda varsa, öncelikli depo (pin priority) kullanılır. İstenmeyen depoları pasif hale getirebilir veya pin ayarları ile öncelik belirleyebilirsiniz.

---

## 2. Sistem Zamanı Yönetimi

### Tarih ve Saat Komutları

#### date Komutu
```bash
# Tarih ve saati görüntüleme
date

# Tarih ve saat ayarlama
date -s "2025-12-31 23:59:59"

# UTC zamanını gösterme
date -u
```

#### timedatectl Komutu (systemd tabanlı sistemler)
```bash
# Detaylı zaman bilgisi
timedatectl

# Zaman ayarlama
timedatectl set-time "2025-12-31 23:59:59"

```

---

## 3. Dosya İndirme ve Transfer Araçları

### curl Komutu

**curl**, komut satırı veya script'lerde veri transferi için kullanılan güçlü bir araçtır. Günlük olarak her internet kullanan insan tarafından kullanılan ve yirmi milyar kurulumda bulunan yazılımlarda internet transfer motoru olarak görev yapan bu araç, çok çeşitli protokolleri destekler.

**Özellikler:**
- HTTP, HTTPS, FTP, SFTP, SCP, LDAP gibi birçok protokol desteği
- Dosya indirme ve yükleme
- API çağrıları yapma
- Form verileri gönderme

**Yaygın Kullanım Örnekleri:**
```bash
# Basit dosya indirme
curl -O https://example.com/file.zip

# Belirli isimle kaydetme
curl -o myfile.zip https://example.com/file.zip
```

### install -d Komutu

**install** komutu sadece dosya kopyalamakla kalmaz, aynı zamanda izinleri, sahibini ve grubunu da ayarlayabilir. Bu özellik **mkdir**'den farkını yaratır.

```bash
# Basit dizin oluşturma (mkdir ile aynı)
install -d /path/to/directory

# İzinleri belirleyerek dizin oluşturma
install -d -m 755 /path/to/directory

# Sahibini ve grubunu belirleyerek dizin oluşturma
install -d -o user -g group /path/to/directory

# Tüm ayarları bir arada
install -d -m 755 -o www-data -g www-data /var/www/mysite
```

### source Komutu (.)

**source** komutu, belirtilen dosyadaki komutları mevcut shell ortamında okur ve çalıştırır. Bu komut alt shell (sub-shell) başlatmadığı için, çalıştırdığınız her şey mevcut ortamınızı etkiler.

**Kullanım Alanları:**
- Yapılandırma dosyalarını yükleme
- Fonksiyonları ve değişkenleri mevcut shell'e aktarma
- .bashrc, .profile gibi dosyaları yeniden yükleme

```bash
# Kaynak dosyayı çalıştırma
source /path/to/script.sh

# Nokta ile kısaltma (aynı işlev)
. /path/to/script.sh

# .bashrc dosyasını yeniden yükleme
source ~/.bashrc
```

---

## 4. Kullanıcı Yönetimi ve Güvenlik

### Temel Kavramlar

**Önemli Not:** Linux'ta yetkilendirmeler kullanıcılara değil, **dosyalara** verilir. Sistem her dosya işleminde o dosyanın yetkilendirilmesini kontrol eder.

**Terminoloji:** Linux sistemlerinde "şifre" değil "**parola**" terimi kullanılır.

### Kullanıcı Bilgilerinin Saklandığı Dosyalar

- **`/etc/passwd`:** Sistemdeki kullanıcıların listesi
- **`/etc/shadow`:** Kullanıcıların parola bilgileri (hash'lenmiş)
- **`/etc/group`:** Sistemdeki grupların listesi

### Kullanıcı Bilgilerini Öğrenme Komutları

```bash
# Hangi kullanıcıda olduğunuzu gösterir
whoami

# Kullanıcının bağlı olduğu grupları gösterir
groups

# Belirli kullanıcının gruplarını gösterir
groups username
```

---

## 5. Dosya İzinleri ve Yetkilendirme

### Dosya İzin Yapısı

Linux dosya sisteminde her dosya ve dizin için üç farklı yetki grubu bulunur:

| İzin Grubu | Açıklama | Konumu |
|------------|----------|---------|
| **Owner (u)** | Dosya sahibi | İlk 3 karakter |
| **Group (g)** | Dosya grubu | Orta 3 karakter |
| **Others (o)** | Diğer kullanıcılar | Son 3 karakter |

### İzin Türleri

| Sembol | Değer | Dosyalar için | Dizinler için |
|--------|-------|---------------|---------------|
| **r** | 4 | Okuma | İçeriği listeleme |
| **w** | 2 | Yazma/Değiştirme | Dosya oluşturma/silme |
| **x** | 1 | Çalıştırma | Dizine girme |

### Örnek İzin Analizi
```
-rw-r--r-- 1 root root 1523 Aug 28 07:58 /etc/passwd
```

| Karakter | Anlamı |
|----------|--------|
| `-` | Dosya türü (- = normal dosya, d = dizin, l = sembolik bağ) |
| `rw-` | Sahip izinleri: okuma+yazma |
| `r--` | Grup izinleri: sadece okuma |
| `r--` | Diğer kullanıcı izinleri: sadece okuma |
| `1` | Hard link sayısı |
| `root` (1.) | Dosya sahibi |
| `root` (2.) | Dosya grubu |

### /etc/passwd Dosya Yapısı

```
ubuntuvm:x:1000:1000:ubuntuvm:/home/ubuntuvm:/bin/bash
```

| Sıra | Alan | Açıklama |
|------|------|----------|
| 1 | `ubuntuvm` | Kullanıcı adı |
| 2 | `x` | Parola alanı (artık kullanılmıyor, /etc/shadow'da) |
| 3 | `1000` | User ID (UID) |
| 4 | `1000` | Group ID (GID) |
| 5 | `ubuntuvm` | Kullanıcı bilgisi/açıklaması |
| 6 | `/home/ubuntuvm` | Kullanıcının ev dizini |
| 7 | `/bin/bash` | Varsayılan shell |

### Grup Kullanmanın Faydaları

**Neden grup kullanırız?**
- **Yönetim kolaylığı:** Birden fazla kullanıcıya aynı izinleri tek seferde verebilirsiniz
- **Zaman tasarrufu:** Her kullanıcı için ayrı ayrı izin vermek yerine grup bazında yönetim
- **Güvenlik:** Benzer roller için standart yetki setleri oluşturma
- **Organizasyon:** Departman, proje veya fonksiyon bazında kullanıcı gruplandırması

### İzin Kontrolü Mantığı

**Özel Durumlar:**
- Dosya sahibi **özgür**, dosya **s207** grubunda, izinler: `-r---w---x`
- özgür kullanıcısı bu dosyada sadece **okuma** yapabilir
- Linux izin kontolünde **özelden genele** doğru gidilir ve ilk eşleşen kural uygulanır

---

## 6. Bağ Türleri ve İzinler

### Sembolik Bağ (Symbolic Link)
- Tüm yetkiler varsayılan olarak açıktır (`rwxrwxrwx`)
- Gerçek izin kontrolü hedef dosyada yapılır
- Sembolik bağa verilen izinler doğrudan hedef dosyaya uygulanır

### Sabit Bağ (Hard Link)  
- İki dosyanın da yetkileri aynıdır
- Birinin yetkisi değiştirildiğinde diğerinin yetkileri de değişir
- Aynı inode'u paylaşırlar

---

## 7. İzin Değiştirme Komutları

### Sahiplik Değiştirme

**Önemli:** Dosya sahipliği ve grubu değiştirme işlemi sadece **root** kullanıcısı tarafından yapılabilir.

```bash
# Dosya sahibini değiştirme
chown ozgur deneme.txt

# Dosya grubunu değiştirme  
chgrp uucp deneme.txt

# Sahip ve grubu aynı anda değiştirme
chown root:root deneme.txt
```

### İzin Değiştirme (chmod)

#### 1. Yöntem: Sembolik Mod
```bash
# Gruba yazma yetkisi verme
chmod g+w deneme.txt

# Kullanıcıdan okuma yetkisini alma
chmod u-r deneme.txt

# Others'a çalıştırma yetkisi verme
chmod o+x deneme.txt
```

#### 2. Yöntem: Mutlak Atama
```bash
# Others'a okuma+çalıştırma yetkisi verme
chmod o=rx deneme.txt

# Kullanıcıya sadece yazma yetkisi verme
chmod u=w deneme.txt

# Gruba sadece çalıştırma yetkisi verme
chmod g=x deneme.txt
```

#### 3. Yöntem: Sayısal Mod (Octal)
```bash
# Herkese tam yetki (rwx rwx rwx)
chmod 777 deneme.txt

# Sadece gruba tam yetki (--- rwx ---)
chmod 070 deneme.txt

# Sadece others'a tam yetki (--- --- rwx)
chmod 007 deneme.txt

# Yaygın izin kombinasyonları
chmod 755 file  # rwx r-x r-x (çalıştırılabilir dosya)
chmod 644 file  # rw- r-- r-- (normal dosya)
chmod 600 file  # rw- --- --- (sadece sahip)
```

---

## 8. Gelişmiş Dosya İşlemleri

### find -exec Kullanımı

`find -exec` komutu, bulunan dosya/dizinler üzerinde başka komutlar çalıştırmak için kullanılır.

```bash
# /etc altındaki sonu "rc" ile biten dosyaları mevcut dizine kopyalama
find /etc -type f -name "*rc" -exec cp {} ./ \;

# /root/etc altındaki tüm dizinlerin others yetksini rwx yapma
find /root/etc -type d -exec chmod o=rwx {} \;

# Belirli UID'ye sahip dosyaları bulup sahiplik değiştirme
find / -uid 1001 -exec chown ozgur:ozgur {} \;
```

**Sözdizimi Açıklaması:**
- `{}` : Bulunan dosyanın yerini tutar
- `\;` : Komutun sonunu işaretler
- Her bulunan dosya için komut tekrar çalıştırılır

### umask (Dosya Maskeleme)

**umask**, yeni oluşturulan dosya ve dizinlerin varsayılan izinlerini kontrol eder.

**Mantığı:**
```
Gerçek İzin = Başlangıç İzinleri - umask
```

**Varsayılan Başlangıç İzinleri:**
- Dosyalar: 666 (rw-rw-rw-)  
- Dizinler: 777 (rwxrwxrwx)

**Örnekler:**
```bash
# Mevcut umask değerini görme
umask

# umask 022 ile dosya oluşturma
# Dosya: 666 - 022 = 644 (rw-r--r--)
# Dizin: 777 - 022 = 755 (rwxr-xr-x)

# umask ayarlama
umask 077  # Sadece sahibe tam yetki
```

---

## 9. Kullanıcı Yönetimi İşlemleri

### Kullanıcı Oluşturma ve Silme

```bash
# Kullanıcı oluşturma
useradd ozgur

# Parola atama
passwd ozgur

# Kullanıcı silme
userdel ozgur

# Kullanıcı silme (ev dizini ile birlikte)
userdel -r ozgur
```

**Önemli:** `useradd` komutu dağıtımların varsayılan ayarlarına göre çalışır. Her dağıtımda ev dizini otomatik oluşturmayabilir.

### Kullanıcı Silme Sonrası Dosya Durumu

Kullanıcı silindiğinde:
- Kullanıcının ev dizini ve dosyaları sistemde kalır
- Sahiplik alanında kullanıcı adı yerine **UID numarası** görünür  
- Aynı UID'ye sahip yeni kullanıcı oluşturulursa, eski dosyaların sahibi olur

### Grup İşlemleri

```bash
# Kullanıcıyı gruba ekleme (-a: append, -G: groups)
usermod -aG grup_adı kullanıcı_adı

# Kullanıcının tüm gruplarını değiştirme
usermod -G grup1,grup2 kullanıcı_adı
```

---

## 10. Kullanıcı Geçiş Komutları

### su vs sudo Farkları

#### su (Switch User)
- Geçiş yapılacak kullanıcının parolasını ister
- Varsayılan olarak root'a geçiş yapar

#### sudo (Switch User Do)  
- Mevcut kullanıcının parolasını ister
- `-u` parametresi ile başka kullanıcı belirtebilirsiniz
- Varsayılan hedef: root

### Çevresel Değişken Farkları

```bash
# 1. Yöntem: Çevresel değişkenleri almaz
su nano

# 2. Yöntem: Çevresel değişkenleri alır (login shell)
su -l nano
# veya
su - nano

# 3. Yöntem: sudo ile geçiş
sudo -u nano bash
```

**Farklar:**
- **1. yöntem:** .bashrc, .profile gibi dosyaları yüklemez, PATH ve diğer değişkenler eksik kalabilir
- **2. yöntem:** Tam login shell simülasyonu, tüm çevresel ayarları yükler  
- **3. yöntem:** sudo'nun kendi güvenlik politikalarını uygular

---

## 11. Servis Kullanıcıları ve Güvenlik

### Servisler Nasıl Çalışır?

Servisler dosya sistemi yetkilendirmelerini kullanarak çalışır. Bu nedenle:

1. **Servis kullanıcısı** oluşturulur
2. Servis bu kullanıcı ile çalıştırılır  
3. Gerekli dosyalara bu kullanıcı için izinler verilir
4. Servis sadece yetkilendirildiği dosyalara erişebilir

**Örnek:** Web sunucusu `www-data` kullanıcısı ile çalışır ve sadece web dosyalarına erişim izni vardır.

---

## 12. Gelişmiş Dosya Yetkileri (ACL)

### ACL (Access Control Lists)

Standard Linux izin sistemi sadece sahip-grup-others ayrımı yapar. ACL ile daha detaylı izin yönetimi mümkündür.

```bash
# Belirli kullanıcıya dosyada izin verme
setfacl -m u:kullanici:rw dosya_adi

# Belirli gruba izin verme  
setfacl -m g:grup:rx dosya_adi

# ACL izinlerini görüntüleme
getfacl dosya_adi

# ACL izinlerini kaldırma
setfacl -x u:kullanici dosya_adi
```

**ACL Göstergesi:**
Dosya listesinde ACL bulunan dosyaların sonunda `+` işareti görünür:
```
-rw-rw-r--+ 1 user group size date filename
```

---

## 13. Sistem Süreçleri ve /proc Dizini

### /proc Dizini
- Çalışan süreçlerin anlık bilgilerini içerir
- `/proc/<PID>/fd/` altında sürecin açık dosya tanımlayıcıları listelenir
- Bu dosyalar gerçek dosyalar değil, sanal bağlantılardır
- Süreç bittiğinde `/proc/<PID>` tamamen kaybolur

### Dosya Kurtarma İşlemi Hatası

find komutunda `/proc` dizininde şu hatayı alabilirsiniz:
```
find: '/proc/15307/fd/5' no such file or directory
```

**Sebebi:** 
- find komutu dosyayı buluyor ama erişmeden önce süreç sonlandığı için dosya kayboluyor
- /proc anlık bilgiler verdiği için dosyaları sürekli değişir

---

## 14. Parola Değiştirme

### Kullanıcıların Kendi Parolasını Değiştirmesi

Normal kullanıcılar kendi parolalarını `passwd` komutu ile değiştirebilirler:

```bash
# Mevcut kullanıcının parolasını değiştirme
passwd

# Sistem süreci:
# 1. Mevcut parola sorulur (güvenlik için)
# 2. Yeni parola sorulur  
# 3. Parola tekrarı istenir
# 4. PAM modülleri parola kalitesini kontrol eder
# 5. Başarılı ise /etc/shadow dosyası güncellenir
```

**Bilgi:** Kullanıcı sadece kendi parolasını değiştirebilir. Başka kullanıcıların parolasını değiştirmek için root yetkisi gerekir.

---

## 15. Arşiv ve Metadata İşlemleri

### tar Komutu ve Metadata

**tar** komutu dosyaları arşivlerken metadata bilgilerini (izinler, sahiplik, zaman damgaları) de korur:

```bash
# Metadata ile arşivleme
tar -czf arsiv.tar.gz /path/to/files

# Metadata ile açma (root olarak)
tar -xzf arsiv.tar.gz

```

**Sunucular Arası Taşıma:**
- **Root ile açılırsa:** Orijinal UID/GID korunur, aynı UID'ye sahip kullanıcı varsa sahiplik ona geçer
- **Normal kullanıcı ile açılırsa:** Sahiplik açan kullanıcıya geçer (yetki kısıtlaması nedeniyle)

---

## 16. Ödev (Kendi Çözümümü Ekledim)
# Linux’ta Kullanıcıyı Sadece Belirli Bir Komutla Sudo Kullanacak Şekilde Kısıtlama

Linux sistemlerinde bir kullanıcıya **sadece "ls -1 /tmp" komutunu `sudo` ile çalıştırma** yetkisi verebilir, diğer tüm `sudo` komutlarını engelleyebilirsin. Bu işlem **`sudoers` dosyası** üzerinden yapılır.  

---

### 16.1. sudoers Dosyasını Aç
```bash
sudo visudo
```

---

### 16.2. Kullanıcıya Özel Kural Ekle
Diyelim kullanıcı adı **odev** olsun. Dosyanın sonuna şunu ekle:  

```bash
odev ALL=(ALL) /bin/ls -1 /tmp
```

📌 Açıklama:  
- `odev` → kuralın geçerli olduğu kullanıcı  
- `ALL=(ALL)` → odev, herhangi bir kullanıcı (root dahil) adına çalıştırabilir  
- `/bin/ls -1 /tmp` → sadece bu komuta izin verilir  

> **Not:** `which ls` ile `ls` komutunun tam yolunu öğren. Çoğu sistemde `/bin/ls` ya da `/usr/bin/ls` olur.  

---

### 16.3. Test Senaryosu

```bash
$ pwd
/tmp
```

### ❌ Yetkisiz Komut Denemesi
```bash
$ sudo cat hebe
[sudo] password for odev: 
Sorry, user odev is not allowed to execute '/usr/bin/cat hebe' as root on main.
```

### ✅ İzin Verilen Komut
```bash
$ sudo ls -1 /tmp
[sudo] password for odev: 
deneme.txt
hebe
snap-private-tmp
systemd-private-f2155867899944f1b9d0aad858b5064b-polkit.service-v84trP
systemd-private-f2155867899944f1b9d0aad858b5064b-systemd-logind.service-jvWfoM
systemd-private-f2155867899944f1b9d0aad858b5064b-systemd-resolved.service-8LrCRz
systemd-private-f2155867899944f1b9d0aad858b5064b-systemd-timesyncd.service-AaKDFD
```

### ❌ Başka Bir sudo Komutu Denemesi
```bash
$ sudo touch test /tmp
Sorry, user odev is not allowed to execute '/usr/bin/touch test /tmp' as root on main.
```

---

### 16.4. Özet
- Kullanıcı `odev` sadece `sudo ls -1 /tmp` komutunu çalıştırabilir.  
- Başka hiçbir `sudo` komutu çalıştıramaz.  
- Böylece en az yetki prensibi uygulanmış olur.

---

## 17. Komut Referansları

| Kategori | Komut | Açıklama | Parametreler |
|----------|-------|----------|--------------|
| **Zaman Yönetimi** | `date` | Tarih ve saati gösterir/ayarlar | `-s "tarih saat"` |
| **Zaman Yönetimi** | `timedatectl` | Detaylı zaman bilgisi ve ayarları | `set-time "tarih saat"`: zaman ayarlama |
| **Dosya Transfer** | `curl` | Dosya indirir, URL transfer eder | `-O`: uzak dosya adıyla kaydet`-o file`: belirtilen adla kaydet |
| **Dosya İşlemleri** | `install -d` | İzinleri belirleyerek dizin oluşturur | `-d`: dizin oluştur`-m mode`: izin ayarla`-o owner`: sahip ayarla`-g group`: grup ayarla |
| **Shell** | `source` | Dosyadaki komutları mevcut shell'de çalıştırır | `source file` veya `. file` |
| **Kullanıcı Bilgisi** | `whoami` | Mevcut kullanıcıyı gösterir | |
| **Grup Bilgisi** | `groups` | Kullanıcının gruplarını gösterir | `groups username`: belirli kullanıcı |
| **Sahiplik** | `chown` | Dosya sahibini değiştirir | `chown user file`: sahip değiştir`chown user:group file`: sahip+grup |
| **Grup** | `chgrp` | Dosya grubunu değiştirir | `chgrp group file`: grup değiştir`-R`: recursive |
| **İzinler** | `chmod` | Dosya izinlerini değiştirir | `u/g/o +/-/= r/w/x`: sembolik mod`755, 644, etc`: sayısal mod`-R`: recursive |
| **Dosya Arama** | `find` | Dosya ve dizin arama | `-type f/d`: dosya/dizin`-name "pattern"`: kullanıcı ID`-exec cmd {} \;`: bulunan dosyada komut çalıştır |
| **Maskeleme** | `umask` | Varsayılan dosya izin maskesi | `umask`: mevcut değer`umask 022`: yeni değer |
| **Kullanıcı Yönetimi** | `useradd` | Kullanıcı oluşturur | `useradd username` |
| **Parola** | `passwd` | Kullanıcı parolası değiştirir | `passwd`: kendi parolası`passwd username`: belirli kullanıcı |
| **Kullanıcı Silme** | `userdel` | Kullanıcı siler | `userdel username-r`: ev dizini ile birlikte |
| **Kullanıcı Düzenle** | `usermod` | Kullanıcı özelliklerini değiştirir | `-aG group`: gruba ekle`-G groups`: grup listesi değiştir |
| **Kullanıcı Geçişi** | `su` | Başka kullanıcıya geçiş | `su username`: kullanıcı geçişi`su -` veya `su -l`: login shell |
| **Sudo** | `sudo` | Yetki ile komut çalıştırma | `sudo command`: root yetkisiyle`sudo -u user command`: belirli kullanıcı`sudo -i`: root shell |
| **ACL** | `setfacl` | Gelişmiş dosya izinleri ayarlar | `-m u:user:perms file`: kullanıcı izni`-m g:group:perms file`: grup izni`-x u:user file`: izin kaldır |
| **ACL Görüntüleme** | `getfacl` | Dosya ACL bilgilerini gösterir | `getfacl filename` |
| **Arşivleme** | `tar` | Dosya arşivleme ve açma | `-c`: oluştur`-x`: aç |
