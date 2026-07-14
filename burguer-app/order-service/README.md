# Order Service

Microserviço Flask responsável pela criação e pelo acompanhamento dos pedidos. Usa as coleções `orders` e `users` do banco compartilhado `burguer_app_db` e consulta o Product Service para preencher produtos e categorias no formulário.

## Funcionalidades

- criação de pedidos para usuários cadastrados;
- cálculo do total a partir dos itens, quantidades e preços;
- listagem geral ou por usuário;
- visualização dos detalhes;
- atualização do status;
- exclusão de pedidos;
- consulta de produtos e categorias no Product Service.

Todo pedido novo recebe o status inicial `pending`.

## Rotas

| Método | Rota | Descrição |
| --- | --- | --- |
| `GET` | `/` | Redireciona para a lista de pedidos |
| `GET`, `POST` | `/order/create` | Exibe o formulário ou cria um pedido |
| `GET` | `/order/list` | Lista todos os pedidos |
| `GET` | `/order/details/<order_id>` | Exibe os detalhes de um pedido |
| `GET` | `/order/user/<user_email>` | Lista os pedidos de um usuário |
| `POST` | `/order/update_status/<order_id>` | Atualiza o status do pedido |
| `POST` | `/order/delete/<order_id>` | Exclui o pedido |

O serviço fica disponível em <http://localhost:5002>.

## Dependências de execução

- MongoDB, para usuários e pedidos;
- User Service, para cadastrar previamente o usuário do pedido;
- Product Service na porta `5003`, para carregar o catálogo no formulário.

Atualmente, a URL do Product Service está definida como `http://localhost:5003` no controller. Ela atende à execução direta no host. Em contêineres, use o hostname `product-service` no código ou torne essa URL configurável por variável de ambiente.

## Variáveis de ambiente

Crie ou ajuste `.env` neste diretório:

```dotenv
MONGO_URI=mongodb://appuser:apppass@localhost:27017/burguer_app_db?authSource=burguer_app_db
SECRET_KEY=troque-por-um-valor-aleatorio
JWT_SECRET=troque-por-outro-valor-aleatorio
PORT=5002
appName=order-service
```

Não versione segredos reais.

## Executar localmente

Na raiz do repositório:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r burguer-app/order-service/requirements.txt
cd burguer-app/order-service
python app.py
```

Mantenha também o Product Service em execução para carregar produtos e categorias.

## Executar com Docker

O modo recomendado para subir o conjunto completo está no [README geral](../../README.md). Para construir o Order Service, o Product Service e o MongoDB:

```bash
cd burguer-app
export MONGO_URI='mongodb://appuser:apppass@mongo:27017/burguer_app_db?authSource=burguer_app_db'
docker compose up --build order-service product-service mongo
```

Consulte a observação acima sobre a URL usada na comunicação entre Order Service e Product Service.

## Testes

Com o ambiente virtual ativo:

```bash
cd burguer-app/order-service
python -m pytest
```

Para gerar o relatório de cobertura HTML:

```bash
python -m pytest --cov-report=html
```

## Estrutura

```text
order-service/
├── app.py
├── config/database.py
├── controllers/order_controller.py
├── models/order_model.py
├── services/order_service.py
├── templates/
└── test/
```
