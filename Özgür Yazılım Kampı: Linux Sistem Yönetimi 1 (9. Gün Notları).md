# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (9. Gün Notları - **SSH Servisi**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin son gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

### SSH Nedir ve Nasıl Çalışır?

SSH, bilgisayarlara güvenli uzaktan erişim sağlayan bir protokoldür:
- **Port**: 22 (varsayılan)
- **Çalışma Şekli**: Root yetkisiyle çalışır çünkü başka kullanıcılar adına oturum açma yetkisi gerekir
- **Güvenlik**: Tüm iletişim şifrelenir

#### SSH Servis Durumu Kontrolü
```bash
systemctl status ssh
```
Bu komut SSH servisinin:
- Çalışıp çalışmadığını
- Port dinleme durumunu
- Son bağlantı loglarını gösterir

### Unix Socket Nedir?

Unix socket, işlemler arası iletişim (IPC) için kullanılan bir dosya türüdür:
- Linux'ta "her şey bir dosya" felsefesinin bir parçasıdır
- IP adresi ve port yerine dosya sistemi yolu kullanır
- Aynı makine üzerindeki süreçler arasında hızlı iletişim sağlar
- Ağ protokollerinden daha hızlı ve güvenlidir

**Örnek**: MySQL bazen TCP yerine Unix socket kullanır:
```
/var/run/mysqld/mysqld.sock
```

### SSH Güvenlik Mekanizmaları

#### 1. Kullanıcı Doğrulama
- Olmayan bir kullanıcıyla giriş denediğinde "parola hatalı" der (güvenlik için)
- Bu sayede saldırganlar hangi kullanıcıların sistemde olduğunu öğrenemez

#### 2. Host Doğrulama (known_hosts)
- İlk bağlantıda sunucunun parmak izini (fingerprint) kaydeder
- `~/.ssh/known_hosts` dosyasında saklar
- Sonraki bağlantılarda MITM (Man-in-the-Middle) saldırılarını önler
- Host doğrulaması yapılsa da parola istenmesinin sebebi çok katmanlı güvenlik

#### 3. SSH Anahtar Çiftleri
SSH anahtarları iki tipte gelir:
- **Özel Anahtar (Private Key)**: Sadece kullanıcının bilgisayarında kalır
- **Açık Anahtar (Public Key)**: Sunucuya kopyalanır

**Avantajları:**
- Paroladan çok daha güvenli
- Brute force saldırılarına karşı dayanıklı
- Otomasyon için gerekli

### SSH Anahtar Yönetimi

#### Açık Anahtarı Sunucuya Kopyalama
```bash
ssh-copy-id kullanici@192.168.56.104
```
Bu komut:
- Yerel açık anahtarı uzak sunucunun `~/.ssh/authorized_keys` dosyasına ekler
- Gerekli dizin ve dosya izinlerini ayarlar
- İlk kez çalıştırıldığında parola ister, sonrasında parolasız giriş sağlar

### Parolasız Giriş Neden Önemli?

1. **Otomasyon**: Script'ler ve cron job'lar manuel müdahale olmadan çalışabilir
2. **Güvenlik**: Parolalar ağ üzerinden gönderilmez
3. **Verimlilik**: Sürekli parola girme gerekliliği ortadan kalkar
4. **Batch İşlemler**: Birden fazla sunucuyla eşzamanlı işlem yapılabilir

## Dosya Transfer ve Senkronizasyon

### SCP (Secure Copy)
Güvenli dosya kopyalama protokolü:
```bash
# Yerel dosyayı uzak sunucuya kopyala
scp dosya.txt kullanici@sunucu:/hedef/dizin/

# Uzak sunucudan yerel bilgisayara kopyala
scp kullanici@sunucu:/kaynak/dosya.txt ./

# Dizin kopyalama (recursive)
scp -r /yerel/dizin/ kullanici@sunucu:/hedef/
```

**Özellikler:**
- SSH üzerinden çalışır (güvenli)
- Tek seferlik kopyalama
- Basit kullanım

### Rsync (Remote Sync)
Gelişmiş senkronizasyon aracı:

**Çalışma Prensibi:**
1. **İlk Çalışma**: Tüm dosyaları hedef konuma kopyalar
2. **Sonraki Çalışmalar**: Sadece değişen dosyaları aktarır (delta transfer)
3. **Checksumlar**: Dosya bütünlüğünü kontrol eder
4. **Sıkıştırma**: Ağ trafiğini azaltır

```bash
# Temel senkronizasyon
rsync -avz /kaynak/dizin/ kullanici@sunucu:/hedef/dizin/

# Silinen dosyaları da senkronize et
rsync -avz --delete /kaynak/ kullanici@sunucu:/hedef/
```

**Her İki Sunucuda Bulunmalı**: Rsync hem kaynak hem hedef sunucuda kurulu olmalı.

### SSHFS (SSH File System)
Uzak dosya sistemini yerel olarak mount etme:

```bash
# Mount işlemi
sshfs kullanici@sunucu:/uzak/dizin /yerel/mount/noktasi

# Unmount işlemi
fusermount -u /yerel/mount/noktasi
```

**Kullanım Alanları:**
- Uzak dosyalara yerel gibi erişim
- Geliştirme ortamları için ideal
- Gerçek zamanlı düzenleme

**Her İki Sunucuda Bulunmalı**: SSHFS hem istemci hem sunucu tarafında desteklenmelidir.

## SSH Yapılandırma Dosyaları

### İstemci Yapılandırması (~/.ssh/config)
```
Host webserver
    HostName 192.168.1.100
    User admin
    Port 22
    IdentityFile ~/.ssh/id_rsa_webserver

Host db-server
    HostName 10.0.0.50
    User dbadmin
    Port 2222
    LocalForward 3306 localhost:3306
```

### Sunucu Yapılandırması (/etc/ssh/sshd_config)
```
# Port değiştirme (güvenlik)
Port 2222

# Root girişini engelleme
PermitRootLogin no

# Parola girişini kapatma
PasswordAuthentication no

# Sadece belirli kullanıcılara izin
AllowUsers admin webmaster
```

## Komut Referansı

### SSH Komutları

| Komut | Parametreler | Açıklama |
|-------|-------------|----------|
| `ssh` | `user@host` | SSH bağlantısı kurar |
| `ssh` | `-p port user@host` | Belirli port üzerinden bağlanır |
| `ssh` | `-i keyfile user@host` | Belirli anahtar dosyası ile bağlanır |
| `ssh-keygen` | `-t rsa -b 4096` | RSA anahtar çifti oluşturur (4096 bit) |
| `ssh-keygen` | `-t ed25519` | Ed25519 anahtar çifti oluşturur (modern, hızlı) |
| `ssh-copy-id` | `user@host` | Açık anahtarı uzak sunucuya kopyalar |
| `ssh-add` | `~/.ssh/id_rsa` | Anahtarı SSH agent'a ekler |
| `ssh-add` | `-l` | Agent'ta yüklü anahtarları listeler |
| `systemctl status ssh` | - | SSH servis durumunu kontrol eder |

### Dosya Transfer Komutları

| Komut | Parametreler | Açıklama |
|-------|-------------|----------|
| `scp` | `file user@host:/path` | Dosyayı uzak sunucuya kopyalar |
| `scp` | `user@host:/path/file ./` | Uzak dosyayı yerel bilgisayara kopyalar |
| `scp` | `-r dir/ user@host:/path` | Dizini recursive olarak kopyalar |
| `scp` | `-P port` | Belirli port kullanır |
| `rsync` | `-avz source/ dest/` | Gelişmiş senkronizasyon yapar |
| `rsync` | `-a` | Archive modu (izinler, tarihler korunur) |
| `rsync` | `-v` | Verbose (detaylı çıktı) |
| `rsync` | `-z` | Sıkıştırma aktif eder |
| `rsync` | `--delete` | Hedefte fazla olan dosyaları siler |
| `rsync` | `--dry-run` | Sadece simülasyon yapar, değişiklik yapmaz |
| `rsync` | `-e "ssh -p port"` | Belirli SSH portu kullanır |
| `sshfs` | `user@host:/path /local/mount` | Uzak dizini yerel olarak mount eder |
| `fusermount` | `-u /mount/point` | SSHFS mount'unu kaldırır |

### Sistem ve Güvenlik Komutları

| Komut | Parametreler | Açıklama |
|-------|-------------|----------|
| `systemctl` | `status/start/stop/restart service` | Systemd servis yönetimi |
| `netstat` | `-tlnp` | Dinlenen portları gösterir |
| `ss` | `-tlnp` | Modern netstat alternatifi |
| `ufw` | `allow/deny port` | UFW firewall yönetimi |
| `tail` | `-f /var/log/auth.log` | SSH giriş loglarını takip eder |
| `last` | - | Son kullanıcı girişlerini gösterir |
| `who` | - | Şu anda bağlı kullanıcıları gösterir |
| `w` | - | Kullanıcı aktivitelerini detaylı gösterir |

### Dosya ve Dizin İzinleri

| İzin | Sayısal | Anlamı |
|------|---------|--------|
| `rwx------` | 700 | Sadece sahip tam erişim |
| `rwxr-xr-x` | 755 | Sahip tam, diğerleri okuma+çalıştırma |
| `rw-r--r--` | 644 | Sahip okuma+yazma, diğerleri sadece okuma |
| `rw-------` | 600 | Sadece sahip okuma+yazma (wp-config.php için) |
| `rwxrwxrwx` | 777 | Herkes tam erişim (güvenli değil!) |

Bu rehber, WordPress kurulumu ve SSH yönetimi için gerekli olan tüm temel bilgileri içermektedir. Güvenlik öncelikli yaklaşım benimsenmiş ve pratik örnekler verilmiştir.
