# 19/02/2018 - Jenkins 2

## Procedimento de instalação
Deve-se seguir o passo a passo do site oficial, lembrando que por ser uma aplicação JAVA, também é necessário a instalação dele.
Abaixo o exemplo para CentOS:

```
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum install java-1.8.0-openjdk
yum install jenkins
/etc/init.d/jenkins start
```

Após esse processo, o Jenkins fica disponível na porta 8080.

## Configuração e manutenção
Primeiramente deve-se buscar a senha gerada dinamicamente durante a instalação, que se localiza em:

```
/var/log/jenkins/jenkins.log
```

Nesse arquivo deve-se localizar a parte de **initial setup** e retirar a senha, como no exemplo abaixo:

```
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:
1f34c34326644dbfbe8e80c67deebbd7
This may also be found at: /var/lib/jenkins/secrets/initialAdminPassword
```

Nesse caso, a senha é: **1f34c34326644dbfbe8e80c67deebbd7**, ela deve ser usada na interface gráfica como senha inicial.


## Recomendações
Além dos plug-ins padrões, são recomendados para adição manual os abaixo:

```
Chuck Norris
Dashboard View
Mission Control
Build Monitor View
```
