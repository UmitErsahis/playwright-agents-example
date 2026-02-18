# 🔧 Jenkins - Playwright CI/CD Kurulum Rehberi

Bu rehber, kendi bilgisayarınızda Jenkins kurarak Playwright testlerini çalıştırmayı gösterir.

---

## Ön Gereksinimler

- ✅ Docker Desktop kurulu (Jenkins'i Docker'da çalıştıracağız)
- ✅ Git kurulu
- ✅ Proje klasörü
- ❌ **Jenkins hesabı GEREKMESİZ** (Lokal kurulum)

---

## Adım 1: Docker Desktop'ı Başlatın

### Windows:
```powershell
Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe"
Start-Sleep -Seconds 30
```

### Manuel:
- Start menüden **"Docker Desktop"** açın
- Docker ikonu sistem tepsisinde görünene kadar bekleyin

---

## Adım 2: Jenkins Container'ını Başlatın

```powershell
# Jenkins'i Docker ile başlat
docker run -d `
  --name jenkins `
  -p 8080:8080 `
  -p 50000:50000 `
  -v jenkins_home:/var/jenkins_home `
  -v /var/run/docker.sock:/var/run/docker.sock `
  -u root `
  jenkins/jenkins:lts
```

**Parametreler:**
- `-d`: Background'da çalış
- `--name jenkins`: Container ismi
- `-p 8080:8080`: Web arayüzü portu
- `-p 50000:50000`: Agent portu
- `-v jenkins_home:/var/jenkins_home`: Jenkins verilerini sakla
- `-v /var/run/docker.sock:/var/run/docker.sock`: Docker erişimi
- `-u root`: Root kullanıcı (Docker komutları için)

**Süre:** ~30-60 saniye başlama süresi

---

## Adım 3: Jenkins Container'ına Docker CLI Kurun

```powershell
# Docker CLI'yi Jenkins container'ına kur
docker exec jenkins sh -c "apt-get update && apt-get install -y docker.io"
```

**Süre:** ~2-3 dakika

---

## Adım 4: Admin Şifresini Alın

### İlk Kurulum İçin:
```powershell
# Şifreyi göster
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

**Çıktı örneği:**
```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
```

**Bu şifreyi kopyalayın!** ✂️

---

## Adım 5: Jenkins Web Arayüzünü Açın

### PowerShell'den:
```powershell
Start-Process "http://localhost:8080"
```

### Manuel:
Tarayıcınızda: **http://localhost:8080**

---

## Adım 6: İlk Kurulum Sihirbazı

### 1️⃣ Unlock Jenkins:
- Admin şifresini yapıştırın
- **"Continue"** → Tıklayın

### 2️⃣ Customize Jenkins:
- **"Install suggested plugins"** → Tıklayın
- 5-10 dakika plugin kurulumu (☕ kahve molası)

### 3️⃣ Create First Admin User:
- **Username:** `admin` (veya istediğiniz)
- **Password:** Güçlü şifre girin
- **Full name:** Adınız
- **Email:** Email adresiniz
- **"Save and Continue"**

### 4️⃣ Instance Configuration:
- **Jenkins URL:** `http://localhost:8080/`
- **"Save and Finish"**

### 5️⃣ Jenkins is ready!
- **"Start using Jenkins"** → Tıklayın

---

## Adım 7: Jenkinsfile Oluşturun

### Dosya: `Jenkinsfile` (Proje kök dizininde)

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t playwright-agents-example .'
            }
        }
        
        stage('Run Playwright Tests in Docker') {
            steps {
                sh '''
                    docker run --rm \
                    -v ${WORKSPACE}/test-results:/app/test-results \
                    -v ${WORKSPACE}/playwright-report:/app/playwright-report \
                    playwright-agents-example
                '''
            }
        }
    }
    
    post {
        always {
            // Test sonuçlarını arşivle
            archiveArtifacts artifacts: 'playwright-report/**/*', allowEmptyArchive: true
            archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
        }
    }
}
```

**Dosya konumu:**
```
playwright-agents-example/
├── Jenkinsfile    ← BU DOSYA (kök dizinde)
├── tests/
├── package.json
└── playwright.config.ts
```

---

## Adım 8: Jenkins'te Pipeline Projesi Oluşturun

### Jenkins Dashboard'da:

1. **"New Item"** (veya **"+ New Item"**) → Tıklayın
2. **Item name:** `Playwright-Tests`
3. **Pipeline** seçin → **OK**

### Pipeline Yapılandırması:

**Pipeline Section:**
- **Definition:** `Pipeline script from SCM`
- **SCM:** `Git`
- **Repository URL:** `https://github.com/KULLANICI_ADINIZ/playwright-agents-example.git`
- **Credentials:** `None` (public repo için)
- **Branch Specifier:** `*/main`
- **Script Path:** `Jenkinsfile`

**Save** → Tıklayın

---

## Adım 9: İlk Build'i Başlatın

1. **"Build Now"** → Tıklayın (sol menü)
2. **Build History'de** **#1** görünecek 🟡
3. **#1** → Tıklayın
4. **"Console Output"** → Tıklayın
5. Logları canlı izleyin

---

## Adım 10: Test Raporlarını Görüntüleyin

### Build tamamlandıktan sonra:

**Yöntem 1: Workspace:**
1. Build sayfasında **"Workspace"** → Tıklayın
2. **"playwright-report"** klasörünü açın
3. Dosyaları görüntüleyin

**Yöntem 2: Artifacts:**
1. Build sayfasında **"Build Artifacts"** bölümü
2. **"playwright-report"** → ZIP indir
3. ZIP'i açın
4. `index.html` → Tarayıcıda açın

---

## Otomatik Build (Opsiyonel)

### Her Commit'te Otomatik Çalışsın:

**Pipeline Configure:**
1. **Playwright-Tests** → **Configure**
2. **Build Triggers** bölümü
3. **"Poll SCM"** ✅ işaretleyin
4. **Schedule:** `H/5 * * * *` (Her 5 dakikada kontrol)
5. **Save**

**Açıklama:**
- Jenkins her 5 dakikada GitHub'ı kontrol eder
- Yeni commit varsa otomatik build başlar

---

## Jenkins Komutları

```powershell
# Jenkins container'ı durdur
docker stop jenkins

# Jenkins container'ı başlat (tekrar)
docker start jenkins

# Jenkins loglarını gör
docker logs jenkins

# Jenkins'i tamamen sil (DİKKAT!)
docker stop jenkins
docker rm jenkins
docker volume rm jenkins_home
```

---

## Sorun Giderme

### Port 8080 Kullanımda
```
Error: Bind for 0.0.0.0:8080 failed
```

**Çözüm:**
```powershell
# Farklı port kullan
docker run -d --name jenkins -p 9090:8080 ...
# Sonra http://localhost:9090 açın
```

### Docker Daemon Hatası (Jenkins Container'ında)
```
Cannot connect to the Docker daemon
```

**Çözüm:**
```powershell
# Docker socket mount'u kontrol edin
docker inspect jenkins | Select-String "Mounts" -Context 0,10

# Yoksa yeniden başlatın:
docker rm -f jenkins
docker run -d ... -v /var/run/docker.sock:/var/run/docker.sock ...
```

### "docker: not found" Hatası
**Çözüm:**
```powershell
# Container'a Docker CLI kurun
docker exec jenkins sh -c "apt-get update && apt-get install -y docker.io"
```

### Build Takıldı (Stuck)
**Çözüm:**
- Console Output'u inceleyin
- Timeout ekleyin: `timeout(time: 30, unit: 'MINUTES') { ... }`
- Build'i durdurun: Build sayfasında **"Stop"**

### Admin Şifresi Unutuldu
**Çözüm:**
```powershell
# Yeni admin kullanıcısı oluştur
docker exec jenkins sh -c 'echo "jenkins.model.Jenkins.instance.securityRealm.createAccount(\"admin2\", \"newpassword\")" | java -jar /var/jenkins_home/war/WEB-INF/jenkins-cli.jar -s http://localhost:8080/ groovy ='
```

---

## Gelişmiş Özellikler

### Parametreli Build:
```groovy
pipeline {
    parameters {
        choice(name: 'BROWSER', choices: ['chromium', 'firefox', 'webkit'], description: 'Browser')
        string(name: 'BASE_URL', defaultValue: 'https://example.com', description: 'Base URL')
    }
    
    stages {
        stage('Test') {
            steps {
                sh "npx playwright test --project=${params.BROWSER}"
            }
        }
    }
}
```

### Email Bildirimleri:
```groovy
post {
    failure {
        emailext (
            subject: "Jenkins Build Failed: ${env.JOB_NAME}",
            body: "Build ${env.BUILD_NUMBER} failed. Check: ${env.BUILD_URL}",
            to: "your-email@example.com"
        )
    }
}
```

### Paralel Çalıştırma:
```groovy
stage('Test') {
    parallel {
        stage('Chromium') {
            steps {
                sh 'npx playwright test --project=chromium'
            }
        }
        stage('Firefox') {
            steps {
                sh 'npx playwright test --project=firefox'
            }
        }
    }
}
```

---

## Avantajlar

✅ **Ücretsiz:** Tamamen bedava, limit yok  
✅ **Lokal:** Kendi bilgisayarınızda çalışır  
✅ **Kontrol:** Tam kontrol sizde  
✅ **Hesap Gerektirmez:** Cloud hesabı yok  
✅ **Özelleştirilebilir:** Yüzlerce plugin  
✅ **Sınırsız Build:** İstediğiniz kadar koşun  

---

## Dezavantajlar

❌ **Kurulum Gerekli:** Docker + Jenkins kurulumu  
❌ **Bakım:** Manuel güncelleme gerekli  
❌ **Lokal:** Internet bağlantısı gerekmese de bilgisayar açık olmalı  
❌ **Yalnız:** Takım için merkezi sunucu kurmak gerekir  

---

## Platform Karşılaştırması

| Özellik | GitHub Actions | GitLab CI/CD | Jenkins |
|---------|----------------|--------------|---------|
| **Hesap** | ✅ GitHub | ✅ GitLab | ❌ Gereksiz |
| **Kurulum** | ❌ Yok | ❌ Yok | ✅ Gerekli |
| **Konum** | ☁️ Bulut | ☁️ Bulut | 💻 Local |
| **Ücretsiz** | 2000 dk/ay | 400 dk/ay | ♾️ Sınırsız |
| **Başlangıç** | Kolay | Kolay | Orta |

---

## Sonraki Adımlar

- Blue Ocean plugin kurun (Modern UI)
- HTML Publisher plugin kurun (Raporlar için)
- Jenkins agents ekleyin (Dağıtık build)
- HTTPS yapılandırması yapın
- Backup stratejisi oluşturun

---

**Başarılar! 🚀**
