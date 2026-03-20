# Prompt de UX Design para Ferramentas de Prototipagem (Google Stitch / v0)

Copie e cole o prompt abaixo em uma ferramenta de geração de UI baseada em IA (como Google Stitch, v0.dev ou similares) para iniciar a criação dos protótipos em código do projeto.

---

**Atue como um Especialista Sênior em UX/UI Design de Produtos B2B SaaS.**

Estou desenvolvendo um **Agente de IA Generativa especializado em SEO Imobiliário**. Este sistema ajudará Especialistas em Conteúdo SEO e Gestores de Marketing a escalar a produção de conteúdos imobiliários originais (como páginas descritivas de bairros, condomínios e tipologias) já estruturados para ranqueamento (E-E-A-T, H1, Meta Tags, FAQ).

Sua tarefa é gerar o código e os protótipos funcionais das interfaces do nosso MVP. A stack de frontend utiliza **Next.js (App Router) e TailwindCSS**, e vamos usar componentes baseados em Radix UI/shadcn_ui se estiverem disponíveis. O design precisa ser **desktop-first** com layout responsivo para tablets/mobile. O visual deve focar em eficiência operacional, facilidade de leitura prolongada (editor text-heavy) e transmitir uma aparência "premium", porém limpa e corporativa.

Siga rigorosamente estas especificações baseadas nos nossos documentos de Produto (PRD) e Sistemas (Tech e UI Spec) para gerar as telas:

### Componentes Globais da Aplicação (App Shell)
- **Top bar (Cabeçalho):** Logotipo do sistema, Seletor de Projeto/Tenant (dropdown), Barra de busca rápida global e Avatar do usuário com menu de configurações (INT-11).
- **Sidebar (Menu Lateral):** Navegação colapsável com ícones e labels claros: Dashboard, Projetos, Biblioteca, Geração em Lote e Configurações.
- **Feedback visual:** Use "Loading Skeletons" para demonstrar a espera das operações de LLM (que podem levar até 30s) e use Toast Notifications para sucessos e erros da API.

--- 

Quando você for gerar as telas, faça por etapas ou permita guias. Seguem as descrições precisas das principais telas do MVP:

### Tela 1: Dashboard e Visão Geral (INT-02)
- Crie a tela principal contendo uma área de métricas no topo: Cards para "Conteúdos gerados no período", "Jobs em fila de IA" e "Última taxa de sucesso/erro".
- Abaixo dos cards, inclua uma seção de "Acesso Rápido" com botões primários em destaque: "Novo Briefing" e "Biblioteca de Conteúdo".
- Liste os conteúdos recentemente alterados em uma pequena tabela.

### Tela 2: Detalhes do Projeto (INT-04)
- Deve ser subdividida com um componente Tabs (Abas): "Briefings", "Conteúdos Gerados" e "Configurações do Projeto".
- Na aba de Configurações, garanta a presença de campos de formulário como "Nome do Projeto", um `<textarea>` expansível para "Tom de Voz / Guidelines da Marca" e a seleção do idioma padrão.

### Tela 3: Formulário de Briefing (INT-05)
- Crie um formulário multi-etapas (estilo Wizard Stepper) com progress flow horizontal ou vertical.
- **Passo 1 (Contexto Local):** Inputs flexíveis para "Cidade", "Bairro", "Tipo de Imóvel" e "Características da região".
- **Passo 2 (Diretrizes de SEO):** Input de Palavra-chave alvo principal, um input do tipo "tags" multi-seleção para Palavras-chave secundárias, e um `<select>` para Tipo de página.
- **Passo 3 (Geração):** Um resumo estático das configurações escolhidas e o CTA principal "Gerar Conteúdo com IA" que exiba o skeleton form aguardando.

### Tela 4: Editor de Conteúdo Assistido por IA (INT-06 e INT-07)
*Esta é a tela mais importante do produto, onde deve estar o maior nível de polimento.*
- Utilize um layout de **Split-pane (Tela Dividida)**.
- **Painel Principal (2/3 da tela - Editor Livre):** Uma área central livre de distrações, com barra de ferramentas de edição rica fixada no topo. Campos de topo fixos para **Título SEO** e **Meta Description** com contadores circulares de caracteres limitadores de SEO. O texto principal entra logo abaixo.
- **Painel Lateral (1/3 da tela - Assistente de SEO e IA):** Uma coluna à direita (ou "Drawer") categorizada que mostra as recomendações da IA através de "Inline Cards". Mostre Alertas visuais de densidade de keywords sugeridas, leitura de E-E-A-T, lista sumarizada de Headings (H2/H3/FAQ) e botões visíveis do tipo "Aplicar Sugestão de Melhoria" diretamente em cada card da sidebar.

**Instrução final para você, assistente:** Gere primeiramente o "App Shell" e a **Tela 4 (Editor Assistido por IA)**, criando dados mockados interativos para mostrar a dinâmica entre o texto no editor e o feedback da sidebar inteligente.
