# Burguer App

Aplicação didática de uma hamburgueria organizada em quatro microserviços Flask. Os serviços compartilham um banco MongoDB e podem ser executados em conjunto com Docker Compose ou separadamente durante o desenvolvimento.

## Arquitetura

| Serviço | Responsabilidade | Porta | Documentação |
| --- | --- | ---: | --- |
| Auth Service | Login, logout, sessão e geração de JWT | `5000` | [README](burguer-app/auth-service/README.md) |
| User Service | Cadastro e manutenção de usuários | `5001` | [README](burguer-app/user-service/README.md) |
| Order Service | Criação e acompanhamento de pedidos | `5002` | [README](burguer-app/order-service/README.md) |
| Product Service | Catálogo, categorias e administração de produtos | `5003` | [README](burguer-app/product-service/README.md) |
| MongoDB | Persistência compartilhada no banco `burguer_app_db` | `27017` | — |

O projeto usa as coleções `users`, `orders` e `products`. O Auth Service consulta os usuários cadastrados pelo User Service, enquanto o Order Service valida usuários e consulta o catálogo do Product Service. Ao iniciar com uma coleção de produtos vazia, o Product Service cadastra um catálogo inicial.

Principais tecnologias: Python 3.11, Flask, Jinja2, PyMongo, MongoDB 6, pytest, pytest-cov, Docker e Docker Compose.

## Estrutura do projeto

```text
.
├── README.md
└── burguer-app/
    ├── docker-compose.yml
    ├── docker-compose.override.yml
    ├── mongo/
    ├── auth-service/
    ├── user-service/
    ├── order-service/
    └── product-service/
```

Cada microserviço segue a separação em `controllers`, `services`, `models`, `config`, `templates` e `test`.

## Pré-requisitos

Para executar toda a aplicação com contêineres:

- Docker Engine ou Docker Desktop;
- Docker Compose v2 (`docker compose`).

Para executar os serviços diretamente no host:

- Python 3.11;
- `pip` e suporte à criação de ambientes virtuais;
- uma instância do MongoDB acessível.

## Configuração

Os quatro serviços usam as mesmas variáveis em seus respectivos arquivos `.env`:

| Variável | Finalidade |
| --- | --- |
| `MONGO_URI` | String de conexão com o MongoDB |
| `SECRET_KEY` | Assinatura da sessão Flask |
| `JWT_SECRET` | Assinatura dos tokens gerados pelo Auth Service |
| `PORT` | Porta de referência do serviço |
| `appName` | Nome de identificação da aplicação |

Não versione valores reais de segredo. Para desenvolvimento, gere valores aleatórios para `SECRET_KEY` e `JWT_SECRET`.

## Executar com Docker Compose

A partir da raiz do repositório, defina a conexão interna com o MongoDB do Compose e suba a aplicação:

```bash
cd burguer-app
export MONGO_URI='mongodb://appuser:apppass@mongo:27017/burguer_app_db?authSource=burguer_app_db'
docker compose up --build
```

O `docker-compose.override.yml` é carregado automaticamente, cria o MongoDB local e repassa `MONGO_URI` aos serviços.

Depois da inicialização, acesse:

- autenticação: <http://localhost:5000/auth/login>;
- usuários: <http://localhost:5001/user/create>;
- pedidos: <http://localhost:5002/order/list>;
- produtos: <http://localhost:5003/product/list>;
- administração de produtos: <http://localhost:5003/product/admin>.

Para acompanhar os contêineres ou encerrar o ambiente:

```bash
docker compose ps
docker compose logs -f
docker compose down
```

O comando `docker compose down` preserva os dados no volume `mongo_data`. Para também remover os dados locais, use `docker compose down -v`.

> O Order Service consulta o Product Service por uma URL `localhost` definida no código. Essa integração funciona ao executar ambos diretamente no host. Dentro do Compose, o endereço entre contêineres deve ser `http://product-service:5003` para que produtos e categorias apareçam na criação do pedido.

## Executar diretamente no host

Crie um único ambiente virtual na raiz e instale as dependências. Os quatro arquivos `requirements.txt` têm atualmente o mesmo conjunto de pacotes, portanto basta instalar um deles:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r burguer-app/auth-service/requirements.txt
```

Se não houver MongoDB instalado, suba somente o banco com Docker:

```bash
cd burguer-app
docker compose up -d mongo
cd ..
```

Configure a URI vista pelo host e os segredos no terminal:

```bash
export MONGO_URI='mongodb://appuser:apppass@localhost:27017/burguer_app_db?authSource=burguer_app_db'
export SECRET_KEY='troque-por-um-valor-aleatorio'
export JWT_SECRET='troque-por-outro-valor-aleatorio'
```

Abra quatro terminais, ative o ambiente virtual em cada um e execute um serviço por terminal:

```bash
source .venv/bin/activate
cd burguer-app/auth-service
python app.py
```

Troque `auth-service` por `user-service`, `order-service` e `product-service` nos demais terminais. Cada aplicação usa a porta definida em seu `app.py`, conforme a tabela de arquitetura.

## Executar os testes

Com o ambiente virtual ativo e as dependências instaladas, execute toda a suíte a partir da raiz:

```bash
for service in auth-service user-service order-service product-service; do
  (cd "burguer-app/$service" && python -m pytest)
done
```

Para testar apenas um serviço:

```bash
cd burguer-app/auth-service
python -m pytest
```

O `pytest.ini` de cada serviço habilita saída detalhada e cobertura de `models`, `controllers`, `services` e `config`. Para gerar também um relatório HTML:

```bash
python -m pytest --cov-report=html
```

O relatório será criado em `htmlcov/index.html` dentro do serviço testado.

## Fluxo básico de uso

1. Cadastre um usuário em <http://localhost:5001/user/create>.
2. Faça login em <http://localhost:5000/auth/login>.
3. Consulte ou administre o catálogo em <http://localhost:5003/product/list>.
4. Crie e acompanhe pedidos em <http://localhost:5002/order/create>.
