# Grafana

Usar as máquinas do Prometheus

## Instalar o Net Data em todas as máquinas
bash <(curl -Ss https://my-netdata.io/kickstart.sh)

## Na máquina do Prometheus
Editar o arquivo /etc/prometheus/prometheus.yml para:
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
        labels:
	  type: monitoringOS
	  dc: UK
  - job_name: 'netdata'
    metrics_path: '/api/v1/allmetrics'
    params:
      format: [prometheus]
    honor_labels: true
    scrape_interval: 5s
    static_configs:
      - targets: ["10.142.0.18:19999","10.142.0.19:19999"]
```

## Reinicia o Prometheus
systemctl restart prometheus

## Instala o alertmanager
```
export alertmanager_version="0.11.0"
curl -LO https://github.com/prometheus/alertmanager/releases/download/v${alertmanager_version}/alertmanager-${alertmanager_version}.linux-amd64.tar.gz
tar -zxvf alertmanager-0.11.0.linux-amd64.tar.gz
cp alertmanager-0.11.0.linux-amd64/alertmanager /usr/local/bin
chown prometheus:prometheus /usr/local/bin/alertmanager
rm -fr alertmanager-0.11.0.linux-amd64 alertmanager-0.11.0.linux-amd64.tar.gz
mkdir /etc/alertmanager
```

## Configura o alertmanager
vim /etc/alertmanager/config.yml
```
route:
    repeat_interval: 30m
    group_interval: 30m
    receiver: 'rocketchat'

receivers:
    - name: 'rocketchat'
      webhook_configs:
          - send_resolved: false
	    url: '${WEBHOOK_URL}'
```

## Configuração de permissão
```
chown prometheus:prometheus /etc/alertmanager/ -R
mkdir /alertmanager
chown prometheus:prometheus /alertmanager
```

## Configuração do serviço
vim /etc/systemd/system/alertmanager.service
```
[Unit]
Description=AlertManager
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/alertmanager -config.file=/etc/alertmanager/config.yml -storage.path=/alertmanager

[Install]
WantedBy=multi-user.target
```

## Adiciona o AlertManager ao Prometheus
```
global:
  scrape_interval: 15s

rule_files:
  - 'alert.rules'

alerting:
  alertmanagers:
    - scheme: http
      static_configs:
        - targets: ['localhost:9093']

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9100','10.142.0.19:9100']
        labels:
	  type: monitoringOS
	  dc: UK
  - job_name: 'netdata'
    metrics_path: '/api/v1/allmetrics'
    params:
      format: [prometheus]
    honor_labels: true
    scrape_interval: 5s
    static_configs:
      - targets: ["10.142.0.18:19999","10.142.0.19:19999"]
```

## Configura as regras do AlertManager
vim /etc/prometheus/alert.rules
```
groups:
- name: example HPD
  rules:

  # Alert for any instance that is unreachable for > 2 minutes
  - alert: service_down
    expr: up == 0
    for: 2m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 2 minutes."

  - alert: high_load
    expr: node_load1 > 0.5
    for: 2m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} under high load"
      description: "{{ $labels.instance }} of job {{ $labels.job }} is under high load."
```

## Configura a permissão das regras do AlertManager
```
chown prometheus:prometheus /etc/prometheus/alert.rules
```

## Reinicia o serviço do Prometheus
```
systemctl restart prometheus
```

# Instalação do Grafana
```
yum install https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana-5.0.4-1.x86_64.rpm
systemctl daemon-reload
systemctl enable grafana-server
systemctl start grafana-server
```

Depois é só acessar na porta 3000 com usuário e senha Admin.

Exercícios:
- Instalar o projeto do giropops:
- URL: https://github.com/badtuxx/giropops-monitoring
