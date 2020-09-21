## Serverless Web Application Workshop

Neste workshop, você implantará um aplicativo web simples que permite que os usuários solicitem passeios de [Wild Rydes][wildrydes]. O aplicativo apresentará aos usuários uma interface de usuário baseada em HTML para indicar o local onde eles gostariam de ser capturados e fará interface no backend com um serviço Web RESTful para enviar a solicitação e despachar um unicórnio próximo. O aplicativo também fornecerá facilidades para que os usuários se registrem no serviço e façam login antes de solicitar passeios.

A arquitetura do aplicativo usa [AWS Lambda] , [Amazon API Gateway] , [Amazon S3] , [Amazon DynamoDB] , [Amazon Cognito]  e [Console do AWS Amplify]. O Amplify Console hospeda recursos estáticos da Web, incluindo HTML, CSS, JavaScript e arquivos de imagem que são carregados no navegador do usuário via S3. O JavaScript executado no navegador envia e recebe dados de uma API de back-end pública criada usando o Lambda e o API Gateway. O Amazon Cognito fornece funções de gerenciamento e autenticação de usuários para proteger a API de back-end. Finalmente, o DynamoDB fornece uma camada de persistência na qual os dados podem ser armazenados pela função do Lambda da API.


![Wild Rydes Web Application Architecture](images/wildrydes-complete-architecture.png)

### Modules

Este workshop é dividido em quatro módulos. Cada módulo descreve um cenário de
o que vamos construir e orientações passo a passo para ajudá-lo a implementar o
arquitetura e verificar o seu trabalho.

| Modulo | Descricao |
| ---------------- | -------------------------------------------------------- |
| [Static Web hosting]| Implante o site estático usando o AWS Amplify Console criando primeiro um repositório git no CodeCommit e, em seguida, enviando o código do site. |
| [User Management] | Configurar o gerenciamento de usuários para o site usando o Amazon Cognito. |
| [Serverless Backend]| Crie uma função do AWS Lambda que persistirá dados em uma tabela do Amazon DynamoDB. |
| [RESTful APIs] | Exponha a função do Lambda por meio de um Amazon API Gateway como uma API RESTful que o site estático pode chamar. |



### Próximo passo

:white_check_mark: Inicie com o [setup guide][setup] do ambiente.

[wildrydes]: http://wildrydes.com/
[unicorns]: http://www.wildrydes.com/unicorns.html
[amplify-console]: https://aws.amazon.com/amplify/console/
[cognito]: https://aws.amazon.com/cognito/
[lambda]: https://aws.amazon.com/lambda/
[api-gw]: https://aws.amazon.com/api-gateway/
[s3]: https://aws.amazon.com/s3/
[dynamodb]: https://aws.amazon.com/dynamodb/
[setup]: 0_Setup/
[static-web-hosting]: 1_StaticWebHosting/
[user-management]: 2_UserManagement/
[serverless-backend]: 3_ServerlessBackend/
[restful-apis]: 4_RESTfulAPIs/
[cleanup]: 9_CleanUp/
