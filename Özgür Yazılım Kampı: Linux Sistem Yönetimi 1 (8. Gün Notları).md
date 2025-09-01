# Özgür Yazılım Kampı: Linux Sistem Yönetimi 1 (8. Gün Notları - **Temel Network Kavramları ve Sistem Protokolleri**)

Bu yazı, Özgür Yazılım Kampı'nda aldığım Linux Sistem Yönetimi 1 dersinin sekizinci gününde tuttuğum notların derlenmiş hâlidir. Notlarda yer alan tüm komutların listesi dosyanın en sonunda sıralanmıştır, eğer sadece komutları incelemek isterseniz doğrudan en sona atlayabilirsiniz.

## 1. Temel Ağ Kavramları

### 1.1 Protokol
Protokol, ağ üzerinde cihazlar arası iletişimi düzenleyen kural setidir. Her protokolün kendine özgü işlevi ve çalışma mantığı vardır.

### 1.2 Port
Port, gelen isteklerin hangi servislere yönlendirileceğini ayırmak için kullanılır. Gelen pakete yalnızca destination IP vermekle kalmıyor, aynı zamanda servisin kullanacağı portu da belirtiyoruz. Hangi uygulamanın hangi porttan çalıştığı `/etc/services` dosyasında tanımlanır.

**Önemli Not:** Aynı port birden fazla servise verilirse, ilk giren portu kaplar ve ikinci servis için "Error: Address already in use" hatası alınır.

### 1.3 Port Yönlendirme (Port Forwarding)
Dışarıdan IP adresine gelen isteklerin, belirli bir porttan içerideki servisin kullandığı porta yönlendirilmesi işlemidir. Bu sayede external ağdan internal servislere erişim sağlanabilir.

### 1.4 Bandwidth (Bant Genişliği)
İnternet hızıyla alakalı olan ve internete giden yolun ne kadar geniş olduğunu ifade eden kavramdır. Bant genişliği daraldığında, giden istek üzerinde daha kolay tarama yapılabilir çünkü alan daralacağı için eşzamanlı geçiş yapan paket sayısında azalma olur ve bu paketler daha hızlı ve kolay taranabilir.

### 1.5 TTL (Time to Live)
Network özelinde, paketin hedefe giderken kaç tane router üzerinden geçeceğini belirler. Her router'dan geçişte TTL değeri azalır ve sıfıra ulaştığında paket drop edilir.

## 2. Dosya Transfer Protokolleri

### 2.1 SFTP/FTP (File Transfer Protocol)
Dosya transferi için kullanılan protokollerdir. Download ve upload işlemlerini gerçekleştirir. SFTP, FTP'nin güvenli versiyonudur ve SSH üzerinden çalışır.

## 3. E-posta Protokolleri

### 3.1 SMTP (Simple Mail Transfer Protocol)
E-posta aktarımı için kullanılan protokoldür. Ancak güvenli değildir ve e-postalar düz metin olarak gönderilir.

### 3.2 IMAP (Internet Message Access Protocol)
İnternet üzerinden mesaj erişimi için kullanılır. E-posta SMTP sunucusuna geldiğinde, kullanıcıların telefon, PC, tablet gibi farklı cihazlardan e-postalarına erişim sağlamasını mümkün kılar.

## 4. Yönetim ve İzleme Protokolleri

### SNMP (Simple Network Management Protocol)
Ağ cihazlarının izlenmesi ve yönetilmesi için kullanılan protokoldür. Monitoring amacıyla kullanılır. Merkezi sunucu vasıtasıyla diğer sunucuların metriklerini ve bilgilerini toplar. Bu veriler arka planda kaydedilir ve birileri sunucunun anlık veya belirli zaman aralığındaki durumu hakkında soru sorduğunda yanıt alabilir.

## 5. DNS (Domain Name System)

### DNS Nedir?
DNS (Domain Name System), alan adlarını IP adreslerine çeviren sistemdir. İnternetin telefon rehberi olarak da bilinir ve tarayıcıların internet kaynaklarına erişebilmesini sağlar.

### DNS Nasıl Çalışır?
DNS yapısı en üstten başlayarak aşağıya doğru inen, hiyerarşik yapıya sahip bir veritabanıdır. Bir web sitesine erişmek istediğinizde:

1. Tarayıcı, alan adını yazdığınızda DNS sunucusuna sorgu gönderir
2. DNS sunucusu recursive (özyinelemeli) sorgu işlemi başlatır
3. DNS sunucusu, alan adına karşılık gelen IP adresini bulur
4. IP adresi tarayıcıya gönderilir ve bağlantı kurulur

### DNS TTL (Time to Live)
DNS TTL, bir DNS kaydının ne kadar süre cache'de tutulacağını belirler. Eğer 2 hafta TTL verilen bir DNS adresinde güncelleme yapmak isteniyor ama 2 hafta beklemek istemiyorsanız:

**Çözüm:** Değişikliği en az 2 hafta önceden planlayıp TTL süresini düşürmek gerekir (örneğin 30 saniye). 2 hafta bekledikten sonra içerik güncellenip, ardından TTL tekrar 2 hafta olarak ayarlanabilir.

## 6. Güvenlik ve Ağ Yönetimi

### 6.1 Firewall (Güvenlik Duvarı)
Ağ giriş ve çıkışlarında filtreleme yapar. Gelen network paketlerini engeller veya geçmesine izin verir. Gönderici/Alıcı IP adresi, port numarası gibi network paketi üzerindeki bilgilere göre filtreleme yapabilir.

### 6.2 VPN (Virtual Private Network)
Sanal özel ağ oluşturarak güvenli bağlantı sağlar. İnternet üzerinden şifreli tünel kurarak veri güvenliğini artırır ve kullanıcının gerçek IP adresini gizler.

### 6.3 ARP Tablosu
Bilgisayarın haberleştiği cihazların MAC-IP adresi eşleşmelerinin tutulduğu tablodur. Local ağdaki cihazların fiziksel adreslerini (MAC) IP adresleriyle eşleştirir.

## 7. Proxy Sistemleri

### 7.1 Proxy (Forward Proxy)
İstemci cihazlarla sunucular arasında geçit görevi gören sistemdir. Kullanıcı adına istekleri karşılar ve yanıtları iletir. İstemciyi korur ve anonimleştirir.

**Avantajları:**
- İstemci IP adresini gizler
- Cache ile performans artışı
- İçerik filtreleme imkanı
- Bant genişliği kontrolü

### 7.2 Reverse Proxy
İnternet ve web server arasındaki ağ trafiğinin düzgün akışını sağlamak için kullanılır. Sunucuların önüne yerleşen vekil sunucudur.

**Avantajları:**
- Load balancing (yük dağıtımı)
- SSL termination
- Cache'leme
- DDoS koruması
- Sunucu IP adreslerini gizleme

**Fark:** Forward proxy istemciyi korurken, reverse proxy sunucuları korur.

## 8. Sistem Konfigürasyon Dosyaları

### 8.1 /etc/resolv.conf
DNS sorgu sunucu bilgilerinin tutulduğu dosyadır. Sistemin hangi DNS sunucularını kullanacağını belirler.

### 8.2 /etc/hosts
Linux'ta DNS sorgusu atıldığında varsayılan olarak önce bu dosyaya bakılır. Manuel IP-domain eşleştirmeleri burada tanımlanır.

### 8.3 /etc/nsswitch.conf
Sistem önce hangi kaynaklara bakacağını burada belirler. DNS çözümleme sırasının ayarlandığı dosyadır.

## 9. Ağ Komutları

### 9.1 DNS Sorgu Komutları

#### nslookup
- `nslookup kamp.linux.org.tr` → Sistemde tanımlı DNS server'dan sorgulama
- `nslookup kamp.linux.org.tr 8.8.8.8` → Belirli DNS sunucusundan sorgu
- `nslookup q=mx kamp.linux.org.tr` → Mail sunucusunu gösterir
- `nslookup q=ns kamp.linux.org.tr` → DNS sunucusunu gösterir

#### dig
- `dig kamp.linux.org.tr` → DNS sorgusu (nslookup alternatifi)
- `dig -t mx kamp.linux.org.tr` → Mail sunucusu sorgusu
- `dig -t ns kamp.linux.org.tr` → Name server sorgusu

#### whois
- `whois linux.org.tr` → Domain sahibi, kayıt tarihi, süresi gibi bilgileri gösterir

### 9.2 Bağlantı Test Komutları

#### ping
- ICMP paketi gönderir, port belirtmez
- İki sunucu arasında bağlantı kontrolü
- Haberleşme hızını ve paket kaybını gösterir

#### telnet
- Belirli portlara erişip erişemediğini test etmek için kullanılır
- Port connectivity testi yapar

#### traceroute
- Bir hedefe bağlantı kurarken hangi route'lardan gittiğini gösterir
- Paketin gittiği yolu ve üzerinden geçtiği IP adreslerini listeler

### 9.3 Port ve Servis İzleme

#### netstat/ss
- `netstat -tulnp` veya `ss -tulnp`
- Açık olan ve kullanılan portlar hakkında bilgi verir
- Hangi servislerin hangi portları dinlediğini gösterir

### 9.4 Ağ Analizi ve Güvenlik

#### tcpdump
- Ağ üzerinden geçen tüm paketleri görür
- Network traffic analizi yapar
- Paket içeriklerini inceleyebilir

#### netcat (nc)
- Bir portu alıp başka porta yönlendirir
- Port listening ve connection testing
- Data transfer için kullanılabilir

#### nmap
- Ağ üzerinde keşif yapmak için kullanılır
- Port scanning ve service detection
- Network security assessment

---

## 10. Komut Parametreleri ve Açıklamaları
**Not: Derste işlediğimiz konular dahilinde kendi çalışmalarım sırasında kullandığım komutları da ekledim**

### nslookup
```bash
nslookup [domain]              # Varsayılan DNS sunucudan sorgu
nslookup [domain] [dns-server] # Belirli DNS sunucudan sorgu  
nslookup q=mx [domain]         # MX (mail) kaydı sorgusu
nslookup q=ns [domain]         # NS (name server) kaydı sorgusu
```

### dig
```bash
dig [domain]                   # A kaydı sorgusu
dig -t mx [domain]             # MX kaydı sorgusu
dig -t ns [domain]             # NS kaydı sorgusu
dig -t any [domain]            # Tüm kayıt türleri
dig +trace [domain]            # DNS çözümleme yolunu gösterir
```

### netstat/ss
```bash
netstat -tulnp                 # TCP/UDP listening portları
  -t: TCP bağlantıları
  -u: UDP bağlantıları  
  -l: Listening (dinleyen) portlar
  -n: Numerik gösterim (port numaraları)
  -p: Process bilgisi

ss -tulnp                      # Modern netstat alternatifi
```

### tcpdump
```bash
tcpdump -i [interface]         # Belirli interface dinleme
tcpdump port [port-number]     # Belirli port trafiği
tcpdump host [ip-address]      # Belirli host trafiği
tcpdump -w [file.pcap]         # Pakettleri dosyaya kaydetme
```

### nmap
```bash
nmap [ip-address]             # Temel port tarama
nmap -p 1-1000 [ip]           # Belirli port aralığı tarama
nmap -sV [ip]                 # Servis versiyonu tespiti
nmap -O [ip]                  # OS detection
nmap -A [ip]                  # Aggressive scan (OS, versiyon, script)
```

### traceroute
```bash
traceroute [domain/ip]         # Route izleme
traceroute -n [domain/ip]      # Numerik gösterim (DNS çözümleme yapmaz)
traceroute -m [max-hops]       # Maksimum hop sayısı belirleme
```

### netcat (nc)
```bash
nc -l [port]                  # Port dinleme
nc [host] [port]              # Bağlantı kurma
nc -v [host] [port]           # Verbose mode
nc -z [host] [port-range]     # Port connectivity testi
```

### ping
```bash
ping [domain/ip]               # Sürekli ping
ping -c [count] [domain/ip]    # Belirli sayıda ping
ping -i [interval] [domain/ip] # Ping aralığı belirleme
ping -s [size] [domain/ip]     # Paket boyutu belirleme
```

### whois
```bash
whois [domain]                # Domain bilgileri
whois [ip-address]            # IP address bilgileri
```

### Dosya Yolları
- `/etc/services` → Port-servis eşleştirmeleri
- `/etc/resolv.conf` → DNS sunucu ayarları 
- `/etc/hosts` → Lokal DNS eşleştirmeleri
- `/etc/nsswitch.conf` → Name service switch yapılandırması
```
