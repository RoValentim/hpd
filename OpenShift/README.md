# 28/02/2018 - OpenShift

## Instalação
Usar a versão 3.7.0, antes de atualizar, sempre gerar novo ambiente e testar tudo antes, pois não há garantia de compatibilidade.

```
wget https://github.com/openshift/origin/releases/download/v3.7.0/openshift-origin-server-v3.7.0-7ed6862-linux-64bit.tar.gz
yum install yum-utils -y
yum-config-manager -add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --enable rhui-REGION-rhel-server-extras
yum install telnet git docker-ce epel-release -y
service docker start
echo {\"insecure-registries\" : [\"172.30.0.0/16\"]} > /etc/docker/daemon.json
(sempre usar nesse IP, pois é referente ao IP do Kubernetes que será instalado)
tar -zxvf openshift-origin-server-v3.7.0-7ed6862-linux-64bit.tar.gz
mv openshift-origin-server-v3.7.0-7ed6862-linux-64bit /opt
service docker restart
cd /opt/openshift-origin-server-v3.7.0-7ed6862-linux-64bit/
export PATH=$(pwd):PATH
```
export PATH=/root/bin:/usr/local/bin:/usr/bin:/bin:/opt/openshift-origin-server-v3.7.0-7ed6862-linux-64bit


Para subir o cluster
oc cluster up --help

http://nip.io/

```
oc cluster up --routing-suffix='35.229.20.224.nip.io' --public-hostname='35.229.20.224'
oc cluster down
oc cluster up --routing-suffix='35.229.20.224.nip.io' --public-hostname='35.229.20.224'
```
Precisa desligar o cluster e religar

```
oc login -u system:admin

oc --help
oc projects
```

Pega o nome do projeto padrao
```
oc status
```
Pega informações de determinado projeto
```
oc status -n default
```

Cria um projeto
```
oc new-project <PROJECT NAME>
oc new-project cgela


oc new-app --code=<URL DO REPOSITORIO> --name=<NAME> --image-stream=<LANGUAGE>:<LANGUAGE VERSION>
oc new-app --code=https://github.com/codeopsbr/ruby-ci-example.git --name=ruby-example --image-stream=ruby
```
https://github.com/codeopsbr/ruby-ci-example.git
https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html



Mostra todas as rotas existentes para a aplicação, inclusive as externas
```
oc get route
oc describe routes
oc describe service/ruby-example
```

Cria uma rota a aplicação
```
oc expose service ruby-example
```

```
oc logs -f dc/ruby-example
```








#
