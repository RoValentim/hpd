--- Elasticsearch

sudo su
yum updated
yum install wget
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.rpm
rpm -ivh elasticsearch-6.2.3.rpm
yum install java-1.8.0-openjdk
/etc/init.d/elasticsearch start
/etc/init.d/elasticsearch status
ps -aux | grep java
cd /var/log/elasticsearch

cat elasticsearch.log
- publish_address {127.0.0.1:9200}, bound_addresses {[::1]:9200}

curl -i localhost:9200

cd /etc/elasticsearch/
ls -la


vim elasticsearch.yml

network.host: 35.196.98.225
http.port: 80



cd
wget https://github.com/lmenezes/cerebro/releases/download/v0.7.2/cerebro-0.7.2.tgz
tar -xvf cerebro-0.7.2.tgz
cd cerebro-0.7.2


vim conf/application.conf
# A list of known hosts
hosts = [
  {
    host = "http://10.142.0.15:9200"
    name = "Elastic Cluster"
  #},
  # Example of host with authentication
  #{
  #  host = "http://some-authenticated-host:9200"
  #  name = "Secured Cluster"
  #  auth = {
  #    username = "username"
  #    password = "secret-password"
  #  }
  }
]


./bin/cerebro

Pelo navegador entra no endereço configurado do cerebro, ou seja, o mesmo na porta 9000
(http://10.142.0.15:9000)

Colocar o IP do Host do Elasticsearch como node:
http://10.142.0.15:9200






--- LogStash
(https://www.elastic.co/guide/en/logstash/current/logstash-settings-file.html)
(https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html)
(https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)
(https://www.elastic.co/guide/en/logstash/current/plugins-filters-mutate.html)
(https://github.com/elastic/beats/tree/master/filebeat/module)


wget https://artifacts.elastic.co/downloads/logstash/logstash-6.2.3.rpm
rpm -Uvh logstash-6.2.3.rpm

cd /etc/logstash/conf.d

vim stdin.conf

input {
  stdin {
  }
}
#filter {
#
#}
output {
  elasticsearch {
    hosts => ["10.142.0.15:9200"]
  }
  stdout { codec => rubydebug }
}




service logstash start
service logstash status

ou para rodar um arquivo específico de configuração:
cd /usr/share/logstash/bin
./logstash -f /etc/logstash/conf.d/stdin.conf




vim tcp.conf

input {
  tcp {
    port => 5000
    type => syslog
  }
}
#filter {
#
#}
output {
  elasticsearch {
    hosts => ["10.142.0.15:9200"]
  }
  stdout { codec => rubydebug }
}




yum install httpd -y



vim httpd.conf

input {
  file {
    path => "/var/log/httpd/access_log"
    start_position => "beginning"
  }
}

filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "apache" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
  }
}

output {
  elasticsearch {
    hosts => ["10.142.0.15:9200"]
  }
  stdout { codec => rubydebug }
}




Quando vai em Discovery e verifica que tem campo com o símbolo de exclamação, basta ir em Management, clicar em Index Patterns e depois em Refresh, isso vai atualizar os index e seus respectivos campos.





--- Kibana

wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.3-x86_64.rpm
rpm -ivh kibana-6.2.3-x86_64.rpm
/etc/init.d/kibana start
ps -aux | grep Kibana
cd /etc/kibana
ifconfig

vim kibana.yml
server.host: "10.142.0.13"
elasticsearch.url: "http://10.142.0.15:9200"


/etc/init.d/kibana restart
acessar pelo navegador o Kibana, lembrando que a porta padrão é a 5601

Clicar em "Setup Index Pattern"
Index pattern: logstash-*
Clicar em Next
Time Filter field name
@timestamp
Create index pattern
