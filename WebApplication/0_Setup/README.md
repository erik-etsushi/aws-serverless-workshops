## Setup


### AWS Cloud9 IDE

O AWS Cloud9 é um ambiente de desenvolvimento integrado (IDE) baseado em nuvem que permite
você escreve, executa e depura seu código apenas com um navegador. Ele inclui um código
editor, depurador e terminal. O Cloud9 vem com ferramentas essenciais
para linguagens de programação populares e a interface de linha de comando (CLI) da AWS
pré-instalado para que você não precise instalar arquivos ou configurar seu laptop para
este workshop. Seu ambiente Cloud9 terá acesso à mesma conta AWS
como o usuário com o qual você efetuou login no AWS Management Console.


**:white_check_mark: Passo a passo**

1. Vá até o AWS Management Console, clique em **Services** e depois em **Cloud9**
   abaixo de Developer Tools.

1. Clique **Create environment**.

1. Entre com `Development` como **Name** e dê uma **Description**.

1. Clique **Next step**.

1. Você pode deixar **Environment settings** na configuração padrão para instância
   **t2.micro** 

1. Clique **Next step**.

1. Revise as configurações do ambiente e clique em **Criar ambiente**. Ele vai
 demorar alguns minutos para que seu ambiente seja provisionado e preparado.

1. Quando estiver pronto, seu IDE será aberto em uma tela de boas-vindas. Abaixo disso, você deve
 ver um prompt de terminal.


    Execute o seguinte comando e tome nota do resultado:

    ```console
    ec2-user:~/environment $ aws sts get-caller-identity
    ```
    ```json
    {
        "Account": "123456789012",
        "UserId": "AKIAI44QH8DHBEXAMPLE",
        "Arn": "arn:aws:iam::123456789012:user/Alice"
    }
    ```

Mantenha seu IDE do AWS Cloud9 aberto em uma guia ao longo deste workshop.


### Próximo passo

:white_check_mark: Siga para o próximo módulo, [Static Web Hosting][static-web-hosting], 
onde você criará um website estático usando o Amplify.

[region-table]: https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/
[static-web-hosting]: ../1_StaticWebHosting/
