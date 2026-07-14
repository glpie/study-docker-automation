# Auth Service

Microserviço Flask responsável por autenticar usuários, manter a sessão web e gerar um JWT após um login válido. Os dados são lidos da coleção `users` do banco MongoDB compartilhado `burguer_app_db`.

## Funcionalidades

- formulário e processamento de login;
- validação de senha com hash Werkzeug;
- criação de sessão Flask;
- geração de JWT com e-mail e perfil do usuário;
- dashboard protegido por sessão;
- logout;
- redirecionamento para o cadastro do User Service.

## Rotas

| Método | Rota | Descrição |
| --- | --- | --- |
| `GET` | `/` | Redireciona para o login |
| `GET` | `/auth/login` | Exibe o formulário de login |
| `POST` | `/auth/login` | Valida as credenciais e cria a sessão |
| `GET` | `/auth/register` | Redireciona para o User Service |
| `GET` | `/auth/dashboard` | Exibe os dados do usuário autenticado |
| `GET` | `/auth/logout` | Encerra a sessão |

O serviço fica disponível em <http://localhost:5000>.

## Variáveis de ambiente

Crie ou ajuste `.env` neste diretório:

```dotenv
MONGO_URI=mongodb://appuser:apppass@localhost:27017/burguer_app_db?authSource=burguer_app_db
SECRET_KEY=troque-por-um-valor-aleatorio
JWT_SECRET=troque-por-outro-valor-aleatorio
PORT=5000
appName=auth-service
```

`SECRET_KEY` protege a sessão Flask e `JWT_SECRET` assina os tokens. Não versione segredos reais.

## Executar localmente

Na raiz do repositório:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r burguer-app/auth-service/requirements.txt
cd burguer-app/auth-service
python app.py
```

É necessário que o MongoDB indicado em `MONGO_URI` esteja acessível e contenha um usuário criado pelo User Service.

## Executar com Docker

O modo recomendado para subir o conjunto completo está no [README geral](../../README.md). Para construir e iniciar somente este serviço, com o MongoDB do Compose:

```bash
cd burguer-app
export MONGO_URI='mongodb://appuser:apppass@mongo:27017/burguer_app_db?authSource=burguer_app_db'
docker compose up --build auth-service mongo
```

## Testes

Com o ambiente virtual ativo:

```bash
cd burguer-app/auth-service
python -m pytest
```

O `pytest.ini` executa os testes com detalhes e cobertura. Para criar o relatório HTML:

```bash
python -m pytest --cov-report=html
```

## Estrutura

```text
auth-service/
├── app.py
├── config/database.py
├── controllers/auth_controller.py
├── models/user_model.py
├── services/auth_service.py
├── utils/jwt_handler.py
├── templates/
└── test/
```
