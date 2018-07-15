# Minikube

Instalar o Minikube

Inicie um novo cluster com os comandos:
`minikube start` ou `minikube start --vm-driver kvm2`
dependendo do driver de virtualização da sua máquina

Após a criação do cluster execute: 
`minikube dashboard`
para abrir o painel no navegador ou:
`minikube status`
para exibir seu ip.

Baixe e descompacte o repositório https://github.com/wsvblack/Minikube/archive/master.zip, depois de descompactado acesse a pasta para instalar as aplicações.

Acesse a pasta Nginx e crie a aplicação utilizando o comando:
`kubectl create -f nginx/nginx-svc.yml && kubectl create -f nginx/nginx-deploy.yml`

Após a criação da aplicação execute o comando:
`minikube service nginx-svc --url`
para visualizar o IP da aplicação com a porta.

Acesse a pasta Tomcat e crie a aplicação utilizando o comando:
`kubectl create -f tomcat/tomcat-svc.yml && kubectl create -f tomcat/tomcat-deploy.yml`

Após a criação da aplicação execute o comando:
`minikube service tomcat-svc --url`
para visualizar o IP da aplicação com a porta.

Acesse a pasta Jenkins e crie a aplicação utilizando o comando:
`kubectl create -f jenkins/jenkins-svc.yml && kubectl create -f jenkins/jenkins-deploy.yml`

Após a criação da aplicação execute o comando:
`minikube service jenkins-svc --url`
para visualizar o IP da aplicação com a porta.

Após acessar o ip no navegador será necessário pegar o token para terminar a configuração do Jenkins, para isso execute o comando:
`kubectl get pods | grep jenkins`
o resultado será o nome do Pod em execução, pegue o nome e execute o comando:
`kubectl logs <PODNAME>`
o resultado será o log da aplicação e um código para copiar e continuar o processo de configuração. Exemplo:

*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

`3087bc9756054a5b8558387abf3b266c`

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************

Após adicionar o código no campo e clicar em continuar clique em `Install Suggested Plugins`.
Após o término da instalação preencha com usuário, senha e e-mail do administrador, clique em `Save and Finish` e depois `Start using Jenkins`.
Crie um novo Job clicando em `create new jobs`, dê um nome para o Job, selecione Pipeline e clique em OK.
Dentro da configuração do Job em `Pipeline` copie o código a seguir alterando a variável `IP` e `PORT` da aplicação Tomcat.
```
def USER="admin"
def PASSWORD="admin"
def IP=""
def PORT=""

node {
    stage('Download War'){
        sh "wget https://tomcat.apache.org/tomcat-8.0-doc/appdev/sample/sample.war -O /tmp/sample.war"
    }
    stage('Deploy war'){
        sh "curl -T '/tmp/sample.war' 'http://${USER}:${PASSWORD}@${IP}:${PORT}/manager/text/deploy?path=/&update=true'"
        // sh "curl -T '/tmp/sample.war' 'http://${USER}:${PASSWORD}@${IP}:${PORT}/manager/text/deploy?path=/sample&update=true'"
    }
}
```

Save e execute o Job clicando em `Build Now` para fazer o deploy da aplicação `sample.war`