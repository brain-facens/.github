[<- Inicio](/profile/README.md)
# ⚙️ Estrutura de API
Documento que instrui como organizar projetos de IA em APIs.


## Sumário
- [*Arquivos e Pastas*](#arquivos-e-pastas)
- [*Padrões de Rotas*](#padrões-de-rotas)
- [*Testes*](#testes)
- [*Orientações*](#orientações)


## [Arquivos e Pastas](#sumário)
Segue abaixo uma visão geral de estrutura de arquivos de pastas de um projeto de IA com o uso de API.
```
project
├──.github
|   └──workflows/
|       ├──structure.yaml
|       └──api.yaml
├──src
|   ├──configs/
|   ├──routes/
|   ├──schemas/
|   ├──models/
|   ├──utils/
|   └──main.py
├──docs/
├──requirements
|   ├──dev.txt
|   ├──prod.txt
|   └──base.txt
├──tests
|   ├──utils/
|   ├──auth/
|   └──routes/
├──develop/
├──.env/
├──Dockerfile
├──.gitignore
└──.dockerignore
```
|Arquivo/Pasta|Descrição|
|-|-|
|.github/|Possui os *action* que serão executados após o uso dos comandos *push* ou *pull-request*.|
|.github/workflows/structure.yaml|Comandos que verificaram a organização e padronização de código feito no projeto, um exemplo prático é a utilização do [Pylint](https://github.com/pylint-dev/pylint) para verificar o código seguindo os padrões do Google, dando uma pontuação de quão bem organizado está seu código, caso ele não atinga a pontuação máxima o *push* ou *pull-request* é negado.|
|.github/workflows/api.yaml|Roda todos os testes criados para o projeto, certificando que tudo está funcionando para então permitir tanto o *push* quanto um *pull-request*.|
|src/|Todo o código referente a API como as configurações da API.|
|src/routes/|Código com as rotas da API.|
|src/schemas/|Código com os esquemas de *Requests* e *Response* das rotas.|
|src/models/|Código com os modelos e estrutura do banco de dados.|
|src/utils/|Funções extras para serem utilizadas na API.|
|src/main.py|Script principal que executa o seviço.|
|docs/|Documentação do projeto que será mostrada no seu repositório.|
|requirements/|Arquivos de dependências para o projeto.|
|requirements/base.txt|Bibliotécas bases para o projeto.|
|requirements/dev.txt|Bibliotécas de desenvolvimento.|
|requirements/prod.txt|Bibliotécas usadas em produção.|
|tests/|Testes unitários para verificar o código desenvolvido e certificar de que ele está funcionando.|
|tests/utils/|Testes referentes as funções extras do projeto.|
|tests/auth/|Testes referentes a autenticação de usuário.|
|tests/routes/|Testes referentes ao uso das rotas.|
|develop/|Código de desensolvimento do projeto, isso inclui notebooks, tratamento e visualização de dados, treinamento e teste de modelos entre outros.|
|.env/|Ambiente virtual para trabalhar no projeto.|
|Dockerfile|Arquivo Docker usado para construir uma imagem da API para ser executada em um servidor em Cloud no futuro.|
|.gitignore|Arquivo com todos os arquivos e pastas que devem ser ignorados pelo *git*|
|.dockerignore|Arquivo com todos os arquivos e pastas que devem ser ignorados pelo Docker.|

## [Padrões de Rotas](#sumário)
Cada projeto é único, falando em questão das rotas *(url)* de uma API, cada projeto poderá ter rotas exclusivas que façam sentido para o seu uso final, porém, algumas delas serão frequentemente usadas independente do que o projeto se trate. Segue abaixo algumas rotas obrigatórias que deveram existir em cada projeto e como defini-lás.

#### Endpoint
Todos os endpoints deveram começar com o nome do projeto seguido de sua versão como mostra o exemplo abaixo.
```
http://127.0.0.1:8000/tumor-detector/v2
http://127.0.0.1:8000/field-vison/v1
http://127.0.0.1:8000/helmet-detector/v1.3
...
```
Para isso, é possível definir um *prefix* para cada rota utilizando **FastAPI**. Segue o exemplo abaixo:
```
from fastapi import FastAPI, APIRouter
import uvicorn

router_1 = APIRouter(prefix="/rota_1")
@router_1.get(path="/")
async def root() -> str:
    return "Rota 1"

router_2 = APIRouter(prefix="/rota_2")
@router_2.get(path="/")
async def root() -> str:
    return "Rota 2"

app = FastAPI()
app.include_router(router=router_1, prefix="/app/v1")
app.include_router(router=router_2, prefix="/app/v1")

uvicorn.run(app=app, host="127.0.0.1", port=8000)
```
Ao rodar o código acima e acessar http://127.0.0.1:8000/docs, irá ser mostrado as endpoints:
- (GET) /app/v1/rota_1
- (GET) /app/v1/rota_2

As principais rotas que todo o serviço de IA deverá ter são:

#### Autenticação (Auth)
|Rota|Método|Descrição|
|-|-|-|
|/auth/login|POST|Rota destinada para a autenticação do usuário para utilizar o serviço.|

#### Usuário (Users)
|Rota|Método|Descrição|
|-|-|-|
|/users|GET|Rota que retorna todos os usuários cadastrados no serviço.|
|/users|POST|Rota para a criação de um novo usuário para o serviço.|
|/users/{id}|DELETE|Rota para a deleção de um usuário do serviço.|
|/users/me|GET|Rota que retorna todas as informações do usuário autenticado.|

#### Inferência (Inference)
|Rota|Método|Descrição|
|-|-|-|
|/inference|POST|Rota raiz para todo tipo de inferência realizada.|

#### Informações (Docs)
|Rota|Método|Descrição|
|-|-|-|
|/docs|GET|Rota que retorna todas as informações do projeto.|
|/model/docs|GET|Rota que retorna todas as informações do modelo/algoritmo usado.|

## [Testes](#sumário)
Os testes servem para certificar de que todo o código está funcionando, essa etapa é recomendada que seja automatizada com o **.github/workflows** após um *push* para a *branch* remota.

Cada rota deverá possuir obrigatóriamente um ou mais testes para ela, e toda função criada em **utils/** que vá ser chamada por alguma rota durante um *Request* também deverá possuir seus testes.

## [Orientações](#sumário)
- Antes de começar pra valer qualquer projeto, defina a ferramenta de teste *(Unittest, Pytest, ..)* para testar seu projeto.
- Antes de começar a programar, monte o *actions* que serão rodados após os comandos *push* ou *pull-request*. Evite aceitar qualquer tipo de código no repositório nas branchs de desenvolvimento e principalmente a de produção sem garantir de que ele esteja funcionando corretamente.
- Começe primeiro com a autenticação do usuário e certifique que ela esteja funcionando corretamente, já que as demais rotas só poderam ser acessadas se o usuário for primeiramente autenticado para o seu uso.
- Ao definir uma nova rota ou função, planeje logo em seguida testes que verificaram sua funcionalidade. É interessante testar diferentes dados de entrada e cenários para eles, afim de garantir de que tudo está funcionando corretamente até nas situações mais inesperadas.
