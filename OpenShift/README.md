# 28/02/2018 - OpenShift
dc = deployment config


## Instalação
Usar a versão 3.7.0, antes de atualizar, sempre gerar novo ambiente e testar tudo antes, pois não há garantia de compatibilidade.

```
wget https://github.com/openshift/origin/releases/download/v3.7.0/openshift-origin-server-v3.7.0-7ed6862-linux-64bit.tar.gz
yum install yum-utils -y
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --enable rhui-REGION-rhel-server-extras
yum install telnet git docker-ce epel-release -y
service docker start
echo {\"insecure-registries\" : [\"172.30.0.0/16\"]} > /etc/docker/daemon.json
(sempre usar nesse IP, pois é referente ao IP do Kubernetes que será instalado)
tar -zxvf openshift-origin-server-v3.7.0-7ed6862-linux-64bit.tar.gz
mv openshift-origin-server-v3.7.0-7ed6862-linux-64bit /opt
service docker restart
cd /opt/openshift-origin-server-v3.7.0-7ed6862-linux-64bit/
export PATH=$(pwd):$PATH
```

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

Pega o nome do projeto atual e informações sobre ele
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


https://github.com/codeopsbr/ruby-ci-example.git
https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html
http://nip.io/



# 01/03/2018 - OpenShift Client

## Backup do OpenShift
Basta realizar o backup do arquivo:
```
~/.kube/config
```

## Instalação do Cliente

Fazer download da versão exata do servidor
```
https://github.com/openshift/origin/releases/tag/v3.7.0
```

## Login
```
oc login <URL> <OPENSHIFT ID>

C:\Projetos\Treinamento_DevOps\OpenShift>oc login https://35.185.38.134:8443
The server uses a certificate signed by an unknown authority.
You can bypass the certificate check, but any data you send to the server could be intercepted by others.
Use insecure connections? (y/n): y

Authentication required for https://35.185.38.134:8443 (openshift)
Username: developer
Password:
Login successful.

You have one project on this server: "myproject"

Using project "myproject".
Welcome! See 'oc help' to get started.
```

### Para logar como usuário system
```
oc login -u system https://35.185.38.134:8443
```

### Para trocar de usuário
```
oc login -u system
```

### Para dair do usuário
```
oc logout
```

### Visualiza os projetos do usuário
```
oc projects
```

### Visualiza as configurações
```
oc config view
```

### Muda de projeto
```
oc project <PROJECT NAME>
oc project default
```

oc new-project marketplace-api
oc status
oc new-app --image-stream=ruby --code=https://github.com/codeopsbr/ruby-ci-example.git --name=ruby-api
oc logs -f bc/ruby-api
oc status

### Expoe a rota da aplicação
```
oc expose service ruby-api
```

### Infações detalhadas sobre a aplicação
```
oc describe
```

### Infações detalhadas sobre a aplicação
```
oc describe
```

### Informações detalhadas
```
oc describe routes
oc describe service ruby-api
```

oc get pods
git clone https://github.com/codeopsbr/ruby-ci-example.git ruby-ci-example
git pull
edit qualquer coisa para mudar o fonte
git add .
git commit -m "testando"

oc start-build ruby-api --follow

git: python-openshift-hpd

criar arquivo app.py dentro, contendo:
```
from flask import Flask
from flask import render_template
from flask import request

app = Flask(__name__)

@app.route("/")
@app.route("/index")

def index():
	return "version 0.0.1"

if __name__ == "__main__":
	app.run(host="0.0.0.0", port="8080")
```

Outro chamado requirements.txt, contendo:
```
flask==0.12.2
```

Adicionar novo projeto:
```
oc new-app https://github.com/RoValentim/python-openshift-hpd.git
oc logs -f bc/python-openshift-hpd
oc status
oc logs dc/python-openshift-hpd
oc expose service python-openshift-hpd
```

Se alterar o codigo:
```
oc start-build python-openshift-hpd
```

oc describe dc python-openshift-hpd

Para rollback
```
oc rollback python-openshift-hpd
```

Para rollback de versão expecífica
```
oc rollback python-openshift-hpd-2
```

Quando se faz rollback, o trigger automático para deploy é desativado, para reativar:
```
oc set triggers dc/python-openshift-hpd --auto
```

Se quiser forçar para manual:
```
oc set triggers dc/python-openshift-hpd --manual
```

oc get templates
oc get templates -n openshift


oc cluster down
oc cluster up --routing-suffix='35.229.20.224.nip.io' --public-hostname='35.229.20.224' --host-data-dir=/opt/openshift-data


https://docs.openshift.org/latest/cli_reference/basic_cli_operations.html#object-types
https://docs.openshift.org/latest/using_images/index.html
https://semver.org/
https://github.com/openshift/origin/blob/master/examples/gitserver/hooks/detect-language
https://docs.openshift.com/enterprise/3.1/dev_guide/deployments.html
https://paasfinder.org
https://docs.openshift.com/enterprise/3.0/dev_guide/templates.html





# 02/03/2018 - OpenShift Client Web
Subir com metrics
```
oc cluster up --routing-suffix='35.231.119.29 .nip.io' --public-hostname='35.231.119.29 ' --metrics=true
```

## Webhook
Sempre usar o content-type json
Dependendo da versão do OpenShift, o secret já vem na URL, se não vier, ele fica disponível
```
```

## Autoscaling
Sempre usar o content-type json
Dependendo da versão do OpenShift, o secret já vem na URL, se não vier, ele fica disponível
```
```
## Exercicio
```
Fluxo
  Start do Pipeline
    Stage de Pre-build
      Defina steps de pre-build
    Stage de Testes
      Defina steps de testes(ex.: echo "aplicando testes...")
    Stage de Deploy
      Criar um projeto no openshift com o nome agility-gtw-pag
      Criar uma app via pipeline, Jenkins ou GitLab C.I, com o nome pagamento-restful-api usando o codigo fonte do seu repo
      Exponha a rota deste service do Openshift
      Mostre no log do C.I a descrição deste service
      Mostre no log do C.I o status deste projeto no Openshift
  Fim do Pipeline
```


Ir ao GitHub e criar um projeto
Ir ao Jenkins e fazer CI desse projeto
Ir ao OpenShift e criar aplicação para esse projeto
Ir ao Jenkins e configurar no build para chamar o OpenShift Client para fazer build
Testar a URL exposta pelo OpenShift
```
Máquina local

git clone https://github.com/RoValentim/agility-gtw-pag.git agility-gtw-pag



Máquina 2 (Jenkins)

- Terminal
yum update
yum install wget yum-utils -y
wget https://github.com/openshift/origin/releases/download/v3.7.0/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit.tar.gz
tar -zxvf openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit.tar.gz
mv openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit /opt
cd /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit
export PATH=$(pwd):$PATH

wget https://github.com/openshift/origin/releases/download/v3.7.0/openshift-origin-server-v3.7.0-7ed6862-linux-64bit.tar.gz
yum-config-manager -add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager -add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --enable rhui-REGION-rhel-server-extras
yum install docker-ce epel-release -y
docker --version
yum update
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum install java-1.8.0-openjdk jenkins -y
/etc/init.d/jenkins start
cat /var/log/jenkins/jenkins.log

- Jenkins
Acessa a URL: http://35.231.73.41:8080/
Conectar usando o token do log
Instalar plugins: Chuck Norris, Dashboard View, Mission Control, Build Monitor View, Blue Ocean
Conectar ao GitHub usando o token: 23e1271f1ec38ba1a56740face0b731239df8e9d
Usar a repo: https://github.com/RoValentim/agility-gtw-pag.git
Configurar Pipeline com o nome pagamento-restful-api:
Stage de Pre-build - Message
Stage de Testes - Script: echo "aplicando testes..."
Stage de Deploy - Scripts para: /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit/oc login https://35.231.119.29:8443 --username=develop --password=' ' --insecure-skip-tls-verify=true; /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit/oc project agility-gtw-pag; /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit/oc start-build agility-gtw-pag; /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit/oc describe service; /opt/openshift-origin-client-tools-v3.7.0-7ed6862-linux-64bit/oc status



Máquina OpenShift

- Cliente Web
Acessar a URL: https://35.231.119.29:8443
Usuário develop com qualquer senha
Criar um projeto com o nome agility-gtw-pag
Criar uma aplicação PHP dentro do projeto agility-gtw-pag
Usar a repo: https://github.com/RoValentim/agility-gtw-pag.git
```



https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks
https://docs.openshift.com/container-platform/3.5/dev_guide/builds/triggering_builds.html
http://deis.com






#
