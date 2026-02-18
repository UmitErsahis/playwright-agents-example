# 🦊 GitLab CI/CD - Playwright Pipeline Kurulum Rehberi

Bu rehber, Playwright testlerinizi GitLab CI/CD ile otomatik çalıştırmayı gösterir.

---

## Ön Gereksinimler

- ✅ GitLab hesabı (gitlab.com - ücretsiz)
- ✅ Git kurulu
- ✅ Proje klasörü

---

## Adım 1: GitLab Hesabı Oluşturun

1. **gitlab.com** → Giriş yapın veya kayıt olun
2. Ücretsiz plan yeterli (400 dakika/ay)

---

## Adım 2: GitLab'da Proje Oluşturun

### GitLab'da:
1. Sol üst **"+ New project"** (veya **"+"** → **"New project"**)
2. **"Create blank project"** seçin
3. **Project name:** `playwright-agents-example`
4. **Visibility Level:** **Public** veya **Private**
5. ⚠️ **"Initialize repository with a README"** ✖️ İŞARETLEMEYİN
6. **"Create project"**

---

## Adım 3: Pipeline Dosyasını Oluşturun

### Dosya: `.gitlab-ci.yml` (Proje kök dizininde)

```yaml
# GitLab CI/CD Pipeline for Playwright Tests

stages:
  - test

# Normal Playwright Test (Hızlı - Önerilen)
playwright-test:
  stage: test
  image: mcr.microsoft.com/playwright:v1.58.2-jammy
  before_script:
    - npm ci
  script:
    - npx playwright test
  artifacts:
    when: always
    paths:
      - playwright-report/
      - test-results/
    expire_in: 30 days
  only:
    - main
    - master
    - merge_requests

# Docker ile Test (Opsiyonel - Yerel ile %100 aynı)
playwright-test-docker:
  stage: test
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker info
  script:
    - docker build -t playwright-agents-example .
    - docker run --rm 
        -v $CI_PROJECT_DIR/test-results:/app/test-results 
        -v $CI_PROJECT_DIR/playwright-report:/app/playwright-report 
        playwright-agents-example
  artifacts:
    when: always
    paths:
      - playwright-report/
      - test-results/
    expire_in: 30 days
  only:
    - main
    - master
    - merge_requests
```

**Dosya konumu:**
```
playwright-agents-example/
├── .gitlab-ci.yml    ← BU DOSYA (kök dizinde)
├── tests/
├── package.json
└── playwright.config.ts
```

---

## Adım 4: Git'i Başlatın ve Dosyaları Ekleyin

```powershell
# Proje klasörüne gidin
cd C:\Users\KULLANICI_ADINIZ\Desktop\PLAYWRIGHT_AGENTS_EXAMPLE

# Git'i başlatın (ilk kez)
git init

# Tüm dosyaları ekleyin
git add .

# İlk commit
git commit -m "Initial commit: Playwright tests with GitLab CI/CD"

# Branch'i main yapın
git branch -M main
```

---

## Adım 5: GitLab'a Bağlanın ve Push Edin

### GitLab'dan URL'yi Kopyalayın:
GitLab proje sayfasında **"Clone"** düğmesi → **"Clone with HTTPS"** → URL'yi kopyalayın

Örnek:
```
https://gitlab.com/KULLANICI_ADINIZ/playwright-agents-example.git
```

### PowerShell'de:
```powershell
# GitLab repository'nizi bağlayın
git remote add gitlab https://gitlab.com/KULLANICI_ADINIZ/playwright-agents-example.git

# Kodu GitLab'a gönderin
git push -u gitlab main
```

**Not:** İlk push'ta GitLab kullanıcı adı ve şifre/token isteyebilir.

---

## Adım 6: Pipeline'ı İzleyin

### GitLab'da:
1. **Projeniz** → Sol menü **"CI/CD"** → **"Pipelines"**
2. Yeni pipeline otomatik başlayacak 🟡
3. Pipeline'a tıklayın
4. **Job'lara** tıklayın (playwright-test, playwright-test-docker)
5. Logları canlı izleyin

**Durum İkonları:**
- 🟡 **Pending/Running:** Çalışıyor
- 🟢 **Passed:** Başarılı
- 🔴 **Failed:** Başarısız
- ⚪ **Canceled:** İptal edildi

---

## Adım 7: Test Raporlarını İndirin/Görüntüleyin

### Pipeline tamamlandıktan sonra:

**Yöntem 1: Browse (Tarayıcıda Görüntüle):**
1. **CI/CD** → **Pipelines** → Tamamlanan pipeline
2. Sağ tarafta **"Browse"** butonu
3. **"playwright-report"** klasörünü açın
4. `index.html` → Görüntüleyin

**Yöntem 2: Download (İndir):**
1. **CI/CD** → **Pipelines** → Tamamlanan pipeline
2. Job'a tıklayın (playwright-test)
3. Sağ tarafta **"Download"** butonu
4. ZIP dosyasını indirin ve açın
5. `playwright-report/index.html` → Tarayıcıda açın

---

## Pipeline Tetikleyicileri

```yaml
only:
  - main               # main branch'e push → Pipeline koş
  - master             # master branch'e push → Pipeline koş
  - merge_requests     # MR açılınca → Pipeline koş
```

**Manuel Tetikleme:**
- CI/CD → Pipelines → **"Run pipeline"** butonu

**Scheduled Pipelines:**
- CI/CD → Schedules → New schedule
- Cron syntax: `0 0 * * *` (Her gün gece 00:00)

---

## Ücretsiz Limitler

| Plan | Dakika/Ay | Depolama |
|------|-----------|----------|
| **Free** | 400 dakika | 10 GB |
| **Premium** | 10,000 dakika | 100 GB |
| **Ultimate** | 50,000 dakika | 500 GB |

**Ortalama test süresi:** 5-7 dakika (normal), 8-12 dakika (Docker)

**400 dakika = ~50-80 test koşusu/ay**

---

## Sorun Giderme

### Pipeline Başlamadı
**Çözüm:**
- `.gitlab-ci.yml` dosyasının **kök dizinde** olduğunu kontrol edin
- YAML syntax'ını kontrol edin: CI/CD → Editor → Validate

### npm ci Hatası
```
npm ERR! The `npm ci` command can only install with an existing package-lock.json
```

**Çözüm:**
- `package-lock.json` dosyasının commit edildiğini kontrol edin
- Yoksa: `npm install` → Commit → Push

### Docker Service Hatası
```
Cannot connect to the Docker daemon
```

**Çözüm:**
- `services: - docker:dind` eklendiğinden emin olun
- `image: docker:latest` kullanın

### Runner Stuck
Pipeline "pending" durumunda takılı kaldıysa:
- GitLab.com shared runner'lar meşgul olabilir
- 5-10 dakika bekleyin veya kendi runner'ınızı kurun

---

## Gelişmiş Özellikler

### Paralel Test Çalıştırma:
```yaml
playwright-test:
  parallel:
    matrix:
      - BROWSER: [chromium, firefox, webkit]
  script:
    - npx playwright test --project=$BROWSER
```

### Sadece Belirli Branch'lerde Koş:
```yaml
only:
  - main
  - develop
except:
  - feature/*
```

### Farklı Stage'ler:
```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - npm ci

test:
  stage: test
  script:
    - npx playwright test

deploy:
  stage: deploy
  script:
    - echo "Deploy to production"
  only:
    - main
```

### Environment Variables:
```yaml
variables:
  BASE_URL: "https://staging.example.com"

script:
  - npx playwright test
```

**Secrets:**
- Settings → CI/CD → Variables → Add variable
- Type: **Variable** (normal) veya **File** (dosya)
- Protected: ✅ (Sadece protected branch'lerde kullan)
- Masked: ✅ (Loglarda gizle)

---

## Avantajlar

✅ **Entegre:** GitLab ile aynı platformda  
✅ **Güçlü:** Advanced pipeline özellikleri  
✅ **Artifacts:** 30 gün rapor saklama  
✅ **Containerized:** Docker desteği built-in  
✅ **Ücretsiz:** 400 dakika/ay  

---

## GitHub vs GitLab Karşılaştırma

| Özellik | GitHub Actions | GitLab CI/CD |
|---------|----------------|--------------|
| **Dosya** | `.github/workflows/*.yml` | `.gitlab-ci.yml` |
| **Ücretsiz Dakika** | 2000/ay (private) | 400/ay |
| **Syntax** | Workflow + Jobs + Steps | Stages + Jobs + Scripts |
| **Docker** | Docker action gerekli | Built-in `docker:dind` |
| **Artifacts** | 90 gün | 30 gün (varsayılan) |

---

## Sonraki Adımlar

- Badge ekleyin README'ye
- Code quality checks ekleyin
- Security scanning ekleyin
- Auto DevOps'u keşfedin
- Kubernetes deployment ekleyin

---

**Başarılar! 🚀**
