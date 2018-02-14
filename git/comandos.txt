# Treinamento de DevOps - HPD
Todos os arquivos usados/criados durante o treinamento HPD.


## 14/02/2018 - GIT

Obs.: origin é apelido para o repositório remoto

Abaixo os comandos vistos:

### Incialização do GIT local
```
git init
```

### Adiciona uma URL de GIT remoto ao GIT local
```
git remote add origin <URL>
```

### Incialização do GIT local com adição do GIT remoto e com todos os seus arquivos
```
git clone <URL> <NEW DIRECTORY>
```

### Lista a URL do repositório remoto e suas branches
```
git remote show origin
```

### Renomeia a referência origin por outro nome
```
git remote rename origin <NAME>
```

### Deleta o GIT remoto para o projeto local
```
git remote rm origin
```

### Adição de arquivo
```
git add <file>
```

### Adição de todos os arquivos alterados
```
git add .
```

### Verifica o status atual de modificações do GIT
```
git status
```

### Faz o envio de uma alteração para o GIT local com mensagem sobre a alteração
```
git commit -m "<Message>" <File>
```

### Faz o envio das alterações para o GIT local com mensagem sobre as alterações
```
git commit -m "<Message>"
```

### Altera a mensagem original que foi enviada ao GIT local
```
git commit --amend
```

### Envia as alterações do GIT local ao repositório On Line
```
git push
```

### Envia as alterações do GIT local ao repositório On Line para uma determinada branch
```
git push <branch name>
```

### Mostra o caminho do repositório remoto
```
git remote -v
```

### Deleta um arquivo do GIT local
```
git rm <file>
```

### Busca os dados no GIT remoto e traz para o diretório local
```
git fetch origin
```

### Listar todas as branches
```
git branch
```

### Cria uma nova branch
```
git branch <Name>
```

### Mudança de branch
```
git checkout <Name>
```

### Mostra a diferença entre duas branches
```
git diff <branch> <branch>
```

### Fazer merge mantendo com o histórico
```
git merge --no-ff aula
```

### Fazer merge sem o histórico
```
git merge aula
```

### Deleta uma branch
```
git branch -d aula
```

### Deleta uma branch mesmo em caso de erro
```
git branch -D aula
```

### Retorna o repositório local para como estava antes do commit
```
git reset origin/master
```

### Retorna o repositório local para ficar igual ao remoto
```
git reset --hard origin/master
```

### Mostra todos os Logs
```
git log
```

### Mostra os últimos N Logs
```
git log -<N>
```

#### Mostra os logs em uma linha
```
git log --pretty=oneline
```

#### Mostra os logs de modo formatado
```
git log --pretty=format:"%h %s" --graph
```

#### Outras opções para Logs
```
git log --pretty=full
git log --pretty=fuller
git log --pretty=short
git log -p
git log --stat
```

#### Opções para format
```
%H - Commit hash
%h - Abbreviated commit hash
%T - Tree hash
%t - Abbreviated tree hash
%P - Parent hashes
%p - Abbreviated parent hashes
%an - Author name
%ae - Author email
%ad - Author date (format respects the --date=option)
%ar - Author date, relative
%cn - Committer name
%ce - Committer email
%cd - Committer date
%cr - Committer date, relative
%s - Subject
```

### Cria uma tag para o commit atual
```
git tag <NAME>
```

### Cria uma tag para um commit específico
```
git tag <NAME> <HASH de um commit pego no LOG>
```

### Envia uma tag do GIT local ao GIT remoto
```
git push origin master --tags
```

### Deleta uma tag
```
git tag -d <NAME>
```

### Envia a deleção de uma tag ao GIT remoto
```
git push origin :refs/tags/<NAME>
```
