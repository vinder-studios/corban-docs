# Corban Marketing — Documento de Produto

**Status:** Draft
**Aplicação:** Marketing (terceira aplicação do Corban, ao lado de Admin e CRM)
**Versão:** 0.9
**Data:** [a definir]
**Módulos:** Prospecção v0.6 · Carteira Ativa v0.7 · Dashboard v0.3

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.9 | **Notificações entram na v1** (decisão do usuário, 10/10/2026). Sai "notificações" da lista do que está fora da v1 (§1). Nova **RN-MKT-10**: o Marketing notifica, pelo sino do Shell, só exceções — pré-análise pronta aguardando aprovação e falha — a quem pode agir. Injeção concluída não notifica (é rotina e aparece no Dashboard). |
| 0.8 | **Pendências de produto fechadas.** Confirmados pelo usuário: momento da gravação da Simulação (na injeção), criação de Clientes, frequências, duplicidade sem CPF e campos do Contrato. A Simulação de origem Marketing fica no histórico do Cliente e é visível a quem tem acesso a ele. |
| 0.7 | **Decisões do usuário.** (1) A avaliação em lote **grava Simulação** quando o contato tem ao menos uma Oferta aprovada e a Lead é injetada (RN-MKT-02; Simulações v0.12). (2) **Declarações de base legal e retenção saem do escopo inicial**: a RN-MKT-06 fica reservada. (3) Os itens abertos da §7 que fogem da definição do produto saem. |
| 0.6 | **Decisões do usuário (Carga removida, Lista guardada, Lista de bloqueio em evolução).** (1) A **Carga de base** deixa de existir, e o recurso `client_loads` sai do catálogo (§2). (2) Na Prospecção, o **Cliente nasce depois do mapeamento**, pode nascer **sem CPF**, e o gestor escolhe entre **distribuir** as Leads ou **concluir sem distribuir**, com a Lista guardada para distribuir depois (§3, RN-MKT-07, RN-MKT-09). (3) A Carteira Ativa passa a ler o **histórico de contratos** do Cliente (Clientes v0.9, Propostas v0.12) e a Varredura **Recorrente** tem **frequência definida na criação** (§5, item 8). (4) A **Lista de bloqueio fora do Marketing** vira **nota de evolução** (§6), e o item aberto 3 sai da §7. As declarações de base legal e a retenção ficam como estão, para definir com o jurídico. |
| 0.5 | **Fecha itens abertos.** (1) **Valor potencial em lote** (RN-MKT-08): sem valor desejado na linha, parcela = margem disponível e prazo = máximo da Oferta. (2) **Região na pré-análise**: a pré-análise é do tenant e não conhece a Loja, então considera as Ofertas ativas de qualquer Região; a Região só atua na Simulação da Loja que recebe a Lead (Regiões, RN-REG-04). (3) **Lista de bloqueio** (RN-MKT-05) vale só para a injeção do Marketing por ora; revisitar quando a Landing Page (V2) entrar. Decisões do usuário. |
| 0.4 | **Catálogo de permissões do Marketing definido** (§2): recursos `prospecting_lists`, `client_loads`, `blocklist`, `marketing_settings`, `sweeps` e `marketing_finance`, todos só em escopo `all`, nas Matrizes de Permissões dos três módulos. A regra de login já estava na RN-USR-16 (Usuários v0.6). Item 6 do §5 aplicado; Dashboard passa a v0.3 |
| 0.3 | **Acompanha Lojas v0.13 (tipos de lead por Segmento).** No rodízio (§3, passo 6), a Lead só é oferecida às Lojas que têm ligada a Origem dela (Prospecção ou Carteira Ativa) no Segmento. A Loja do tenant, com "Pode receber lead sem saldo", tem todas as Origens ligadas e travadas, então uma Lead injetada continua sem risco de ficar sem Loja |
| 0.2 | **Notas de evolução e princípio de arquitetura**, a partir do rascunho do autor. (1) Nova §6 com o roadmap: **Landing Pages (V2)**, **Integrações (V3)** e **Anúncios (V3)**. (2) **O módulo V3 de anúncios se chama "Anúncios"**, para não colidir com a Origem da lead "Campanha" nem com os disparos de mensagem em massa. (3) **O gasto de anúncios alimenta o bloco financeiro do Dashboard**, ao lado do Custo da base, e não o preço de lead que a Loja paga. (4) Princípio **Dependência zero no MVP** na §1. (5) Coluna de especificação por módulo na tabela da §1. Itens abertos passam a §7. |
| 0.1 | Versão inicial. Reúne a visão da aplicação, o fluxo de ponta a ponta e as **regras compartilhadas** entre Prospecção e Carteira Ativa (RN-MKT), definidas no levantamento do Marketing. |

---

# 1. O que é o Marketing

O Marketing é a aplicação do **tenant** (acima das Lojas) que **gera demanda**: transforma bases de contatos, externas ou da própria casa, em Leads qualificadas no funil das Lojas. Não atende clientes nem negocia (isso é do CRM) e não configura a parte comercial (isso é do Admin): ele **consome** as Ofertas, Regras, Segmentos e Funis do Admin e **entrega** Leads e Clientes ao CRM.

| Módulo | Para que serve | Especificação |
|---|---|---|
| **Prospecção** | Importar bases externas, criar os Clientes, pré-analisar e distribuir Leads (ou guardar a Lista). Mantém a **Lista de bloqueio**. | `corban-marketing-prospeccao-v0_6` |
| **Carteira Ativa** | Varrer a base de Clientes do tenant e injetar Leads de quem está elegível a uma nova operação. | `corban-marketing-carteira-ativa-v0_7` |
| **Dashboard** | Medir volume, conversão e, com permissão, retorno financeiro. | `corban-marketing-dashboard-v0_3` |

**Dependência zero no MVP.** A v1 gera valor só com a inteligência interna do Corban (motor de Ofertas e Regras do Admin e dados do CRM), sem depender de APIs de terceiros como Meta ou Google.

**Fora da v1:** Inbound e Anúncios (previstos na §6); e **Campanhas** no sentido de disparos de mensagem em massa. **Notificações entram na v1**, só para exceções (RN-MKT-10).

---

# 2. Atores e acesso

- **Gestor de Marketing (tenant)** — opera os três módulos.
- **Owner / gestão financeira** — vê também o bloco financeiro do Dashboard.
- **Sistema** — executa pré-análises, varreduras e injeções em segundo plano.

O acesso é por **permissão**, como no resto do produto. A aplicação aparece no app switcher para quem tem alguma permissão de Marketing. Os recursos no catálogo de permissões, todos em escopo `all` porque o Marketing atua acima das Lojas:

| Recurso | Ações | Módulo |
|---|---|---|
| `prospecting_lists` | `view`, `create`, `approve`, `delete` | Prospecção |
| `blocklist` | `view`, `manage` | Prospecção |
| `marketing_settings` | `view`, `edit` | Prospecção (carência) |
| `sweeps` | `view`, `create`, `edit`, `approve`, `delete` | Carteira Ativa |
| `marketing_finance` | `view` | Dashboard (bloco financeiro) |

`approve` (aprovar a injeção, pausar, retomar e cancelar) é separada de `create` e de `edit`, porque liberar a injeção debita o saldo das Lojas. O Dashboard não tem recurso próprio além do financeiro: cada bloco depende do `view` da fonte.

---

# 3. Fluxo de ponta a ponta

1. **Entrada.** Uma Lista (base externa) é importada e mapeada, e os Clientes nascem; ou uma Varredura é configurada sobre o histórico de contratos dos Clientes.
2. **Pré-análise.** O motor do Admin (Ofertas e Regras) avalia cada contato ou Cliente. A análise não grava Simulação; a Simulação só é gravada na injeção, para o contato com Oferta aprovada (RN-MKT-02).
3. **Resultado.** O gestor vê o funil: inválidos, bloqueados, descartados (com motivo), qualificados.
4. **Decisão e aprovação.** Nada é injetado sem aprovação. Na Lista, o gestor escolhe **distribuir** (tudo agora ou limite diário) ou **concluir sem distribuir**, e a Lista fica guardada para distribuir depois. Na Varredura, aprova a primeira execução.
5. **Injeção.** A cada injeção, o sistema reavalia duplicidade e bloqueio, cria ou associa o Cliente e cria a Lead com Origem **Prospecção** ou **Carteira Ativa**.
6. **Rodízio.** A Lead entra na distribuição entre as Lojas que operam o Segmento **e têm ligada a Origem dela** (Lojas, RN-LOJ-17), com cobrança (Leads, RN-LED-02, 03 e 13).
7. **Desfecho.** A Lead segue no CRM até Pago ou Cancelado; o Dashboard mede o resultado.

Os **Clientes nascem na importação da Lista**, depois do mapeamento, mesmo que as Leads nunca sejam distribuídas, e sem Loja. O Cliente pode nascer **sem CPF**, que é preenchido depois (Clientes, RN-CLI-11).

---

# 4. Regras compartilhadas (RN-MKT)

**RN-MKT-01 — Origem e rastreio da Lead.** Toda Lead do Marketing nasce com a **Origem da lead** definida pelo sistema, **Prospecção** ou **Carteira Ativa** (Leads, RN-LED-12), e com a **referência à Lista ou Varredura** que a gerou, ambas **imutáveis**. A Origem da lead define o preço (RN-LED-13). Se a Lista ou a Varredura tiver **Origem de Venda**, o rótulo é **copiado** à Lead. A Tag de categoria "Origem" não é usada para isso.

**RN-MKT-02 — A análise em lote só grava Simulação na injeção.** Durante a análise, a avaliação em lote **não grava Simulação**: o resultado por contato fica na Lista ou na Varredura que o gerou. Quando o contato tem **ao menos uma Oferta aprovada** e a Lead dele é injetada, a avaliação é **gravada como Simulação de origem Marketing** no histórico do Cliente (Simulações, RN-SIM-01), ligada à Lead. Descartados e contatos nunca distribuídos não geram Simulação. *(Decisão do usuário.)*

**RN-MKT-03 — Campo sem valor não desclassifica.** Quando uma Regra depende de um campo que o contato não trouxe, o requisito fica **"não avaliado"**: não desclassifica, e o resultado e a Lead mostram quais requisitos ficaram sem avaliação, para o operador confirmar na primeira simulação. Cliente sem dados mínimos para o motor (Carteira Ativa) aparece como "Sem dados para análise".

**RN-MKT-04 — Duplicidade.** O sistema **não injeta** um contato se, no momento da injeção: (a) o Cliente tem **Lead ativa** no mesmo Segmento; (b) o Cliente tem **Proposta em andamento** (fora de Pago e Cancelado); ou (c) o Cliente foi **injetado pelo Marketing nos últimos N dias** (carência, padrão **90**, configurável pelo tenant). A regra vale para a Prospecção e para a Carteira Ativa, e a recusa é contada no resultado.

**RN-MKT-05 — Lista de bloqueio ("não perturbe").** CPF ou telefone presente na Lista de bloqueio **não é injetado**: bloqueio rígido, contado no resultado. **Hoje vale só para a injeção do Marketing.** Estendê-la a Leads manuais, Huggy e landing pages é nota de evolução (§6).

**RN-MKT-06 — *(removida na v0.7; número reservado).*** A declaração de base legal e a retenção de dados saíram do escopo inicial do produto.

**RN-MKT-07 — Injeção controlada.** Nada é injetado sem **aprovação** do gestor (a primeira execução de cada Lista e de cada Varredura). O gestor define o ritmo: **tudo agora** ou **limite diário**, **obrigatório** na Varredura Recorrente. Na Lista, o gestor também pode **concluir sem distribuir**, e a Lista fica guardada. Toda injeção em andamento pode ser **pausada** e **cancelada** (o que já foi injetado continua), e cada injeção **revalida** duplicidade e bloqueio (RN-MKT-04 e 05).

**RN-MKT-08 — O que viaja com a Lead.** A Lead leva o **valor potencial** (o maior valor pré-aprovado entre as Ofertas elegíveis) e o **resumo das Ofertas elegíveis** no bloco Ofertas, com os requisitos não avaliados (RN-MKT-03). O valor é de referência: não garante aprovação nem condição, e a Simulação gravada na injeção registra a avaliação (RN-MKT-02). **Cálculo em lote:** quando a linha não traz valor desejado, usa **parcela = margem disponível** e **prazo = máximo da Oferta**. **Região:** a pré-análise roda no nível do tenant, sem Loja, e considera as Ofertas ativas de **qualquer Região**; a Região só vale na Simulação da Loja que recebe a Lead (Regiões, RN-REG-04), e por isso o resumo é só referência.

**RN-MKT-09 — O Cliente do Marketing é do tenant e nasce sem Loja.** Clientes criados pelo Marketing (na importação da Lista ou na injeção) pertencem ao tenant e **não têm Loja associada**. A Loja passa a ter acesso quando uma Lead dele é distribuída a ela (Clientes, RN-CLI-04, RN-CLI-10).

**RN-MKT-10 — Notificações só de exceção, a quem pode agir.** O Marketing usa o sino do Shell Privado, no mesmo regime do produto: notificar exceção, não rotina. Três eventos, iguais para Lista (Prospecção) e Varredura (Carteira Ativa):

| Evento | Quando | Quem recebe |
|---|---|---|
| **Aguardando aprovação** | A pré-análise (ou o reprocessamento) termina e a Lista ou a Varredura entra em Aguardando aprovação | Quem tem `prospecting_lists:approve` (Lista) ou `sweeps:approve` (Varredura) |
| **Falha** | A Lista ou a Varredura entra em Falha | Quem criou e quem tem a permissão de aprovar |
| **Falha de execução recorrente** | Uma execução da Varredura Recorrente falha | Quem criou e quem tem `sweeps:approve` |

A notificação leva ao detalhe da Lista ou da Varredura. **Não notificam:** injeção concluída, execução diária bem-sucedida da Recorrente e Lead injetada (aparecem no Dashboard e no board da Loja). A tela aberta da Lista ou da Varredura se atualiza sozinha quando o estado muda, sem recarregar.

---

# 5. Impactos em outros módulos

Mudanças que as decisões do Marketing exigem fora desta aplicação. Todos os itens abaixo já foram aplicados.

| # | Módulo | Mudança | Situação |
|---|---|---|---|
| 1 | **Clientes (CRM)** | Sai a importação (RF-CLI-02, RN-CLI-05, permissão `clients:import:store`, página de importação). Clientes v0.7. | Aplicado |
| 2 | **Propostas (CRM)** | A referência ao "padrão de importação de Clientes" aponta para o Marketing. | Aplicado |
| 3 | **Leads (CRM)** | Campos novos, somente leitura nas Leads do Marketing: **referência à Lista ou Varredura**, **valor potencial**, **resumo das Ofertas elegíveis** e requisitos não avaliados; Origem de Venda copiada. | Aplicado (Leads v0.11) |
| 4 | **Layout de Lead (Admin)** | O bloco Ofertas passa a exibir o resumo que viaja com a Lead; Valor potencial no cartão. | Aplicado (Layout de Lead v0.7) |
| 5 | **Origem de Venda (Admin)** | Passa a ter a Lista e a Varredura como consumidoras do rótulo. | Aplicado (Origem de Venda v0.8) |
| 6 | **Catálogo de permissões** | Recursos do Marketing (§2) e a regra de login "tem alguma permissão na aplicação". | Aplicado (Matrizes de Permissões do Marketing) |
| 7 | **Regras (Admin)** | RN-RGR-11 diz "operador"; no Marketing a mensagem de erro é lida pelo Gestor de Marketing. | Aplicado (Regras v0.9) |
| 8 | **Clientes e Propostas (CRM)** | Clientes: CPF opcional, preenchido depois, e o histórico de contratos. Propostas: Valor, Parcela e Prazo do contrato, e a geração do Contrato ao chegar a Pago. | Aplicado (Clientes v0.9, Propostas v0.12) |
| 9 | **Simulações (CRM)** | Terceira origem: Marketing, gravada na injeção da Lead com Oferta aprovada. | Aplicado (Simulações v0.13) |

---

# 6. Notas de evolução (roadmap)

Direção prevista, **sem compromisso de escopo**. Cada item vira um módulo com documento próprio quando entrar em especificação.

## Evolução — Lista de bloqueio fora do Marketing

- Hoje a Lista de bloqueio vale só para a injeção do Marketing (RN-MKT-05). Estendê-la a Leads manuais, Huggy e landing pages é uma evolução, a decidir quando houver domínio do tema.
- **Impacto:** Leads (CRM) e a captura de landing pages passariam a consultá-la.

## V2 — Inbound: Landing Pages

- Gestão de templates de páginas de conversão.
- A captura cria o Cliente e a Lead (Origem da lead **Campanha** ou **Orgânica**, Leads RN-LED-12) e injeta no funil do Segmento, pela distribuição normal.
- Subdomínios e scripts de rastreio (Google Tag Manager, Pixel do Meta).
- **Impacto:** o Leads v0.12 já trata landing page como entrada tenant-wide (RF-LED-03). Ao entrar, o módulo passa a ser o dono da página e da captura, sem mudar a regra da Lead. A Lista de bloqueio fora do Marketing é uma nota de evolução (abaixo).

## V3 — Integrações (hub técnico)

- Credenciais e fluxo OAuth2 com Meta Business, Google Ads e TikTok Ads.
- Guarda de tokens e tratamento de limites de taxa das APIs.

## V3 — Anúncios

- Painel para ativar, pausar e ajustar orçamentos de anúncios a partir do Corban.
- Parâmetros UTM para rastrear do anúncio até a Proposta Paga.
- **Sincronização do gasto.** O gasto diário das plataformas entra no **bloco financeiro do Dashboard** (RN-DMB-04), ao lado do Custo da base, e permite calcular custo por Lead e por Proposta Paga das Leads de anúncio. **Não** altera o preço de lead que a Loja paga (Funis, RN-FNL-07): preço de lead é receita do tenant, e gasto de anúncio é custo dele.
- **Impacto:** o Dashboard §7 já prevê esse custo. Ao entrar, ganha um filtro por campanha de anúncio e a regra de cálculo em RN-DMB-05.

**Nome.** O módulo se chama **Anúncios** para não colidir com a Origem da lead "Campanha" (contato de campanha externa) nem com "Campanhas" no sentido de disparos de mensagem em massa, fora da v1.

---

# 7. Itens abertos

Os itens abertos de cada módulo estão em Prospecção §9 e Carteira Ativa §9. Os que atravessam a aplicação:

1. *(Removido na v0.7: fora do escopo do produto.)*
2. *(Removido na v0.7: fora do escopo do produto.)*
3. *(Movido na v0.6 para as notas de evolução, §6.)*
4. *(Resolvido na v0.5: a pré-análise considera qualquer Região.)*

---

*Fim do documento. Marketing — Documento de Produto v0.9. Próximo passo: Fase 2 de cada módulo (Descrição de Telas, Mapa, Matriz de Estados, Microcopy, Variações). A Matriz de Permissões dos três módulos já está pronta.*
