# AnkaPanjur VDS Sorun Giderme Rehberi

## Yaygın Sorunlar ve Çözümler

### 1. NPM Install Hatası
```bash
# Node.js versiyonunu kontrol et
node --version
npm --version

# npm cache temizle
npm cache clean --force

# node_modules sil ve tekrar yükle
rm -rf node_modules package-lock.json
npm install
```

### 2. Database Connection Error
```bash
# PostgreSQL çalışıyor mu?
sudo systemctl status postgresql

# PostgreSQL yeniden başlat
sudo systemctl restart postgresql

# Database bağlantısını test et
psql -U ankapanjur_user -h localhost ankapanjur_db

# .env dosyasını kontrol et
cat .env
```

### 3. PM2 Başlatma Sorunu
```bash
# PM2 durumunu kontrol et
pm2 status

# PM2 loglarını kontrol et
pm2 logs ankapanjur

# Uygulamayı yeniden başlat
pm2 restart ankapanjur

# PM2'yi tamamen temizle ve yeniden başlat
pm2 delete all
pm2 start ecosystem.config.js
```

### 4. Port 5000 Erişim Sorunu
```bash
# Port kullanımını kontrol et
sudo netstat -tulpn | grep 5000

# Firewall durumunu kontrol et
sudo ufw status

# Port 5000'i aç
sudo ufw allow 5000

# Uygulama çalışıyor mu kontrol et
curl http://localhost:5000
```

### 5. TypeScript/TSX Hatası
```bash
# TypeScript ve TSX paketlerini yükle
npm install -g tsx typescript

# Veya local olarak
npm install tsx typescript

# package.json scripts kontrol et
cat package.json | grep scripts -A 10
```

### 6. Drizzle Database Push Hatası
```bash
# Drizzle kit versiyonunu kontrol et
npx drizzle-kit --version

# Database bağlantısını test et
psql -U ankapanjur_user -h localhost ankapanjur_db -c "SELECT version();"

# Manuel olarak tabloları oluştur
npx drizzle-kit generate
npx drizzle-kit push --force
```

### 7. Upload Klasörü İzin Sorunu
```bash
# Upload klasörü izinlerini düzelt
sudo chown -R $USER:$USER uploads/
chmod -R 755 uploads/

# Klasörlerin varlığını kontrol et
ls -la uploads/
```

### 8. Memory/Disk Space Sorunu
```bash
# Disk alanını kontrol et
df -h

# Memory kullanımını kontrol et
free -h

# PM2 memory limitini artır
pm2 delete ankapanjur
# ecosystem.config.js dosyasında max_memory_restart: '2G' yap
pm2 start ecosystem.config.js
```

### 9. Environment Variables Sorunu
```bash
# .env dosyasının varlığını kontrol et
ls -la .env

# .env dosyası içeriğini kontrol et (şifreler gizli)
cat .env | grep -v PASSWORD

# .env dosyası izinlerini kontrol et
chmod 600 .env
```

### 10. Admin Login Sorunu
```bash
# Seed script'i çalıştırıldı mı kontrol et
psql -U ankapanjur_user -h localhost ankapanjur_db -c "SELECT * FROM users;"

# Seed'i tekrar çalıştır
npx tsx scripts/seed.ts
```

## Debugging Komutları

### Sistem Durumu
```bash
# Tüm servislerin durumu
sudo systemctl status postgresql
pm2 status

# Port dinleme durumu
sudo netstat -tulpn | grep :5000
sudo netstat -tulpn | grep :5432

# Disk ve memory
df -h
free -h
```

### Log Kontrolü
```bash
# PM2 logs
pm2 logs ankapanjur --lines 50

# PostgreSQL logs
sudo tail -f /var/log/postgresql/postgresql-14-main.log

# System logs
sudo journalctl -u postgresql -f
```

### Network Testi
```bash
# Local bağlantıyı test et
curl http://localhost:5000

# Dış bağlantıyı test et (başka makineden)
curl http://89.47.113.251:5000
```

## Acil Durum - Sıfırdan Başlatma

Eğer hiçbir şey çalışmıyorsa:

```bash
# 1. PM2'yi durdur
pm2 delete all

# 2. Node modules'ı sil
rm -rf node_modules package-lock.json

# 3. Database'i sıfırla
sudo -u postgres psql -c "DROP DATABASE ankapanjur_db;"
sudo -u postgres psql -c "CREATE DATABASE ankapanjur_db OWNER ankapanjur_user;"

# 4. Tekrar kurulum
npm install
npm run db:push
npx tsx scripts/seed.ts
pm2 start ecosystem.config.js
```

## İletişim

Sorun devam ederse:
1. PM2 loglarını kontrol edin: `pm2 logs ankapanjur`
2. Database bağlantısını test edin
3. .env dosyasının doğru olduğundan emin olun
4. Firewall ayarlarını kontrol edin