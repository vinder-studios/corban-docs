# Corban CRM · Propostas — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Propostas (módulo v0.7)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial (módulo pré-reformulação) — recurso `proposals` com só `view`/`manage`. Obsoleto. |
| 0.2 | Reescrita pós-reformulação — adiciona `create` e `delete` num recurso `proposals` único. |
| 0.3 | Leitura errada de "granularidade por status" — modelei como ações de transição (`advance_to_X`) num recurso só. Não era isso. |
| 0.4 | **Correção: decomposição em sub-recursos**, cada um com seu próprio `manager` — `proposals_documents`, `proposals_actors`, `proposals_dates`, e um por Tipo de Status (`proposals_new`, `proposals_negotiation`, `proposals_paids`, `proposals_canceled`). O recurso base `proposals` fica só com `view`/`create`/`delete`. |
| 0.5 | Adiciona dois sub-recursos que faltavam: `proposals_timeline:view` (Linha do tempo, dado de auditoria — mesmo critério de gate próprio já usado em Documentos) e `proposals_offer:manager` (trocar a Oferta, RN-PRP-07 — ação financeiramente significativa, separada da edição geral). `proposals_offer:manager` cobre Novo, Negociação **e Cancelado** — não só os dois primeiros.

## Como usar este documento

Cobre **oito recursos**, todos começando por `proposals` — o recurso base e sete sub-recursos, cada um cobrindo uma parte específica do Card de Proposta. Formato: `recurso:ação:escopo`.

---

## 1. Matriz

### Recurso base

| Ação | Escopos oferecidos |
|---|---|
| `proposals:view` | `self`, `team`, `store`, `all` |
| `proposals:create` | `store` |
| `proposals:delete` | `store`, `all` |

### Sub-recursos — painéis do Card

| Ação | Escopos oferecidos |
|---|---|
| `proposals_documents:view` | `self`, `team`, `store`, `all` |
| `proposals_documents:manager` | `self`, `team`, `store`, `all` |
| `proposals_actors:manager` | `self`, `team`, `store`, `all` |
| `proposals_dates:manager` | `self`, `team`, `store`, `all` |
| `proposals_timeline:view` | `self`, `team`, `store`, `all` |
| `proposals_offer:manager` | `self`, `team`, `store`, `all` |

### Sub-recursos — por Tipo de Status

| Ação | Escopos oferecidos |
|---|---|
| `proposals_new:manager` | `self`, `team`, `store`, `all` |
| `proposals_negotiation:manager` | `self`, `team`, `store`, `all` |
| `proposals_paids:manager` | `team`, `store`, `all` |
| `proposals_canceled:manager` | `self`, `team`, `store`, `all` |

---

## 2. Notas sobre os sub-recursos

**Por que sub-recursos, não ações numa entidade só.** Cada painel do Card (Documentos, Atores, Datas) e cada Tipo de Status precisa poder ser concedido independentemente — um Cargo pode gerenciar Datas sem poder tocar em Documentos, ou avançar uma negociação sem poder marcar como Paga.

**`proposals_documents:view` é separado do `proposals:view` geral.** Ver o Card (nome, grid, tags) não implica ver os Documentos anexados — gate próprio para dado potencialmente sensível, mesmo critério já usado em outros módulos para dado financeiro/sensível.

**`proposals_actors:manager` e `proposals_dates:manager` não têm `view` próprio.** Ver Atores e Datas já vem incluído no `proposals:view` geral — só a **edição** exige a permissão do sub-recurso. Editar respeita, além disso, o travamento por campo (RN-PRP-09 — Ator travado não é editável nem por quem tem a permissão).

**Os quatro por Tipo de Status cobrem edição de dados gerais e a transição para aquele Tipo** — exceto a Oferta, que tem permissão própria (abaixo).

**`proposals_offer:manager` cobre trocar a Oferta (RN-PRP-07) em Novo, Negociação e Cancelado.** Ação financeiramente significativa o bastante para ter permissão separada da edição geral — um Cargo pode editar Datas/Atores sem poder trocar a Oferta, por exemplo. Relevante em três dos quatro Tipos: Novo e Negociação (negociação ainda aberta) e **Cancelado** (a Oferta pode seguir ajustável até o fechamento definitivo daquele caminho). **Não relevante em Pago** — a Oferta já está definitivamente travada como snapshot (RN-PRP-05) antes desse Tipo ser alcançado.

**`proposals_timeline:view` é separado do `proposals:view` geral**, mesmo critério de `proposals_documents:view` — a Linha do tempo é dado de auditoria (quem fez o quê, quando), sensível o bastante para gate próprio.

**`proposals_paids:manager` não oferece `self`.** Marcar como Pago tem efeito financeiro direto (gera valor de meta, RN-PRP-05) — exige ao menos visão de Time, mesmo critério já usado antes.

**Comentário (RN-PRP-10), sempre opcional em qualquer mudança de Status**, é preenchido dentro da mesma ação de transição — não tem permissão própria.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Propostas v0.5.*
