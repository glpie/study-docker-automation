# Product Service

Microserviço Flask responsável pelo catálogo da hamburgueria. Mantém produtos e categorias na coleção `products` do banco compartilhado `burguer_app_db`, oferece páginas HTML de consulta e administração e disponibiliza endpoints JSON para integração.

Quando a coleção está vazia, o serviço cria automaticamente um catálogo inicial de hambúrgueres e bebidas.

## Funcionalidades

- listagem de produtos disponíveis;
- filtro por categoria;
- consulta de detalhes;
- criação, edição e exclusão de produtos;
- controle de disponibilidade;
- API JSON de produtos e categorias.

## Rotas

| Método | Rota | Descrição |
| --- | --- | --- |
| `GET` | `/` | Exibe um atalho para o catálogo |
| `GET` | `/product/list` | Lista produtos disponíveis e permite filtrar por categoria |
| `GET` | `/product/admin` | Lista todos os produtos para administração |
| `GET`, `POST` | `/product/create` | Exibe o formulário ou cria um produto |
| `GET`, `POST` | `/product/edit/<product_id>` | Exibe o formulário ou atualiza um produto |
| `POST` | `/product/delete/<product_id>` | Exclui um produto |
| `GET` | `/product/details/<product_id>` | Exibe os detalhes do produto |
| `GET` | `/product/api/products` | Retorna produtos disponíveis em JSON; aceita `?category=...` |
| `GET` | `/product/api/categories` | Retorna as categorias em JSON |

O serviço fica disponível em <http://localhost:5003>.

Exemplos da API:

```bash
curl http://localhost:5003/product/api/products
curl 'http://localhost:5003/product/api/products?category=Hamb%C3%BArgueres'
curl http://localhost:5003/product/api/categories
```

Use exatamente o nome de categoria retornado pela API, incluindo acentos.

## Variáveis de ambiente

Crie ou ajuste `.env` neste diretório:

```dotenv
MONGO_URI=mongodb://appuser:apppass@localhost:27017/burguer_app_db?authSource=burguer_app_db
SECRET_KEY=troque-por-um-valor-aleatorio
JWT_SECRET=troque-por-outro-valor-aleatorio
PORT=5003
appName=product-service
```

Não versione segredos reais.

## Executar localmente

Na raiz do repositório:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r burguer-app/product-service/requirements.txt
cd burguer-app/product-service
python app.py
```

É necessário que o MongoDB indicado em `MONGO_URI` esteja acessível. A inicialização do catálogo acessa o banco assim que o controller é carregado.

## Executar com Docker

O modo recomendado para subir o conjunto completo está no [README geral](../../README.md). Para construir e iniciar somente este serviço, com o MongoDB do Compose:

```bash
cd burguer-app
export MONGO_URI='mongodb://appuser:apppass@mongo:27017/burguer_app_db?authSource=burguer_app_db'
docker compose up --build product-service mongo
```

## Testes

Com o ambiente virtual ativo:

```bash
cd burguer-app/product-service
python -m pytest
```

Como o controller inicializa produtos ao ser importado, testes que importem esse módulo precisam de MongoDB disponível ou de mocks configurados antes da importação.

Para gerar o relatório de cobertura HTML:

```bash
python -m pytest --cov-report=html
```

## Estrutura

```text
product-service/
├── app.py
├── config/database.py
├── controllers/product_controller.py
├── models/product_model.py
├── services/product_service.py
├── templates/
└── test/
```
