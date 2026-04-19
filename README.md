# ASP.NET CI/CD with Azure DevOps & Docker Desktop Kubernetes

## 🎯 Proje Amacı
Bu projenin amacı, **ASP.NET Core** tabanlı bir web uygulamasının Azure DevOps Repo üzerinde yönetilmesi, VS Code üzerinden yapılan `commit` & `push` işlemi sonrası Azure DevOps Pipeline’ın otomatik olarak tetiklenmesi ve uygulamanın Docker Desktop üzerinde çalışan Kubernetes ortamına otomatik olarak deploy edilmesidir.

Bu çalışma, manuel müdahale olmadan uçtan uca çalışan bir **CI/CD** sürecini göstermektedir.

## 🛠️ Kullanılan Teknolojiler
* **ASP.NET Core**
* **Azure DevOps** (Repo & Pipelines)
* **Azure DevOps Self-Hosted Agent** (Local PC)
* **Docker Desktop**
* **Kubernetes** (Docker Desktop Kubernetes)
* **kubectl**
* **Visual Studio Code**

## 🔄 Mimari Akış
1. Proje kaynak kodu Azure DevOps Repo’da tutulur.
2. Geliştirici VS Code üzerinden kod değişikliği yapar.
3. Değişiklik `commit` edilip repoya `push` edilir.
4. `master` branch’ine yapılan push işlemi pipeline’ı otomatik olarak tetikler.
5. Pipeline, geliştiricinin kendi bilgisayarında çalışan self-hosted agent üzerinde çalışır.
6. Docker image, Docker Desktop Linux engine (`desktop-linux`) context’inde build edilir.
7. Kubernetes manifest dosyaları uygulanır.
8. Deployment yeni image ile güncellenir ve rollout tamamlanır.
9. Uygulama Kubernetes üzerinde ayağa kalkar ve web üzerinden erişilebilir hale gelir.

---

## CI/CD Pipeline Açıklaması (`azure-pipelines.yml`)

### Pipeline Tetiklenmesi

Pipeline, `master` branch’ine yapılan her push işleminde otomatik olarak çalışır.


### Agent Yapılandırması

Pipeline, sanal makine yerine doğrudan geliştiricinin kendi bilgisayarında çalışan self-hosted agent (`local-docker-agent`) üzerinden çalışmaktadır.


### Adım 1 – Docker ve Kubernetes Doğrulama

Pipeline ilk olarak agent üzerinde Docker ve Kubernetes erişimini doğrular.


### Adım 2 – Docker Image Build

Docker image, Docker Desktop Kubernetes ortamının Linux tabanlı olması sebebiyle `desktop-linux` context’inde build edilir.


### Adım 3 – Kubernetes Deploy

Pipeline, Kubernetes manifestlerini uygular ve deployment’ı yeniden başlatarak yeni image’ın aktif olmasını sağlar.


## Kubernetes Kaynakları

### Deployment (`deployment.yaml`)

- Deployment Adı: `aspnet-app`  
- Replica Sayısı: `1`  
- Container Image: `aspnet-app:latest`  
- Container Port: `8081`  


### Service (`service.yaml`)

- Service Adı: `aspnet-service`  
- Service Tipi: `NodePort`  
- Service Port: `8081`  
- NodePort: `30081`  

---

## Uygulamaya Erişim

Kubernetes deploy işlemi tamamlandıktan sonra uygulamaya aşağıdaki adres üzerinden erişilir:

http://localhost:30081

---

## Doğrulama

Deploy işleminin başarılı olduğu aşağıdaki komutlarla doğrulanabilir:

kubectl get pods -o wide
kubectl get svc -o wide
kubectl get endpoints aspnet-service -o wide

Pod durumunun Running olması ve endpoint bilgisinin dolu olması, uygulamanın Kubernetes üzerinde başarıyla çalıştığını gösterir.

---


## 📁 Repository Yapısı
```text
├── azure-pipelines.yml
├── deployment.yaml
├── service.yaml
├── Dockerfile
├── aspnetapp/
│   └── ASP.NET uygulama dosyaları
└── README.md
