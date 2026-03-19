# Especificação técnica (spec_tech.md) - Júllia e Marcos

**Documentação relacionada:** [`prd.md`](prd.md) · [`spec_ui.md`](spec_ui.md) · [`revisao_refinamento.md`](revisao_refinamento.md).

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
### AI Orchestration Layer
Integra com provedores de LLM
### SEO Optimization Engine
Ajustes automáticos: Densidade de palavras-chave, estrutura semântica (H1, H2, etc.), entidades relacionadas (NER)

### Observabilidade
Métricas: Tempo de geração por conteúdo, custo por geração, taxa de erro de LLM
### Autenticação e Autorização
OAuth 2.0 / OpenID Connect
RBAC (Role-Based Access Control)
### Protocolos de Comunicação
HTTP/Rest (principal)
Webhooks (integrações externas)
Mensageria (filas)
### Infraestrutura de Deployment
**Cloud primária (decisão alinhada às integrações):** AWS (hospedagem de containers, filas e armazenamento de objetos).  
Container: Docker (imagens para serviços de API e workers).  
*Nota de revisão:* evitar combinação simultânea Azure + AWS na mesma baseline sem decisão arquitetural explícita; demais nuvens permanecem opcionais para migração futura.

## Stack Tecnológica

### FrontEnd
Linguagem: TypeScript  
Framework web: React (Next.js)  
Estilização: TailwindCSS  

### BackEnd (padrão recomendado)
**BFF / camada de API voltada ao portal:** Next.js (Route Handlers ou server actions) *ou* serviço dedicado em Node.js quando necessário — expõe contratos consumidos pelo frontend e repassa ao núcleo de geração.  
**Content Generation Service e workers assíncronos:**  
- Linguagem: **Python 3.11+**  
- Runtime: Python  
- Framework: **FastAPI** (API síncrona quando aplicável) + workers (Celery/RQ ou fila gerenciada) para jobs de lote  
Persistência: PostgreSQL + Redis (cache e filas)  
ORM: **SQLAlchemy** (Python); Prisma apenas se um serviço Node for mantido de fato com persistência própria  

*Alternativa documentada:* stack 100% Node com NestJS para o serviço de geração — exige atualizar este documento e o desenho de filas; não misturar “Python + NestJS” no mesmo runtime sem decomposição clara em serviços.
## Stack de Desenvolvimento
IDE: VS Code
Gerenciamento de pacotes: npm / pnpm / pip
Ambiente de desenvolvimento local: Docker Compose
Infraestrutura como Código (IaC): Terraform
Pipeline CI/CD: GitHub Actions / GitLab CI
## Integrações

### Persistência: PostgreSQL (dados estruturados), S3 (conteúdos)
Deployment: AWS (ECS / EKS / Lambda, conforme carga e modelo operacional)
Segurança (autenticação e autorização): Auth0 / Cognito
Observabilidade: Datadog / Prometheus + Grafana
### Segurança (Autenticação e Gestão de Sessão)
Tokens JWT com expiração curta
Refresh tokens seguros
Proteção contra session hijacking
### Controle de Acesso e Autorização
RBAC com perfis: admin, especialista, editor
### Segurança de Dados e Validação
Validação de input em todas as APIs
Proteção contra prompt injection
### Criptografia e proteção dos dados
Dados sensíveis criptografados em repouso (AES-256)
### Segurança da Infraestrutura e Configuração
Secrets via Vault / Secrets Manager
Network isolation (VPC)
Rate limiting e proteção contra abuso de API
### Segurança no Desenvolvimento e Operação (DevSecOps)
SAST e DAST no pipeline
Scan de dependências
Monitoramento contínuo de vulnerabilidades
### APIs (recursos principais)
`/api/v1/content/generate` — disparo de geração unitária (síncrono ou retorno de job id, conforme implementação)  
`/api/v1/content/{id}` — leitura/atualização de artefato de conteúdo  
`/api/v1/briefs` — CRUD de briefings  
`/api/v1/projects` — CRUD de projetos  
`/api/v1/jobs` ou `/api/v1/batches` — **(v2 / lote)** criação e listagem de jobs de geração em massa; status por item (enfileirado, processando, concluído, erro)  
`/api/v1/jobs/{id}` — detalhe, progresso e relatório de falhas do lote  

Health: `GET /api/v1/health` ou `/health` (público), conforme padrão adotado no gateway.
### Versionamento
Versionamento via URI (/v1/)
Compatibilidade retroativa garantida
## Padrão de Nomenclatura
RESTful
Recursos no plural
Verbos evitados nos endpoints
### Autenticação
Bearer Token (JWT)
### Endpoints Públicos e Protegidos
Health check (público)
Geração de conteúdo (protegidos)
Gestão de projetos (protegidos)
Acesso a dados enriquecidos (protegidos)
### Tenancy (Estrategia)
Multi-tenant (SaaS)
### Tenancy (Isolamento)
Logical isolation por tenant_id
Possibilidade futura de isolamento físico
### Tenancy (Identificação)
Cada requisição contém tenant_id
Associado ao token JWT

### Tenancy (Migrações)
Migrações versionadas por tenant
Estratégia backward compatible
### Tenancy (Segurança)
Isolamento de dados garantido via políticas no banco
Row-Level Security (RLS) no PostgreSQL

---

## Diretrizes para desenvolvimento assistido por IA

1. **Ordem de leitura:** `prd.md` (o quê/por quê) → este documento (como) → `spec_ui.md` (telas e fluxos). Em dúvida de escopo, respeitar a tabela **“Mapeamento RFN × versão”** do PRD.  
2. **Serviços:** gerar código por **fronteira de serviço** (BFF, Content Generation, worker de fila, camada LLM), evitando monólito acoplado ao provedor de modelo.  
3. **Multi-tenant:** toda query e todo DTO persistido deve carregar ou filtrar por `tenant_id`; não expor IDs internos de outros tenants em respostas ou logs.  
4. **LLM:** isolar prompts e templates em módulo versionável; validar e sanitizar entradas do usuário (**prompt injection**); nunca logar segredos nem prompts com PII sem mascaramento.  
5. **APIs:** manter versionamento por prefixo `/v1/`; erros no formato consistente (código, mensagem, detalhes opcionais); paginação em listagens.  
6. **UI:** respeitar identificadores **INT-xx** do `spec_ui.md` ao nomear rotas, testes E2E e componentes de página.  
7. **Mudanças estruturais:** alterações de stack ou de nuvem exigem atualização **neste arquivo** e menção na próxima revisão de refinamento.


