# AnkaPanjur Ubuntu 22.04 VDS - Sıfırdan Kurulum Rehberi

## ✅ KURULUM ÖNCESİ BİLGİLER

### VDS Bilgileri
- **IP Adresi:** 89.47.113.251
- **Domain:** ankapanjur.com
- **İşletim Sistemi:** Ubuntu 22.04 LTS
- **Minimum Gereksinimler:** 2GB RAM, 20GB Disk, 1 CPU

### Kurulacak Teknolojiler
- **Backend:** Node.js 18.x + Express.js
- **Frontend:** React + TypeScript + Vite
- **Veritabanı:** PostgreSQL 14
- **Süreç Yöneticisi:** PM2
- **Reverse Proxy:** Nginx (opsiyonel)

---

## 🚀 ADIM 1: VDS'E İLK BAĞLANTI

### SSH ile VDS'e Bağlanın
```bash
# Root kullanıcısı ile
ssh root@89.47.113.251

# Ubuntu kullanıcısı ile (eğer varsa)
ssh ubuntu@89.47.113.251
```

### İlk Güvenlik Ayarları
```bash
# Sistem güncellemesi
apt update
apt upgrade -y

# Temel araçları yükle
apt install -y curl wget git unzip nano htop ufw build-essential
```

---

## 🔧 ADIM 2: NODE.JS KURULUMU

### Node.js 18.x LTS Kurulumu
```bash
# NodeSource repository ekle
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -

# Node.js'i yükle
apt-get install -y nodejs

# Versiyonları kontrol et
node --version
npm --version
```

**Beklenen Çıktı:**
```
v18.x.x
9.x.x
```

### NPM Global Paketleri
```bash
# PM2 (süreç yöneticisi) kurulumu
npm install -g pm2

# TypeScript ve TSX
npm install -g typescript tsx

# Versiyonları doğrula
pm2 --version
tsc --version
```

---

## 🗄️ ADIM 3: POSTGRESQL KURULUMU

### PostgreSQL 14 Kurulumu
```bash
# PostgreSQL ve ek araçları yükle
apt install -y postgresql postgresql-contrib

# PostgreSQL servisini başlat
systemctl start postgresql
systemctl enable postgresql

# Servis durumunu kontrol et
systemctl status postgresql
```

### PostgreSQL Yapılandırması
```bash
# PostgreSQL kullanıcısına geç
sudo -u postgres psql

# PostgreSQL komut satırında şu komutları çalıştır:
```

**PostgreSQL komutları (psql içinde):**
```sql
-- Kullanıcı oluştur
CREATE USER ankapanjur_user WITH PASSWORD 'AnkaPanjur2024Strong!';

-- Veritabanı oluştur
CREATE DATABASE ankapanjur_db OWNER ankapanjur_user;

-- Yetkileri ver
GRANT ALL PRIVILEGES ON DATABASE ankapanjur_db TO ankapanjur_user;

-- Çıkış
\q
```

### Veritabanı Bağlantısını Test Et
```bash
psql -U ankapanjur_user -h localhost ankapanjur_db -c "SELECT version();"
```

---

## 📁 ADIM 4: UYGULAMA KLASÖRÜ HAZIRLIĞI

### Uygulama Kullanıcısı ve Klasör
```bash
# Uygulama kullanıcısı oluştur (opsiyonel)
useradd -m -s /bin/bash ankapanjur
usermod -aG sudo ankapanjur

# Ana uygulama klasörünü oluştur
mkdir -p /var/www/ankapanjur
chown $USER:$USER /var/www/ankapanjur
cd /var/www/ankapanjur
```

### Upload Klasörleri
```bash
# Upload klasörlerini oluştur
mkdir -p uploads/services uploads/projects public/images

# İzinleri ayarla
chmod -R 755 uploads
chmod -R 755 public
```

---

## 📦 ADIM 5: PROJE DOSYALARINI YÜKLEME

### Yöntem 1: Replit'ten ZIP İndirme
1. **Replit'te:** Shell → Files → Download as ZIP
2. **Lokal:** ZIP dosyasını açın
3. **WinSCP/FileZilla ile:** Tüm dosyaları `/var/www/ankapanjur/` klasörüne yükleyin

### Yöntem 2: Git Clone (Önerilen)
```bash
# GitHub'a projeyi push edin, sonra:
git clone https://github.com/kullaniciadi/ankapanjur.git /var/www/ankapanjur
cd /var/www/ankapanjur
```

### Dosya Yapısını Kontrol Et
```bash
ls -la /var/www/ankapanjur
```

**Olması gereken dosyalar:**
```
client/          # React frontend
server/          # Express backend
shared/          # Paylaşılan TypeScript tipleri
scripts/         # Database seed scriptleri
public/          # Statik dosyalar
package.json     # NPM dependencies
tsconfig.json    # TypeScript config
drizzle.config.ts # Database config
tailwind.config.ts
vite.config.ts
postcss.config.js
components.json
```

---

## ⚙️ ADIM 6: ENVIRONMENT DOSYASI

### .env Dosyası Oluşturun
```bash
cd /var/www/ankapanjur
nano .env
```

### .env İçeriği (Tam Dosya)
```bash
# Veritabanı Bağlantısı
DATABASE_URL=postgresql://ankapanjur_user:AnkaPanjur2024Strong!@localhost:5432/ankapanjur_db
PGHOST=localhost
PGPORT=5432
PGUSER=ankapanjur_user
PGPASSWORD=AnkaPanjur2024Strong!
PGDATABASE=ankapanjur_db

# Uygulama Ayarları
NODE_ENV=production
PORT=5000
HOST=0.0.0.0

# Güvenlik
SESSION_SECRET=ankapanjur_session_super_secure_key_change_this_in_production_2024

# Dosya Yükleme
UPLOAD_DIR=./uploads

# Site URL
SITE_URL=https://ankapanjur.com
```

### .env Dosyası Güvenliği
```bash
# Dosya izinlerini kısıtla
chmod 600 .env
chown $USER:$USER .env
```

---

## 📚 ADIM 7: NPM PAKETLERI VE DERLEME

### Package.json Kontrolü
```bash
# Package.json'ı kontrol et
cat package.json | head -20
```

### NPM Paketlerini Yükle
```bash
cd /var/www/ankapanjur

# Cache temizle (güvenlik için)
npm cache clean --force

# Dependencies yükle (bu işlem 2-5 dakika sürebilir)
npm install

# Development dependencies (eğer gerekirse)
npm install --only=dev
```

### TypeScript Derleme Testi
```bash
# TypeScript'in çalıştığını test et
npx tsc --noEmit

# TSX'in çalıştığını test et
npx tsx --version
```

---

## 🗃️ ADIM 8: VERİTABANI KURULUMU

### Drizzle Schema Push
```bash
cd /var/www/ankapanjur

# Database schema'yı gönder
npm run db:push
```

**Beklenen çıktı:**
```
✓ Your schema is in sync with the database
```

### Seed Data Yükleme
```bash
# Varsayılan verileri yükle (admin kullanıcısı, servisler, vs.)
npx tsx scripts/seed.ts
```

**Beklenen çıktı:**
```
✓ Seed data loaded successfully
✓ Admin user created
✓ Services loaded
✓ Company info loaded
```

### Database Doğrulama
```bash
# Tabloların oluştuğunu kontrol et
psql -U ankapanjur_user -h localhost ankapanjur_db -c "\dt"

# Admin kullanıcısının oluştuğunu kontrol et
psql -U ankapanjur_user -h localhost ankapanjur_db -c "SELECT username FROM users;"
```

---

## 🔄 ADIM 9: PM2 KONFIGÜRASYONU

### Ecosystem Dosyası Oluşturun
```bash
cd /var/www/ankapanjur
nano ecosystem.config.js
```

### Ecosystem İçeriği
```javascript
module.exports = {
  apps: [{
    name: 'ankapanjur',
    script: 'npm',
    args: 'start',
    cwd: '/var/www/ankapanjur',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'production',
      PORT: 5000,
      HOST: '0.0.0.0'
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_file: './logs/combined.log',
    time: true
  }]
};
```

### Log Klasörü Oluşturun
```bash
mkdir -p logs
chmod 755 logs
```

---

## 🚀 ADIM 10: UYGULAMAYI BAŞLATMA

### Package.json Script Kontrolü
```bash
# Start script'inin varlığını kontrol et
cat package.json | grep -A 5 '"scripts"'
```

**Olması gereken scripts:**
```json
{
  "scripts": {
    "dev": "NODE_ENV=development tsx server/index.ts",
    "start": "NODE_ENV=production tsx server/index.ts",
    "build": "tsc && vite build",
    "db:push": "drizzle-kit push"
  }
}
```

### PM2 ile Başlatma
```bash
cd /var/www/ankapanjur

# Uygulamayı başlat
pm2 start ecosystem.config.js

# PM2'yi sistem başlangıcına ekle
pm2 startup

# Mevcut konfigürasyonu kaydet
pm2 save

# Durumu kontrol et
pm2 status
```

### Uygulama Testi
```bash
# Local olarak test et
curl http://localhost:5000

# Response örneği (HTML dönmeli)
curl -I http://localhost:5000
```

---

## 🔥 ADIM 11: FIREWALL KONFIGÜRASYONU

### UFW Firewall Ayarları
```bash
# Firewall'ı etkinleştir
ufw --force enable

# SSH erişimini koru
ufw allow 22

# HTTP ve HTTPS portları
ufw allow 80
ufw allow 443

# Uygulama portu
ufw allow 5000

# Durumu kontrol et
ufw status verbose
```

**Beklenen çıktı:**
```
Status: active
To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
80                         ALLOW       Anywhere
443                        ALLOW       Anywhere
5000                       ALLOW       Anywhere
```

---

## 🌐 ADIM 12: NGINX REVERSE PROXY (OPSİYONEL)

### Nginx Kurulumu
```bash
apt install -y nginx

# Nginx'i başlat
systemctl start nginx
systemctl enable nginx
```

### Site Konfigürasyonu
```bash
nano /etc/nginx/sites-available/ankapanjur
```

### Nginx Konfigürasyon İçeriği
```nginx
server {
    listen 80;
    server_name ankapanjur.com www.ankapanjur.com 89.47.113.251;
    
    client_max_body_size 50M;
    
    # Static files
    location /uploads/ {
        alias /var/www/ankapanjur/uploads/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    location /public/ {
        alias /var/www/ankapanjur/public/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Proxy to Node.js app
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
        proxy_read_timeout 300;
        proxy_connect_timeout 300;
        proxy_send_timeout 300;
    }
}
```

### Nginx'i Aktifleştir
```bash
# Site'ı etkinleştir
ln -s /etc/nginx/sites-available/ankapanjur /etc/nginx/sites-enabled/

# Default site'ı kaldır
rm -f /etc/nginx/sites-enabled/default

# Konfigürasyonu test et
nginx -t

# Nginx'i yeniden başlat
systemctl restart nginx
```

---

## ✅ ADIM 13: KURULUM DOĞRULAMA

### Sistem Durumu Kontrolü
```bash
# Tüm servislerin durumu
systemctl status postgresql
systemctl status nginx
pm2 status

# Port dinleme durumu
netstat -tulpn | grep :5000
netstat -tulpn | grep :80
netstat -tulpn | grep :5432
```

### Web Sitesi Testi
```bash
# Doğrudan Node.js uygulaması
curl http://localhost:5000

# Nginx üzerinden (eğer kurulmuşsa)
curl http://localhost:80

# Dış erişim testi
curl http://89.47.113.251:5000
```

### Database Bağlantı Testi
```bash
# Uygulama üzerinden database testi
curl http://localhost:5000/api/public/company-info
```

---

## 🔐 ADIM 14: GÜVENLİK VE SON AYARLAR

### Admin Şifresini Değiştirin
1. **Tarayıcıda açın:** http://89.47.113.251:5000/admin
2. **Giriş yapın:** admin / admin123
3. **Profil → Şifre Değiştir** menüsünden güçlü bir şifre belirleyin

### Sistem Güvenliği
```bash
# Fail2ban kurulumu (SSH saldırı koruması)
apt install -y fail2ban

# Automatic security updates
apt install -y unattended-upgrades
dpkg-reconfigure -plow unattended-upgrades
```

### Log Rotasyonu
```bash
# PM2 log rotasyon
pm2 install pm2-logrotate
```

---

## 🎯 KURULUM TAMAMLANDI!

### ✅ Erişim Bilgileri

**Web Sitesi:**
- **Direkt:** http://89.47.113.251:5000
- **Nginx ile:** http://89.47.113.251

**Admin Paneli:**
- **URL:** http://89.47.113.251:5000/admin
- **Varsayılan Giriş:** admin / admin123
- **⚠️ İLK GİRİŞTE ŞİFREYİ DEĞİŞTİRİN!**

### 📊 Sistem Kontrol Komutları

```bash
# Uygulama durumu
pm2 status
pm2 logs ankapanjur

# Sistem durumu
systemctl status postgresql
systemctl status nginx

# Database kontrolü
psql -U ankapanjur_user -h localhost ankapanjur_db

# Disk ve memory
df -h
free -h
```

### 🚨 Acil Durum Komutları

```bash
# Uygulamayı yeniden başlat
pm2 restart ankapanjur

# Database'i yeniden başlat
systemctl restart postgresql

# Nginx'i yeniden başlat
systemctl restart nginx

# Logları kontrol et
pm2 logs ankapanjur --lines 50
tail -f /var/log/nginx/error.log
```

---

## 🌐 DNS VE SSL KURULUMU

### DNS Ayarları
Domain sağlayıcınızdan şu kayıtları ekleyin:
```
A Record: ankapanjur.com → 89.47.113.251
A Record: www.ankapanjur.com → 89.47.113.251
```

### SSL Sertifikası (DNS aktif olduktan sonra)
```bash
# Certbot kurulumu
apt install -y certbot python3-certbot-nginx

# SSL sertifikası alma
certbot --nginx -d ankapanjur.com -d www.ankapanjur.com

# Otomatik yenileme testi
certbot renew --dry-run
```

---

## 📞 DESTEK

Kurulum sırasında sorun yaşarsanız:

1. **Log kontrol:** `pm2 logs ankapanjur`
2. **Database test:** `psql -U ankapanjur_user -h localhost ankapanjur_db`
3. **Port test:** `netstat -tulpn | grep 5000`
4. **Firewall test:** `ufw status`

**🎉 KURULUM TAMAMLANDI! AnkaPanjur web sitesi başarıyla çalışıyor!**