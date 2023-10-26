# ITHome - 工程師踏入Kubernetes的第一步，DIY 自己的 Kubernetes 叢集經驗分享

因製造業特性以及公司對於資安的高度要求，相較雲端平台的方便，點選之間再複雜的環境幾分鐘就幫你架設完成。但在獨立網路的情況下，不論從Docker或Kubernetes技術導入，我們都需要把技術能否落地列為重點，因此必須一步一步純手工來進行安裝及設定。

在地端導入的過程，無法讓你像雲端透過【下一步】【下一步】來取得所需的服務，取而代之，你必須了解更多細節，方便代表你看不見細節，DIY代表一種樂趣，一種學習的樂趣，一種突破障礙的樂趣。本場次實際在Hyper-V模擬一個獨立執行環境，說明地端Kubernetes叢集架設過程應注意的相關事項，讓你輕輕鬆鬆突破由0到1的第一步。

## Enable Nested Virtualization

> * [啟用巢狀虛擬化](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/enable-nested-virtualization#configure-nested-virtualization?WT.mc_id=DOP-MVP-4038201)
> * PowerShell 需要 Admin 權限。

```powershell
Set-VMProcessor -VMName <VMName> -ExposeVirtualizationExtensions $true
Set-VMProcessor -VMName <VMName> -ExposeVirtualizationExtensions $false
```

## Enable Hyper-V and Containers

> * [在 Windows 10 上安裝 Hyper-V](https://learn.microsoft.com/zh-tw/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v?WT.mc_id=DOP-MVP-4038201)
> * PowerShell 需要 Admin 權限。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V

Enable-WindowsOptionalFeature -Online -FeatureName Containers
Get-WindowsOptionalFeature -Online -FeatureName containers
```

## Enable WSL

wsl.exe 非 PowerShell 執行環境，因此輸出中文時會有亂碼。

> [如何使用 WSL 在 Windows 上安裝 Linux](https://learn.microsoft.com/zh-tw/windows/wsl/install?WT.mc_id=DOP-MVP-4038201)

```powershell
# wsl --install
#wsl.exe --list --online
wsl.exe --version
wsl.exe --status
```

## Labs

```powershell
kubectl create deployment web --image=nginx:1.22
kubectl get pods -o wide
kubectl scale deployment web --replicas=3
kubectl get pods -o wide
kubectl scale deployment web --replicas=1
kubectl get pods -o wide
kubectl delete deployment web
kubectl get pods -o wide
```

## Docker Desktop

> [Docker Desktop](https://www.docker.com/products/docker-desktop/)

```powershell
winget install Docker.DockerDesktop
```

## Minikube

K8s 單節點模擬工具。

> [minikube](https://minikube.sigs.k8s.io/docs/start/)

```powershell
winget install minikube
minikube start --driver=hyperv
minikube start --driver=docker

mimikube stop 
mimikube start
```

## Wi-Fi Share

https://github.com/loxia01/PSInternetConnectionSharing

## Get Ubuntu Server (22.04 LTS)

https://ubuntu.com/download/server

## MicroK8s

> [MicroK8s](https://microk8s.io)

### Azure ExpressRoute

[Azure ExpressRoute](https://learn.microsoft.com/zh-tw/azure/expressroute/expressroute-introduction?WT.mc_id=DOP-MVP-4038201)

### Ubuntu Server透過Proxy安裝MicroK8s注意事項

[Ubuntu Server透過Proxy安裝MicroK8s注意事項](https://blog.kkbruce.net/2022/10/ubuntu-server-proxy-microk8s-note.html)

```bash
#!/bin/bash
sudo snap install microk8s --classic
microk8s add-node
```

## Kubernetes

> * [Kubernetes Documentation](https://kubernetes.io/docs/home/)
> * [教你5分鐘在Ubuntu 22.04架設Kubernetes Cluster](https://blog.kkbruce.net/2023/08/5min-ubuntu-2204-kubernetes-cluster.html)

```bash
#!/bin/bash
sudo kubeadm config images pull
sudo kubeadm init --apiserver-advertise-address=192.168.56.60 --pod-network-cidr=10.244.0.0/16
sudo kubeadm init --apiserver-advertise-address=192.168.56.60 --pod-network-cidr=192.168.0.0/16
kubeadm join 192.168.56.60:6443 --token token --discovery-token-ca-cert-??? sha256:???
```

## Kind

K8s多節點模擬工具。

> [Kind](https://kind.sigs.k8s.io), Kubernetes in Docker。

```bash
#!/bin/bash
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

## 小結

Docker Desktop 的 K8s 使用起來會感到比較笨重（啟動，停止）。

minikube 或 Kind 是一個非常合適的測試環境，不斷修改與測試 yaml 非常好用。如果你不需要接觸到 Infra 的話，那麼會是開發人員不錯的選擇。

---

只想要熟悉 Ubuntu 及 K8s 並且不想去搞那堆底層設定的人，那麼 MicroK8s 是個可以考慮的選項。並且它有官方技術支援可以購買。

Kubenetes 是能玩能能摸最完整 K8s 的內容，架設最費力費時，需要學的最多但也最扎實。但技術支援本身由社群運作，除非購買 OpenShift 之類商用服務，如果碰到非大眾化的問題，很容易求助無門。
