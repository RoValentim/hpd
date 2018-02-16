# 14/02/2018 - GitLab

GitLab: Ferramenta para gerenciamento de repositórios GIT.
Funciona baseado em serviços, a qual utiliza vários componentes para isso, como por exemplo, o Chef para própria configuração.
É desenvolvido em Ruby On Rails.

Repositórios: No GitLab não existe repositório, o mesmo é chamado de Projeto, uma vez que além da funcionalidade padrão de repositório, o GitLab fornece outras, fazendo assim que cada repositório se comporte como um projeto.


## Procedimento de instalação
Primeiramente acesse o link oficial do GitLab e faça o download de acordo com o sistema operacional.
```
https://about.gitlab.com/installation/
```

Siga as instruções em tela, sendo que o Postfix é necessário apenas para entrega de notificações por e-mail, sendo assim opicional.

No comando de configuração da URL, deve-se configurar de acordo com o IP Externo do servidor, como no exemplo abaixo:
```
sudo EXTERNAL_URL="http://35.231.123.179" apt-get install gitlab-ee
```

## Arquivo de configuração
Após a instalação a configuração do GitLab fica em:
```
/etc/gitlab/gitlab.rb
```

Ao editar esse arquivo, para que a alteração seja aplicada, é necessário executar o comando:
```
gitlab-ctl reconfigure
```

Com isso o Chef será chamado e vai fazer a reconfiguração automática do GitLab e reiniciar o serviço se necessário.

## Utilização

Após entrar na tela inicial, é necessário configurar a senha do usuário root (administrador principal do sistema).
Uma vez isso feito, deve-se entrar no GitLab com o usuário root e a senha cadastrada.

### Configuração de um projeto

Abaixo segue passo a passo resumido para criar um projeto:

1. Criar um grupo, o mesmo deve receber o nome de acordo com o time que ira trabalhar nele (por exemplo, DevOps, ou Comercial), ou de acordo com o projeto (por exemplo, Projeto_A), pois neles serão inseridos projetos, os quais ficarão agrupados e terão suas regras de autorização específicias para o grupo criado.
2. Criar um projeto
3. Direcionar um projeto a um grupo
..* Dentro do projeto criado, ir em **Settings**, clicar em **Expand** para **Advanced Settings** e na opção **Transfer project**, selecionar o grupo desejado.
..* Confirmar colocando o nome do grupo na tela que se abre.

Ou, após criar o grupo, dentro dele clicar em **New Project**, evitando assim a necessidade do passo 3 acima.

### Níveis de permissão ao usuário dentro de um agrupado

Abaixo os níveis de permissão para cada membro do grupo:

..* Guest - Só pode visualizar a estrutura
..* Reporter - Pode visualizar e reportar problemas
..* Developer - Tem acesso a alterar repositórios que não estão protegidos e solicitar Merge Request para essas branches
..* Master - Pode alterar a branch master e aceitar o Merge Request
..* Owner - Tem todas as permissões
