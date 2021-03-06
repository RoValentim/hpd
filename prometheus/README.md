# Prometheus

## Usuários do Prometheus
```
useradd --no-create-home --shell /bin/false prometheus
useradd --no-create-home --shell /bin/false node_exporter
```

## Configurações do Prometheus
```
mkdir /etc/prometheus
```

## Banco de Dados do Prometheus
```
mkdir /var/lib/prometheus
```

## Ajuste nas Permissões
```
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

## Download
```
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.0.0/prometheus-2.0.0.linux-amd64.tar.gz
```

## Descompactação
```
tar -zxvf prometheus-2.0.0.linux-amd64.tar.gz
```

## Ajuste nos diretórios
```
cp prometheus-2.0.0.linux-amd64/prometheus /usr/local/bin
cp prometheus-2.0.0.linux-amd64/promtool /usr/local/bin
```

## Ajuste nas permissões
```
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

## Arquivos de Configuração
```
cp -r prometheus-2.0.0.linux-amd64/consoles /etc/prometheus
cp -r prometheus-2.0.0.linux-amd64/console_libraries /etc/prometheus
chown -R prometheus:prometheus /etc/prometheus
```

## Limpeza de aquivos desnecessários
```
rm -rf prometheus-2.0.0.linux-amd64 prometheus-2.0.0.linux-amd64.tar.gz
```

## Criação de arquivo de Configuração
vim /etc/prometheus/prometheus.yml

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
```

## Ajustar a configuração do arquivo de configuração
```
chown -R prometheus:prometheus /etc/prometheus/prometheus.yml
```

## Executa o Prometheus
```
prometheus --config.file /etc/prometheus/prometheus.yml --storage.tsdb.path /var/lib/prometheus --web.console.templates /etc/prometheus/consoles --web.console.libraries /etc/prometheus/console_libraries
```

## Cria serviço para o Prometheus
vim /etc/systemd/system/prometheus.service
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
	--config.file /etc/prometheus/prometheus.yml \
	--storage.tsdb.path /var/lib/prometheus/ \
	--web.console.templates=/etc/prometheus/consoles \
	--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

## Ajusta o usuário para rodar o seriço sem problema
```
chown -R prometheus:prometheus /var/lib/prometheus
```

## Atualiza o sistema para aceitar o serviço do Prometheus
```
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
```

## Download do Coletor de Métricas
```
curl -LO https://github.com/prometheus/node_exporter/releases/download/v0.15.1/node_exporter-0.15.1.linux-amd64.tar.gz
```

## Descompactação do Coletor de Métricas
```
tar -xzvf node_exporter-0.15.1.linux-amd64.tar.gz
```

## Ajuste nos diretórios
```
cp node_exporter-0.15.1.linux-amd64/node_exporter /usr/local/bin
```

## Ajuste nas Permissões
```
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

## Limpeza de aquivos desnecessários
```
rm -fr node_exporter-0.15.1.linux-amd64 node_exporter-0.15.1.linux-amd64.tar.gz
```

## Criação de arquivo de Configuração
vim /etc/systemd/system/node_exporter.service

```
[Unit]
Description=Node Explorer
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
#ExecStart=/usr/local/bin/node_exporter --collectors.enabled cpu,diskstats,meminfo,loadavg,filesystem
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

## Atualiza o sistema para aceitar o serviço do Node Exporter
```
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
```

## Configura o Prometheus para ler métricas do Node Exporter
Mudar o aquivo /etc/prometheus/prometheus.yml para:

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9100','10.142.0.19:9100']
#        labels:
#	  type: monitoringOS

```

## Restartar o Prometheus para utilizar nova configuração
```
systemctl restart prometheus
```

## Máquina para monitoração 10.142.0.19
### Usuários do Prometheus
```
useradd --no-create-home --shell /bin/false node_exporter
```

### Download do Coletor de Métricas
```
curl -LO https://github.com/prometheus/node_exporter/releases/download/v0.15.1/node_exporter-0.15.1.linux-amd64.tar.gz
```

### Descompactação do Coletor de Métricas
```
tar -xzvf node_exporter-0.15.1.linux-amd64.tar.gz
```

### Ajuste nos diretórios
```
cp node_exporter-0.15.1.linux-amd64/node_exporter /usr/local/bin
```

### Ajuste nas Permissões
```
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

### Limpeza de aquivos desnecessários
```
rm -fr node_exporter-0.15.1.linux-amd64 node_exporter-0.15.1.linux-amd64.tar.gz
```

### Criação de arquivo de Configuração
vim /etc/systemd/system/node_exporter.service

```
[Unit]
Description=Node Explorer
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
#ExecStart=/usr/local/bin/node_exporter --collectors.enabled cpu,diskstats,meminfo,loadavg,filesystem
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

### Atualiza o sistema para aceitar o serviço do Node Exporter
```
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
```

## Adiciona permissão de acesso ao Prometheus
Instalação de servidor web para proxy e configuração de usuário para autenticação
```
yum install httpd-tools
yum install nginx
htpasswd -c /etc/nginx/.htpasswd valentim
```

## Configuração do Nginx para proxy do Prometheus
vim /etc/nginx/conf.d/prometheus
```
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /var/www/html;

	index index.html index.htm;

	server_name _;

	location / {
		auth_basic "Prometheus server authentication";
		auth_basic_user_file /etc/nginx/.htpasswd;
		proxy_pass http://localhost:9090;
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host $host;
		proxy_cache_bypass $http_upgrade;
	}
}
```

## inicialização do servidor web
```
systemctl daemon-reload
systemctl start nginx
systemctl enable nginx
```

Caso após acessar o site pela porta 80 não funcione, deve-se verificar o firewall da máquina e desabilitar o SELinux.
