# Revisão do refinamento

**Data:** 2026-03-19  
**Escopo:** revisão cruzada de `prd.md`, `spec_tech.md` e `spec_ui.md`, conforme roteiro de discovery (seção “Revisão do refinamento”).  
**Metodologia:** checagem de consistência funcional, de escopo por versão, de terminologia e de rastreabilidade entre produto, técnica e interface.

---

## 1. Sumário executivo

Os três documentos descrevem coerentemente um **agente de IA para SEO imobiliário** (conteúdo local, E-E-A-T, escala). Foram encontradas **lacunas de alinhamento** principalmente em: (a) **escopo MVP vs. funcionalidades listadas** (lote e enriquecimento local); (b) **especificação técnica** com **decisões duplicadas ou conflitantes** (Azure vs. AWS; Python vs. NestJS); (c) **UI** com CTAs de **lote no dashboard** sem marcar explicitamente como pós-MVP. As correções foram **aplicadas nos próprios documentos**; este arquivo registra o racional.

---

## 2. Achados por documento

### 2.1 PRD (`prd.md`)

| ID | Achado | Severidade |
|----|--------|------------|
| P1 | RFN-01 a RFN-06 listados como “principais”, mas o **Escopo v1** exclui lote (RFN-04), enriquecimento local (RFN-02) e parte de export/CMS avançado — risco de equipe implementar tudo no MVP. | Média |
| P2 | Persona **Gestor de Marketing** não tinha **permissões** explícitas frente à UI (fluxo somente leitura/aprovação). | Baixa |
| P3 | Falta **ponte** explícita para documentos de técnica e UI. | Baixa |

### 2.2 Especificação técnica (`spec_tech.md`)

| ID | Achado | Severidade |
|----|--------|------------|
| T1 | **Deployment:** “Cloud (Azure)” na infraestrutura e **AWS** nas integrações — decisão de nuvem conflitante. | Alta |
| T2 | **Backend:** “Linguagem: Python” com “Framework: NestJS ou FastAPI” mistura stacks; NestJS é ecossistema Node, não Python. | Alta |
| T3 | Ortografia: “Role-Based **Acess** Control”. | Baixa |
| T4 | Template do roteiro prevê **“Diretrizes para Desenvolvimento Assistido por IA”** — seção ausente. | Média |
| T5 | Endpoints não cobriam explicitamente **jobs de lote** (RFN-04 / fila), embora arquitetura mencione assíncrono. | Média |

### 2.3 Especificação de UI (`spec_ui.md`)

| ID | Achado | Severidade |
|----|--------|------------|
| U1 | **INT-02** sugere “Nova geração em lote” como ação de destaque; no PRD, **lote é v2** — risco de protótipo MVP incorreto. | Média |
| U2 | **Diretrizes para IA** citam INT-08 como v2, mas o dashboard não deixava isso explícito no nível de componente. | Baixa |

---

## 3. Recomendações aplicadas (changelog conceitual)

1. **PRD:** inclusão da tabela **“RFN × versão alvo”** e nota sobre papel do Gestor; bloco **“Documentação relacionada”**.
2. **spec_tech:** **AWS** como nuvem primária alinhada a S3/ECS/Cognito; backend padrão **Python + FastAPI** para serviços de geração, com BFF alinhado ao **Next.js**; correção RBAC; inclusão de endpoints de **jobs/lotes**; nova seção **Diretrizes para desenvolvimento assistido por IA**.
3. **spec_ui:** CTAs de **lote** marcados como **(v2)** onde aparecem no fluxo principal; reforço nas **Diretrizes para IA** e referência a este relatório.

---

## 4. Itens em aberto (não bloqueantes)

- **`spec_req.md`** não entrou nesta revisão formal do roteiro; ainda há divergência histórica de performance (10s vs 30s) em relação ao PRD — recomenda-se alinhar em revisão futura única com RNF-02.
- **Design system** e **modelo de dados** (fase 1.3 do roteiro) continuarão detalhando tokens visuais e entidades.

---

## 5. Checklist pós-revisão

- [x] PRD, spec_tech e spec_ui atualizados com as correções acima  
- [x] Relatório arquivado em `docs/revisao_refinamento.md`  
- [ ] Commit e push no repositório remoto (realizado na mesma entrega, se aplicável)
