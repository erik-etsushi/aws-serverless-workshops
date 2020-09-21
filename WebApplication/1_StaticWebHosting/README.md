# Módulo 1: Website estático com AWS Amplify

Neste módulo, você configurará o AWS Amplify Console para hospedar os recursos estáticos para seu aplicativo web. Nos módulos subsequentes, você adicionará funcionalidade dinâmica a essas páginas usando JavaScript para chamar APIs RESTful remotas criadas com o AWS Lambda e o Amazon API Gateway.

## Overview da arquitetura

A arquitetura para este módulo é muito simples. Todo o conteúdo estático da web, incluindo HTML, CSS, JavaScript, imagens e outros arquivos, será gerenciado pelo AWS Amplify Console e servido via Amazon CloudFront. Seus usuários finais acessarão seu site usando o URL do site público exposto pelo AWS Amplify Console. Você não precisa executar nenhum servidor web ou usar outros serviços para disponibilizar seu site.

![Static website architecture](../images/static-website-architecture.png)

## Instruções passo a passo


### Seleção de Região

Tenha certeza de ter selecionado a região "N. Virginia".

![Region selection screenshot](../images/region-selection.png)

### Criar o repositório git usando CodeCommit

#### Usando o AWS CodeCommit
**:white_check_mark: Explicação passo a passo**

O ambiente de desenvolvimento do AWS Cloud9 vem com credenciais temporárias gerenciadas da AWS associadas ao usuário do IAM. Use essas credenciais com o auxiliar de credenciais da CLI da AWS. Ative o auxiliar de credenciais executando os dois comandos a seguir no terminal do ambiente do Cloud9.

```bash
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```

Em seguida, você precisa criar o repositório e cloná-lo para o seu ambiente Cloud9:
1. Abra o [AWS CodeCommit]
1. Selecione **Create Repository**
1. Nomeie o *Repository name** como "wildrydes-site"
1. Selecione **Create**
1. Na caixa de seleção *Clone URL* , selecione *Clone HTTPS*

Agora, a partir do seu ambiente de desenvolvimento Cloud9:
1. A partir de uma janela de terminal, execute `git clone` e a URL HTTPS do respositório:
    ```
    ec2-user:~/environment $ git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/wildrydes-site
    Cloning into 'wildrydes-site'...
    warning: You appear to have cloned an empty repository.
    ec2-user:~/environment $ 
    ```


#### Popule o repositório git
Depois de usar o AWS CodeCommit para criar seu repositório git e cloná-lo localmente, você precisará copiar o conteúdo do site de um bucket S3 acessível ao público existente associado a este workshop e adicionar o conteúdo ao seu repositório.

**:white_check_mark: Instruções passo a passo**
Do seu ambiente de desenvolvimento Cloud9 
1. Alterar diretório em seu repositório:
    ```
    cd wildrydes-site/
    ```
1. Copie os arquivos do S3:
    ```
    aws s3 cp s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website ./ --recursive
    ```
1. Commit os arquivos no seu serviço git (talvez seja necessário inserir um e-mail e um nome de usuário para a confirmação):
    ```
    $ git add .
    $ git config --global user.email "<EMAIL ADDRESS>"
    $ git config --global user.name "<USER NAME>"
    $ git commit -m "initial checkin of website code"
    $ git push
    
    Username for 'https://git-codecommit.us-east-1.amazonaws.com': wildrydes-codecommit-at-xxxxxxxxx
    Password for 'https://wildrydes-codecommit-at-xxxxxxxxx@git-codecommit.us-east-1.amazonaws.com': 
    Counting objects: 95, done.
    Compressing objects: 100% (94/94), done.
    Writing objects: 100% (95/95), 9.44 MiB | 14.87 MiB/s, done.
    Total 95 (delta 2), reused 0 (delta 0)
    To https://git-codecommit.us-east-1.amazonaws.com/v1/repos/wildrydes-site
     * [new branch]      master -> master
    ```

### Deploy do site com o AWS Amplify Console
Em seguida, você usará o [Console do AWS Amplify] para implantar o site que acabou de se cadastrar no git. O Console Amplify cuida do trabalho de configurar um local para armazenar seu código de aplicativo Web estático e fornece uma série de recursos úteis para simplificar o ciclo de vida desse aplicativo, bem como habilitar as práticas recomendadas.

**:white_check_mark: Instruções passo a passo**
1. Abra o Amplify Console
1. Clique em **Get Started** abaixo de Deploy
1. Selecione o *Repository service provider* usado e clique em **Next**
1. Da lista, selecione o *Repository* e o *Branch* criados hoje
    
    ![Amplify Repository configuration](../images/amplify-console-repository-setup.png)
1. Em "Configure build settings" deixe tudo como padrão e clique **Next**
1. Na página de Review, clique em **Save and deploy**
    
    O processo leva alguns minutos para o Amplify Console criar os recursos necessários e implantar seu código.
    
    ![Amplify Deployment](../images/amplify-deploy-status.png)

Uma vez concluído, clique na imagem do site para iniciar seu site Wild Rydes.

![Wild Rydes homepage screenshot](../images/wildrydes-homepage.png)

Se você clicar no link para *Master*, você verá várias informações sobre a implantação do seu site, incluindo exemplos de renderizações em várias plataformas:

![Amplify Client Renderings](../images/amplify-renderings.png)

### Modifique o website
O AWS Amplify Console reconstruirá e reimplantará o aplicativo quando detectar alterações no repositório conectado. Faça uma alteração na página principal para testar este processo.

**:white_check_mark: Instruções passo a passo**
1. No ambiente do Cloud9, abra o arquivo ```index.html``` no diretório raiz do repositório.
1. Modifique a linha de título:
    ```
      <title>Wild Rydes</title>
    ```
    Para:
    ```
      <title>Workshop Serverless Alelo!</title>
    ```
    Salve o arquivo
1. Commit o código novamente no seu repositório:
    ```
    $ git add index.html 
    $ git commit -m "updated title"
    [master dfec2e5] updated title
     1 file changed, 1 insertion(+), 1 deletion(-)
    
    $ git push
    Counting objects: 3, done.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 315 bytes | 315.00 KiB/s, done.
    Total 3 (delta 2), reused 0 (delta 0)
    remote: processing 
    To https://git-codecommit.us-east-1.amazonaws.com/v1/repos/wildrydes-site
       2e9f540..dfec2e5  master -> master
   ```
    O Amplify Console começará o build do site novamente logo após ele perceber a atualização para o repositório. Vai acontecer muito rapidamente! Volte para a [página de console do Amplify Console]  para assistir ao processo. 

1. Uma vez concluído, reabra o site Wild Rydes e observe a mudança de título.
    
    ![title updated](../images/title-update.png)

### :star: Revisão

:key: O AWS Amplify Console facilita a implantação de sites estáticos seguindo um modelo contínuo de integração e entrega. Ele tem recursos para “build” de aplicativos baseados em framework javascript mais complicados e pode mostrar-lhe uma visualização de seu aplicativo como seria renderizado em plataformas móveis populares.

:wrench: Neste módulo, você criou um site estático que será a base para o nosso negócio Wild Rydes.

### Próximo

:white_check_mark: Siga para o próximo módulo, [User Management][user-management], 
onde você configurará os grupos de usuários do Amazon Cognito para gerenciar os usuários do nosso aplicativo.

[setup]: ../0_Setup/
[commit]: https://aws.amazon.com/codecommit
[github]: https://github.com
[iam-console]: https://console.aws.amazon.com/iam/home
[codecommit-free]: https://aws.amazon.com/codecommit/pricing/
[codecommit-console]: https://console.aws.amazon.com/codesuite/codecommit/repositories
[create-repo]: https://help.github.com/en/articles/create-a-repo
[github-new-sshkey]: https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
[github-clone]: https://help.github.com/en/articles/cloning-a-repository
[amplify-console]: https://aws.amazon.com/amplify/console/
[amplify-console-console]: https://console.aws.amazon.com/amplify/home
[user-management]: ../2_UserManagement/
[region-services]: https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/
