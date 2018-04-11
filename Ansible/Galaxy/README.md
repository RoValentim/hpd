https://galaxy.ansible.com/


mkdir HPD-ANSIBLE
cd HPD-ANSIBLE

mkdir roles
cd roles
ansible-galaxy init nginx
ansible-galaxy init wordpress
ansible-galaxy init php-fpm
ansible-galaxy init mariadb
ansible-galaxy init common


# Instala as roles em ~/.ansible/roles/
ansible-galaxy install geerlingguy.apache
ansible-galaxy install geerlingguy.mysql
ansible-galaxy install geerlingguy.php


cd ..
mkdir lamp
cd lamp
cp ../../hosts .


vim lamp.yml

---
- hosts: all
  become: yes
  roles:
  - geerlingguy.mysql
  - geerlingguy.apache
  - geerlingguy.php


ansible-playbook -i hosts lamp.yml


cd ..
mkdir gitlab
cd gitlab
cp ../../hosts .

ansible-galaxy install geerlingguy.gitlab

---
- hosts: all
  become: yes
  roles:
  - geerlingguy.gitlab


ansible-playbook -i hosts gitlab.yml

# Para pegar usuário e senha do GitLab
ansible-galaxy info geerlingguy.gitlab

# Para pegar as roles instaladas
ansible-galaxy list

# Para pegar a versao de determinada role instalada
ansible-galaxy list geerlingguy.gitlab

# Se quiser pesquisar sem ser pelo site
ansible-galaxy search apache

# Para remover
ansible-galaxy remove geerlingguy.gitlab




TODO:
1 - Mover o projeto de ontem, para essa nova estrutura. Inclusive preenchendo os README. Sou um cliente e vocês precisam entregar o projeto. (Usando o ansible-galaxy)

Como fazer:
- Pegar o clone do gitlab do wordpress e passar para a estrutura do ansible-galaxy criado para o HPD-ANSIBLE/roles
- Subir no GitHub
- Passar endereço do GitHub por e-mail

2 - Utilizando o ansible-galaxy para instalar os roles, suba um serviço web, com mais de um componente e nos envie o link por email. Caso não consiga deixa-lo em pé  por muito tempo, envie-nos alguns prints.

Como fazer:
- Pegar qualquer projeto que seja web do galaxy (por exemplo, o Jenkins) e fazer funcionar
- Subir no GitHub o passo a passo
- Passar endereço do GitHub por e-mail e print screen de que funcionou
