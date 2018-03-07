# Provisionamento de MongoDB pelo Ansible
Instalacao do MongoDB em Ubuntu Server 16.04

#Resumo

Este playbook foi criado para automatizar a instalacao do MongoDB em um Linux Ubuntu 16.04, em uma instancia  EC2 na AWS

Pela natureza do teste, nao foram considerados alguns pontos de instalacao para DEV/QA/PROD, como configuracao de parametros avancados, attach de volume para dados, entre outros.

Ambiente utilizado:

* uma instancia EC2 na AWS, com Linux Ubuntu 16.04 Server
* SSH habilitado e acesso por chave privada, importada da AWS
* um container (imagem Docker com Ubuntu 16.04) com a ultima versao do Ansible instalada - foi testada a versao 2.0.0.2
  (poderia ter sido utilizados uma maquina fisica ou virtual; minha escolha por um container foi ter uma imagem Ubuntu consumindo poucos recursos do meu notebook) 

OBS: Nao ha necessidade de prompt de login, uma vez que a chave privada esta setada na configuracao do host no Ansible

#Instalacao

##Verificacao de conectividade do Ansible para a instancia EC2

```bash
root@d1084509850b:/etc/ansible# ansible mongodb -m ping
ec2-52-201-242-246.compute-1.amazonaws.com | SUCCESS => {
    "changed": false, 
    "ping": "pong"
```

##Teste de execucao

A instalacao realizada atraves desse playbook tem output semelhante ao que se segue abaixo:

```bash
root@d1084509850b:/etc/ansible# ansible-playbook mongodb.yml 

PLAY [Habilita Gather] *********************************************************

TASK [setup] *******************************************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [create groups based on distribution] *************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com]
Localizacao do diretorio de dados: /data
Storage engine a ser utilizado: wiredTiger ou mmapv1 [wiredTiger]: 

PLAY [Instalacao do MongoDB] ***************************************************

TASK [setup] *******************************************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [debug] *******************************************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com] => {
    "msg": "dbPath = /data"
}

TASK [debug] *******************************************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com] => {
    "msg": "engine = wiredTiger"
}

TASK [Executa apt-get update] **************************************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Instala chave para o repositorio oficial MongoDB] ************************
ok: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Instala repositorio oficial do MongoDB] **********************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Instala MongoDB] *********************************************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Copia o arquivo de configuracao do template para /etc] *******************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Cria diretorio de dados] *************************************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Configura permissoes para diretorio de dados] ****************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Copia o script para desabilitar Transparent Huge Pages] ******************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Configura o script para ser executado no boot] ***************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

TASK [Reincia o MongoDB] *******************************************************
changed: [ec2-52-201-242-246.compute-1.amazonaws.com]

PLAY RECAP *********************************************************************
ec2-52-201-242-246.compute-1.amazonaws.com : ok=15   changed=8    unreachable=0    failed=0 
```

##Verificando a instalacao do MongoDB

A validacao da instalacao pode ser realizada executando o comando `mongo` no shell da instancia EC2.
Se o MongoDB estiver ativo, o seguinte output sera apresentado:

```
ubuntu@ip-172-31-43-223:~$ mongo
MongoDB shell version: 3.2.19
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
        http://docs.mongodb.org/
Questions? Try the support group
        http://groups.google.com/group/mongodb-user
>
```

##Este playbook foi escrito apenas para a instalacao do MongoDB em Ubuntu 16.04 
