Kubernetes 基础集群部署与应用管理

项目简介

这个项目通过 Kubernetes 部署与管理应用，使用 kubeadm 搭建了一个 3 节点的 Kubernetes 集群，并成功地部署了 Ghost 博客应用。项目还配置了自动扩缩容（HPA），使应用能够根据流量需求自动扩展 Pod 数量以应对突发流量。此外，利用 Helm 简化了部署流程，将部署时间大幅缩短。

技术栈
	•	Kubernetes: 开源的容器编排平台，自动化管理容器化应用的部署、扩展和管理。
	•	kubeadm: 用于快速初始化和管理 Kubernetes 集群的工具。
	•	Helm: Kubernetes 的包管理工具，简化应用部署和管理。
	•	Ghost: 一个简单而强大的开源博客平台，作为示例应用进行部署。

项目亮点

1. 搭建 Kubernetes 集群

使用 kubeadm 在三台主机上成功搭建了一个 3 节点的 Kubernetes 集群，集群配置如下：
	•	主节点（Master）：10.0.0.231
	•	工作节点 1：10.0.0.232
	•	工作节点 2：10.0.0.233

集群包括 Kubernetes 核心组件如 API Server、Controller Manager、Scheduler 和 etcd 等。

2. 部署 Ghost 博客应用

在 Kubernetes 集群上，使用 Helm 安装和管理 Ghost 博客应用。Ghost 是一个现代化的开源博客平台，应用通过 Docker 容器化部署，并且利用 Helm charts 管理配置和部署。

部署过程中包括：
	•	配置持久化存储卷和数据库连接。
	•	配置服务发现和负载均衡，确保应用高可用。

3. 自动扩缩容（HPA）

通过配置 Horizontal Pod Autoscaler（HPA），Ghost 博客应用能够根据集群负载（如 CPU 和内存使用率）自动扩展或缩减 Pod 数量。这样，当流量高峰时，系统会自动扩展 Pod 数量，而在低流量时，减少资源消耗。

4. 简化部署流程（Helm）

通过 Helm 包管理工具，大大简化了 Ghost 应用的部署过程，避免了手动配置 YAML 文件的繁琐步骤，节省了部署时间。

部署流程

前提条件
	•	至少 3 台机器，配置为 Kubernetes 集群中的 3 节点（1 主节点，2 工作节点）。
	•	安装了 Docker 和 Kubernetes 相关工具：kubeadm、kubelet、kubectl。
	•	安装 Helm。

步骤

1. 初始化主节点

在主节点 10.0.0.231 上执行以下命令初始化 Kubernetes 集群：

sudo kubeadm init --pod-network-cidr=10.244.0.0/16

初始化完成后，记下 kubeadm join 命令，用于在工作节点上加入集群。

2. 配置 kubectl

初始化后，配置 kubectl 访问集群


3. 安装 Pod 网络

在主节点上安装网络插件（Flannel）：

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

4. 加入工作节点

在工作节点 10.0.0.232 和 10.0.0.233 上执行 kubeadm join 命令，加入集群：

kubeadm join 10.0.0.231:6443 --token <token> --discovery-token-ca-cert-hash <hash>

5. 安装 Helm

在主节点上安装 Helm：

curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

6. 部署 Ghost 应用

通过 Helm 部署 Ghost 博客应用：

helm install ghost bitnami/ghost

你可以通过以下命令查看应用的服务：

kubectl get svc

7. 配置 HPA（自动扩缩容）

为 Ghost 应用配置 HPA：

kubectl autoscale deployment ghost --cpu-percent=50 --min=1 --max=10

HPA 将确保当 CPU 使用率超过 50% 时自动扩展 Pod 数量。

