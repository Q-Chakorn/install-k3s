# test install-k3s for single node

update

```bash
sudo apt update && sudo apt upgrade -y
```
วิธีใช้ k3s ร่วมกับ Docker (แทน containerd)
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker --now
```
ตรวจสอบว่า Docker ทำงาน:
```bash
docker version
```
close swap

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```
install k3s แบบใช้งานร่วมกับ docker (แต่ต้องติดตั้ง docekr ก่อน)

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--docker" sh -
```
check status

```bash
sudo k3s kubectl get nodes
```
เพิ่ม alias เพื่อใช้ kubectl ได้สะดวก
```bash
echo 'alias kubectl="sudo k3s kubectl"' >> ~/.bashrc
source ~/.bashrc
```
ถ้าอยากให้ kubectl ใช้ได้โดยไม่ต้องพิมพ์ sudo k3s
```bash
mkdir -p $HOME/.kube
sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
หรือจะ export ไว้ใน .bashrc:
```bash
echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bashrc
source ~/.bashrc
```
ตรวจสอบว่า Pod ใช้ Docker จริง
```bash
kubectl get nodes -o wide
```
หรือดู Runtime โดยตรง
```bash
kubectl get node $(hostname) -o jsonpath='{.status.nodeInfo.containerRuntimeVersion}'
```
ควรได้ output เช่น:
```text
docker://....
```

