# PráticaTecnológica@GFT: 

# Treinamento - API - Dia #2: API Management

## Requisitos:

- Maven
- Java
- WSO2
- Swagger Codegen

## Configurando seu Ambiente de Desenvolvimento:

- Criar arquivo setup-part2.bat com script abaixo:

***
```bat
REM Resolve PowerShell
IF EXIST c:\windows\sysnative\NUL (
	SET ps="c:\windows\sysnative\windowspowershell\v1.0\powershell.exe"
) else (
	SET ps="c:\windows\system32\windowspowershell\v1.0\powershell.exe"
)
REM Install PowerShell 4
WHERE choco
IF %ERRORLEVEL% NEQ 0 %PS% -NoProfile -ExecutionPolicy unrestricted -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%systemdrive%\ProgramData\chocolatey\bin
choco install powershell4 --acceptlicense -y
REM Install Requirements
choco install maven --acceptlicense -y
choco install jdk8 --acceptlicense -y
***
```

## O que é o WSO2 API Manager?
WSO2 API Manager é uma solução completa de gerenciamento de API de classe empresarial que combina acesso API fácil, gerenciado com governança e análise de APIs completas. O gerenciador de tráfego com um mecanismo de aceleração dinâmico gerencia e escalona facilmente o tráfego da API, enquanto sua análise aprimorada fornece uma visão mais ampla do uso, desempenho e anomalias da API. 
Esta categoria de produtos alavanca componentes comprovados da plataforma WSO2 para proteger APIs e fornece uma governança mais forte em APIs, serviços e aplicativos com visualização completa do ciclo de vida da API.

## Configurando WSO2 API Manager
Faça o download do [WS02 API Manager Server](http://wso2.com/api-management/)

Vamos chamar o diretório de instalação do WSO2 API Management de %WSO2AM_HOME%

Execute em um terminal:
```
%WSO2AM_HOME%/bin/wso2server.bat --run
```

Acompanhe o log de startup no terminal e depois de um certo tempo verifique se os links estão disponíveis

- [Store](http://localhost:9763/store/): Loja de APIs - Portal do desenvolvedor
- [Publisher](https://localhost:9443/publisher): Portal do administrador de API para controlar e gerenciar o ciclo de vida
- [Portal](https://localhost:9443/portal): Administrativo do portal do desenvolver e seus respectivos dashboards
- [Admin Portal](https://localhost:9443/portal): Administração Geral do API Manager e Gateway


## O que é o Swagger CodeGen?
Swagger-codegen possui um mecanismo com base em modelos para gerar documentação, clientes API e stubs de servidor em diferentes idiomas analisando sua definição OpenAPI / Swagger. http://swagger.io

## Vamos gerar um Stub da API Pague Rápido
Primeiramente baixe do github o pacote do swagger codegen

- [Swagger CodeGen](https://github.com/swagger-api/swagger-codegen)

Para compilar o pacote do Swagger CodeGen utilize o maven
```
mvn clean package
```

Utilizando a documentação do próprio github gere um server stub para a api pague-seguro com o swagger desenvolvido no exercício do primeiro dia.

Após o stub gerado suba o servidor node express
```
npm install
npm start
```

Utilize o [postman](https://www.getpostman.com/) para realizar testes a esta API.


## Criando / Publicando uma Frontend API
A frontend API é o conceito utilizado para se referir a API que será exposta para os canais consumidores. Esta API geralmente é criada e gerenciada pelo módulo do API manager para disponibilizar um proxy no API gateway, onde políticas de segurança e controle de vazão são aplicadas.

- Frontend API: API que será contruída no API Manager da WSO2 e consumida pelos canais.
- Backend API: API que realmente implementa a lógica para executar as operações definias em seu contrato. No caso deste exercício em particular é o stub gerado com o swagger codegen


Portanto, acesse o portal Publisher do WSO2 com o usuário administrativo:
- usuário: admin
- senha: admin

Execute as seguintes tarefas para criar a API:
- Crie a API com o swagger do pague-rapido
- Utilize as configurações padrões para criar e publicar a api

Para maiores informações sobre o produto consulte a documentação [API Manager - WSO2 - Docs](https://docs.wso2.com/display/AM200/Getting+Started).
- [Tutorial - Criar uma API](https://docs.wso2.com/display/AM200/Create+and+Publish+an+API)


## Sou um Developer. Preciso assinar esta API
Nesta etapa vamos executar o papel de um desenvolver que irá consumir a API do pague-rapido.

Para esta atividade realize as atividades a seguir
- Acesse o portal do desenvolvedor / store
- Crie um novo usuário como desenvolvedor
- Realize o login
- Crie uma nova aplicação que irá consumir a api
- Para esta nova api gere as chaves de acesso a API
- Selecione a api de pague-rapido e a assine
- Realize o teste na aba de api console
- Realize o teste no postman utilizando as credenciais gerada para sua aplicação

Para maiores informações:
- [Tutorial - Assinar uma API](https://docs.wso2.com/display/AM200/Subscribe+to+an+API)

Observações:
 - Durante os testes no console, caso as chamadas retornem erro, verifique se a chamada é HTTPS (Ex.: https://192.168.255.73:8243/PagueRapidoAPIv1/1.0.0/pagamentos). Caso seja, abra a URL em uma nova aba e aceite o certificado de segurança.

## Sua API não está segura, o que fazer?
API Gateway possuem mecanismos de policies para fazer o controle de sergurança e ameaças.
Para este exercício vamos editar e republicar a API para aplicar uma política de prevenção a ataques DDoS (Política de Throttling).

Para este passo atue como administrador das APIs
- Acesse o portal publisher
- Edite a api de pague-rapido
- Configure uma política de Throttling para 10K por minuto
- Salve e publique a api

Na sequência atue como um desenvolvedor da api
- Realize um teste de carga com a ferramenta SoapUI para atingir a taxa que irá barrar o acesso a API
- Para maiores informações sobre um load test do SoapUI. [SoapUI Load Test](https://www.soapui.org/load-testing/concept.html)


## Auditoria
Como estatística e registro de auditoria os payloads ajudam de maneira geral em investigações de problemas e avaliações de auditoria.

Para isto atue como administrador da API para configurar a auditoria:
- Acesse o portal publisher
- Edite a api de pague-rapido
- Configure uma política de mediação log message para o fluxo de entrada e saída da API
- Salve e publique a api

Na sequência atue como desenvolvedor da api
- Realize o teste da api novamente
- Acompanhe no log do CMD os dados de entrada e saída de cada requisição / resposta

## Controlando o Ciclo de Vida
Nesta seção vamos trabalhar com as versões e ciclo de vida da api pague-rapido

Como administrador de api crie uma nova versão api e aposente versão v1
- Acesse o portal publisher
- Crie uma versão v2 da api pague-rapido
- Para esta nova versão continue utilizando o mesmo endpoint da backend API
- Publique a versão v2 da api pague-rapido
- Edite a versão v1 da api pague-rapido para aposentada

Como desenvolvedor realize as seguintes etapas
- Acesso o portal do desenvolver / store
- Realize uma nova assinatura para api de pague-rapido v2
- Para a sua aplicação regere novamente as credenciais
- Realize o teste da api pague-rapido v1
- Realize o teste da api pague-rapido v2

## Concluindo
Neste exercício passamos pelos principais conceitos que envolvem o API Management e focamos no produto Open Source da WSO2. Porém, existem vários outros produtos e estratégias customizadas para trabalhar com a exposição de APIs publicas e privadas.

Para maiores informações:
- [RESTful Cookbook](http://restcookbook.com/)
- [Winning in the API Economy](https://www.3scale.net/wp-content/uploads/2013/10/Winning-in-the-API-Economy-eBook-3scale.pdf)
- [API - A Strategy Guide](http://shop.oreilly.com/product/0636920021223.do)
