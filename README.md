# Minikube

Instale o Minikube<br />
https://kubernetes.io/docs/tasks/tools/install-minikube/ <br />

Inicie um novo cluster com os comandos:<br />
`minikube start` ou `minikube start --vm-driver kvm2`<br />
dependendo do driver de virtualização da sua máquina<br />

Após a criação do cluster execute:<br />
`minikube dashboard`<br />
para abrir o painel no navegador ou:<br />
`minikube status`<br />
para exibir seu ip.<br />

Baixe e descompacte o repositório https://github.com/wsvblack/Minikube/archive/master.zip, depois de descompactado acesse a pasta para instalar as aplicações.<br />

Crie a aplicação Nginx utilizando o comando:<br />
`kubectl create -f nginx/nginx-svc.yml && kubectl create -f nginx/nginx-deploy.yml`<br />

Após a criação da aplicação execute o comando:<br />
`minikube service nginx-svc --url`<br />
para visualizar o IP da aplicação com a porta.<br />

Crie a aplicação Tomcat utilizando o comando:<br />
`kubectl create -f tomcat/tomcat-svc.yml && kubectl create -f tomcat/tomcat-deploy.yml`<br />

Após a criação da aplicação execute o comando:<br />
`minikube service tomcat-svc --url`<br />
para visualizar o IP da aplicação com a porta.<br />

Crie a aplicação Jenkins utilizando o comando:<br />
`kubectl create -f jenkins/jenkins-svc.yml && kubectl create -f jenkins/jenkins-deploy.yml`<br />

Após a criação da aplicação execute o comando:<br />
`minikube service jenkins-svc --url`<br />
para visualizar o IP da aplicação com a porta.<br />

Após acessar o ip no navegador será necessário pegar o token para terminar a configuração do Jenkins, para isso execute o comando:<br />
`kubectl get pods | grep jenkins`<br />
o resultado será o nome do Pod em execução, pegue o nome e execute o comando:<br />
`kubectl logs <PODNAME>`<br />
o resultado será o log da aplicação e um código para copiar e continuar o processo de configuração. Exemplo:<br />

```
*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

3087bc9756054a5b8558387abf3b266c

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
```

Após adicionar o código no campo e clicar em continuar clique em `Install Suggested Plugins`.<br />
Após o término da instalação preencha com usuário, senha e e-mail do administrador, clique em `Save and Finish` e depois `Start using Jenkins`.<br />
Crie um novo Job clicando em `create new jobs`, dê um nome para o Job, selecione Pipeline e clique em OK.<br />
Dentro da configuração do Job em `Pipeline` copie o código a seguir alterando a variável `IP` e `PORT` da aplicação Tomcat.<br />
```groovy
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
    }
}
```

Save e execute o Job clicando em `Build Now` para fazer o deploy da aplicação `sample.war`