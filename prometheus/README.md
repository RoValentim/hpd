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

scrap_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
```
