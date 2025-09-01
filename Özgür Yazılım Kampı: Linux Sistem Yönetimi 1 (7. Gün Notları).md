# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (7. Gün Notları - **Linux Dosya Sistemi ve Disk Yönetimi Rehberi**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin yedinci gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Süreç (Process) Yönetimi

### 1.1 ps Komutu
`ps` komutu sistemde çalışan süreçlerle işlem yapmak için kullanılır. Tek başına çalıştırıldığında mevcut kullanıcının bulunduğu session'da başlattığı süreçleri listeler.

### 1.2 ps aux Komutu
`ps aux` komutu sistemdeki tüm süreçleri ayrıntılı şekilde listeler. Çıktı formatı şu şekildedir:

```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.5  0.3  21852 12844 ?        Ss   06:37   0:00 /sbin/init
root           2  0.0  0.0      0     0 ?        S    06:37   0:00 [kthreadd]
```

**Sütun açıklamaları:**
- **USER**: Süreci çalıştıran kullanıcı
- **PID**: Süreç numarası (Process ID)
- **%CPU**: Sürecin kullandığı CPU yüzdesi
- **%MEM**: Sürecin kullandığı RAM yüzdesi
- **VSZ**: Virtual Set Size - Sürecin sanal bellek kullanımı (KB)
- **RSS**: Resident Set Size - Sürecin fiziksel bellekte kapladığı alan (KB)
- **TTY**: Sürecin bağlı olduğu terminal (? işareti terminale bağlı olmadığını gösterir)
- **STAT**: Sürecin durumu (R=Running, S=Sleeping, Z=Zombie, vb.)
- **START**: Sürecin başlatılma zamanı
- **TIME**: Sürecin gerçekte harcadığı işlemci zamanı
- **COMMAND**: Çalıştırılan komut ve parametreleri

**Not:** Köşeli parantez [ ] ile gösterilen süreçler kernel space'de çalışan çekirdek süreçleridir. Köşeli parantez olmayan süreçler ise kullanıcı tarafından çalıştırılan user space süreçleridir.

### 1.3 top Komutu
`top` komutu sistemin anlık durumunu ve süreçleri canlı olarak gösterir:

```
top - 09:59:33 up 23 min,  2 users,  load average: 0.31, 0.28, 0.25
Tasks: 326 total,   2 running, 324 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.0 us,  1.3 sy,  0.0 ni, 96.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem :  15114.1 total,   9053.9 free,   4146.5 used,   2975.5 buff/cache     
MiB Swap:   4096.0 total,   4096.0 free,      0.0 used.  10967.5 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND  
   2864 mirzasak  20   0 4645940 341676 165844 S  14.3   2.2   1:59.57 gnome-s+ 
   4478 mirzasak  20   0 2905188 184544 124420 S   9.6   1.2   0:34.40 gnome-t+ 
   5235 mirzasak  20   0 3580788 622452 514792 S   5.3   4.0   1:40.58 Virtual+
```

## 2. Süreç Önceliklendirme

### 2.1 Nice ve Priority
- **PR (Priority)**: Çekirdeğin süreç için belirlediği öncelik değeri
- **NI (Nice)**: Kullanıcının belirtebileceği öncelik değeri
- **Nice değerleri**: -20 ile +19 arasında değer alır
  - Ne kadar küçük olursa süreç o kadar yüksek öncelikli olur
  - Ne kadar büyük olursa süreç o kadar düşük öncelikli olur

### 2.2 nice ve renice Komutları
- **nice**: Yeni bir süreci belirli bir nice değeriyle başlatmak için kullanılır
- **renice**: Çalışan bir sürecin nice değerini değiştirmek için kullanılır

**Örnek:**
```bash
nice -n 10 kommut    # Komutu nice değeri 10 ile başlat
renice 5 1234        # PID 1234'ün nice değerini 5 yap
```

### 2.3 pstree Komutu
`pstree` komutu çalışan süreçlerin ağaç (tree) yapısındaki dizilimini gösterir. Bu sayede hangi sürecin hangi sürecin alt süreci olduğunu görebiliriz.

## 3. Sistem Başlatma Süreci

### 3.1 Boot Süreci
1. **BIOS**: Bilgisayar açıldığında güç tuşundan elektrik sinyali verilir ve anakart üzerinde bulunan firmware olan BIOS çalışır
2. **Bootloader Arama**: BIOS yazılım aramaya başlar, ilk olarak MBR'ye sonra diskin kalan kısımlarında tarama yapar ve bootloader yazılımını (GRUB) bulur
3. **GRUB**: Hangi işletim sistemini açacağına /boot dizininden bakar
4. **initramfs**: GRUB açılırken initramfs adında bir image çalıştırır (mini bir Linux sistemi)
5. **Dosya Sistemi**: initramfs dosya sistemini bağlayıp asıl Linux çekirdeğini başlatır
6. **systemd**: Linux çekirdeği systemd'yi çalıştırır ve sistem yönetimini ona bırakır

### 3.2 /boot Dizini
/boot dizininin ayrı bir bölümde bulunmasının nedeni, GRUB'ın okuyabileceği bir dosya formatında oluşturmak istememizdir. GRUB içinde /boot dizininin dosya formatını okuyacak komutlar bulunur.

## 4. systemd Sistem Yöneticisi

### 4.1 systemd Nedir?
Systemd, Linux işletim sisteminin temel bileşenlerinden biri olan bir init sistemi ve sistem yöneticisidir. Geleneksel SysVinit ve diğer init sistemlerinin yerini almak için geliştirilmiştir. Sistem açılışından kapanışına kadar tüm sistem süreçlerini ve servislerini yönetir.

### 4.2 systemd Target
systemd target'ları, sistemin belirli bir duruma gelmesi için gerekli olan unit'lerin gruplandırılmasıdır. Eski runlevel sisteminin modern karşılığıdır. Örneğin graphical.target grafik arayüzlü sistem, multi-user.target ise çok kullanıcılı sistem durumunu ifade eder.

### 4.3 systemd Service
systemd service'leri, sistemde çalışan hizmetleri yöneten unit dosyalarıdır. Her servis için bir .service dosyası bulunur ve bu dosyalar servisin nasıl başlatılacağı, durdurulacağı ve yönetileceğini tanımlar.

### 4.4 systemctl Komutları
systemd'yi yönetmek için `systemctl` komutu kullanılır:

```bash
systemctl start firewalld      # Servisi başlat
systemctl stop firewalld       # Servisi durdur
systemctl restart firewalld    # Servisi yeniden başlat (stop+start)
systemctl status firewalld     # Servis durumunu göster
systemctl enable firewalld     # Sistem açılışında otomatik başlat
systemctl disable firewalld    # Otomatik başlatmayı kapat
systemctl edit firewalld       # Servis yapılandırmasını düzenle
```

### 4.5 Açılışta Çalışan Servisleri Listeleme
```bash
systemctl list-unit-files --type=service --state=enabled
```

### 4.6 systemd Konfigürasyon Dizinleri
- **/usr/lib/systemd/system**: Orijinal systemd unit dosyaları
- **/etc/systemd/system**: Kullanıcı tarafından özelleştirilen ayarlar (/usr/lib/systemd/system'deki ayarları ezer)

## 5. Süreç Sonlandırma ve Sinyal Yönetimi

### 5.1 kill Komutu
`kill` komutu süreçlere sinyal göndermek için kullanılır. Varsayılan olarak SIGTERM (15) sinyali gönderir.

```bash
kill PID                # SIGTERM sinyali gönder (nazikçe kapat)
kill -9 PID             # SIGKILL sinyali gönder (zorla kapat)
kill -l                 # Mevcut sinyalleri listele
```

### 5.2 Klavye Sinyalleri
- **Ctrl+C**: SIGINT (2) sinyali - Süreci sonlandır
- **Ctrl+Z**: SIGSTOP (19) sinyali - Süreci duraksat (pause)

### 5.3 Önemli Sinyaller
- **SIGHUP (1)**: Sürecin ayar dosyasını yeniden okumasını sağlar (reload işlemi)
- **SIGTERM (15)**: Süreci nezakitle sonlandırır
- **SIGKILL (9)**: Süreci zorla sonlandırır
- **SIGSTOP (19)**: Süreci duraklatır

## 6. İş (Job) Yönetimi

### 6.1 jobs Komutu
`jobs` komutu mevcut bash shell'de çalışan süreçleri listeler. `ps` komutundan farkı, sadece mevcut shell'in sorumluluğundaki süreçleri gösterir.

### 6.2 Arka Plan ve Ön Plan İşlemleri
```bash
kommut &                # Komutu arka planda çalıştır
bg %1                   # 1 numaralı job'ı arka planda devam ettir
fg %1                   # 1 numaralı job'ı ön plana getir
nohup kommut &          # Terminal kapandığında bile çalışmaya devam et
disown %1               # Job'ın sorumluluğunu shell'den systemd'ye aktar
```

### 6.3 Terminal Çoklayıcıları
Screen ve tmux gibi komutlar terminal multiplexer (çoklayıcı) olarak adlandırılır. Aynı terminal içinde birden fazla session açma ve yönetme imkanı sağlar.

## 7. Daemon Süreçleri

### 7.1 Daemon Nedir?
Daemon, Linux'ta arka planda çalışan ve doğrudan kullanıcı müdahalesi olmaksızın belirli işlemler gerçekleştiren veya hizmetler sağlayan süreçlerdir. Bu süreçler herhangi bir kontrol terminaline bağımlı olmaksızın sürekli çalışır. Genellikle sistem seviyesinde görevler yerine getirirler.

**Özellikleri:**
- Arka planda çalışır
- Terminale bağımlı değildir
- Sistem açılışından kapanışına kadar çalışabilir
- Sistem servisleri olarak görev yapar

## 8. Log Yönetimi

### 8.1 syslogd
System logger servisi. Sistem loglarını toplar, kategorize eder ve dosyalara yazar. Logları sadece yerel makineye değil, uzak makinelere de gönderebilir.

### 8.2 journalctl
`journalctl` komutu systemd'nin journald servisinin loglarını görüntüler. Varsayılan olarak sistem açılışından itibaren tüm logları tutar.

### 8.3 logger Komutu
`logger` komutu ile manuel olarak syslog mesajı oluşturup rsyslog'a veya journald'ye gönderebilirsiniz.

### 8.4 logrotate
Log dosyalarının boyutlarını kontrol etmek için kullanılan systemd servisi. Logları belirli aralıklarla (günlük, aylık, yıllık) döndürerek arşivler ve eski logları siler.

## 9. Süreç Hiyerarşisi ve Yönetimi

### 9.1 systemd ve Süreç Hiyerarşisi
Tüm süreçler systemd altında çalışır. Bir süreç başka bir süreci başlattığında fork işlemi gerçekleşir. Ana süreç sonlandırıldığında:
- Alt süreçler ana sürece bağımlıysa sonlanır
- Bağımsızsa systemd altında çalışmaya devam edebilir

### 9.2 sleep Komutu
Belirtilen süre kadar bekleme yapar:
```bash
sleep 300; kommut    # 5 dakika (300 saniye) bekle, sonra komutu çalıştır
```

## 10. Log Mesaj Sistemi

### 10.1 logger Komutu
Özel log mesajları oluşturmak ve sistem log'una göndermek için kullanılır:
```bash
logger "Bu bir test mesajıdır"
```

### 10.2 rsyslog
Gelişmiş sistem log yöneticisi. Logları kategorize eder, uzak sunuculara gönderebilir ve çeşitli filtreleme işlemleri yapabilir.

## 11. Zamanlanmış Görevler

### 11.1 at/atd
Tek seferlik çalıştırılacak komutlar için kullanılır. Periyodik tekrarlayan görevler için değildir.

### 11.2 cron/crontab
Periyodik olarak tekrarlanan görevler için kullanılır.

**Format:**
```
*  *  *  *  * user-name command
```

**Sıralama:** dakika saat gün ay haftanın_günü

**Örnek:**
```bash
0 3 * * * root /usr/bin/yedekle.sh    # Her gün saat 03:00'te yedekleme yap
13 13 13 * fri                        # 13. günün 13:13'ünde ve Cuma gününde (çok özel durum)
```

**Not:** Tüm koşulları aynı anda sağlaması gerekir. Yukarıdaki örnek sadece 13. günün Cuma'ya denk geldiği durumlarda çalışır.

## 12. Network (Ağ) Yönetimi

### 12.1 Temel Network Kavramları

**TCP (Transmission Control Protocol):** Paketi karşı tarafa gönderirken, emin olana kadar (acknowledgment alana kadar) gönderim yapmaya devam eder. Güvenilir veri iletimi sağlar.

**UDP (User Datagram Protocol):** Paketi gönderir, karşı tarafın alıp almadığını kontrol etmez. Hızlı ancak güvenilmez veri iletimi.

### 12.2 IP Protokolleri

**IPv4:** 32 bit adres sistemi. Yaklaşık 4.3 milyar farklı adres imkanı sunar. Örnek: 192.168.1.1

**IPv6:** 128 bit adres sistemi. IPv4'ün adres eksikliği problemini çözmek için geliştirilmiş. Çok daha fazla adres imkanı sunar. Örnek: 2001:0db8:85a3::8a2e:0370:7334

### 12.3 Network Adresleme

**MAC Adresi:** Ağ cihazlarının üreticisi tarafından belirlenen eşsiz donanım adresi. Fiziksel katmanda haberleşme için kullanılır.

**IP Adresi:** Network katmanında kullanılan mantıksal adres. DHCP ile otomatik olarak atanabilir.

**DHCP (Dynamic Host Configuration Protocol):** Ağdaki cihazlara otomatik olarak IP adresi, subnet mask, gateway ve DNS bilgileri atar.

### 12.4 Network Türleri

**LAN (Local Area Network):** Yerel ağ sistemi. Genellikle bir bina veya kampüs içindeki ağdır.

**VLAN (Virtual Local Area Network):** Fiziksel olarak aynı ağda olan cihazları mantıksal olarak farklı ağlara ayırma teknolojisi.

**WLAN (Wireless Local Area Network):** Kablosuz yerel ağ.

**WAN (Wide Area Network):** Geniş alan ağı. İnterneti de kapsayan büyük ağ sistemleri.

### 12.5 Network Yapılandırmaları

**NAT (Network Address Translation):** Private IP adreslerini public IP adreslerine dönüştürerek internete çıkış sağlar.

**Bridge Network:** Sanal makine doğrudan host ağından IP alır, sanki ayrı bir fiziksel makine gibi davranır.

**Host-Only Adapter:** Sadece host makine ve sanal makineler arasında iletişim sağlar. İnternete erişim yoktur.

**Subnet:** IP ağlarını bölümlere ayırmak için kullanılır. IP adresinin sonundaki /24 gibi değerler subnet mask'ını belirtir.

### 12.6 ping Komutu
`ping` komutu iki ağ cihazı arasında bağlantı testi yapmak için kullanılır. ICMP paketleri gönderir ve:
- İletişim olup olmadığını test eder
- Gidiş-dönüş süresini ölçer
- Paket kaybı olup olmadığını kontrol eder

## 13. Komut Referansı

### 13.1 Süreç Yönetimi Komutları

| Komut | Parametreler | Açıklama |
|-------|--------------|----------|
| `ps` | `-aux`, `-ef`, `-u kullanıcı` | Sistem süreçlerini listeler |
| `top` | `-u kullanıcı`, `-p PID` | Canlı süreç monitörü |
| `pstree` | `-p`, `-u` | Süreç ağacını gösterir |
| `kill` | `-SIGNAL PID`, `-l` | Süreçlere sinyal gönderir |
| `killall` | `-SIGNAL isim` | İsimle süreç sonlandırır |
| `nice` | `-n değer komut` | Komut öncelikle başlatır |
| `renice` | `değer PID` | Çalışan sürecin önceliğini değiştirir |
| `jobs` | `-l`, `-r`, `-s` | Shell job'larını listeler |
| `bg` | `%job_numarası` | Job'ı arka planda çalıştırır |
| `fg` | `%job_numarası` | Job'ı ön plana getirir |
| `nohup` | `komut &` | Terminal kapanınca bile çalışır |
| `disown` | `%job_numarası` | Job sorumluluğunu systemd'ye devreder |
| `sleep` | `süre[s,m,h]` | Belirtilen süre bekler |

### 13.2 systemd Yönetim Komutları

| Komut | Parametreler | Açıklama |
|-------|--------------|----------|
| `systemctl` | `start/stop/restart servis` | Servis durum yönetimi |
| `systemctl` | `enable/disable servis` | Otomatik başlatma ayarı |
| `systemctl` | `status servis` | Servis durumu görüntüleme |
| `systemctl` | `list-unit-files --type=service` | Servis dosyalarını listeler |
| `systemctl` | `edit servis` | Servis yapılandırması düzenler |
| `systemctl` | `reload servis` | Servisi yeniden yükler (SIGHUP) |

### 13.3 Log Yönetim Komutları

| Komut | Parametreler | Açıklama |
|-------|--------------|----------|
| `journalctl` | `-u servis`, `-f`, `-r` | systemd loglarını gösterir |
| `logger` | `-p facility.level "mesaj"` | Sistem log'una mesaj yazar |
| `logrotate` | `-f config_dosyası` | Log rotasyonu yapar |

### 13.4 Network Komutları

| Komut | Parametreler | Açıklama |
|-------|--------------|----------|
| `ping` | `-c sayı hedef` | Ağ bağlantısı test eder |
| `ip` | `addr show`, `route show` | IP ayarlarını gösterir |
| `netstat` | `-tuln`, `-r` | Ağ bağlantılarını listeler |
| `ss` | `-tuln` | Soket durumlarını gösterir |

### 13.5 Özel Karakterler ve Anlamları

| Karakter | Anlamı |
|----------|--------|
| `+` | Komut başında: root olarak çalıştır |
| `-` | Komut başında: hata verse de çalıştır |
| `?` | TTY sütununda: terminale bağlı değil |
| `[ ]` | Süreç adında: kernel space süreci |
| `&` | Komut sonunda: arka planda çalıştır |

### 13.6 Sinyal Listesi (kill -l)

| Sinyal | Numara | Açıklama |
|--------|--------|----------|
| SIGHUP | 1 | Ayar dosyasını yeniden oku |
| SIGINT | 2 | Klavyeden kesme (Ctrl+C) |
| SIGQUIT | 3 | Klavyeden çıkış (Ctrl+\\) |
| SIGKILL | 9 | Zorla sonlandır |
| SIGTERM | 15 | Nazikçe sonlandır |
| SIGSTOP | 19 | Duraklat (Ctrl+Z) |
| SIGCONT | 18 | Devam ettir |
