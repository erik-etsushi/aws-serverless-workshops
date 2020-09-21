# Módulo 2: Autenticação e registro de usuários com grupos de usuários do Amazon Cognito

Neste módulo, você criará um grupo de usuários do [Amazon Cognito] [cognito] para gerenciar as contas de seus usuários. Você implantará páginas que permitem que os clientes se registrem como um novo usuário, verifiquem seus endereços de e-mail e façam login no site.

## Overview da arquitetura

Quando os usuários visitam seu site, eles primeiro registrarão uma nova conta de usuário. Para fins deste workshop, apenas solicitaremos que eles forneçam um endereço de e-mail e senha para se cadastrar. No entanto, você pode configurar o Amazon Cognito para exigir atributos adicionais em seus próprios aplicativos.

Depois que os usuários enviarem seu registro, o Amazon Cognito enviará um e-mail de confirmação com um código de verificação para o endereço fornecido. Para confirmar sua conta, os usuários retornarão ao seu site e inserirão seu endereço de e-mail e o código de verificação que receberam. 

Depois que os usuários tiverem uma conta confirmada (usando o processo de verificação de e-mail ), eles poderão entrar. Quando os usuários fazem login, eles inserem seu nome de usuário (ou e-mail) e senha. Uma função JavaScript então se comunica com o Amazon Cognito, autentica usando o protocolo SRP (Secure Remote Password Protocol) e recebe de volta um conjunto de JSON Web Tokens (JWT). Os JWTs contêm reivindicações sobre a identidade do usuário e serão usados no próximo módulo para autenticar contra a API RESTful que você cria com o Amazon API Gateway.

![Authentication architecture](../images/authentication-architecture.png)

## Implementação

### 1. Crie um Amazon Cognito User Pool

#### Visão geral

O Amazon Cognito fornece dois mecanismos diferentes para autenticar usuários. Você pode usar os grupos de usuários do Cognito para adicionar a funcionalidade de inscrição e login ao seu aplicativo ou usar os Pools de identidades do Cognito para autenticar usuários por meio de provedores de identidade social, como Facebook, Twitter ou Amazon, com soluções de identidade SAML ou usando seu próprio sistema de identidade. Para este módulo, você usará um grupo de usuários como backend para as páginas de registro e login fornecidas.

Use o console do Amazon Cognito para criar um novo grupo de usuários usando as configurações padrão. Depois que seu pool for criado, observe o ID do pool. Você usará esse valor em uma seção posterior.

**:white_check_mark: Instruções passo a passo**

1. Vá até [Amazon Cognito Console][cognito-console]
1. Escolha **Manage your User Pools**.
1. Escolha **Create a User Pool**
1. Dê um nome para o seu user pool como `WildRydes`, e selecione **Review Defaults**
    ![Create a user pool screenshot](../images/create-a-user-pool.png)
1. Na página de review, clique em **Create pool**.
1. Anote o **Pool Id** que foi gerado.

### 2. Adicione um App Client no seu User Pool

No console do Amazon Cognito, selecione seu grupo de usuários e, em seguida, selecione a seção **App Clients**. Adicione um novo aplicativo e verifique se a opção Gerar segredo do cliente está desmarcada. Segredos de cliente não são suportados com o SDK JavaScript. Se você criar um aplicativo com um segredo gerado, exclua-o e crie um novo com a configuração correta.

**:white_check_mark: Instruções passo a passo**
1. Selecione **App clients** dentro de **General settings**.
1. Escolha **Add an app client**.
1. Dê um nome como `WildRydesWebApp`.
1. **Uncheck** a opção Gerar segredo do cliente. Segredos de cliente não são suportados para uso com aplicativos baseados em navegador.
1. Escolha **Create app client**.
   <kbd>![Create app client screenshot](../images/add-app.png)</kbd>
1. Anote o **App client id** criado.

### 3. Atualize o arquivo config.js do seu Website

O arquivo [/js/config.js][configjs] contém configurações para o ID do grupo de usuários, ID do cliente do aplicativo e Região. Atualize este arquivo com as configurações do grupo de usuários e aplicativo que você criou nas etapas anteriores e confirme o arquivo de volta para seu repositório git.

**:white_check_mark: Instruções passo a passo**
1. Em seu ambiente de desenvolvimento Cloud9 abra `js/config.js`
1. Atualize a seção `cognito` com os valores corretos para o grupo de usuários e aplicativo que você acabou de criar.
 Você pode encontrar o valor para `userPoolid` na página de detalhes do pool do console do Amazon Cognito depois de selecionar o grupo de usuários criado.

    ![Pool ID](../images/pool-id.png)


    ![Pool ID](../images/client-id.png)


    O arquivo config.js atualizado deve ser assim. Note que os valores reais para o seu arquivo serão diferentes:
    ```JavaScript
    window._config = {
        cognito: {
            userPoolId: 'us-east-1_uXboG5pAb', // e.g. us-east-1_uXboG5pAb
            userPoolClientId: '25ddkmj4v6hfsfvruhpfi7n4hv', // e.g. 25ddkmj4v6hfsfvruhpfi7n4hv
            region: 'us-east-1' // e.g. us-east-1
        },
        api: {
            invokeUrl: '' // e.g. https://rc7nyt4tql.execute-api.us-east-1.amazonaws.com/prod,
        }
    };
    ```
1. Salve o arquivo modificado certificando-se de que o nome do arquivo ainda está `config.js`.
1. Commit as mudanças no seu repositório git:
    ```
    $ git add js/config.js 
    $ git commit -m "configure cognito"
    $ git push
    ...
    Counting objects: 4, done.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 415 bytes | 415.00 KiB/s, done.
    Total 4 (delta 3), reused 0 (delta 0)
    To https://git-codecommit.us-east-1.amazonaws.com/v1/repos/wildrydes-site
       7668ed4..683e884  master -> master
    ```

    O Amplify Console deve pegar as alterações e começar a fazer build e implantar seu aplicativo Web.

## Validação da implementação

**:white_check_mark: Instruções passo a passo**
1. Visite `register.html` no domínio do seu site, ou escolha o**Giddy Up!** na página inicial do seu site.

1. Preencha o formulário de inscrição e escolha **Let's Ryde**. Você pode usar seu próprio e-mail. Certifique-se de escolher uma senha que contenha pelo menos uma letra maiúscula, um número e um caractere especial. Não se esqueça da senha que você inseriu para mais tarde. Você deverá ver um alerta que confirme que seu usuário foi criado.


1. Se utilizou um endereço de e-mail que controla, pode concluir o processo de verificação da conta visitando `/verify.html` no domínio do seu website e introduzindo o código de verificação que lhe é enviado por e-mail. Observe que o e-mail de verificação pode acabar na sua pasta de spam.

1. Depois de confirmar o novo usuário usando a página `/verify.html` ou o console do Cognito, visite `/signin.html` e faça login usando o endereço de e-mail e a senha que você inseriu durante a etapa de registro.

1. Se for bem sucedido, você deve ser redirecionado para `/ride.html`. Você deve ver uma notificação informando que a API não está configurada.

    ![Successful login screenshot](../images/successful-login.png)

### :star: Revisão

:key: O Amazon Cognito fornece dois recursos diferentes para gerenciar usuários, identidades federadas e grupos de usuários. Os grupos de usuários do [Amazon Cognito] podem lidar com quase todos os aspectos sobre o gerenciamento de usuários, suas credenciais de login, manipulação de redefinições de senha, autenticação multifator e muito mais!

:wrench: Neste módulo você usou grupos de usuários para criar um sistema de gerenciamento de usuários completamente hospedado e gerenciado que nos permitirá autenticar seus usuários e gerenciar suas informações de usuário. A partir daí, você atualizou o site para usar o grupo de usuários e utilizou os AWS SDKs para fornecer um formulário de login no site.

### Próximo

:white_check_mark: Depois de ter logado com sucesso na sua aplicação web, você pode prosseguir para o próximo módulo, [Serverless Backend][serverless-backend].

[static-web-hosting]: ../1_StaticWebHosting/
[amplify-console]: https://aws.amazon.com/amplify/console/
[cognito]: https://aws.amazon.com/cognito/
[setup]: ../0_Setup/
[serverless-backend]: ../3_ServerlessBackend/
[cognito-console]: https://console.aws.amazon.com/cognito/home
[configjs]: ../1_StaticWebHosting/website/js/config.js
[jwt-decoder]: https://jwt.io/
