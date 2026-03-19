# Especificação de UI

Documento de interface para o **Agente de IA Generativa especializado em SEO Imobiliário** (portal do Especialista em Conteúdo SEO e Gestor de Marketing), alinhado ao `prd.md` e à visão de frontend da `spec_tech.md` (Next.js, desktop-first com layout responsivo).

---

## Interfaces gráficas

### INT-01 - Login e recuperação de acesso

- **Tipo:** página (fluxo de autenticação)
- **Campos:** e-mail corporativo, senha; (fluxo secundário) e-mail para recuperação de senha
- **Botões:** Entrar, Esqueci minha senha, Voltar ao login
- **Links:** Termos de uso, Política de privacidade, Suporte
- **Considerações:** OAuth/OIDC pode substituir ou complementar o formulário; mensagens de erro claras (credenciais inválidas, conta bloqueada); suporte a branding do tenant (logo) quando multi-tenant

---

### INT-02 - Dashboard (visão geral)

- **Tipo:** página com cards e atalhos
- **Campos:** (opcional) seletor de projeto/tenant no topo
- **Botões:** Novo briefing, Nova geração em lote, Ver todos os conteúdos
- **Links:** Projetos, Conteúdos recentes, Configurações, Documentação da API (conforme perfil)
- **Considerações:** Cards com métricas resumidas (conteúdos gerados no período, jobs em fila, último erro de geração); atalhos diferenciados para Especialista SEO vs. Gestor (somente leitura ou aprovações, se aplicável)

---

### INT-03 - Lista de projetos

- **Tipo:** página com tabela e filtros
- **Campos:** busca por nome, filtros (status, data de criação)
- **Botões:** Criar projeto, Ações em linha (Abrir, Duplicar, Arquivar)
- **Links:** Nome do projeto → detalhe do projeto
- **Considerações:** Paginação ou scroll infinito; estado vazio com CTA para primeiro projeto; confirmação antes de arquivar

---

### INT-04 - Detalhe do projeto e briefings

- **Tipo:** página com abas ou seções (Briefings | Conteúdos gerados | Configurações do projeto)
- **Campos:** nome do projeto, descrição, tom de voz / guidelines (texto), idioma padrão
- **Botões:** Salvar alterações, Novo briefing
- **Links:** Voltar à lista de projetos; cada briefing → INT-05 ou INT-06 conforme estágio
- **Considerações:** Guidelines do projeto alimentam o contexto do agente (E-E-A-T, marca)

---

### INT-05 - Formulário de briefing (geração unitária)

- **Tipo:** página / formulário multi-etapas (wizard opcional: Contexto → SEO → Revisão)
- **Campos:** cidade, bairro, tipo de imóvel, características da região (textarea ou tags), palavras-chave alvo (primária + secundárias), tipo de página (bairro, condomínio, tipologia), opções de tom/comprimento (se MVP permitir)
- **Botões:** Gerar conteúdo, Salvar rascunho, Cancelar
- **Links:** Ajuda contextual (?): o que é briefing, boas práticas de keyword
- **Considerações:** Feedback de carregamento durante geração (alvo PRD: até ~30 s); preview de parâmetros antes de disparar; validação obrigatória de campos mínimos para RFN-01

---

### INT-06 - Editor de conteúdo assistido por IA

- **Tipo:** página com layout dividido: editor principal + painel lateral (SEO / sugestões)
- **Campos:** corpo do artigo (rich text ou markdown), título SEO, meta description, slug sugerido; painel: lista de sugestões (inline cards), novas keywords sugeridas, alertas (densidade, legibilidade)
- **Botões:** Salvar, Regenerar seção (se disponível), Aprovar, Exportar, Desfazer/Refazer
- **Links:** Abrir estrutura de headings (âncoras), Ir para FAQs
- **Considerações:** Atende RFN-05: edição livre + sugestões automáticas; indicar trechos afetados por sugestão; estado “gerando sugestões…” não bloquear edição quando possível

---

### INT-07 - Painel de estrutura SEO (metadados e outline)

- **Tipo:** painel (drawer ou coluna) acionado a partir do editor ou página dedicada
- **Campos:** H1, lista H2/H3 (outline), bloco de FAQs (pergunta/resposta), lista de long-tail keywords geradas
- **Botões:** Copiar meta tags, Copiar outline, Aplicar sugestão (por item)
- **Links:** Documentação interna sobre limites de caracteres (title, description)
- **Considerações:** Contadores de caracteres para title e meta description; destaque visual quando fora do intervalo recomendado

---

### INT-08 - Geração em lote (upload e acompanhamento)

- **Tipo:** página com upload + tabela de jobs
- **Campos:** upload de arquivo (CSV/planilha) com colunas mapeáveis (bairro, cidade, tipo, etc.); mapeamento coluna → campo; opções de template de briefing
- **Botões:** Validar arquivo, Iniciar lote, Pausar/Retomar (se suportado), Baixar relatório de erros, Exportar resultados
- **Links:** Modelo de planilha (download), Ver detalhe do job
- **Considerações:** Barra de progresso e estimativa; fila visível (RFN-04); falhas por linha com motivo legível; exportação zip/CSV dos conteúdos (alinhado a RFN-06)

---

### INT-09 - Biblioteca de conteúdos (lista)

- **Tipo:** página com tabela, filtros e busca full-text simples
- **Campos:** filtros (projeto, status: rascunho/aprovado/publicado, data, tipo de página)
- **Botões:** Abrir no editor, Exportar selecionados, Duplicar briefing
- **Links:** Título do conteúdo → INT-06
- **Considerações:** Seleção múltipla para export em massa; badges de status e de “precisa revisão” quando o score SEO estiver baixo (futuro spec_req)

---

### INT-10 - Exportação e integração

- **Tipo:** modal ou página
- **Campos:** formato (Markdown, HTML, JSON), escopo (página atual vs. seleção), opções (incluir meta tags, incluir FAQs separado)
- **Botões:** Baixar, Copiar para área de transferência, (v2) Enviar para CMS
- **Links:** Chaves / documentação de API para integração (RFN-06)
- **Considerações:** Nome de arquivo sugerido com slug + data; aviso se conteúdo não aprovado

---

### INT-11 - Configurações da conta e equipe

- **Tipo:** página com seções (Perfil | Equipe | Notificações | Billing somente admin)
- **Campos:** nome, foto, preferências de notificação (e-mail ao concluir lote), idioma da UI
- **Botões:** Salvar, Convidar usuário, Revogar acesso
- **Links:** Central de ajuda
- **Considerações:** RBAC refletido na UI (ocultar convites se não for admin); multi-tenant: troca de workspace se permitido

---

## Fluxo de Navegação

1. **Login (INT-01)** → **Dashboard (INT-02)**  
2. **Dashboard** → **Projetos (INT-03)** → **Projeto (INT-04)** → **Novo briefing (INT-05)** → **Geração** → **Editor (INT-06)** com **Painel SEO (INT-07)** acessível a qualquer momento  
3. **Dashboard** ou **Projeto** → **Geração em lote (INT-08)** → linhas concluídas abrem **Editor (INT-06)** ou export direto via **INT-10**  
4. **Biblioteca (INT-09)** acessível pelo menu global; qualquer item leva ao **Editor (INT-06)**  
5. **Exportação (INT-10)** a partir do editor, da biblioteca (massa) ou do resumo de lote (INT-08)  
6. **Configurações (INT-11)** sempre disponíveis no menu do usuário (avatar)  

**Componentes visuais recorrentes:** barra superior (logo, projeto atual, busca rápida de conteúdo, avatar), menu lateral colapsável, toasts para sucesso/erro, skeletons durante geração LLM, empty states com CTA.

**Fluxo Gestor (somente leitura ou aprovação):** mesmo shell, com rotas restritas: Dashboard resumido, Biblioteca (aprovar/reprovar), sem edição avançada se política assim definir no PRD futuro.

---

## Diretrizes para IA

- **Fonte de verdade:** priorizar este documento em conjunto com `prd.md` para decisões de UI; em conflito de detalhe, seguir critérios de aceitação do PRD.  
- **Escopo por versão:** no **MVP**, implementar prioritariamente INT-01, INT-02, INT-03, INT-04, INT-05, INT-06, INT-07, INT-09, INT-10 (export Markdown); INT-08 (lote) e integrações CMS avançadas podem seguir o escopo v2 do PRD.  
- **Consistência:** reutilizar padrões de formulário, tabela e modais; não inventar novos fluxos de negócio sem atualizar o PRD.  
- **Acessibilidade:** foco visível, labels em todos os campos, contraste adequado, mensagens de erro associadas aos campos; teclado no editor quando possível.  
- **Prototipagem:** ao gerar telas em ferramentas (ex.: Stitch), manter nomenclatura **INT-xx** nos títulos dos frames para rastreabilidade com este documento.  
- **Código (Next.js):** preferir composição de layout (app router), componentes de design system quando existirem (`design_system.md` na fase 1.3 do roteiro); não acoplar strings de UI a lógica de negócio sem i18n se o produto for multilíngue.
