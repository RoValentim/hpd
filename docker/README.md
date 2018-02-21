# 21/02/2018 - Docker


## Instalação
```
curl -fsSL https://get.docker.com | bash
```

### Para testar e verificar que está tudo ok:
```
docker container run hello-world
```

### Controlando a inicialização do docker
```
systemctl status docker
systemctl start docker
systemctl stop docker
systemctl restart docker
```

## Comandos do docker

### Cria um container
```
docker container run -ti <CONTAINER>

Ex.:
docker container run -ti debian:8
docker container run -ti ubuntu:14.04
```

Onde
docker container run -ti nginx (nesse caso conecta direto no processo do nginx ao invés do terminal, ou seja, o nginx fica em primeiro plano)
docker container run -d nginx (roda como deamon)

-ti junto com run significa que vai ter terminal e interatividade, ou seja, vai acessar assim que criar e/ou quando quiser

### Lista os containers ativos
```
docker container ls
```

### Lista todos os containers ativos
```
docker container ls -a
```

### Limpa todos os recursos dispensáveis
Pega tudo que é container, redes e recursos em geral que não estão sendo usados no momento e os remove
```
docker system prune
```


Conectar a um container
```
docker container attach <CONTAINER ID>
```

Roda um processo dentro do container
```
docker container exec -ti <CONTAINER ID> <COMMAND>
Ex:
docker container exec -ti 71fec5e44ca3 bash
```

Remove um container parado
```
docker container rm <CONTAINER ID>
```

Remove um container em execução
```
docker container rm <CONTAINER ID>
```

Para um container
```
docker container stop <CONTAINER ID>
```

Inicia um container
```
docker container start <CONTAINER ID>
```

Reinicia um container
```
docker container restart <CONTAINER ID>
```

Para temporariamente o container
```
docker container pause <CONTAINER ID>
```

Retoma o container temporariamente parado
```
docker container unpause <CONTAINER ID>
```

Informações do docker
```
/var/lib/docker
```

Remover mais de um container simultaneamente
```
docker container rm -f <4 STARTED DIGITS FROM CONTAINER ID> <4 STARTED DIGITS FROM CONTAINER ID>
```

?
```
--hostname --name -p 8080:80 nginx
Sempre que bater na porta 8080 do meu host, redireciona a porta 80 do meu container

--hostname --name -p 8080:80 -e <VARIABLE>=<VARIABLE DATA> nginx
seta junto uma variavel de ambiente
```

```
docker volume create <NAME>
Cria um diretorio para ser compartilhado entre containers, sem que fique dentro do container em si
Fica em /var/lib/docker/volumes/

docker volume create OiMundo
```




```
--hostname --name -p 8080:80 -e <VARIABLE>=<VARIABLE DATA> --mount type=volume,source=<VOLUME NAME>,destination=<DESTINY DIRECTORY> nginx
--hostname --name -p 8080:80 -e <VARIABLE>=<VARIABLE DATA> --mount type=volume,source=OiMundo,destination=/mnt/oimundo nginx
```

```
--hostname --name -p 8080:80 -e <VARIABLE>=<VARIABLE DATA> --mount type=bind,source=<FILE>,destination=<DESTINY DIRECTORY> nginx
```
Ao montar esses volumes/bind, eles sobrepoem o atual do container, assim que eles forem desatachados, o volume original volta a ficar ativo






```
--storage-opt size=1G
Para sistemas XFS seta uma cota
```

```
docker system df
```

```
iptable -L
Verifica as portas usadas pelos containers
```

Validar:
exit - sai e mata o container
CONTROL + p + q -  sai sem matar o cointaier
CONTROL + c -  sai matando o container

docker container top
docker container stats <CONTAINER NAME>
docker container inspect

docker volume inspect
docker volume prune
docker volume rm <CONTAINER NAME>

docker image ls
docker image rm

docker pull <IMAGE NAME>
Faz o download da imagem sem instalar

-m <TAMANHO EM MEGAS>
--cpus <PERCENTUAL DE CPU> pode ser 0.5 por exeplo


stress para stressar a vm
free dentro da vm docker mostra o total do host, por isso usar o stats no lugar
