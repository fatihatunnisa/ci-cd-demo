# 🚀 Setup Jenkins dan Docker di Laptop

## 1️⃣ Install Jenkins di macOS
Jika belum terinstall, jalankan:

```sh
brew install jenkins-lts
brew services start jenkins-lts
```

Buka Jenkins di browser:

```
http://localhost:8080
```

Dapatkan password admin untuk login:

```sh
cat /Users/$(whoami)/.jenkins/secrets/initialAdminPassword
```

## 2️⃣ Install Docker di macOS
Jika belum ada, install dengan:

```sh
brew install --cask docker
```

Pastikan Docker berjalan dengan:

```sh
docker --version
```

---

## 📌 3️⃣ Buat Repositori GitLab untuk Aplikasi

Buat folder proyek di laptop:

```sh
mkdir ci-cd-demo && cd ci-cd-demo
git init
```

Buat aplikasi Python sederhana (`app.py`):

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, CI/CD!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

Buat `requirements.txt` untuk dependency:

```sh
echo "flask" > requirements.txt
```

Buat `Dockerfile` untuk aplikasi:

```dockerfile
FROM python:3.9
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Tambahkan ke Git dan commit:

```sh
git add .
git commit -m "Initial commit"
git remote add origin https://gitlab.com/username/ci-cd-demo.git
git push -u origin main
```

---

## 🔄 4️⃣ Buat Pipeline di Jenkins

### 🔹 Tambahkan `Jenkinsfile` ke Repository
Buat file `Jenkinsfile` di dalam folder proyek:

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ci-cd-demo'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://gitlab.com/username/ci-cd-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE pytest tests/'
            }
        }

        stage('Deploy to Localhost') {
            steps {
                sh 'docker run -d -p 5000:5000 $DOCKER_IMAGE'
            }
        }
    }
}
```

### 🔹 Konfigurasi Jenkins Job
1. Buka **Jenkins**
2. Klik **"New Item"** → Pilih **"Pipeline"** → Masukkan nama proyek
3. Pilih **"Pipeline"** dan scroll ke bawah ke bagian **"Pipeline Script"**
4. Masukkan script **Jenkinsfile** di atas
5. Klik **"Save"** dan jalankan pipeline

---

## ✅ 5️⃣ Hasil Akhir
- **Jenkins** akan checkout kode dari **GitLab**
- **Build Docker image**
- **Jalankan test otomatis**
- **Deploy aplikasi ke localhost**

Akses aplikasi di browser:

```
http://localhost:5000
```

---

💡 **Selamat!** Anda telah berhasil membuat pipeline otomatis dengan **Jenkins, GitLab, dan Docker**! 🚀

