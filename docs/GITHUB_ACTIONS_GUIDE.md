# ☁️ GitHub Actions - Playwright CI/CD Kurulum Rehberi

Bu rehber, Playwright testlerinizi GitHub Actions ile otomatik çalıştırmayı gösterir.

---

## Ön Gereksinimler

- ✅ GitHub hesabı
- ✅ Git kurulu
- ✅ Proje klasörü

---

## Adım 1: GitHub Repository Oluşturun

### GitHub'da:
1. **github.com** → Giriş yapın
2. Sağ üst **"+"** → **"New repository"**
3. **Repository name:** `playwright-agents-example`
4. **Public** veya **Private** seçin
5. ⚠️ **README ekLEMEYİN** (zaten kodunuz var)
6. **"Create repository"**

---

## Adım 2: Workflow Dosyasını Oluşturun

### Dosya: `.github/workflows/playwright.yml`

```yaml
name: Playwright Tests

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*
    
    - name: Install dependencies
      run: npm ci
    
    - name: Install Playwright Browsers
      run: npx playwright install --with-deps
    
    - name: Run Playwright tests
      run: npx playwright test
    
    - uses: actions/upload-artifact@v4
      if: ${{ !cancelled() }}
      with:
        name: playwright-report
        path: playwright-report/
        retention-days: 30
```

**Dosya konumu:**
```
playwright-agents-example/
├── .github/
│   └── workflows/
│       └── playwright.yml    ← BU DOSYA
├── tests/
├── package.json
└── playwright.config.ts
```

---

## Adım 3: (Opsiyonel) Docker İle Workflow

Yerel ortamla %100 aynı olsun isterseniz:

### Dosya: `.github/workflows/playwright-docker.yml`

```yaml
name: Playwright Tests (Docker)

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Build Docker image
      run: docker build -t playwright-agents-example .
    
    - name: Run Playwright tests in Docker
      run: |
        docker run --rm \
          -v ${{ github.workspace }}/test-results:/app/test-results \
          -v ${{ github.workspace }}/playwright-report:/app/playwright-report \
          playwright-agents-example
    
    - name: Upload test results
      uses: actions/upload-artifact@v4
      if: ${{ !cancelled() }}
      with:
        name: playwright-report-docker
        path: playwright-report/
        retention-days: 30
```

---

## Adım 4: Git Repository Başlatın

```powershell
# Proje klasörüne gidin
cd C:\Users\KULLANICI_ADINIZ\Desktop\PLAYWRIGHT_AGENTS_EXAMPLE

# Git'i başlatın (ilk kez)
git init

# Tüm dosyaları ekleyin
git add .

# İlk commit
git commit -m "Initial commit: Playwright tests with GitHub Actions"

# Branch'i main yapın
git branch -M main
```

---

## Adım 5: GitHub'a Bağlanın ve Push Edin

```powershell
# GitHub repository'nizi bağlayın
git remote add origin https://github.com/KULLANICI_ADINIZ/playwright-agents-example.git

# Kodu GitHub'a gönderin
git push -u origin main
```

**Not:** İlk push'ta GitHub kullanıcı adı ve şifre/token isteyebilir.

---

## Adım 6: Workflow'u İzleyin

### GitHub'da:
1. **Repository** → **Actions** sekmesi
2. Yeni workflow otomatik başlayacak 🟡
3. **"Playwright Tests"** → Tıklayın
4. **"test"** job → Tıklayın
5. Logları canlı izleyin

**Durum İkonları:**
- 🟡 **Sarı nokta:** Çalışıyor
- 🟢 **Yeşil tik:** Başarılı
- 🔴 **Kırmızı X:** Başarısız

---

## Adım 7: Test Raporlarını İndirin

### Workflow tamamlandıktan sonra:
1. **Actions** → Tamamlanan workflow
2. Aşağı kaydırın → **"Artifacts"** bölümü
3. **"playwright-report"** → **Download**
4. ZIP dosyasını açın
5. `index.html` → Tarayıcıda açın

---

## Workflow Tetikleyicileri

```yaml
on:
  push:
    branches: [ main, master ]    # main/master'a push → Test koş
  pull_request:
    branches: [ main, master ]    # PR açılınca → Test koş
  schedule:
    - cron: '0 0 * * *'           # Her gün gece 00:00 → Test koş
  workflow_dispatch:              # Manuel tetikleme butonu
```

---

## Ücretsiz Limitler

| Plan | Dakika/Ay | Depolama |
|------|-----------|----------|
| **Public repo** | ♾️ Sınırsız | 500 MB |
| **Private repo (Free)** | 2000 dakika | 500 MB |
| **Private repo (Pro)** | 3000 dakika | 2 GB |

**Ortalama test süresi:** 5-7 dakika

---

## Sorun Giderme

### Workflow Başlamadı
**Çözüm:**
- `.github/workflows/` klasör yapısını kontrol edin
- YAML dosyasının `playwright.yml` olduğunu kontrol edin
- Actions sekmesinde error var mı bakın

### npm ci Hatası
```
npm ERR! The `npm ci` command can only install with an existing package-lock.json
```

**Çözüm:**
- `package-lock.json` dosyasının commit edildiğini kontrol edin
- Yoksa: `npm install` → Commit → Push

### Browser Install Hatası
```
Failed to install browsers
```

**Çözüm:**
- Workflow'da `npx playwright install --with-deps` kullanın
- `--with-deps` sistem bağımlılıklarını da yükler

---

## Gelişmiş Özellikler

### Paralel Test Çalıştırma:
```yaml
strategy:
  matrix:
    browser: [chromium, firefox, webkit]
steps:
  - run: npx playwright test --project=${{ matrix.browser }}
```

### Ortam Değişkenleri:
```yaml
env:
  BASE_URL: https://staging.example.com
steps:
  - run: npx playwright test
```

### Secrets Kullanımı:
```yaml
steps:
  - run: npx playwright test
    env:
      API_KEY: ${{ secrets.API_KEY }}
```

**Secrets ekleme:**
- Repository → Settings → Secrets and variables → Actions → New secret

---

## Avantajlar

✅ **Otomatik:** Her commit'te testler koşar  
✅ **Ücretsiz:** Public repo'lar için sınırsız  
✅ **Hızlı:** Optimize edilmiş runner'lar  
✅ **Entegre:** Pull Request'lerde otomatik status  
✅ **Raporlama:** Artifacts ile detaylı raporlar  

---

## Sonraki Adımlar

- Badge ekleyin README'ye: `![Tests](https://github.com/USER/REPO/actions/workflows/playwright.yml/badge.svg)`
- Slack/Discord notifikasyonları ekleyin
- Test coverage raporları ekleyin
- Environmental deployment testleri yapın

---

**Başarılar! 🚀**
