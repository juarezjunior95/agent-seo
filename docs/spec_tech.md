# Especificação técnica (spec_tech.md) - Júllia e Marcos
## Visão Geral Técnica
Este documento define a arquitetura e as diretrizes técnicas para o desenvolvimento de um Agente de IA Generativa especializado em SEO para o mercado imobiliário.


## Público-alvo técnico deste documento
Engenheiros de software
Arquitetos de solução
Engenheiros de dados/IA
DevOps / SRE

## Arquitetura de Referência
Arquitetura baseada em micro serviços leves + orquestração de workflows
Uso de event-driven architecture para processamento assíncrono de geração de conteúdo

**Message Broker:** AWS SQS + SNS (escolha para MVP)
- **Justificativa:** managed service, baixo custo operacional, integração nativa com AWS
- **Alternativas avaliadas:** RabbitMQ (mais complexo para gerenciar), Kafka (overengineering para escala inicial)
- **Filas principais:**
  - `content-generation-queue`: jobs de geração de conteúdo
  - `enrichment-queue`: jobs de enriquecimento com dados locais
  - `dead-letter-queue`: jobs falhos para análise

Camada de AI orchestration (LLM pipeline) desacoplada
## Componentes principais

### Frontend (Portal do Especialista SEO)
Interface para criação de briefs
Gestão de conteúdos gerados
Aprovação/edição humana
### API Gateway / Backend for Frontend (BFF)
Centraliza autenticação, autorização e roteamento
Orquestra chamadas para serviços internos
### Content Generation Service
Serviço principal de orquestração de geração de conteúdo
Gerenciamento de prompts, contexto e templates

**Parâmetros de geração configuráveis:**
- `min_words`: mínimo de palavras (default: 800, range: 500-2000)
- `max_words`: máximo de palavras (default: 1500, range: 800-3000)
- `language`: idioma do conteúdo (default: pt-BR)
- `tone`: tom de voz (formal, informal, técnico)
- `include_faqs`: incluir FAQs (default: true)
- `include_local_data`: enriquecer com dados locais (default: true)

### AI Orchestration Layer
Integra com provedores de LLM
---
### SEO Optimization Engine
Ajustes automáticos: Densidade de palavras-chave, estrutura semântica (H1, H2, etc.), entidades relacionadas (NER)

**Implementação técnica:**
- **Densidade de keywords:** regex pattern matching + contagem TF-IDF (biblioteca: scikit-learn)
- **Estrutura semântica:** validação de hierarquia HTML via BeautifulSoup + regras customizadas
- **NER (Named Entity Recognition):** spaCy com modelo pt_core_news_lg para entidades em português
- **Legibilidade:** índice Flesch-Kincaid adaptado para português (textstat library)
- **Score SEO:** algoritmo ponderado (keyword density 25%, estrutura 25%, NER 20%, legibilidade 15%, meta tags 15%)
---
### Observabilidade
Métricas: Tempo de geração por conteúdo, custo por geração, taxa de erro de LLM

**Métricas expandidas:**
- **Latência:** P50, P95, P99 por endpoint e por tipo de geração
- **Error rate:** por tipo de erro (timeout, rate_limit, invalid_response, content_filter)
- **Cost tracking:** custo por tenant, por projeto, por tipo de conteúdo (tokens consumidos)
- **Throughput:** jobs/minuto, conteúdos/hora por tenant
- **Queue health:** profundidade da fila, tempo médio na fila, DLQ volume
- **Business metrics:** conteúdos aprovados vs rejeitados, score SEO médio
---
### Autenticação e Autorização
OAuth 2.0 / OpenID Connect
RBAC (Role-Based Acess Control)
---
### Protocolos de Comunicação
HTTP/Rest (principal)
Webhooks (integrações externas)
Mensageria (filas)
---
### Infraestrutura de Deployment
Cloud (Azure)
Container (Docker)
--- 
## Stack Tecnológica

### FrontEnd
- Linguagem: TypeScript
- Framework web: React (Next.js)
- Estilização: TailwindCSS

### BackEnd
- Linguagem: Python
- Runtime: Python 3.11+
- Framework: **FastAPI** (decisão final MVP)
  - **Justificativa:** ecossistema Python superior para IA/ML, integração nativa com LangChain/LlamaIndex, tipagem com Pydantic, async nativo
  - **Alternativa descartada:** NestJS (Node) - exigiria bridge Python para pipelines de IA
- Persistência: PostgreSQL + Redis (cache)
- ORM: SQLAlchemy 2.0 + Alembic (migrações)
--- 
## Stack de Desenvolvimento
- IDE: VS Code
- Gerenciamento de pacotes: npm / pnpm / pip
- Ambiente de desenvolvimento local: Docker Compose
- Infraestrutura como Código (IaC): Terraform
- Pipeline CI/CD: GitHub Actions / GitLab CI
--- 
## Integrações

### Persistência: PostgreSQL (dados estruturados), S3 (conteúdos)
- Deployment: AWS (ECS / EKS / Lambda)
- Segurança (autenticação e autorização): Auth0 / Cognito
- Observabilidade: Datadog / Prometheus + Grafana

### Segurança (Autenticação e Gestão de Sessão)
- Tokens JWT com expiração curta
- Refresh tokens seguros
- Proteção contra session hijacking

### Controle de Acesso e Autorização
- RBAC com perfis: admin, especialista, editor

### Segurança de Dados e Validação
- Validação de input em todas as APIs
- Proteção contra prompt injection

### Criptografia e proteção dos dados
- Dados sensíveis criptografados em repouso (AES-256)

### Segurança da Infraestrutura e Configuração
- Secrets via GitHub secrets
- Network isolation (VPC)
- Rate limiting e proteção contra abuso de API

### Segurança no Desenvolvimento e Operação (DevSecOps)
- SAST e DAST no pipeline
- Scan de dependências
- Monitoramento contínuo de vulnerabilidades

### APIs (Endpoint Principal)
- /api/v1/content/generate
- /api/v1/content/{id}
- /api/v1/briefs
- /api/v1/projects

### Versionamento
- Versionamento via URI (/v1/)
- Compatibilidade retroativa garantida

**Estratégia de Backward Compatibility:**
- Novos campos em responses são sempre opcionais (clients devem ignorar campos desconhecidos)
- Campos deprecados mantidos por 6 meses com header `Deprecation: true`
- Breaking changes apenas em major versions (v1 → v2)
- Changelog público em `/api/changelog`
- **Exemplos de versionamento:**
  - `GET /api/v1/content/123` (versão atual)
  - `GET /api/v2/content/123` (futura, com schema modificado)
  - Header `X-API-Version: 2024-03-19` para sunset date

## Padrão de Nomenclatura
- RESTful
- Recursos no plural
- Verbos evitados nos endpoints

### Autenticação
- Bearer Token (JWT)

### Endpoints Públicos e Protegidos
- Health check (público)
- Geração de conteúdo (protegidos)
- Gestão de projetos (protegidos)
- Acesso a dados enriquecidos (protegidos)

### Tenancy (Estrategia)
- Multi-tenant (SaaS)

### Tenancy (Isolamento)
- Logical isolation por tenant_id
- Possibilidade futura de isolamento físico

### Tenancy (Identificação)
- Cada requisição contém tenant_id
- Associado ao token JWT

### Tenancy (Migrações)
- Migrações versionadas por tenant
- Estratégia backward compatible

### Tenancy (Segurança)
- Isolamento de dados garantido via políticas no banco
- Row-Level Security (RLS) no PostgreSQL

**Implementação RLS:**
```sql
-- Habilitar RLS na tabela
ALTER TABLE contents ENABLE ROW LEVEL SECURITY;

-- Policy para SELECT
CREATE POLICY tenant_isolation_select ON contents
  FOR SELECT USING (tenant_id = current_setting('app.current_tenant')::uuid);

-- Policy para INSERT/UPDATE/DELETE
CREATE POLICY tenant_isolation_write ON contents
  FOR ALL USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

- **Aplicação:** middleware FastAPI seta `app.current_tenant` em cada request
- **Tabelas com RLS:** contents, projects, briefs, users, audit_logs
- **Bypass para admin:** role `superadmin` com `BYPASSRLS`




















