# AnkaPanjur Ubuntu 22.04 VDS Manuel Kurulum

## VDS Bilgileri
- **IP:** 89.47.113.251
- **Domain:** ankapanjur.com
- **OS:** Ubuntu 22.04 LTS

## ADIM ADIM MANUEL KURULUM

### 1. VDS'e Bağlanın
```bash
ssh root@89.47.113.251
# veya
ssh ubuntu@89.47.113.251
```

### 2. Sistem Güncellemesi
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl wget git unzip nano htop ufw
```

### 3. Node.js 18.x Kurulumu
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
node --version
npm --version
```

### 4. PostgreSQL Kurulumu
```bash
sudo apt install -y postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
```

### 5. PostgreSQL Yapılandırması
```bash
# PostgreSQL kullanıcısına geç
sudo -u postgres psql

# PostgreSQL içinde şu komutları çalıştır:
CREATE USER ankapanjur_user WITH PASSWORD 'AnkaPanjur2024Strong!';
CREATE DATABASE ankapanjur_db OWNER ankapanjur_user;
GRANT ALL PRIVILEGES ON DATABASE ankapanjur_db TO ankapanjur_user;
\q
```

### 6. Uygulama Klasörü Oluştur
```bash
sudo mkdir -p /var/www/ankapanjur
sudo chown $USER:$USER /var/www/ankapanjur
cd /var/www/ankapanjur
```

### 7. Proje Dosyalarını Yükleyin
**MANUEL YÜKLEME:** 
- Replit'ten projeyi ZIP olarak indirin
- WinSCP/FileZilla ile `/var/www/ankapanjur/` klasörüne yükleyin
- Tüm dosyaları bu klasöre çıkarın

### 8. Environment Dosyası (.env) Oluşturun
```bash
cd /var/www/ankapanjur
nano .env
```

Aşağıdaki içeriği yapıştırın:
```
DATABASE_URL=postgresql://ankapanjur_user:AnkaPanjur2024Strong!@localhost:5432/ankapanjur_db
PGHOST=localhost
PGPORT=5432
PGUSER=ankapanjur_user
PGPASSWORD=AnkaPanjur2024Strong!
PGDATABASE=ankapanjur_db
NODE_ENV=production
PORT=5000
HOST=0.0.0.0
SESSION_SECRET=ankapanjur_session_very_secure_key_here_change_this
UPLOAD_DIR=./uploads
SITE_URL=https://ankapanjur.com
```

Dosyayı kaydedin (Ctrl+O, Enter, Ctrl+X)

### 9. Upload Klasörleri Oluşturun
```bash
mkdir -p uploads/services uploads/projects
chmod -R 755 uploads
```

### 10. NPM Paketlerini Yükleyin
```bash
npm install
```

### 11. Veritabanını Hazırlayın
```bash
npm run db:push
npx tsx scripts/seed.ts
```

### 12. PM2 Kurulumu (Süreç Yöneticisi)
```bash
sudo npm install -g pm2
```

### 13. PM2 Ecosystem Dosyası Oluşturun
```bash
nano ecosystem.config.js
```

İçeriği:
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
    }
  }]
};
```

### 14. Uygulamayı Başlatın
```bash
pm2 start ecosystem.config.js
pm2 startup
pm2 save
pm2 status
```

### 15. Test Edin
```bash
# Uygulama çalışıyor mu kontrol
curl http://localhost:5000

# PM2 durumu
pm2 status

# Logları kontrol
pm2 logs ankapanjur
```

### 16. Firewall Ayarları
```bash
sudo ufw allow 22
sudo ufw allow 5000
sudo ufw --force enable
sudo ufw status
```

## Erişim Bilgileri

### Web Sitesi
- **URL:** http://89.47.113.251:5000
- **Domain:** http://ankapanjur.com (DNS ayarlandıktan sonra)

### Admin Paneli
- **URL:** http://89.47.113.251:5000/admin
- **Kullanıcı:** admin
- **Şifre:** admin123

## DNS Ayarları

Domain sağlayıcınızdan:
```
A Record: ankapanjur.com → 89.47.113.251
A Record: www.ankapanjur.com → 89.47.113.251
```

## SSL Sertifikası

DNS ayarları aktif olduktan sonra:
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d ankapanjur.com -d www.ankapanjur.com
```

## Faydalı Komutlar

### Uygulama Yönetimi
```bash
pm2 status                    # Durum kontrolü
pm2 logs ankapanjur          # Log görüntüleme
pm2 restart ankapanjur       # Yeniden başlatma
pm2 stop ankapanjur          # Durdurma
```

### Sistem Kontrolleri
```bash
sudo systemctl status nginx     # Nginx durumu
sudo systemctl status postgresql # PostgreSQL durumu
curl http://localhost:5000      # Local test
```

### Veritabanı Erişimi
```bash
psql -U ankapanjur_user -h localhost ankapanjur_db
```

## Sorun Giderme

### Port 5000'e erişilemiyor
```bash
sudo ufw allow 5000
pm2 restart ankapanjur
sudo systemctl restart nginx
```

### PostgreSQL bağlantı sorunu
```bash
sudo systemctl restart postgresql
```

### Nginx 502 Hatası
```bash
pm2 status
pm2 restart ankapanjur
sudo nginx -t
sudo systemctl restart nginx
```

## Dosya Yapısı

Proje dosyaları `/home/ubuntu/ankapanjur/` klasöründe olmalı:
```
/home/ubuntu/ankapanjur/
├── client/
├── server/
├── shared/
├── scripts/
├── public/
├── uploads/
├── package.json
├── .env
└── ecosystem.config.js
```

## Güvenlik

- Firewall aktif (UFW)
- PostgreSQL yerel erişim
- Session secrets otomatik oluşturulan
- Upload klasörleri güvenli izinler
- Admin şifresi ilk girişte değiştirin