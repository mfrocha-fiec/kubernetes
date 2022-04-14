# Deploy do Hadoop no K8s usando HELM

## Definições
* **HELM**: Helm é uma espécie de empacotador para Kubernetes. Dentro de um "chart" do Helm há o deploy e orquestração de vários pods com as aplicações.

## Preparação e Instalação

**Execução**: Nó mestre.

Antes de iniciar, é necessário ter o ferramental básico do kubernetes instalado e o cluster preparado, referenciar esse [tutorial](https://github.com/eduardoluizgs/dataops/blob/main/install-kubernetes.md).

Para o instalar o HELM é necessário executar a seguinte série de comandos:
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```
Adicionar o repositório do Helm com:
```
helm repo add pfisterer-hadoop https://pfisterer.github.io/apache-hadoop-helm/
```
Agora com o repositório adicionado, podemos inicializar um cluster com 1GB e 2 réplicas com:

```
helm install hadoop pfisterer-hadoop/hadoop --set yarn.nodeManager.resources.limits.memory=1024Mi --set yarn.nodeManager.replicas=2
```

## Referências
* https://helm.sh/docs/intro/install/
* https://github.com/pfisterer/apache-hadoop-helm
