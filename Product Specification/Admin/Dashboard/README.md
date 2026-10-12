# Corban Admin · Dashboard

**Status:** Draft
**Aplicação:** Admin
**Seção:** Visão geral
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha o item aberto 1: define o **estado de Dashboard vazio**, para quando nenhum dos três blocos tem permissão de `view` (RN-DSH-04). |
| 0.3 | O bloco de gráfico passa a exigir também `funnels:manage_pricing`, não só `funnels:view` — é dado financeiro, mesmo critério do módulo de Funis de Vendas. |
| 0.4 | As abas do gráfico de preço passam de Externo/Interno para as quatro Origens da lead com preço: Campanha (padrão), Orgânica, Prospecção e Carteira Ativa (Funis v0.12). |
| 0.5 | **O gráfico carrega o último valor** (RN-DSH-02 reescrita): só fica vazio quando nunca houve registro, e preço estável mostra linha constante. **Os blocos de Lojas e de Usuários respeitam o escopo** (nova RN-DSH-05): a versão anterior dizia que a contagem era sempre do tenant inteiro, o que mostraria a um gestor de Loja os usuários e as Lojas das demais. Os alertas passam a levar a filtros que existem nas Listas (Ofertas e Lojas ganham o seu). Corrige o §9 (uma Oferta ativa nunca está incompleta, então o alerta conta as inativas em construção), o alerta de Lojas (sem Time **ativo**), a coluna da tabela ("Loja · Time"), a regra do Owner (que só existia na Matriz de Estados), a permissão do gráfico (as duas, explícitas), a RN-DSH-04 e o padrão do seletor de Segmento. Reordena esta tabela. |

## Como usar este documento

Doc de módulo: blocos, campos, estados e requisitos funcionais (RF-DSH) próprios. É a tela inicial da aplicação Admin.

> **Diferente do Dashboard do CRM.** O UX v0.1 já especifica o Dashboard do CRM (visão financeira, gerencial, do operador — voltadas a desempenho comercial), mas deixa o do Admin como decisão em aberto. Este documento resolve essa pendência. O eixo aqui não é desempenho de vendas — é **saúde de configuração do tenant**: quem entra no Admin está estruturando o sistema, não vendendo.

---

# 1. Objetivo do módulo

Dar ao gestor, na tela inicial do Admin, uma visão imediata do que precisa de atenção na configuração do tenant, mais um acompanhamento simples da precificação de leads e de quem entrou recentemente no sistema.

---

# 2. Atores

- **Todos os usuários autenticados no Admin** — cada um vê os blocos que suas permissões habilitam, mesmo princípio já usado no Dashboard do CRM (RN057 da Especificação v1.2).

---

# 3. Composição — três blocos, em linha

## 3.1. Linha 1 — Alertas de configuração

Três indicadores, cada um contando itens que precisam de ação:

| Alerta | O que conta | Permissão para ver |
|---|---|---|
| **Funis aguardando configuração** | Funis em status "Aguardando configuração" (módulo de Funis de Vendas) | `funnels:view` |
| **Ofertas com regras incompletas** | Ofertas com ao menos uma associação de Regra incompleta (RN-OFR-03, módulo de Ofertas). Por definição são Ofertas **inativas em construção**: uma Oferta ativa nunca está incompleta (RN-OFR-03, RN-RGR-13) | `offers:view` |
| **Lojas sem time** | Lojas ativas sem nenhum Time **ativo** (módulo de Lojas), dentro do escopo do usuário (RN-DSH-05) | `stores:view` |

Cada alerta é clicável, levando à listagem já filtrada (ex.: clicar em "3 ofertas com regras incompletas" abre a Lista de Ofertas filtrada por esse critério). Os filtros existem nas Listas: Funis, por status; Ofertas, por situação das Regras ("Regras incompletas"); Lojas, por situação dos Times ("Sem Time ativo").

**RN-DSH-01 — Alerta com contagem zero não é ocultado, só neutro.** Diferente de outros vazios do produto, aqui "zero" é a informação (tudo configurado) — o bloco continua visível, com indicador neutro/positivo em vez de sumir, para o gestor confirmar que está tudo certo, não só supor.

## 3.2. Linha 2 — Gráfico de Preço de Lead

Gráfico de área com duas séries sobrepostas — **Custo** e **Preço** — ao longo do tempo, consumindo o Histórico de Preço de Lead (RN-FNL-16, módulo de Funis de Vendas). Referência visual: gráfico de área duplo com gradiente, no estilo de dashboards analíticos comuns (séries translúcidas sobrepostas, não empilhadas).

**Exige `funnels:view` e `funnels:manage_pricing`.** Custo e preço são dado financeiro — mesmo critério já aplicado à aba Preço das leads no módulo de Funis de Vendas. Um Cargo com `funnels:view` mas sem `manage_pricing` vê o alerta de Funis aguardando configuração, mas não este gráfico.

**Controles do bloco:**

| Controle | Comportamento |
|---|---|
| **Seletor de Segmento** | Escolhe de qual Funil (via Segmento) exibir o histórico. Um Segmento por vez. Ao abrir, vem selecionado o primeiro Segmento (por nome) cujo Funil tem histórico de preço; se nenhum tiver, o primeiro por nome, com o estado vazio (RN-DSH-02). *(Escolha desta revisão.)* |
| **Tabs de Origem da lead** | **Campanha** (ativa por padrão) / Orgânica / Prospecção / Carteira Ativa — escolhe qual das quatro linhas de preço (RN-FNL-07) exibir. |
| **Tabs de período** | Últimos 7 dias / Últimos 30 dias / Últimos 3 meses — recorta o histórico exibido. |

**RN-DSH-02 — O gráfico carrega o último valor; só fica vazio quando nunca houve registro.** O histórico só tem registros nas **mudanças** (RN-FNL-16): o valor de Custo e de Preço em cada data é o do **último registro até ela** (a série é em degraus, e não pontos soltos). Assim, um preço fixado há 40 dias aparece como linha constante numa janela de 7 dias. O estado vazio só aparece quando **não há nenhum registro até o fim do período**, isto é, quando o preço daquela linha nunca foi definido (as linhas nascem em 0 e o valor inicial não gera registro, RN-FNL-16). Mostrar "sem dado" para um valor que existe esconderia justamente o que o gráfico serve para mostrar. *(Decisão do usuário, na revisão; substitui a versão anterior da regra, que mostrava vazio sempre que o preço não mudava no período.)*

## 3.3. Linha 3 — Últimos usuários cadastrados

Tabela simples com os **10 usuários mais recentes** (por data de criação) **no escopo da permissão do usuário** (RN-DSH-05), sem o Owner (que não é um usuário comum, RN-USR-03) e sem usuários excluídos, colunas: identificação (Avatar + nome), Cargo, **Loja · Time** (um usuário pode ter Loja sem Time, RN-USR-15), Status, data de cadastro.

**RN-DSH-03 — Sem paginação, sem ação.** É um resumo, não uma listagem funcional — não tem filtro, busca, nem ações inline (isso já existe na Lista de Usuários). Cada linha, no máximo, linka para o Detalhe daquele usuário.

---

# 4. Requisitos funcionais

## RF-DSH-01 — Visão inicial do Admin

**Como** qualquer usuário autenticado no Admin, **eu quero** ver, ao entrar, o que precisa de atenção na configuração do tenant **para** agir sem precisar navegar por cada módulo procurando pendências.

**Comportamento esperado:** os três blocos carregam de forma independente (um pode estar pronto enquanto outro ainda carrega ou falha, sem travar a tela). Cada bloco só aparece se o usuário tiver a permissão de `view` do recurso correspondente.

**RN-DSH-04 — Dashboard vazio quando nenhum bloco tem permissão.** Se o Cargo do usuário não conceder `funnels:view`, `offers:view`, `stores:view` nem `users:view` (o gráfico exige `funnels:view` além de `funnels:manage_pricing`, então não amplia esta lista), os três blocos somem e a tela mostra um **Estado de tela vazio** dedicado — não uma página em branco. Mensagem orientativa, sem CTA de ação (o usuário não tem permissão para agir em nada listado aqui): algo como "Nada para mostrar aqui ainda. Fale com o administrador do seu tenant se precisar de acesso a mais informações." *(Texto final na Microcopy Library.)*

**RN-DSH-05 — Os blocos de Lojas e de Usuários respeitam o escopo da permissão.** `stores:view` e `users:view` existem em `all` e em `store`. Com `store`, o alerta "Lojas sem time" considera só a Loja do próprio usuário (a contagem fica em 0 ou 1) e a tabela de últimos usuários mostra só usuários da Loja dele; com `all`, o tenant inteiro. Os demais blocos (Funis, Ofertas e o gráfico) usam permissões que só existem em `all` e não variam. *(Decisão do usuário, na revisão; substitui a versão anterior, que dizia que o escopo só decidia se o bloco aparecia e que a contagem era sempre do tenant inteiro, o que mostraria a um gestor de Loja os usuários e as Lojas das demais.)*

---

# 5. Regras de negócio (consolidado)

- **RN-DSH-01** — Alerta com contagem zero permanece visível, com indicador neutro.
- **RN-DSH-02** — O gráfico carrega o último valor (série em degraus); só fica vazio quando nunca houve registro até o fim do período.
- **RN-DSH-03** — Tabela de usuários recentes é resumo — sem paginação, filtro ou ação.
- **RN-DSH-04** — Dashboard mostra estado vazio dedicado quando nenhum bloco tem permissão de `view`.
- **RN-DSH-05** — Os blocos de Lojas e de Usuários respeitam o escopo da permissão (`all` ou `store`).

---

# 6. Estados e transições

Não há entidade própria neste módulo — os três blocos consomem estado de outros módulos (Funis, Ofertas, Lojas, Histórico de Preço, Usuários). Estados de tela (carregando, vazio, erro) por bloco ficam para a Matriz de Estados.

---

# 7. Limites do módulo

**Não** inclui: a lógica de cálculo de cada alerta (vive no módulo de origem — ex.: "Oferta com regra incompleta" é RN-OFR-03), a listagem completa de qualquer entidade (cada uma tem seu próprio módulo), nem o Dashboard do CRM (módulo separado, já especificado na UX v0.1).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Funis de Vendas** — status "Aguardando configuração" (alerta) e Histórico de Preço de Lead (gráfico).
- **Ofertas** — contagem de Ofertas com Regras incompletas.
- **Lojas** — contagem de Lojas sem Time.
- **Segmentos** — lista de Segmentos, para o seletor do gráfico.
- **Usuários** — os 10 mais recentes.

**Expõe para:** nada — é tela terminal, só consome.

---

# 9. Itens abertos

1. **Contagem de "Ofertas com regras incompletas" pode ser cara de calcular.** Depende de checar, para cada Oferta **inativa** (uma Oferta ativa nunca está incompleta, RN-OFR-03 e RN-RGR-13), se todas as associações de Regra têm valores completos — potencialmente uma verificação por Oferta, não uma consulta direta. Vale avaliar se esse número é pré-calculado (atualizado a cada mudança) ou calculado sob demanda a cada carregamento do Dashboard. *(Confirmar com o time de backend — sem decisão de produto pendente aqui.)*

---

*Fim do documento. Dashboard v0.5 — módulo da aplicação Admin, seção Visão geral.*
