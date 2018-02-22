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
docker image history

docker pull <IMAGE NAME>
Faz o download da imagem sem instalar

-m <TAMANHO EM MEGAS>
--cpus <PERCENTUAL DE CPU> pode ser 0.5 por exeplo


stress para stressar a vm
free dentro da vm docker mostra o total do host, por isso usar o stats no lugar




mkdir dockerfiles
cd dockerfiles
mkdir 1
cd 1
vim Dockerfile

FROM ubuntu
MAINTAINER rodrigo.valentim@agilitynetworks.com.br

RUN apt-get update \
        && apt-get install -y nginx \
        && apt-get clean

COPY index.html /var/www/html/

ENV rodrigo rules

VOLUME /var/www/html

ENTRYPOINT /usr/sbin/nginx -g 'daemon off;'

EXPOSE 80



OU

FROM nginx
MAINTAINER rodrigo.valentim@agilitynetworks.com.br
ADD
EXPOSE 80




index.html
Minha primeira imagem


docker build -t meu_nginx_ubuntu:1.0 .
ou
docker build -t meu_nginx_ubuntu:1.0 -f <DOCKER FILE NAME>

docker container run -d -p 8080:80 meu_nginx_ubuntu:1.0




FROM ubuntu
MAINTAINER rodrigo.valentim@agilitynetworks.com.br
LABEL maintainer="rodrigo.valentim@agilitynetworks.com.br"
LABEL version="1.0"
LABEL description="Meu primeiro apache"

RUN apt-get update \
        && apt-get install -y apache2 \
        && apt-get clean

ADD index.html /var/www/html
ENV rodrigo rules
VOLUME /var/www/html
USER root
WORKDIR /var/www/html
EXPOSE 80
CMD /usr/sbin/apachectl -D FOREGROUND


docker build -t meu_apache_ubuntu:1.0 .
docker container run -d -p 8888:80 meu_apache_ubuntu:1.0




# 22/02/2018 - DOCKER

criar conta no hub.docker.com
docker login
docker tag meu_nginx_oficial:1.0 linuxtips/nginx_agility:1.0
(docker tag nginx rodrigovalentim/nginx_agility)
Cria alias

docker image ls
docker push linuxtips/nginx_agility:1.0
(docker push rodrigovalentim/nginx_agility)
docker run -d -p 8888:80 linuxtips/nginx_agility:1.0

docker system events
docker system events - since 20180218
docker system prune
docker system info


Swar manager carrega os containers e tem todas as informações do cluster
Swar workers carrega apenas os containers

Conta básica de cluster em docker:
Se perder 50% dos managers, perdeu o cluster inteiro.
Obs.: Quando um manager cai, ele faz eleição para qual manager vai assumir, então é bom colocar o mínimo de managers possíveis, dentro da regra básica


Cria um cluster manager
docker swarm init

Se tiver mais de uma interface:
docker swarm init --advertise-addr

Depois disso ele reporta como adicionar um worker, exemplo:
docker swarm join --token SWMTKN-1-67flv580q6xjwt1gvt2b15xzknyf2zssjlgaqjyg2hocpsg8oa-crstqx7qhxtluc55ddavo3k0o 10.142.0.5:2377

Lista todos os nodes
docker node ls

Adicionar um worker como manager:
docker node promote <NOME DA MAQUINA> (Na máquina do manager)

Transforma um manager em worker:
docker node demote <NOME DA MAQUINA> (Na máquina do manager)

Remove um cluster
docker swarm leave (Na máquina do cluster)
docker rm <NOME DA MAQUINA> (Na máquina do manager)

Mostra detalhes do node
docker node inspect <NOME DA MAQUINA>

Mostra o comando para adicionar um manager
docker swarm join-token manager

Mostra o comando para adicionar um worker
docker swarm join-token worker



Criar serviço com 5 VMs replicadas automaticamente
docker service create --name giropops --replicas 5 -p 8080:80 nginx

docker service ls

Ve onde cada containar está rodando
docker service ps giropops

Log para todos os containers do serviço
docker service logs -f giropops

Aumenta a quantidade de containers para 10
docker service scale giropops=10

Diminui a quantidade de containers para 5
docker service scale giropops=5

Remove o serviço
docker service rm giropops



docker service create --name giropops --replicas 5 -p 8080:80 --mount type=volume,src=strigus,dst=/usr/share/nginx/html/ nginx
```
echo "Funcionou o Giropops" > /var/lib/docker/volumes/strigus/_data/index.html
```


docker network ls

docker network create -d overlay giropops

docker network inspect giropops


docker network create -d overlay strigus


docker service create --name somente_giropops --replicas 5 -p 8080:80 --network giropops nginx
docker service create --name somente_strigus --replicas 3 -p 8088:80 --network strigus nginx
docker service create --name giropops_e_strigus --replicas 3 -p 8888:80 --network strigus --network giropops nginx



docker container exec -ti  <DOCKER ID de máquina giropops_e_strigus> bash
curl somente_strigus (funciona)
curl giropops_e_strigus (funciona)
curl somente_giropops (funciona)

docker container exec -ti  <DOCKER ID de máquina somente_strigus>
curl somente_strigus (funciona)
curl giropops_e_strigus (funciona)
curl somente_giropops (não funciona, pois está em rede diferente)


Opção para criar dentro de uma determinada subnet
docker network create -d overlay --subnet <IP> --gateway <IP> <NOME>
docker network create -d overlay --subnet 192.168.0.0/24 --gateway 255.255.255.0 girus



Para fazer isso de forma automática por arquivo de configuração:
Criar arquivo docker-compose.yml
Nele colocar:

version: "3.5"
services:
        web:
        image: nginx
        deploy:
        replicas: 4
        resources:
        limits:
        cpus: "0.2"
        memory: 50M
        restart_policy:
        condition: on-failure
        ports:
        - "8080:80"
        networks:
        - webserver
networks:
        webserver:



FUNCIONOU pois usa espaço ao invés de tab:
version: "3.5"
services:
  web:
    image: nginx
    deploy:
      replicas: 4
      resources:
        limits:
          cpus: "0.2"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "8080:80"
    networks:
      - webserver
networks:
  webserver:




Depois é só fazer o deploy do arquivo:
docker stack deploy -c docker-compose.yml giropops


docker service ls
docker stack ls
docker stack services giropops
docker stack ps giropops
docker stack rm giropops




version: "3.5"
services:
        db:
        image: mysql:5.7
        volumes:
        - db_data:/var/lib/mysql
        secrets:
        - password_mysql_root
        environment:
        MYSQL_ROOT_PASSWORD_FILE: /run/secrets/password_mysql_root
        MYSQL_DATABASE: wordpress
        MYSQL_USER: wordpress
                        MYSQL_PASSWORD: wordpress
                deploy:
                        placement:
                                constraints:
                                        - node.role == manager
                                        - engine.labels.operatingsystem == ubuntu 16.04.3

        wordpress:
                depends_on:
                        - db
                image:
                        wordpress:latest
                ports:
                        - "8080:80"
                environment:
                        WORDPRESS_DB_HOST: db:3306
                        WORDPRESS_DB_USER: wordpress
                        WORDPRESS_DB_PASSWORD: wordpress
volumes:
        db_data:
secrets:
        password_mysql_root:
        external: true




Para criar um secret
docker secret create <NAME> <FILE WITH CONTENT TO BE GENERATED THE SECRET>
echo -n wordpress | docker secret create password_mysql_root -

 docker secret ls
 docker secret inspect giropops_secret


docker service deploy
docker stack deploy -c docker-compose.yml wp




version: "3.5"
services:
        web:
                image: nginx
        deploy:
                update_config:
                        parallelism: 2
                        deploy: 10s
                replicas: 5
                resources:
                        limits:
                                cpus: "0.2"
                                memory: 64M
                        restart_policy:
                                condition: on-failure
                ports:
                        - "8080:80"
                networks:
                        - webserver

        visualizer:
                image: dockersamples/visualizer:stable
                ports:
                        - "8888:8080"
                volumes:
                        - "/var/run/docker.sock:/var/run/docker.sock"
                deploy:
                        placement:
                                constraints: [node.role == manager]
                networks:
                        - webserver
networks:
        webserver:




FUNCIONOU:
version: "3.5"
services:
  web:
    image: nginx
    deploy:
      update_config:
        parallelism: 2
        delay: 10s
      replicas: 5
      resources:
        limits:
          cpus: "0.2"
          memory: 64M
      restart_policy:
        condition: on-failure
    ports:
      - "8080:80"
    networks:
      - webserver

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8888:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webserver

networks:
  webserver:



docker stack deploy -c docker-compose.yml visu
Para atualizar em caso de alteração, é só rodar novamente o mesmo comando: docker stack deploy -c docker-compose.yml visu







version: "3.5"
services:
        redis:
                image:  redis:alpine
                ports:
                        - "6379"
                networks:
                        - frontend
                deploy:
                        replicas: 2
                        update_config:
                                parallelism: 1
                                delay: 10s
                        restart_policy:
                                condition: on-failure
        db:
                image: postgres:9.4
                volumes:
                        - db-data:/var/lib/postgresql/data
                networks:
                        - backend
                deploy:
                        placement:
                                constraints: [node.role == manager]
        vote:
                image: dockersamples/examplevotingapp_vote:before
                ports:
                        - 5000:80
                networks:
                        - frontend
                depends_on:
                        - redis
                deploy:
                        replicas: 2
                        update_config:
                                parallelism: 2
                        restart_policy:
                                condition: on-failure
        result:
                image: dockersamples/examplevotingapp_result:before
                ports:
                        - 5001:80
                networks:
                        -backend
                depends_on:
                        -db
                deploy:
                        replicas: 1
                        update_config:
                                parallelism: 1
                                delay: 10s
                        restart_policy:
                                condition: on-failure
        worker:
                image: dockersamples/examplevotingapp_worker
                networks:
                        - frontend
                        - backend
                deploy:
                        node: replicated
                        replicas: 1
                        labels:
                                com.example.description: "Esse componente eh nosso worker, coleta no front e armazena no back."
                        restart_policy:
                                condition: on-failure
                                delay: 10s
                                max_attempts: 3
                                window: 120s
                        placement:
                                constraints:
                                        - node.role == manager
                                        - node.hostname == fsociety03
        visualizer:
                image: dockersamples/visualizer:stable
                ports:
                        - "8080:8080"
                volumes:
                        - "/var/run/docker.sock:/var/run/docker.sock"






FUNCIONOU (se não funcionar é por causa do '- node.hostname == fsociety03' que pode não estar conseguindo alocar lá, é só remover tudo e instalar novamente sem essa linha)
version: "3.5"
services:
  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 2
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]
  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure
result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels:
        com.example.description: "Esse componente eh o nosso worker, coleta no front e armazena no back..."
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
placement:
        constraints:
          - node.role == manager
          - node.hostname == fsociety03
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
networks:
  frontend:
  backend:
volumes:
  db-data:





  docker stack deploy -c docker-compose.yml vote
  docker stack services vote

#
