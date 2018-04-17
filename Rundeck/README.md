# Rundeck
https://www.rundeck.com/open-source
http://rundeck.org/docs/administration/configuration-file-reference.html
http://rundeck.org/docs/administration/managing-node-sources.html
http://rundeck.org/docs/api/
http://rundeck.org/docs/administration/access-control-policy.html


## Instalação
```
yum install java-1.8.0
rpm -Uvh http://repo.rundeck.org/latest.rpm
yum install rundeck
```

## Configuração
vim /etc/rundeck/framework.properties
```
# ----------------------------------------------------------------
# Rundeck server connection information
# ----------------------------------------------------------------
framework.server.name = 10.142.0.20
framework.server.hostname = 10.142.0.20
framework.server.port = 4440
framework.server.url = http://10.142.0.20:4440
```

vim /etc/rundeck/rundeck-config.properties
```
grails.serverURL=http://10.142.0.20:4440
```

## Inicialização
```
service rundeckd start
```

## Log de serviço
```
cat /var/log/rundeck/service.lo
```

## Primeiro acesso
```
User: admin
Password: admin
```

## Adição da chave de segurança entre as máquinas
```
su - rundeck
cat /var/lib/rundeck/.ssh/
cat id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRo0JtORs7Khb0e3sI1DszzIB2IO91M7Pryf889Lp6pigSArUbsPLXhoKitVVJl31wLzAQF9Oq1014vTmSOxGC1oRvkJi8n8Wmpiqs50nq93OD+ediYbzs3HJLCFL6Hgamd76i4eVEd2LmQBwncYwjiQ3mwt
Cc5DAys5E2dYJcNeZr2buZkH8UwhPsQfwxDXqYslAkNCEMeFSayF11V2+i2PAHKpU3nt/ZZEG2DdUUQaNWEFtNcrB+0QisyqE8rO12et+0mNGrpZQTHD89rixj6RqLgvhS2C9HCNcz91pe5HlD7Vp41FMojiYf6aq6dVm+5zYO59dglNkvZlP2KDAp


Entra na máqiuna destino:
useradd rundeck
su - rundeck
ssh-keygen -t rsa
cd .ssh


vim authorized_keys
copia a chave ssh-rsa

chmod 600 authorized_keys
```

## Vault
storage-cli
