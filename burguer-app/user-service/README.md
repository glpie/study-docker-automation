# User Service

Microserviço Flask responsável pelo cadastro e pela manutenção dos usuários da hamburgueria. Persiste os dados na coleção `users` do banco compartilhado `burguer_app_db` e armazena senhas usando hash do Werkzeug.

## Funcionalidades

- cadastro com e-mail, senha, nome, endereço e perfil;
- prevenção de e-mails duplicados;
- consulta de perfil;
- edição de nome e endereço;
- exclusão de usuário;
- redirecionamento para o Auth Service após o cadastro.

## Rotas

| Método | Rota | Descrição |
| --- | --- | --- |
| `GET` | `/` | Exibe um atalho para o cadastro |
| `GET`, `POST` | `/user/create` | Exibe o formulário ou cadastra um usuário |
| `GET` | `/user/profile/<email>` | Exibe o perfil de um usuário |
| `GET`, `POST` | `/user/edit/<email>` | Exibe o formulário ou atualiza o perfil |
| `POST` | `/user/delete/<email>` | Exclui um usuário |

O serviço fica disponível em <http://localhost:5001>.

## Variáveis de ambiente

Crie ou ajuste `.env` neste diretório:

```dotenv
MONGO_URI=mongodb://appuser:apppass@localhost:27017/burguer_app_db?authSource=burguer_app_db
SECRET_KEY=troque-por-um-valor-aleatorio
JWT_SECRET=troque-por-outro-valor-aleatorio
PORT=5001
appName=user-service
```

`SECRET_KEY` é usada pelas mensagens de sessão do Flask. Não versione segredos reais.

## Executar localmente

Na raiz do repositório:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r burguer-app/user-service/requirements.txt
cd burguer-app/user-service
python app.py
```

É necessário que o MongoDB indicado em `MONGO_URI` esteja acessível.

## Executar com Docker

O modo recomendado para subir o conjunto completo está no [README geral](../../README.md). Para construir e iniciar somente este serviço, com o MongoDB do Compose:

```bash
cd burguer-app
export MONGO_URI='mongodb://appuser:apppass@mongo:27017/burguer_app_db?authSource=burguer_app_db'
docker compose up --build user-service mongo
```

## Testes

Com o ambiente virtual ativo:

```bash
cd burguer-app/user-service
python -m pytest
```

Para gerar o relatório de cobertura HTML:

```bash
python -m pytest --cov-report=html
```

## Estrutura

```text
user-service/
├── app.py
├── config/database.py
├── controllers/user_controller.py
├── models/user_model.py
├── services/user_service.py
├── templates/
└── test/
```
