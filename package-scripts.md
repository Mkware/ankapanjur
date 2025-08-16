# Package.json Scripts Kontrol

## Mevcut Scripts'leri Kontrol Edin

Önce package.json dosyasındaki scripts bölümünü kontrol edin:

```bash
cat package.json
```

## Gerekli Scripts

package.json dosyasında şu scripts olmalı:

```json
{
  "scripts": {
    "dev": "NODE_ENV=development tsx server/index.ts",
    "start": "NODE_ENV=production tsx server/index.ts",
    "build": "tsc && vite build",
    "db:push": "drizzle-kit push",
    "db:studio": "drizzle-kit studio"
  }
}
```

## Eğer Scripts Eksikse

package.json dosyasını düzenleyin:

```bash
nano package.json
```

Scripts bölümünü ekleyin/düzenleyin ve dosyayı kaydedin.

## Production için Gerekli Dependencies

package.json'da şu dependencies olmalı:

```json
{
  "dependencies": {
    "tsx": "^4.0.0",
    "typescript": "^5.0.0",
    "drizzle-kit": "^0.20.0",
    "drizzle-orm": "^0.29.0"
  }
}
```

Bu paketler eksikse:

```bash
npm install tsx typescript drizzle-kit drizzle-orm
```