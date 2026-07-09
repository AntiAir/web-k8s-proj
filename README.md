### web-k8s-project   test
專案位置:
rocky 10-rocky k8s
```
benjamin@localhost:~/my-k8s/web-k8s-proj$ 
```
---
```
# 執行
benjamin@localhost:~/my-k8s/web-k8s-proj$ podman play kube apache-pod.yml

# 刪除
benjamin@localhost:~/my-k8s/web-k8s-proj$ podman pod rm -f my-apache-pod

# 查看
benjamin@localhost:~/my-k8s/web-k8s-proj$ podman pod ps
POD ID        NAME           STATUS      CREATED        INFRA ID      # OF CONTAINERS
1b32013812bf  my-apache-pod  Running     2 minutes ago  4715a5d0e309  2

```
打開網頁
![image](https://hackmd.io/_uploads/SyhlWqpQfg.png)


下載kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

benjamin@localhost:~$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
[sudo] password for benjamin: 
benjamin@localhost:~$ kubectl version --client --output=yaml
clientVersion:
  buildDate: "2026-06-11T18:12:06Z"
  compiler: gc
  gitCommit: 24e2b02af5543d7910c2bb074c7264df5a8f0467
  gitTreeState: clean
  gitVersion: v1.36.2
  goVersion: go1.26.4
  major: "1"
  minor: "36"
  platform: linux/amd64
kustomizeVersion: v5.8.1

```

---
```
    1  podman pod create --name my-apache-pod -p 8080:80
    2  podman ps
    3  podman pod ps
    4  podman run -d --pod my-apache-pod --name my-web docker.io/library/httpd
    5  podman generate kube my-apache-pod
    6  podman generate kube my-apache-pod >apache-pod.yml
    7  podman pod rm -f my-apache-pod
    8  podman play kube apache-pod.yml
    9  podman down kube apache-pod.yml
   10  ll
   11  podman play kube --down apache-pod.yml
   12  ll
   13  mkdir -p /my-k8s/my-web-project
   14  sudo mkdir -p /my-k8s/my-web-project
   15  ll
   16  sudo mkdir -p /my-k8s/my-web-project
   17  ll
   18  pwd
   19  sudo mkdir -p /my-k8s/my-web-project
   20  ll
   21  cd /my-k8s/my-web-project/
   22  ll
   23  mv ~/apache-pod.yml .
   24  sudo mv ~/apache-pod.yml .
   25  ll
   26  su - root
   27  cd
   28  pwd
   29  ll
   30  pwd
   31  mkdir -p ~/my-k8s/my-web-project
   32  ll
   33  cd /my-k8s/my-web-project/
   34  ll
   35  mv apache-pod.yml ~/my-k8s/my-web-project/
   36  sudo mv apache-pod.yml ~/my-k8s/my-web-project/
   37  cd
   38  ll
   39  cd my-k8s/
   40  cd my-web-project/
   41  ll
   42  cd
   43  sudo rm -rf /my-k8s
   44  pwd
   45  ll
   46  cd my-k8s/
   47  ll
   48  sudo mv my-web-project/ web-project/
```
### (20260710 颱風放假日) Ubuntu 22.04 輕量化 K8s 學習環境架設指南 (使用 Kind + Docker)

本指南記錄了將專案從 Rocky Linux (Podman) 遷移至 Ubuntu 22.04，並利用 **Kind (Kubernetes in Docker)** 現場編譯建立真實單機 K8s 叢集，最終成功部署與推送 GitHub 的完整實戰過程。

---

## 🛠 步驟 1：安裝基礎工具與容器引擎 (Docker)

在乾淨的 Ubuntu 環境中，先安裝 `curl`、`Docker`，並設定免 `sudo` 權限。

```bash
# 1. 更新套件清單並安裝基礎工具與 Docker
sudo apt-get update
sudo apt-get install -y curl docker.io

# 2. 將目前的使用者 (ubuntu) 加入 docker 群組，免去每次輸入 sudo
sudo usermod -aG docker \$USER

# 3. 立刻更新群組設定（免登出登入，即刻生效）
newgrp docker
```
*💡 驗證：輸入 `docker ps`，若無跳出 Permission denied 即代表權限設定成功。*

---

## 🔧 步驟 2：安裝 K8s 控制工具 (kubectl)

使用 Ubuntu 官方推薦的 `snap` 一鍵安裝 Kubernetes 終端控制工具。

```bash
sudo snap install kubectl --classic
```

---

## 🏗 步驟 3：使用 Go 語言現場編譯安裝 Kind

為了解決二進位檔下載容易遭到網路重導向（下載到 42KB HTML 錯誤網頁）的問題，採用終極大招：**現場編譯安裝**。

```bash
# 1. 安裝 Go 語言編譯環境
sudo apt update && sudo apt install -y golang-go

# 2. 線上下載官方原始碼並現場編譯 Kind
go install sigs.k8s.io/kind@v0.24.0

# 3. 將編譯好的執行檔複製到系統公用目錄
sudo cp ~/go/bin/kind /usr/local/bin/kind

# 4. 驗證版本
kind version
```
*預期輸出：`kind v0.24.0 ...`*

---

## 🚀 步驟 4：一鍵啟動 K8s 叢集

```bash
kind create cluster --name my-k8s-cluster
```
*Kind 會自動在 Docker 內部啟動一個扮演「K8s 控制節點」的容器，並自動綁定設定好你的 `kubectl`。*

---

## 📦 步驟 5：從 GitHub 拉取專案並部署

將 Rocky Linux 時代建立的 GitHub 倉庫直接複製到 Ubuntu 中運行。

```bash
# 1. 複製專案
git clone https://github.com/AntiAir/web-k8s-proj
cd web-k8s-proj

# 2. 檢查 K8s 叢集節點狀態
kubectl get nodes

# 3. 使用正統 K8s 指令部署 Apache Pod
kubectl create -f apache-pod.yml

# 4. 觀察 Pod 狀態，等待其轉為 Running
kubectl get pods
```

---

## 🌐 步驟 6：測試存取網頁服務

由於 K8s 內部網路隔離，我們必須透過 Kubernetes 標準的 **Port-forward (連接埠轉發)** 打通流量通道：

```bash
# 執行此指令會常駐並開啟通道 (請保持視窗開啟)
kubectl port-forward pod/my-apache-pod 8080:80
```

**打開另一個終端機視窗測試連線：**
```bash
curl http://localhost:8080
```
*預期輸出：`It works!`*

---

## 🔄 步驟 7：環境演進與持續提交 GitHub

Kubernetes 具有**環境無關性**，這份同一張 YAML 檔案不論底層是 Rocky (Podman) 還是 Ubuntu (Docker) 都能完美兼容。在同一個倉庫下提交修改，最能展現雲端原生「一次編寫，到處運行」的魅力。

```bash
# 1. 設定 Git 客戶端基本資訊
git config --global user.name "你的GitHub帳號"
git config --global user.email "你的GitHub註冊信箱"

# 2. 提交變更並推送
git add apache-pod.yml
git commit -m "Migrate cluster runtime from Podman (Rocky) to Docker (Ubuntu)"
git push origin main
```
# Test SSH
