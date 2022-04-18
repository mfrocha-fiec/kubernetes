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

## Inicialização do cluster

**Execução**: Nó mestre.

Agora com o repositório adicionado, podemos inicializar um cluster com 2 slaves com:

```
helm install hadoop pfisterer-hadoop/hadoop --set hdfs.dataNode.replicas=2
```

Podemos checar se o deploy foi bem sucedido vendo o output do `dfsadmin -report` com:

```
kubectl exec -n default -it hadoop-hadoop-hdfs-nn-0 -- /opt/hadoop/bin/hdfs dfsadmin -report
```

## Inicialização das UIs

**Execução**: Nó mestre.

Para poder explorar as UIs do Hadoop, que seriam no caso o YARN Manager e o HDFS Manager, é necessário fazer o forwarding das duas portas desses serviços, que são a `8088` e a `9870`, respectivamente.

```
kubectl port-forward -n default hadoop-hadoop-yarn-rm-0 8088:8088
kubectl port-forward -n default hadoop-hadoop-hdfs-nn-0 9870:9870
```
Caso esteja executando uma VM e não esteja conseguindo acessar a URL do seu browser, tente fazer o SSL da porta com o seguinte comando:

```
ssh -L 8088:localhost:8088 user@xxx.xxx.x.xxx
ssh -L 9870:localhost:9870 user@xxx.xxx.x.xxx
```

Onde `user` seria o user da VM do nó mestre e `xxx.xxx.x.xxx` o IP do nó mestre. 

## Teste do MapReduce

**Execução**: Nó mestre.

Executar um job de MapReduce padronizados do hadoop.

```
kubectl exec -n default -it hadoop-hadoop-yarn-nm-0 -- /opt/hadoop/bin/hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.2-tests.jar TestDFSIO -write -nrFiles 5 -fileSize 128MB -resFile /tmp/TestDFSIOwrite.txt
```


## Referências
* https://helm.sh/docs/intro/install/
* https://github.com/pfisterer/apache-hadoop-helm
