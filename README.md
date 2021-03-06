# `vai`

_Bash script_ para fazer o deploy do `HEAD` do repositório para uma máquina remota.

Utiliza `SCP` e `SSH` para o envio e execução de comandos. Essas são dependências tanto da máquina
local como da máquina remota.

## Como configurar local

Clone esse repositório e execute o comando abaixo, para criar um link simbólico do executável:

    sudo ln -s <meu_git>/vai/vai /usr/bin/vai 

Para testar, execute:

    vai help

## Como configurar para deploy

### Configurando o projeto

Em seu projeto Git, crie um diretório `deploy` e depois rode o comando `vai configure`:

    mkdir deploy
    vai configure

Será requisitado o preenchimento dos seguintes dados (todos obrigatórios):

* **Host:** IP ou Host do servidor remoto, onde os arquivos serão enviadso

* **Port:** Porta para conexão SSH

* **User:** Usuário que será utilizado para conexão SSH

* **Path:** Path onde o projeto deve ser "instalado"

##### _key gen_

Após o preenchimento dos dados, será configurado o _ssh key_ para facilitar a conexão entre o computador local e o remoto.

Para configurar o _ssh key_ será solicitado um _password_ novo (deve ser digitado duas vezes) e posteriormente a senha do usuário informado anteriormente do computador remoto.

#### Configurando restart da aplicação

O `vai` irá enviar os arquivos para o servidor remoto e executar o script de restart da aplicação (caso ele exista).

Esse script `post-script.sh` deve ser criado dentro do diretório `deploy/`:

    touch deploy/post-script.sh
    vim deploy/post-script.sh

Um exemplo de script de restart:

    #!/bin/bash
    docker-compose down
    docker-compose build
    docker-compose up -d

## Efetuando deploy

Em um projeto com o deploy configurado e o seu _ssh-key_ também configurado, basta executar:

    vai deploy

Caso o _ssh-key_ não esteja configurado em seu computador, execute antes:

    vai keygen

### Instalação limpa

Em alguns casos, pode ser necessário a _instalação limpa_ de um sistema. Isso significa remover todos os arquivos 
e enviar os novos.

Para isso, utilize a opção `--clean-install` (ou simplesmente `-c`):

    vai deploy --clean-install
    vai deploy -c

**Atenção:** Isso irá remover todos os arquivos e diretórios dentro do path da aplicação configurado em `./deploy/deploy.config`.

## Obtendo versão em produção

Para saber qual versão de _commit_ está em produção, execute:

    vai version

Esse comando irá retornar o commit do deploy utilizado.