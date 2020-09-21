# Módulo 4: APIs RESTful com AWS Lambda e Amazon API Gateway

Neste módulo, você usará o API Gateway para expor a função do Lambda que você construiu no módulo anterior como uma API RESTful. Esta API estará acessível na Internet pública. Ele será protegido usando o grupo de usuários do Amazon Cognito que você criou no módulo de gerenciamento de usuários. Usando essa configuração, você transformará seu site hospedado estaticamente em um aplicativo web dinâmico adicionando JavaScript do lado do cliente que faz chamadas AJAX para as APIs expostas.

![Dynamic web app architecture](../images/restful-api-architecture.png)

O diagrama acima mostra como o componente API Gateway que você criará neste módulo se integra aos componentes existentes que você criou anteriormente. Os itens acinzentados são peças que você já implementou em etapas anteriores.

O site estático que você implantou no primeiro módulo já possui uma página configurada para interagir com a API que você criará neste módulo. A página em /ride.html tem uma interface simples baseada em mapa para solicitar um passeio de unicórnio. Após a autenticação usando a página /signin.html, seus usuários poderão selecionar seu local de retirada clicando em um ponto no mapa e solicitando uma viagem escolhendo o botão “Solicitar unicórnio” no canto superior direito.


### 1. Crie uma nova REST API
Use o console do Amazon API Gateway para criar uma nova API chamada `Wildrydes`.

**:white_check_mark: Instruções passo-a-passo**
1. Vá para o [Amazon API Gateway Console][api-gw-console]
1. Escolha **Criar API**.
1. Selecione **REST**, **Nova API** e digite `Wildrydes` para o **Nome API**.
1. Selecione `Edge Optimized` no menu suspenso **Endpoint Type**.
1. Escolha **Criar API**

    ![Create API screenshot](../images/create-api.png)

### 2. Criar um Autorizador de Grupos de Usuários do Cognito

#### Overview
O Amazon API Gateway pode usar os tokens JWT retornados pelos grupos de usuários do Cognito para autenticar chamadas de API. Nesta etapa, você configurará um autorizador para sua API para usar o grupo de usuários criado no segundo módulo.

**:white_check_mark: Instruções passo-a-passo**
1. Em sua API recém-criada, escolha **Autorizadores**.
1. Escolha **Criar novo autorizador**.
1. Digite `wildrydes` para o nome do Autorizador.
1. Selecione **Cognito** para o tipo.
1. No menu suspenso Região em **Pool de usuários do Cognito**, selecione a região onde você criou seu grupo de usuários do Cognito no módulo Gerenciamento de usuários (por padrão, a região atual deve ser selecionada).
1. Digite `wildrydes` (ou o nome que você deu ao grupo de usuários) na entrada **Cognito**.
1. Digite `Autorização` para a fonte **Token**.
1. Escolha **Criar**.

    ![Create user pool authorizer screenshot](../images/create-user-pool-authorizer.png)

#### Verifique sua configuração de autorização

**:white_check_mark: Instruções passo-a-passo**
1. Abra uma nova guia do navegador e visite `/ride.html` sob o domínio do seu site.
1. Se você for redirecionado para a página de login, entre com o usuário que você criou no último módulo. Você será redirecionado de volta para `/ride.html`.
1. Copie o token de autenticação da notificação no `/ride.html`,
1. Volte para a guia anterior, onde você acabou de criar o Autorizador
1. Clique em **Testar** na parte inferior do cartão para o autorizador.
1. Cole o token de autenticação no campo **Token de Autorização** na caixa de diálogo pop-up.
    ![Test Authorizer screenshot](../images/apigateway-test-authorizer.png)

1. Clique no botão**Testar** e verifique se o código de resposta é 200 e se você vê as reivindicações para seu usuário exibidas.

### 3. Criar um novo recurso e método
Crie um novo recurso chamado /ride dentro de sua API. Em seguida, crie um método POST para esse recurso e configure-o para usar uma integração de proxy do Lambda apoiada pela função RequestUnicorn que você criou na primeira etapa deste módulo.

**:white_check_mark: Instruções passo-a-passo**
1. No navegador esquerdo, clique em **Resources** em sua API WildRides.
1. No menu suspenso **Ações** selecione **Criar recurso**.
1. Digite `ride` como o **Nome do recurso**.
1. Verifique se **Caminho do recurso** está definido como `ride`.
1. Selecione **Ativar API Gateway CORS** para o recurso.
1. Clique em **Criar recurso**.

    ![Create resource screenshot](../images/create-resource.png)

1. Com o recurso `/ride` recém-criado selecionado, no menu suspenso **Ação** selecione **Criar método**.
1. Selecione `POST` no novo menu suspenso que aparece e, em seguida, **clique na marca de verificação**.

    ![Create method screenshot](../images/create-method.png)
1. Selecione **Função Lambda** para o tipo de integração.
1. Marque a caixa para **Usar integração do Lambda Proxy**.
1. Selecione a região que você está usando para **Região do Lambda**.
1. Digite o nome da função que você criou no módulo anterior, `requestUniCorn`, para **Função Lambda**.
1. Escolha **Salvar**. Observe que se você receber um erro informando que seu funcionamento não existe, verifique se a região selecionada corresponde à que você usou no módulo anterior.

    ![API method integration screenshot](../images/api-integration-setup.png)

1. Quando solicitado a conceder permissão do Amazon API Gateway para invocar sua função, escolha **OK**.
1. Escolha no cartão**Solicitação de método**.
1. Escolha o ícone de lápis ao lado de **Autorização**.
1. Selecione o autorizador de grupo de usuários WildRides Cognito na lista suspensa e clique no ícone de marca de seleção.

    ![API authorizer configuration screenshot](../images/api-authorizer.png)

### 4. Implante a sua API
No console do Amazon API Gateway, escolha Ações, Implantar API. Você será solicitado a criar um novo estágio. Você pode usar prod para o nome do estágio.

**:white_check_mark: Instruções passo-a-passo**
1. Na lista suspensa **Ações** selecione **Implantar API**.
1. Selecione ** [Novo estágio] ** na lista suspensa **Estágio de implantação**.
1. Digite `prod` para o **Nome do Estágio**.
1. Escolha **Implementar**.
1. Observe o**Invocar URL**. Você vai usá-lo na próxima seção.

### 5. Atualizar a configuração do site
Atualize o arquivo /js/config.js na implantação do site para incluir o URL de chamada do estágio que você acabou de criar. Você deve copiar o URL de chamada diretamente da parte superior da página do editor de palco no console do Amazon API Gateway e colá-lo na chave\ _config.api.InvokeUrl do arquivo /js/config.js do site. Certifique-se de que, ao atualizar o arquivo de configuração, ele ainda contém as atualizações feitas no módulo anterior para o grupo de usuários do Cognito.

**:white_check_mark: Passo-a-passo**
1. Em seu ambiente de desenvolvimento Cloud9 abra `js/config.js`
1. Atualize a configuração **InvokeUrl** na chave **api** no arquivo config.js. Defina o valor como **Invocar URL** para o estágio de implantação criado na seção anterior.
 Um exemplo de um arquivo `config.js` completo está incluído abaixo. Observe que os valores reais em seu arquivo serão diferentes.
    ```JavaScript
    window._config = {
        cognito: {
            userPoolId: 'us-west-2_uXboG5pAb', // e.g. us-east-2_uXboG5pAb
            userPoolClientId: '25ddkmj4v6hfsfvruhpfi7n4hv', // e.g. 25ddkmj4v6hfsfvruhpfi7n4hv
            region: 'us-west-2' // e.g. us-east-2
        },
        api: {
            invokeUrl: 'https://rc7nyt4tql.execute-api.us-west-2.amazonaws.com/prod' // e.g. https://rc7nyt4tql.execute-api.us-west-2.amazonaws.com/prod,
        }
    };
    ```

1. Salve o arquivo modificado certificando-se de que o nome do arquivo ainda é `config.js`.
1. Submeter as alterações no seu repositório git:
    ```
    $ git add js/config.js 
    $ git commit -m "configure api invokeURL"
    $ git push
    ...
    Counting objects: 4, done.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 422 bytes | 422.00 KiB/s, done.
    Total 4 (delta 3), reused 0 (delta 0)
    To https://git-codecommit.us-east-1.amazonaws.com/v1/repos/wildrydes-site
       c15d5d5..09f1c9a  master -> master
    ```

    [Amplify Console][amplify-console-console] deve pegar as alterações e começar a criar e implantar seu aplicativo web. 

## Validação do projeto

**:white_check_mark: Passo-a-passo**
1. Visite `/ride.html` no domínio do seu site.
1. Se você for redirecionado para a página de login, entre com o usuário que você criou no módulo anterior.
1. Depois que o mapa for carregado, clique em qualquer lugar do mapa para definir um local de retirada.
1. Escolha **Solicitar unicórnio**. Você deve ver uma notificação na barra lateral direita dizendo que um unicórnio está a caminho e, em seguida, ver um ícone de unicórnio voar para o seu local de coleta.

### :star: Revisão

:key: [Amazon API Gateway][api-gw] é um serviço totalmente gerenciado que facilita aos desenvolvedores criar, publicar, manter, monitorar e proteger APIs em qualquer escala. Você pode conectar facilmente a Autorização via [Amazon Cognito][cognito] e back-ends como [AWS Lambda][lambda] para criar APIs completamente sem servidor.

:wrench: Neste módulo você usou o API Gateway para fornecer uma API REST para a função do Lambda criada no módulo anterior. A partir daí, você atualizou o site para usar o endpoint da API para que você possa solicitar passeios e as informações sobre o passeio sejam salvas na tabela do DynamoDB criada anteriormente.

:star: Parabéns, você completou o Workshop de Aplicações Web Wild Rydes Serverless!  


[amplify-console]: https://aws.amazon.com/amplify/console/
[amplify-console-console]: https://console.aws.amazon.com/amplify/home
[api-gw]: https://aws.amazon.com/api-gateway/
[api-gw-console]: https://console.aws.amazon.com/apigateway/home
[cleanup]: ../9_CleanUp/
[cognito-console]: https://console.aws.amazon.com/cognito/home
[cognito]: https://aws.amazon.com/cognito/
[configjs]: ../1_StaticWebHosting/website/js/config.js
[dynamodb-console]: https://console.aws.amazon.com/dynamodb/home
[dynamodb]: https://aws.amazon.com/dynamodb/
[iam-console]: https://console.aws.amazon.com/iam/home
[jwt-decoder]: https://jwt.io/
[lambda-console]: https://console.aws.amazon.com/lambda/home
[lambda]: https://aws.amazon.com/lambda/
[restful-apis]: ../4_RESTfulAPIs/
[serverless-backend]: ../3_ServerlessBackend/
[setup]: ../0_Setup/
[static-web-hosting]: ../1_StaticWebHosting/
[user-management]: ../2_UserManagement/
