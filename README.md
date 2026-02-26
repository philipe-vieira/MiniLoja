# MiniLoja

## 📋 Sobre o Projeto

MiniLoja é uma aplicação simples desenvolvida como teste técnico, demonstrando a implementação de um sistema de gerenciamento de produtos e categorias. O projeto utiliza uma arquitetura moderna com backend em **NestJS** e frontend em **Next.js**, ambos containerizados com Docker para facilitar o desenvolvimento e deployment.

---

## 🚀 Como Executar

### Pré-requisitos

- Docker e Docker Compose instalados
- Node.js 20+ (apenas se executar sem Docker)
- Git

### Passos para Execução

1. **Clone o repositório**
```bash
git clone https://github.com/philipe-vieira/MiniLoja.git
cd miniloja
git submodule update --init --recursive 
```

2. **Configure as variáveis de ambiente**

Crie um arquivo `.env` na raiz do projeto com as seguintes variáveis:

```env
# Database
POSTGRES_DB=miniloja
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_PORT=5432
DATABASE_URL=postgresql://postgres:postgres@db:5432/miniloja?schema=public

# Redis
REDIS_PORT=6379
REDIS_URL=redis://cache:6379

# Backend
BACK_NODE_ENV=development
BACK_PORT=3000
BACK_INTERNAL_PORT=3000
LOG_LEVEL=debug
LOG_ENABLED_LEVELS=log,error,warn,debug
LOG_CONSOLE_ENABLED=true
LOG_FILE_ENABLED=false
LOG_FILE_PATH=./logs
LOG_TIMESTAMP_ENABLED=true
LOG_JSON_ENABLED=false

# Swagger
SWAGGER_ENABLED=true
SWAGGER_PATH=api
SWAGGER_TITLE=MiniLoja API
SWAGGER_DESCRIPTION=API de gerenciamento de produtos e categorias
SWAGGER_VERSION=1.0
SWAGGER_SERVERS=http://localhost:3000

# Frontend
FRONT_NODE_ENV=development
FRONT_PORT=3001
FRONT_INTERNAL_PORT=3001
NEXT_PUBLIC_API_URL=http://localhost:3000
```

3. **Inicie os containers**
```bash
docker compose -f docker-compose.dev.yml up -d
```

4. **Execute as migrations do banco de dados**
```bash
docker exec -it miniloja-back-dev npx prisma migrate deploy
```

5. **Acesse as aplicações**

Via Proxy (nginx)
- Frontend: http://localhost:8080
- Backend: http://localhost:8080/api
- Swagger (Documentação da API): http://localhost:8080/api/docs

Direto
- Frontend: http://localhost:3000
- Backend: http://localhost:3001
- Swagger (Documentação da API): http://localhost:3001/docs
- PostgreSQL: localhost:5432
- Redis: localhost:6379

---

## 🛠️ Comandos Úteis

### Docker

```bash
# Iniciar todos os serviços
docker-compose -f docker-compose.dev.yml up -d

# Parar todos os serviços
docker-compose -f docker-compose.dev.yml down

# Ver logs de todos os serviços
docker-compose -f docker-compose.dev.yml logs -f

# Ver logs de um serviço específico
docker-compose -f docker-compose.dev.yml logs -f back
docker-compose -f docker-compose.dev.yml logs -f front

# Reiniciar um serviço
docker-compose -f docker-compose.dev.yml restart back
docker-compose -f docker-compose.dev.yml restart front

# Remover volumes (limpar dados)
docker-compose -f docker-compose.dev.yml down -v
```

### Backend

```bash
# Acessar o container do backend
docker exec -it miniloja-back-dev sh

# Instalar dependências
docker exec -it miniloja-back-dev npm install

# Executar migrations
docker exec -it miniloja-back-dev npx prisma migrate deploy

# Criar nova migration
docker exec -it miniloja-back-dev npx prisma migrate dev --name migration_name

# Abrir Prisma Studio (GUI do banco)
docker exec -it miniloja-back-dev npx prisma studio

# Executar testes unitários
docker exec -it miniloja-back-dev npm test

# Executar testes com cobertura
docker exec -it miniloja-back-dev npm run test:cov

# Executar testes e2e
docker exec -it miniloja-back-dev npm run test:e2e

# Gerar cliente Prisma
docker exec -it miniloja-back-dev npx prisma generate
```

### Frontend

```bash
# Acessar o container do frontend
docker exec -it miniloja-front-dev sh

# Instalar dependências
docker exec -it miniloja-front-dev npm install

# Build de produção
docker exec -it miniloja-front-dev npm run build
```

### Desenvolvimento Local (sem Docker)

```bash
# Backend
cd backend
npm install
npx prisma generate
npx prisma migrate deploy
npm run start:dev

# Frontend
cd frontend
npm install
npm run dev
```

---

## 📚 Documentação da API

A documentação completa da API está disponível via Swagger em: http://localhost:8080/api/docs (ou http://localhost:3001/docs) 

### Rotas Disponíveis

#### Health Check

- **GET** `/hello` - Verifica se a API está online e funcionando

#### Categorias (`/category`)

- **POST** `/category` 
  - Cria uma nova categoria
  - Body: `{ "name": "Nome da Categoria" }`

- **GET** `/category`
  - Lista todas as categorias com paginação e filtros
  - Query params opcionais:
    - `page`, `limit` - Paginação
    - `get_all` - Retorna todos os registros sem paginação
    - `sort_by` - Ordenação (ex: `name:desc`, `createdAt:asc`)
    - `name` - Filtro por nome parcial
    - `id` - Filtro por ID exato
    - `createdAt_gte`, `createdAt_lte`, `createdAt_between` - Filtros de data
    - `updatedAt_gte`, `updatedAt_lte`, `updatedAt_between` - Filtros de data

- **GET** `/category/:id`
  - Busca uma categoria específica por ID

- **PATCH** `/category/:id`
  - Atualiza uma categoria existente
  - Body: `{ "name": "Novo Nome" }`

- **DELETE** `/category/:id`
  - Remove uma categoria por ID

#### Produtos (`/product`)

- **POST** `/product`
  - Cria um novo produto
  - Body: 
  ```json
  {
    "name": "Nome do Produto",
    "description": "Descrição opcional",
    "price": 99.90,
    "categoryId": 1
  }
  ```

- **GET** `/product`
  - Lista todos os produtos com paginação e filtros
  - Query params opcionais:
    - `page`, `limit` - Paginação
    - `get_all` - Retorna todos os registros sem paginação
    - `sort_by` - Ordenação (ex: `price:desc`, `name:asc`)
    - `name` - Filtro por nome parcial
    - `description` - Filtro por descrição parcial
    - `categoryId` - Filtro por categoria
    - `price`, `price_gte`, `price_lte`, `price_between` - Filtros de preço
    - `createdAt_gte`, `createdAt_lte`, `createdAt_between` - Filtros de data
    - `updatedAt_gte`, `updatedAt_lte`, `updatedAt_between` - Filtros de data

- **GET** `/product/:id`
  - Busca um produto específico por ID

- **PATCH** `/product/:id`
  - Atualiza um produto existente
  - Body: 
  ```json
  {
    "name": "Novo Nome",
    "description": "Nova Descrição",
    "price": 149.90,
    "categoryId": 2
  }
  ```

- **DELETE** `/product/:id`
  - Remove um produto por ID

---

## 🏗️ Arquitetura e Tecnologias

### Arquitetura Geral

O projeto segue uma arquitetura **monorepo** com separação clara entre frontend e backend, utilizando containers Docker para isolamento e facilitação do ambiente de desenvolvimento.

```
miniloja/
├── backend/          # API REST em NestJS
├── frontend/         # Interface em Next.js
└── infra/           # Configurações de infraestrutura
```

### Backend (NestJS)

**Arquitetura**: Modular com estrutura orientada a domínios

- **Framework**: NestJS 11
- **ORM**: Prisma 7.4
- **Banco de Dados**: PostgreSQL 17
- **Cache**: Redis 7
- **Documentação**: Swagger/OpenAPI
- **Testes**: Jest (unitários e e2e)
- **Logging**: Sistema customizado com interceptors
- **Validação**: Class Validator e DTO patterns

**Principais Funcionalidades**:
- Sistema de auditoria automática (AuditLog) para todas as operações CRUD
- Cache de queries com Redis
- Sistema de logging estruturado com níveis configuráveis
- Query parser avançado para filtros e ordenação
- Validação robusta de DTOs
- Interceptors para logging HTTP

**Estrutura por Módulo**:
- `category/` - Gerenciamento de categorias
- `product/` - Gerenciamento de produtos
- `prisma/` - Configuração Prisma + auditoria
- `logger/` - Sistema de logs customizado
- `utils/` - Utilitários compartilhados

### Frontend (Next.js)

**Arquitetura**: App Router com componentes React Server e Client

- **Framework**: Next.js 16
- **UI Library**: React 19
- **Estilização**: Tailwind CSS 4
- **Componentes**: Lucide React (ícones)
- **Tabelas**: TanStack Table v8
- **Requisições HTTP**: Axios
- **Notificações**: React Hot Toast

**Principais Funcionalidades**:
- Interface responsiva para gerenciamento de produtos e categorias
- Tabelas com ordenação e paginação
- Modais para criação e edição
- Sistema de notificações toast
- Hooks customizados para gerenciamento de estado

### Infraestrutura

**Docker Compose**: Orquestração de 4 serviços
- `db` - PostgreSQL com persistência
- `cache` - Redis com persistência
- `back` - Backend NestJS com hot-reload
- `front` - Frontend Next.js com hot-reload

**Health Checks**: Todos os serviços possuem health checks configurados para garantir inicialização adequada

**Networking**: Rede bridge customizada para comunicação entre containers

**Volumes**: Persistência de dados para PostgreSQL e Redis + volume para node_modules

---

## 🎯 Pontos Importantes

### Qualidade e Boas Práticas

- ✅ **Commits Assinados e Verificados**: Todos os commits são assinados com GPG e verificados no GitHub, garantindo autenticidade e integridade do código

- ✅ **Testes Automatizados**: Cobertura de testes unitários e e2e implementados no backend

- ✅ **Documentação Swagger**: API completamente documentada com exemplos de requisições e respostas

- ✅ **Type Safety**: TypeScript em 100% do código frontend e backend

- ✅ **Validação de Dados**: Validação rigorosa em todas as entradas da API com Class Validator

- ✅ **Sistema de Auditoria**: Todas as operações CRUD são auditadas automaticamente no banco de dados

- ✅ **Cache Inteligente**: Sistema de cache com Redis para otimização de performance

- ✅ **Logs Estruturados**: Sistema de logging customizado com níveis configuráveis e interceptors HTTP

- ✅ **Health Checks**: Endpoints e verificações de saúde para todos os serviços

- ✅ **Git Ignore**: Configurações adequadas para não versionar arquivos sensíveis ou desnecessários

- ✅ **Environment Variables**: Separação clara de configurações por ambiente

- ✅ **Hot Reload**: Ambiente de desenvolvimento com recarregamento automático para máxima produtividade

### Arquitetura de Código

- **SOLID Principles**: Código segue princípios SOLID para manutenibilidade
- **Repository Pattern**: Separação clara entre lógica de negócio e acesso a dados
- **Dependency Injection**: Utilização extensiva de DI do NestJS
- **DTOs**: Data Transfer Objects para todas as operações
- **Interceptors**: Logging e auditoria via interceptors do NestJS
- **Modularização**: Código organizado em módulos coesos e com baixo acoplamento

### Performance e Escalabilidade

- **Cache Redis**: Redução de carga no banco de dados
- **Connection Pooling**: Configuração adequada do Prisma para pool de conexões
- **Indexes**: Indexes apropriados no banco de dados
- **Paginação**: Todas as listagens suportam paginação
- **Containerização**: Facilita deploy e scaling horizontal

---

## 📝 Licença

UNLICENSED - Projeto desenvolvido para fins de avaliação técnica.

