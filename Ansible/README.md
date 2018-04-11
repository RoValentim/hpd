- Ansible

-- Criar confiança entre máquinas por SSH
ssh-keygen
(aceitar tudo como padrão)

Repetir isso para todas as máquinas envolvidas na confiança


ssh-copy-id -i /root/.ssh/id_rsa.pub <IP DA MAQUINA QUE SE QUER CONECTAR SEM SENHA)



-- Instalação
CENTOS
$ yum install ansible

UBUNTU
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible

DEBIAN
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
$ sudo apt-get update
$ sudo apt-get install ansible



cd
mkdir ansible

vim hosts
[fsociety]
10.142.0.17
10.142.0.16

[webserver]
10.142.0.16

[db]
10.142.0.16




ansible -i hosts all -m ping
ansible -i hosts webserver -m ping
ansible -i hosts db -m ping
ansible -i hosts all -a "/sbin/ifconfig"
ansible -i hosts all -a "uname -r"
ansible -i hosts all -a "bash -c 'ps -ef | grep docker'"
ansible -i hosts db -m copy -a "src=/etc/hosts dest=/tmp/hosts-copia mode=0644"
ansible -i hosts all -m shell -a 'fdisk -l /dev/sda | grep Disklabel' -b

#Adiciona pacote
ansible -i hosts all -b -m yum -a "name=python state=present"

#Remove pacote
ansible -i hosts all -b -m yum -a "name=wget state=absent"

# Usando git
ansible -i hosts webserver -m git -a "repo=https://github.com/badtuxx/giropops-monitoring.git dest=/opt/giropops version=HEAD" -b

# Obtem várias informações da máquina
ansible -i hosts webserver -m setup
ansible -i hosts webserver -m setup -a "filter=ansible_memfree_mb"


# Criar usuários
python -c 'import crypt; print crypt.crypt("GIROPOPS", "$6$opa")'
$6$opa$eZAR6wnH7LJ6DMeCUIHtc0bx7ize2MiLMACv8rZqzlHShfQa5GPhhP6hyMUDBQhp5Gqq1Zn3mGQcXRILpHXUe0

ansible -i hosts all -b -m user -a "name=giropos password=$6$opa$eZAR6wnH7LJ6DMeCUIHtc0bx7ize2MiLMACv8rZqzlHShfQa5GPhhP6hyMUDBQhp5Gqq1Zn3mGQcXRILpHXUe0"


# Instala o Nginx
ansible -i hosts webserver -b -m package -a "name=nginx state=present"

# Inicializa o serviço do Nginx
ansible -i hosts webserver -b -m service -a "name=nginx"

# PLAY BOOKS
vim playbook.yml

#CENTOS/REDHAT
---
- name: Install Nginx
  hosts: webserver
  become: true

  tasks:
  - name: Add epel-release repo
    yum:
      name: epel-release
      state: present

  - name: Install nginx
    yum:
      name: nginx
      state: present
    notify: restart nginx

  - name: Insert Index Page
    template:
      src: index.html
      dest: /usr/share/nginx/html/index.html

  handlers:
    - name: restart nginx
    service:
      name=nginx
      state=restarted



#DEBIAN/UBUNTU
---
  - name: Install Nginx
    hosts: webserver
    become: true

    tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

   - name: Insert Index Page
template:
  src: index.html
  dest: /var/www/html/index.html

- name: Start NGiNX
  service:
    name: nginx
    state: started




#CENTOS/REDHAT V2
---
- name: Install Nginx
  hosts: webserver
  become: true

  tasks:
  - name: Add epel-release repo
    yum:
      name: {{item}}
      state: present

    with_items:
      - nginx
      - php
      - sqlite
    notify: start nginx

  - name: Insert Index Page
    template:
      src: index.html
      dest: /usr/share/nginx/html/index.html

  handlers:
    - name: start nginx
      service:
        name=nginx
        state=started

   - name: restart nginx
     service:
       name=nginx
       state=restarted


ansible-playbook -i hosts playbook.yml
