# Construção de um aplicativo de consulta de notícias

## Você foi contratado por uma empresa de notícias para desenvolver as APIs do aplicativo de notícias.

## O aplicativo deve permitir que os usuários consultem as últimas notícias, com atualizações em tempo real e recursos de filtragem e pesquisa. Será necessário suportar login social (integrado ao IDP atual da empresa) e as APIs ficarão expostas de forma pública para qualquer cliente que queira consumi-las.

**Vamos avaliar os cenário acima, onde diversas escolhas podem ser feitas visando a melhor abordagem para a solução do problema (definição do protocolo de comunicação da API, estratégia de versionamento da API, adoção ou não de um ORM e modelo de autenticação/autorização).**

- **Protocolo de comunicação da API: REST/HTTPS**
    - REST e HTTPS para termos uma aplicação escalável, simples e flexível, além de garantir a segurança na comunicação.
    - WebSockets: Para atualizações em tempo real.
- **Estratégia de versionamento da API:**
    - Vamos utilizar o versionamento via URL (URI path versioning), onde a versão é incluída no caminho da URL:
        - /v1/news
        - /v2/news
    - Essa abordagem permite a evolução da nossa API mantendo compatibilidade retroativa, além de possibilitar documentação específica para cada versão
- ORM:
    - Vamos utilizar o TypeORM  para facilitar a comunicação entre a nossa API e o banco de dados relacional, assim temos mais produtividade, pois reduz a quantidade de código e acelera o desenvolvimento, manutenibilidade pois trabalhando com objetos ao invés de SQL puro, o código se torna mais legível e fácil de manter.
- **Modelo de autenticação/autorização:**
    - OAuth 2.0, OpenID Connect e JWT para termos o gerenciamento de acesso e identidade.
        - Com o OAuth 2.0 podemos permitir que nossa api conceda permissão.
        - Com o OpenID Connect adicionamos uma camada de autenticação, verificando a intentidade do usuário
        - JWT para empacotar os dados tanto as permissões de acesso quanto as informações de identidade.
- Rascunho endpoints iniciais:
    - `GET /v1/news`: Lista todas as notícias (filtros como `category`, `date`)
    - `GET /v1/news/{id}`: Busca uma notícia específica
    - `POST /v1/news`*(admin)*: Publica uma nova notícia
    - `PUT /v1/news/{id}` *(admin)*: Atualiza uma notícia existente
    - `DELETE /v1/news/{id}` *(admin)*: Remove uma notícia
    - `GET /v1/search?q=term`: Pesquisa por termo livre em título/conteúdo
- Diagrama (Rascunho):

```markup
+------------------+          +---------------------+
| Cliente (App/Web)| <------> |  API Gateway (NGINX)|
+------------------+          +---------------------+
		                                    |
		                                    v
		                          +---------------------+
		                          |  Backend REST API   |
		                          |  (NestJS/Express)   |
		                          +----------+----------+
		                                     |
		                                     v
		                           +-------------------+
		                           |     TypeORM       |
		                           +-------------------+
		                                     |
		                                     v
		                           +-------------------+
		                           |  Banco de Dados   |
		                           | (PostgreSQL DB)   |
		                           +-------------------+
	
[Autenticação via OAuth2 / OpenID Connect]
 +---------------------------+
 |         IDP da Empresa    |
 +---------------------------+

```