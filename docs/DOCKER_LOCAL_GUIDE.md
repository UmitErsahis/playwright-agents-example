# 🐳 Docker Local - Playwright Test Çalıştırma Rehberi

Bu rehber, Playwright testlerinizi Docker container'ında local bilgisayarınızda nasıl çalıştıracağınızı gösterir.

---

## Ön Gereksinimler

- ✅ Docker Desktop kurulu olmalı (Windows/Mac/Linux)
- ✅ Git kurulu olmalı
- ✅ Proje klasörü

---

## Adım 1: Docker Desktop'ı Başlatın

### Windows:
```powershell
Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe"
Start-Sleep -Seconds 30
```

### Manuel:
- Start menüden **"Docker Desktop"** açın
- Sistem tepsisinde Docker ikonu görünene kadar bekleyin (30 saniye)

---

## Adım 2: Dockerfile İçeriği

Projenizde `Dockerfile` olmalı:

```dockerfile
# Playwright base image
FROM mcr.microsoft.com/playwright:v1.58.2-jammy

# Çalışma dizini
WORKDIR /app

# Bağımlılıkları kopyala ve yükle
COPY package*.json ./
RUN npm ci

# Playwright browser'ları yükle
RUN npx playwright install

# Tüm proje dosyalarını kopyala
COPY . .

# Test komutunu çalıştır
CMD ["npx", "playwright", "test"]
```

---

## Adım 3: Docker Image'ı Build Edin

```powershell
# Proje klasörüne gidin
cd C:\Users\KULLANICI_ADINIZ\Desktop\PLAYWRIGHT_AGENTS_EXAMPLE

# Docker image'ı oluşturun
docker build -t playwright-agents-example .
```

**Süre:** ~2-5 dakika (ilk kez)

**Çıktı:**
```
[+] Building 120.5s (11/11) FINISHED
=> [1/7] FROM mcr.microsoft.com/playwright...
=> [2/7] WORKDIR /app
=> [3/7] COPY package*.json ./
=> [4/7] RUN npm ci
=> [5/7] RUN npx playwright install
=> [6/7] COPY . .
=> [7/7] CMD ["npx", "playwright", "test"]
=> => exporting to image
=> => naming to docker.io/library/playwright-agents-example
```

---

## Adım 4: Testleri Çalıştırın

### Basit Çalıştırma (Raporlar kaybolur):
```powershell
docker run --rm playwright-agents-example
```

### Raporları Kaydetme (ÖNERİLEN):
```powershell
docker run --rm `
  -v ${PWD}/test-results:/app/test-results `
  -v ${PWD}/playwright-report:/app/playwright-report `
  playwright-agents-example
```

**Açıklama:**
- `--rm`: Test bitince container'ı otomatik sil
- `-v ${PWD}/test-results:/app/test-results`: Test sonuçlarını Windows'a kaydet
- `-v ${PWD}/playwright-report:/app/playwright-report`: HTML raporunu Windows'a kaydet

---

## Adım 5: Test Raporlarını Görüntüleyin

### PowerShell'den:
```powershell
# HTML raporunu tarayıcıda aç
Invoke-Item .\playwright-report\index.html
```

### Windows Explorer'dan:
1. `playwright-report` klasörünü açın
2. `index.html` dosyasına çift tıklayın

---

## Sorun Giderme

### Docker Daemon Çalışmıyor
```
Error response from daemon: Cannot connect to the Docker daemon
```

**Çözüm:**
```powershell
# Docker Desktop'ı başlatın
Start-Process "Docker Desktop.exe"
Start-Sleep -Seconds 30

# Kontrol edin
docker version
```

### Image Bulunamadı
```
Error: No such image: playwright-agents-example
```

**Çözüm:**
```powershell
# Image'ı yeniden build edin
docker build -t playwright-agents-example .
```

### Port 8080 Kullanımda (Başka bir şey için değil)
```
Error: Bind for 0.0.0.0:8080 failed: port is already allocated
```

**Çözüm:**
```powershell
# Çalışan container'ları görün
docker ps

# Gerekirse durdurun
docker stop <container-id>
```

---

## Yararlı Komutlar

```powershell
# Image'ları listele
docker images

# Container'ları listele (çalışan)
docker ps

# Container'ları listele (tümü)
docker ps -a

# Image'ı sil
docker rmi playwright-agents-example

# Tüm durmuş container'ları temizle
docker container prune

# Disk alanı kazanmak için temizlik
docker system prune -a
```

---

## Avantajlar

✅ **İzolasyon:** Testler container'da koşar, sisteminizi etkilemez  
✅ **Tekrarlanabilirlik:** Her yerde aynı şekilde çalışır  
✅ **Temizlik:** Container bitince her şey silinir  
✅ **Versiyon Kontrolü:** Dockerfile ile tüm bağımlılıklar takip edilir  

---

## Sonraki Adımlar

- CI/CD pipeline'a entegre edin (GitHub Actions, GitLab CI, Jenkins)
- Docker Compose ile çoklu servis testleri yapın
- Paralel test çalıştırma için Docker Swarm kullanın

---

**Başarılar! 🚀**
