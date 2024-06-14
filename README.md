# K8S Homelab - CKA

## Pre Reqs da VM

- 2Gb de ram

- 2 Core de CPU

- Algumas portas precisam estar abertas para que o cluster funcione corretamente, as principais:
   
    - Porta 6443: É a porta padrão usada pelo Kubernetes API Server para se comunicar com os componentes do cluster. É a porta principal usada para gerenciar o cluster e deve estar sempre aberta.
    
    - Portas 10250-10255: Essas portas são usadas pelo kubelet para se comunicar com o control plane do Kubernetes. A porta 10250 é usada para comunicação de leitura/gravação e a porta 10255 é usada apenas para comunicação de leitura.

    - Porta 30000-32767: Essas portas são usadas para serviços NodePort que precisam ser acessíveis fora do cluster. O Kubernetes aloca uma porta aleatória dentro desse intervalo para cada serviço NodePort e redireciona o tráfego para o pod correspondente.

    - Porta 2379-2380: Essas portas são usadas pelo etcd, o banco de dados de chave-valor distribuído usado pelo control plane do Kubernetes. A porta 2379 é usada para comunicação de leitura/gravação e a porta 2380 é usada apenas para comunicação de eleição.

## Modulos do Kernel

1. Desabilitar SWAP
````
sudo swapoff -a
````

2. Crie o arquivo k8s.conf
````
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
````

## Parametros de S.O.

````
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
````

## Instalando pacotes

````
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

sudo apt-mark hold kubelet kubeadm kubect
````
