# Módulo 3: Serverless Backend

Neste módulo, você usará [AWS Lambda] [lambda] e [Amazon DynamoDB] [dynamodb] para criar um processo de backend para lidar com solicitações de seu aplicativo da Web. O aplicativo do navegador que você implantou no primeiro módulo permite que os usuários solicitem que um unicórnio seja enviado para um local de sua escolha. Para atender a essas solicitações, o JavaScript em execução no navegador precisará invocar um serviço em execução na nuvem.

Você implementará uma função do Lambda que será invocada cada vez que um usuário solicitar um unicórnio. A função selecionará um unicórnio da frota, registrará a solicitação em uma tabela do DynamoDB e responderá ao aplicativo front-end com detalhes sobre o unicórnio sendo despachado.

![Serverless backend architecture](../images/serverless-backend-architecture.png)

A função é chamada a partir do navegador usando [Amazon API Gateway] [api-gw]. Você implementará essa conexão no próximo módulo. Para este módulo, você apenas testará sua função isoladamente.


### 1. Create an Amazon DynamoDB Table

Use o console do Amazon DynamoDB para criar uma nova tabela do DynamoDB. Chame a sua tabela `Rides` e dê-lhe uma chave de partição chamada `rideId` com o tipo String. O nome da tabela e a chave de partição são sensíveis a maiúsculas. Certifique-se de usar os IDs exatos fornecidos. Use os padrões para todas as outras configurações.

Depois de criar a tabela, anote o ARN para uso na próxima etapa.

**:white_check_mark: Instruções passo a passo**
1. Vá até [Amazon DynamoDB Console][dynamodb-console]
1. Escolha **Create table**.
1. Entre `Rides` para o **Table name**.
1. Entre `RideId` para a **Partition key** e **String** para o key type. 
1. Marque a opção **Use default settings** e escolha **Create**.
    ![Create table screenshot](../images/ddb-create-table.png)
1. Role até a parte inferior da seção Visão geral da nova tabela e anote o **ARN**. Você usará isso na próxima seção.

### 2. Criar uma função do IAM para sua função do Lambda

#### Visão geral

Cada função do Lambda tem uma função do IAM associada a ela. Essa função define com quais outros serviços da AWS a função tem permissão para interagir. Para fins deste workshop, você precisará criar uma função do IAM que conceda permissão à sua função do Lambda para gravar logs no Amazon CloudWatch Logs e acesso para gravar itens em sua tabela do DynamoDB.


**:white_check_mark: Instruções passo-a-passo**
1. Vá para o [AWS IAM Console] [iam-console]
1. Selecione **Funções** na barra de navegação esquerda e escolha **Criar função**.
1. Selecione **Lambda** para o tipo de função no grupo **Serviço da AWS** e clique em **Avançar: Permissões**

1. Comece a digitar `awslambdabasicExecutionRole` na caixa de texto **Filter** e marque a caixa ao lado dessa função.
1. Clique em **Avançar: Tags**. Adicione as tags que desejar.
1. Clique em **Avançar: Revisar**.
1. Digite `WildryDeslambda` para o **Nome da função**.
1. Escolha **Criar função**. 

Em seguida, você precisa adicionar permissões à função para que ela possa acessar sua tabela do DynamoDB.

**:white_check_mark: Instruções passo-a-passo*
1. Enquanto estiver no Console do IAM na página de funções, digite `WildryDeslambda` na caixa de filtro na página Funções e escolha a função que você acabou de criar.
1. Na guia Permissões, escolha o link **Adicionar política inline** no canto inferior direito para criar uma nova política inline.
 ![Inline policies screenshot] (../images/inline-policies.png)
1. Selecione **Escolher um serviço**.
1. Comece a digitar `DynamoDB `na caixa de pesquisa
 **Localizar um serviço** e selecione **DynamoDB ** quando ele aparecer.
 ![Select policy service] (../images/select-policy-service.png)
1. Escolha **Selecionar ações**.
1. Comece a digitar `Putitem` na caixa de pesquisa rotulada **Ações de filtro** e marque a caixa ao lado de **Putitem** quando ela aparecer.
1. Selecione a seção **Recursos**.
1. Com a opção **Específico** selecionada, escolha o link Adicionar ARN na seção **Tabela**.
1. Cole o ARN da tabela que você criou na seção anterior no campo**Especificar ARN para tabela** e escolha **Adicionar**.
1. Escolha **Revisar política**.
1. Digite `DynamoDBWriteAccess` para o nome da política e escolha **Criar política**.
 ![Review Policy] (../images/review-policy.png)

### 3. Criar uma função do Lambda para receber solicitações

#### Visão geral

O AWS Lambda executará seu código em resposta a eventos como uma solicitação HTTP. Nesta etapa, você criará a função principal que processará solicitações de API da aplicação Web para despachar um unicórnio. No próximo módulo, você usará o Amazon API Gateway para criar uma API RESTful que exporá um endpoint HTTP que pode ser invocado a partir dos navegadores de seus usuários. Em seguida, você conectará a função do Lambda criada nesta etapa a essa API para criar um backend totalmente funcional para sua aplicação web.

**:white_check_mark: Instruções passo-a-passo**
1. Acesse o [AWS Lambda] [lambda-console]
1. Clique em **Criar função**.
1. Mantenha o cartão padrão **Author from scratch** selecionado.
1. Digite `requestUnicorn` no campo **Nome**.
1. Selecione **Node.js 10.x** para o **Runtime**.
2. Expanda *Escolha ou crie uma função de execução* em **Permissões**.
1. Certifique-se de que `Escolha uma função existente` esteja selecionado no menu suspenso **Role**.
1. Selecione `WildRydeslambda` no menu suspenso **Função existente**.
 ![Create Lambda function screenshot] (../images/create-lambda-function.png)
1. Clique em **Criar função**.
1. Role para baixo até a seção **Código de função** e substitua o código existente no editor de código **index.js** pelo conteúdo de [requestUnicorn.js] (requestUnicorn.js).
 ![Create Lambda function screenshot] (../images/create-lambda-function-code.png)
1. Clique em **"Salvar"** no canto superior direito da página.

## Validação da implementação

Para este módulo, você testará a função criada usando o console do AWS Lambda. No próximo módulo, você adicionará uma API REST com API Gateway para que você possa invocar sua função a partir do aplicativo baseado em navegador que você implantou no primeiro módulo.

**:white_check_mark: Instruções passo-a-passo**
1. Na tela principal de edição da sua função, selecione **Configurar eventos de teste** no menu suspenso **Selecione um evento de teste... **.
 ![Configure test event] (../images/configure-test-event.png)
1. Manter a opção **Criar novo evento de teste** selecionada.
1. Digite `TestRequestEvent` no campo **Nome do evento**
1. Copie e cole o seguinte evento de teste no editor:
    ```JSON
    {
        "path": "/ride",
        "httpMethod": "POST",
        "headers": {
            "Accept": "*/*",
            "Authorization": "eyJraWQiOiJLTzRVMWZs",
            "content-type": "application/json; charset=UTF-8"
        },
        "queryStringParameters": null,
        "pathParameters": null,
        "requestContext": {
            "authorizer": {
                "claims": {
                    "cognito:username": "the_username"
                }
            }
        },
        "body": "{\"PickupLocation\":{\"Latitude\":47.6174755835663,\"Longitude\":-122.28837066650185}}"
    }
    ```
    ![Configure test event](../images/configure-test-event-2.png)
1. Clique em **Criar**.
1. Na tela principal de edição da função clique em **Test** com `TestRequestEvent` selecionado no menu suspenso. 
1. Role até o topo da página e expanda a seção **Details** da seção **Resultado da execução**.
1. Verifique se a execução foi bem-sucedida e se o resultado da função se parece com o seguinte:
    ```JSON
    {
      "statusCode": 201,
      "body": "{\"RideId\":\"1h0zDZ-6KLZaEQCPyqTxeQ\",\"Unicorn\":{\"Name\":\"Shadowfax\",\"Color\":\"White\",\"Gender\":\"Male\"},\"UnicornName\":\"Shadowfax\",\"Eta\":\"30 seconds\",\"Rider\":\"the_username\"}",
      "headers": {
        "Access-Control-Allow-Origin": "*"
      }
    }
    ```

### :star: Revisão

:key: [AWS Lambda] [lambda] é uma função sem servidor como um produto de serviço que elimina a carga de gerenciamento de servidores para executar seus aplicativos. Você configura um gatilho e define a função que a função pode usar e, em seguida, pode interagir com quase tudo o que você quiser, desde bancos de dados, armazenamentos de dados, para outros serviços, ou publicamente na Internet ou em sua própria Amazon Virtual Private Cloud (VPC). O [Amazon DynamoDB] [dynamodb] é um banco de dados sem servidor não relacional que pode ser dimensionado automaticamente para lidar com grandes quantidades de tráfego e dados sem que você precise gerenciar nenhum servidor.

:wrench: Neste módulo, você criou uma tabela do DynamoDB e, em seguida, uma função do Lambda para gravar dados nela. Esta função será colocada atrás de um Amazon API Gateway no próximo módulo que, por sua vez, será conectado ao seu aplicativo web para capturar os detalhes do passeio de seus usuários.

### Próximo

:white_check_mark: Depois de ter testado com sucesso sua nova função usando o console do Lambda, você pode passar para o próximo módulo, [RESTful APIs][restful-apis].

[amplify-console]: https://aws.amazon.com/amplify/console/
[cognito]: https://aws.amazon.com/cognito/
[lambda]: https://aws.amazon.com/lambda/
[api-gw]: https://aws.amazon.com/api-gateway/
[dynamodb]: https://aws.amazon.com/dynamodb/
[static-web-hosting]: ../1_StaticWebHosting/
[user-management]: ../2_UserManagement/
[restful-apis]: ../4_RESTfulAPIs/
[dynamodb-console]: https://console.aws.amazon.com/dynamodb/home
[iam-console]: https://console.aws.amazon.com/iam/home
[lambda-console]: https://console.aws.amazon.com/lambda/home
