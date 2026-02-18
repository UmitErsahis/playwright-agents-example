# 📚 Playwright CI/CD Rehberleri

Bu klasörde Playwright testlerinizi farklı platformlarda çalıştırmak için detaylı rehberler bulunmaktadır.

---

## 📖 Mevcut Rehberler

### 1. 🐳 [Docker Local Guide](./DOCKER_LOCAL_GUIDE.md)
**Konu:** Playwright testlerini Docker container'ında local bilgisayarınızda çalıştırma

**İçerik:**
- Docker Desktop kurulumu
- Dockerfile oluşturma
- Image build etme
- Test çalıştırma (volume mount ile)
- Sorun giderme

**Kim için?**
- Herkes için temel
- Lokal test ortamı isteyenler
- CI/CD'ye geçmeden önce test etmek isteyenler

**Süre:** ~15-20 dakika

---

### 2. ☁️ [GitHub Actions Guide](./GITHUB_ACTIONS_GUIDE.md)
**Konu:** Playwright testlerini GitHub Actions ile otomatik çalıştırma

**İçerik:**
- GitHub repository oluşturma
- Workflow dosyası (.github/workflows/playwright.yml)
- Git push ve otomatik test
- Test raporlarını indirme
- Gelişmiş özellikler (paralel, secrets, scheduling)

**Kim için?**
- GitHub kullanıcıları
- Otomatik CI/CD isteyenler
- Public repo sahipleri (sınırsız dakika)

**Ücretsiz:** 2000 dakika/ay (private), sınırsız (public)

**Süre:** ~20-30 dakika

---

### 3. 🦊 [GitLab CI/CD Guide](./GITLAB_CICD_GUIDE.md)
**Konu:** Playwright testlerini GitLab CI/CD ile otomatik çalıştırma

**İçerik:**
- GitLab hesabı ve proje oluşturma
- Pipeline dosyası (.gitlab-ci.yml)
- Git push ve otomatik test
- Artifacts (raporlar) görüntüleme
- Gelişmiş pipeline özellikleri

**Kim için?**
- GitLab kullanıcıları
- Entegre DevOps platformu isteyenler
- Docker-in-Docker seven geliştiriciler

**Ücretsiz:** 400 dakika/ay

**Süre:** ~20-30 dakika

---

### 4. 🔧 [Jenkins Guide](./JENKINS_GUIDE.md)
**Konu:** Jenkins'i local'de kurarak Playwright testlerini çalıştırma

**İçerik:**
- Jenkins Docker ile kurulum
- Pipeline oluşturma (Jenkinsfile)
- Build çalıştırma
- Web arayüzü kullanımı
- Otomatik tetikleme (poll SCM)

**Kim için?**
- Bulut hesabı istemeyenler
- Tam kontrol isteyenler
- Sınırsız build isteyenler
- Kurumsal kullanım

**Ücretsiz:** Tamamen ücretsiz, sınırsız

**Süre:** ~30-45 dakika

---

## 🎯 Hangi Rehberi Seçmeliyim?

### Senaryoya Göre Seçim:

| Senaryo | Önerilen Platform |
|---------|-------------------|
| **İlk kez öğreniyorum** | 🐳 Docker Local |
| **GitHub'da proje var** | ☁️ GitHub Actions |
| **GitLab'da proje var** | 🦊 GitLab CI/CD |
| **Hesap istemiyorum** | 🔧 Jenkins (local) |
| **Public proje** | ☁️ GitHub Actions (sınırsız) |
| **Kurumsal, private** | 🔧 Jenkins veya kendi runner |
| **Hızlı başlangıç** | ☁️ GitHub Actions |
| **Advanced DevOps** | 🦊 GitLab CI/CD |

---

## 📊 Platform Karşılaştırması

| Özellik | Docker Local | GitHub Actions | GitLab CI/CD | Jenkins |
|---------|--------------|----------------|--------------|---------|
| **Hesap Gerekli** | ❌ | ✅ | ✅ | ❌ |
| **Kurulum** | Docker Desktop | Yok | Yok | Docker + Jenkins |
| **Konum** | 💻 Local | ☁️ Cloud | ☁️ Cloud | 💻 Local |
| **Ücretsiz Limit** | ♾️ | 2000 dk/ay | 400 dk/ay | ♾️ |
| **Dosya** | Dockerfile | .github/workflows/*.yml | .gitlab-ci.yml | Jenkinsfile |
| **Başlangıç Süresi** | 15 dk | 20 dk | 20 dk | 30 dk |
| **Zorluk** | Kolay | Kolay | Kolay | Orta |
| **Raporlama** | Local HTML | Artifacts | Artifacts | Workspace/Archive |
| **Otomatik** | ❌ Manuel | ✅ Her push | ✅ Her push | ✅ Poll SCM |

---

## 🚀 Önerilen Öğrenme Yolu

### Yeni Başlayanlar İçin:
```
1. Docker Local (Temel öğrenme) 
   ↓
2. GitHub Actions (Otomatikleştirme)
   ↓
3. GitLab veya Jenkins (İhtiyaca göre)
```

### Deneyimli Geliştiriciler İçin:
```
1. İhtiyacınıza uygun platformu seçin
2. İlgili guide'ı takip edin
3. 20-30 dakikada hazır!
```

---

## 📁 Proje Yapısı

Tüm rehberlerde aynı proje yapısı kullanılmaktadır:

```
playwright-agents-example/
├── .github/
│   └── workflows/
│       ├── playwright.yml           # GitHub Actions
│       └── playwright-docker.yml    # GitHub Actions (Docker)
├── .gitlab-ci.yml                   # GitLab CI/CD
├── Jenkinsfile                      # Jenkins
├── Dockerfile                       # Docker image tanımı
├── docs/                            # Bu rehberler
│   ├── DOCKER_LOCAL_GUIDE.md
│   ├── GITHUB_ACTIONS_GUIDE.md
│   ├── GITLAB_CICD_GUIDE.md
│   └── JENKINS_GUIDE.md
├── tests/
│   ├── example.spec.ts
│   └── complex-documentation-workflow.spec.ts
├── package.json
└── playwright.config.ts
```

---

## 💡 İpuçları

1. **Önce Local'de Test Edin:** Docker Local ile testlerinizin çalıştığından emin olun
2. **Bir Platform Seçin:** Tüm platformları aynı anda kurmaya çalışmayın
3. **Dosyaları Commit Edin:** `.github/`, `.gitlab-ci.yml`, `Jenkinsfile` gibi dosyaları Git'e ekleyin
4. **Raporları Kaydedin:** Volume mount veya artifacts kullanın
5. **Secrets Kullanın:** API key'leri kod içine yazmayın

---

## 🆘 Yardım Gerekiyorsa

Her rehberde **"Sorun Giderme"** bölümü bulunmaktadır. Yaygın sorunlar ve çözümleri orada bulabilirsiniz.

---

## 📝 Notlar

- Tüm rehberler Windows PowerShell için optimize edilmiştir
- Linux/Mac için komutları uyarlamanız gerekebilir
- Docker Desktop tüm platformlarda çalışır
- Node.js ve npm gereklidir (Docker dışında)

---

**Kolay gelsin! 🚀**

Herhangi bir rehberi açıp adım adım ilerleyebilirsiniz. Başarılar!
