# 📦 Basic Java Maven — CI/CD Pipeline

[![Build, Test & SonarCloud Analysis](https://github.com/ariqhisyam/ci-cd-mkpl-task/actions/workflows/maven.yml/badge.svg)](https://github.com/ariqhisyam/ci-cd-mkpl-task/actions/workflows/maven.yml)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=ariqhisyam_ci-cd-mkpl-task&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=ariqhisyam_ci-cd-mkpl-task)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=ariqhisyam_ci-cd-mkpl-task&metric=coverage)](https://sonarcloud.io/summary/new_code?id=ariqhisyam_ci-cd-mkpl-task)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=ariqhisyam_ci-cd-mkpl-task&metric=bugs)](https://sonarcloud.io/summary/new_code?id=ariqhisyam_ci-cd-mkpl-task)

> Project Java sederhana menggunakan Maven yang dilengkapi pipeline **CI/CD otomatis** dengan GitHub Actions dan analisis kualitas kode menggunakan **SonarCloud**.

---

## 📁 Struktur Project

```
Basic-Java-Maven-main/
├── .github/
│   └── workflows/
│       └── maven.yml          # Konfigurasi GitHub Actions CI/CD
├── src/
│   ├── main/
│   │   └── java/
│   │       ├── Counter.java   # Class utama dengan logika counter
│   │       └── Driver.java    # Entry point aplikasi
│   └── test/
│       └── java/
│           └── CounterTest.java  # JUnit test cases
└── pom.xml                    # Konfigurasi Maven & dependensi
```

---

## 🚀 Tentang CI/CD Pipeline

Pipeline CI/CD ini berjalan **secara otomatis** setiap kali terjadi:
- **`push`** ke branch `main`
- **`pull request`** yang dibuka, diperbarui, atau dibuka kembali ke branch `main`

### Tujuan Pipeline

| Tujuan | Penjelasan |
|---|---|
| **Otomasi Kompilasi** | Kode dikompilasi otomatis setiap ada perubahan |
| **Otomasi Testing** | JUnit test dijalankan otomatis tanpa perlu manual |
| **Code Coverage** | Laporan coverage dihasilkan via JaCoCo |
| **Analisis Kualitas** | Kode dianalisis SonarCloud untuk bug, smell, dan kerentanan |
| **Pencegahan Error** | Build gagal otomatis jika ada error kompilasi atau test gagal |

---

## 🔄 Alur Pipeline (GitHub Actions)

```
Developer push kode ke GitHub
          │
          ▼
┌─────────────────────────────────┐
│     GitHub Actions Berjalan     │
│         (ubuntu-latest)         │
└─────────────────────────────────┘
          │
          ├─ Step 1: Checkout Repository
          │          └─ Ambil seluruh history kode (fetch-depth: 0)
          │
          ├─ Step 2: Setup JDK 17
          │          └─ Install Java 17 (Zulu distribution)
          │
          ├─ Step 3: Cache SonarCloud Packages
          │          └─ Cache hasil scan sebelumnya agar lebih cepat
          │
          ├─ Step 4: Cache Maven Packages
          │          └─ Cache dependensi Maven agar tidak re-download
          │
          ├─ Step 5: Validate Maven Project
          │          └─ Cek apakah pom.xml valid
          │          └─ ❌ GAGAL jika konfigurasi Maven rusak
          │
          ├─ Step 6: Compile Source Code
          │          └─ Kompilasi Counter.java & Driver.java
          │          └─ ❌ GAGAL jika ada error sintaks Java
          │
          ├─ Step 7: Run JUnit Tests & Generate Coverage
          │          └─ Jalankan 3 test: testReset, testIncrement, testDecrement
          │          └─ Generate laporan JaCoCo (jacoco.xml)
          │          └─ ❌ GAGAL jika ada test yang tidak lulus
          │
          ├─ Step 8: Upload JUnit Test Report
          │          └─ Simpan hasil test di GitHub Artifacts (7 hari)
          │
          ├─ Step 9: Upload JaCoCo Coverage Report
          │          └─ Simpan laporan coverage di GitHub Artifacts (7 hari)
          │
          ├─ Step 10: SonarCloud Analysis
          │          └─ Analisis kualitas kode ke sonarcloud.io
          │          └─ Deteksi: Bug, Code Smell, Vulnerability, Coverage
          │          └─ ❌ GAGAL jika SONAR_TOKEN tidak valid
          │
          └─ Step 11: Upload JAR Artifact
                     └─ File .jar tersedia untuk diunduh dari GitHub
```

---

## 🧪 Unit Testing (JUnit 4)

Project ini memiliki **3 test case** di `CounterTest.java`:

| Test Method | Skenario yang Diuji | Ekspektasi |
|---|---|---|
| `testReset()` | Increment 10x lalu `reset()` | Nilai kembali ke `0` |
| `testIncrement()` | Loop increment 1-9 | Nilai bertambah 1 setiap kali |
| `testDecrement()` | Loop decrement 1-9 | Nilai berkurang 1 (negatif) |

### Menjalankan Test Secara Lokal

```bash
# Jalankan semua test
mvn test

# Output yang diharapkan:
# Tests run: 3, Failures: 0, Errors: 0, Skipped: 0
# BUILD SUCCESS
```

---

## 📊 Code Coverage (JaCoCo)

Pipeline secara otomatis menghasilkan laporan **code coverage** menggunakan plugin JaCoCo. Laporan tersedia di:

- **GitHub Artifacts**: `jacoco-coverage-report` (setiap kali workflow berjalan)
- **SonarCloud Dashboard**: Ditampilkan sebagai persentase coverage

---

## 🔍 Analisis Kualitas Kode (SonarCloud)

Kode dianalisis secara otomatis menggunakan **SonarCloud** setiap kali ada push ke `main`.

**Metrics yang dianalisis:**

| Metric | Deskripsi |
|---|---|
| **Quality Gate** | Status keseluruhan: Pass / Fail |
| **Bugs** | Error logika yang bisa menyebabkan crash |
| **Vulnerabilities** | Celah keamanan pada kode |
| **Code Smells** | Kode yang sulit dibaca atau dipelihara |
| **Coverage** | Persentase kode yang tercakup oleh test |
| **Duplications** | Duplikasi kode yang perlu direfactor |

🔗 **Dashboard**: [sonarcloud.io/project/overview?id=ariqhisyam_ci-cd-mkpl-task](https://sonarcloud.io/project/overview?id=ariqhisyam_ci-cd-mkpl-task)

---

## ⚙️ Konfigurasi

### GitHub Secrets yang Diperlukan

| Secret | Keterangan |
|---|---|
| `SONAR_TOKEN` | Token autentikasi SonarCloud (dari My Account → Security) |
| `GITHUB_TOKEN` | Otomatis tersedia dari GitHub Actions |

### Dependensi (pom.xml)

| Dependensi | Versi | Fungsi |
|---|---|---|
| `junit` | 4.11 | Framework unit testing |
| `jacoco-maven-plugin` | 0.8.11 | Menghasilkan laporan code coverage |

---

## 🛡️ Bagaimana Pipeline Mencegah Masalah

```
❌ Ada error sintaks Java
   → Step "Compile Source Code" GAGAL
   → Build berhenti, developer langsung tahu

❌ Ada unit test yang gagal
   → Step "Run JUnit Tests" GAGAL
   → Kode bermasalah tidak bisa masuk ke main

❌ Kualitas kode buruk (bug, security issue)
   → SonarCloud melaporkan ke dashboard
   → Tim bisa review sebelum merge

✅ Semua step berhasil
   → File JAR tersedia sebagai artifact
   → Kode aman untuk di-deploy
```

---

## 🖥️ Cara Menjalankan Secara Lokal

```bash
# Clone repository
git clone https://github.com/ariqhisyam/ci-cd-mkpl-task.git
cd ci-cd-mkpl-task

# Kompilasi
mvn compile

# Jalankan test
mvn test

# Build JAR
mvn package

# Jalankan program
java -cp target/ContohProject-0.0.1-SNAPSHOT.jar Driver
```

---

## 👤 Author

**Ariq Hisyam Nabil**  
Repository: [github.com/ariqhisyam/ci-cd-mkpl-task](https://github.com/ariqhisyam/ci-cd-mkpl-task)
