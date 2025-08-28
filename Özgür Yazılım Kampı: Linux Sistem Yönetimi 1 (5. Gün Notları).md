# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (5. Gün Notları - **Linux Sistem Yönetimi ve Paket Yöneticileri Rehberi**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin beşinci gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Bellek Yönetimi ve Out of Memory Durumu

### RAM Dolduğunda Ne Olur?

Sistemde RAM alanı dolmuşsa ve kullanılabilir swap alanı da yoksa **Out of Memory Killer (OOMKiller)** devreye girer. Bu mekanizma:

- RAM tüketimini analiz eder
- Sürecin sistem için kritik olup olmadığını değerlendirir
- Önem sıralamasını göz önünde bulundurur
- Belirlenen kriterlere göre süreçleri zorla sonlandırır (kill eder)
- Tüm işlemlerini `/var/log` dizini altında loglar

---

## 2. Paket Yöneticileri

### Paket Yöneticileri Neden Farklılaştı?

Farklı paket yöneticilerinin oluşmasındaki ana sebep, **paket hazırlama sürecinin zorluk derecesidir**. Her Linux dağıtımı kendi paketlerini hazırlarken büyük emek harcadığı için, bu süreçte kendi ihtiyaçlarına uygun paket yöneticilerini geliştirmeyi tercih etmişlerdir. Ayrıca her dağıtım farklı felsefe ve yaklaşımlar benimser:

- **Güvenlik odaklı yaklaşım**: Bazı dağıtımlar paket imzalama ve doğrulama konusunda daha katı kurallar uygular
- **Kullanım kolaylığı**: Grafik arayüz destegi, otomatik bağımlılık çözümü gibi özellikler
- **Performans optimizasyonu**: Hız ve kaynak kullanımı odaklı çözümler

### Debian Tabanlı Paket Yöneticileri

#### dpkg vs apt

**dpkg:**
- Paket deposuna bağlanamaz veya depoları bulamaz
- Depodan önceden çekilmiş paketi verirseniz tüm işlemleri yapar
- Paket deposunun ne olduğunu bilmez, paketi kendimiz vermeliyiz
- Alt seviye paket yöneticisidir

**apt:**
- Paket depolarına bağlanabilir
- Güncelleme, bağımlılık çözümü gibi işlemleri otomatik yapabilir
- dpkg'nin üzerine inşa edilmiştir
- Üst seviye paket yöneticisidir

**Tarihsel Gelişim:**
Başlangıçta sadece dpkg vardı ve manuel olarak kullanılıyordu. Zamanla paket deposu kavramı hayatımıza girince, dpkg'yi geliştirmek yerine daha kullanışlı olan apt geliştirildi.

#### Paket Formatları
- **deb:** Debian paket uzantısı
- **Neden İhtiyaç Duyarız:** Paket yönetimini standartlaştırmak ve bağımlılıkları otomatik çözmek için

### Red Hat Tabanlı Paket Yöneticileri

- **rpm:** dpkg'nin Red Hat tarafındaki eşdeğeri (alt seviye)
- **dnf:** apt'nin Red Hat tarafındaki eşdeğeri (üst seviye)
- **yum:** dnf'in eski ismi, Red Hat 8 öncesinde kullanılırdı, sonra dnf'e dönüştü

### Diğer Paket Yöneticileri

#### pacman (Arch Linux)
- Arch Linux'un paket yöneticisi
- Hem Red Hat hem Debian tabanlı sistemlerin paket formatlarını destekler
- Kaynak kodundan derleme yapmaz (bu özelliği emerge ve pisi'de bulunur)

#### zypper (openSUSE)
- SuSE dağıtımının paket yöneticisi
- Arka planda rpm kullanır (Red Hat'in paket formatı)
- Paket formatı yazmakla uğraşmamış, doğrudan dağıtım için geliştirilmiş

#### emerge (Gentoo)
- Gentoo'nun paket yöneticisi
- 2005 yılında çok popülerdi
- Kullanıcıların çekirdek derlemesi yapabilmesine olanak sağlıyordu
- Uygulamanın kaynak dosyasından paket oluşturabilir
- Kaynak kodundan derleme özelliği sunar

#### pisi (Eski Pardus)
PiSi (açılımı "Packages Installed Successfully as Intended"), 2011.2 sürümüne kadar Pardus'un, güncel olarak da Pisi Linux ve Solus'un paket yöneticisidir.

**Özellikler:**
- Bağımlılıkları takip ederek paket inşa etme
- Paket kurma, kaldırma, yükseltme işlevleri
- Grafik arayüz desteği
- Kaynak kodundan derleme özelliği

#### yaourt (Arch)
**yaourt** (Yet AnOther User Repository Tool) Arch Linux için geliştirilmiş bir AUR (Arch User Repository) yardımcısıdır. AUR'dan paket kurulumunu kolaylaştırır ancak artık resmi olarak desteklenmez ve yerini yay, paru gibi araçlar almıştır.

---

## 3. Paket Kurulumu ve Yönetimi

### Kurulum Süreci Analizi

#### Terminal Çıktısı Analizi
```
Do you want to continue? [Y/n]
```

**Neden Y büyük, n küçük?**
Herhangi bir durumda iki seçenek sunulduğunda büyük harfle yazılan **varsayılan değer**dir. Hiçbir şey girmeden Enter'a basarsanız, büyük harfli seçenek otomatik olarak kabul edilir.

#### Paket İçerik Analizi

**dpkg -L paket_adı:** Yüklenen paketin dosyalarının nerede tutulduğunu gösterir.

**Örnek Çıktı:**
```
root@main:~# dpkg -L doas
/.
/usr
/usr/share
/usr/share/doc
/usr/share/doc/doas
/usr/share/doc/doas/copyright
/usr/share/doc/doas/changelog.Debian.gz
```

### Özel Dizinler ve Kavramlar

#### .d Uzantılı Dizinler
`.d` uzantılı dizinler ana ayar dosyasına otomatik eklenir. Bu modüler yapı sayesinde farklı uygulamalar kendi ayar dosyalarını bu dizinlere bırakabilir.

#### PAM (Pluggable Authentication Modules)
**PAM**, Linux dağıtımlarının çoğunun kullandığı doğrulama sistemidir. Kullanıcı doğrulaması yapar ve modüler bir yapıya sahiptir, bu sayede farklı doğrulama yöntemleri entegre edilebilir.

#### /usr/share/lintian
**lintian**, Debian paketlerinin kalite kontrolü için kullanılan bir araçtır. Paketlerin Debian politikalarına uygun olup olmadığını kontrol eder ve olası sorunları tespit eder.

---

## 4. Sistem İşlemleri ve Kavramlar

### Transaction (İşlem Bütünlüğü)
**Transaction**, ya işlemlerin tamamının başarıyla yapılacağı ya da hiçbirinin yapılmayacağı prensibiyle çalışan işlem yönetim sistemidir. Tüm işlemler tek bir birim gibi ele alınır ve sistem tutarlılığı korunur.

### Cache Kavramı
**Cache** (önbellek), sıkça erişilen verilerin daha hızlı erişim için geçici olarak saklandığı alandır. Paket yöneticileri indirilen paketleri cache'de tutar, böylece aynı paketin yeniden indirilmesine gerek kalmaz ve işlemler hızlanır.

**Bilgi:** Ping ve Uzaklık Ölçümü
İnternette sunucunun size uzaklığını ölçmek veya yakındaki sunucudan veri çekmek için aralarındaki mesafeyi ölçmek amacıyla ping atılır. Gidiş/dönüş süresine bakarak ağ gecikmesi ve bağlantı kalitesi hakkında bilgi edinilir.

---

## 5. Paket Güncelleme İşlemleri

### apt update vs apt upgrade
- **apt update:** Yalnızca depodan güncel indeksi çeker, paketleri güncellemez
- **apt upgrade:** Güncellenen indekse göre yükseltme işlemi yapar

### Yüklenen Paket Geçmişi
Yüklediğimiz paketin adını veya onunla birlikte hangi paketlerin yüklendiğini görmek için:

```bash
cat /var/log/apt/history.log
```

**Örnek Çıktı:**
```
Start-Date: 2025-08-27  08:25:14
Commandline: apt install doas
Requested-By: ubuntuvm (1000)
Install: opendoas:amd64 (6.8.2-1, automatic), doas:amd64 (6.8.2-1)
End-Date: 2025-08-27  08:25:14
```

### Paket Güncelleme Süreci

**Güncellenebilir Paketlerin Listesi:**
- `apt list --installed`: Kurulu olan tüm paketlerin listesi
- `apt list | grep -v installed`: Yüklenebilir tüm paketlerin listesi (kurulu olanlar hariç)

**Güncelleme Süreci Detayları:**
1. **Ayar Dosyası Kontrolü:** `/var/lib/dpkg/info/paket_adi.conffiles`
2. **Hash Kontrolü:** Kurulum sırasında ayar dosyasının hash'i saklanır
3. **Karşılaştırma:** Güncelleme sırasında mevcut dosya hash ile karşılaştırılır
4. **Karar Verme:**
   - Ayar dosyası değişmemişse: Yenisi üzerine yazılır
   - Değişmişse: dnf-rpm sisteminde .rpmnew uzantılı dosya oluşturulur
5. **Uygulama Dosyaları:** Diğer dosyalar silinir ve yeni paket yüklenir
6. **Process Yönetimi:** Uygulama çalışıyorsa restart edilmeden güncel versiyona geçmez

---

## 6. Dosya Sistemi ve Process Yönetimi

### Silinen Dosya Kurtarma

**Durum:** Kullanımda olan bir dosyayı sildiğimizde, işleyen process sona erene kadar dosyayı görmeye/işlemeye devam ederiz.

**Örnek:** Bir mp3 dosyasını açtığımızda, mp3 player dosya inode'una işaret ettiği için, mp3 player de aynı inode'a işaret eder. Mp3 player process'i sonlandırılana kadar mp3 dosyasına erişmeye devam ederiz.

**Kurtarma Yöntemi:**
1. **Process Bulma:**
   ```bash
   lsof | grep 03.mp3
   ps aux | grep mp3
   ```

2. **Process Detayları:**
   ```bash
   cd /proc/[process_numarası]/fd
   ```
   
3. **Dosya Kurtarma:** Bu dizinde silinen dosya "deleted" olarak görünür. Bu dosyayı başka bir yere kopyalarsak silinen dosya kurtarılmış olur.

**Bilgi:** Taşıma işlemi = Kopyalama + Silme

---

## 7. Depot Yönetimi ve Güvenlik

### Depo Pasifleştirme
Debian'da ekli paket depolarından istediğimizi pasif hale getirmek için:
`/etc/apt/sources.list` dosyasında pasif hale getirmek istediğimiz deponun başına `#` işareti eklenir.

### GPG İmzalama ve Doğrulama
**GPG (GNU Privacy Guard):**
- Paket yöneticileri paket kaynağının doğruluğunu ve dosya bütünlüğünü kontrol eder
- Paket geliştirici paketleri imzalar
- Kullanıcı tarafında GPG bu imzayı doğrular
- Yalnızca paket depoları için değil, herhangi bir merkez tarafından gönderilen içerikler için de güvenli kaynak kontrolü yapar

### Firmware Yönetimi
**Firmware Nedir:**
- Eskiden donanım cihazları üzerinde kendi yazılımları bulunuyordu
- Donanımın kendi üzerindeki yazılıma firmware denir
- Firmware güncellemesi sırasında hata veya kesinti donanımı kullanılamaz hale getirebilir

**non-free-firmware:**
- Normalde donanım üzerine kurulan yazılımların güncelleme işlemini kolaylaştırmak için işletim sistemine kurulur
- Özgür yazılım tartışmalarında önemli bir konu

**Önemli Not:** Uygulama veya donanım firmware nedeniyle hata alırsa, paket yöneticisinde aramak yerine o firmware'i internetten araştırmak gerekir.

---

## 8. Komut Referansları

| Kategori | Komut | Sistem | Açıklama | Parametreler |
|----------|-------|--------|----------|--------------|
| **Sistem Tanılama** | `ps` | Tümü | Çalışan süreçlerin listesini verir | `aux`: detaylı liste |
| **Sistem Tanılama** | `lsof` | Tümü | Açık dosyaları listeler | `\| grep <dosya>`: belirli dosya ara`-p <PID>`: process ID'ye göre filtrele |
| **Paket Yönetimi** | `apt install <paket>` | Debian/Ubuntu | Paket kurar | `-y`: onay almadan kur`|
| **Paket Yönetimi** | `apt update` | Debian/Ubuntu | Paket listelerini günceller | |
| **Paket Yönetimi** | `apt upgrade` | Debian/Ubuntu | Kurulu paketleri yükseltir | |
| **Paket Yönetimi** | `apt list` | Debian/Ubuntu | Paketleri listeler | `--installed`: kurulu paketler<br>`--upgradeable`: güncellenebilir paketler |
| **Paket Yönetimi** | `apt autoremove` | Debian/Ubuntu | Gereksiz paketleri kaldırır | |
| **Paket Yönetimi** | `dpkg -L <paket>` | Debian/Ubuntu | Paketin dosyalarını listeler | |
| **Paket Yönetimi** | `dpkg -l` | Debian/Ubuntu | Kurulu paketleri listeler | |
| **Paket Yönetimi** | `dnf install <paket>` | Red Hat/CentOS | Paket kurar | `-y`: onay almadan kur |
| **Paket Yönetimi** | `dnf update` | Red Hat/CentOS | Paketleri günceller | |
| **Paket Yönetimi** | `dnf list` | Red Hat/CentOS | Paketleri listeler | `installed`: kurulu paketler |
| **Paket Yönetimi** | `rpm -qa` | Red Hat/CentOS | Kurulu RPM paketlerini listeler | |
| **Paket Yönetimi** | `rpm -ql <paket>` | Red Hat/CentOS | RPM paketinin dosyalarını listeler | |
| **Dosya İşlemleri** | `cat <dosya>` | Tümü | Dosya içeriğini görüntüler | |
| **Dosya İşlemleri** | `grep` | Tümü | Metin arama | `-v`: tersini ara`-i`: büyük/küçük harf duyarsız |
| **Dosya İşlemleri** | `cd <dizin>` | Tümü | Dizin değiştir | |
| **Log ve Geçmiş** | `cat /var/log/apt/history.log` | Debian/Ubuntu | APT işlem geçmişini gösterir | |
| **Log ve Geçmiş** | `cat /var/lib/dpkg/info/<paket>.conffiles` | Debian/Ubuntu | Paket ayar dosyalarını gösterir | |
| **Process Yönetimi** | `ps aux \| grep <arama>` | Tümü | Belirli süreçleri arar | |
| **Process Yönetimi** | `lsof \| grep <dosya>` | Tümü | Belirli dosyayı kullanan süreçleri bulur | |
| **Process Yönetimi** | `cd /proc/<PID>/fd` | Tümü | Sürecin açık dosya tanımlayıcılarını gösterir | |
