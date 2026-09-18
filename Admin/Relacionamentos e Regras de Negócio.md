# Corban Admin · Relacionamentos e Regras de Negócio

**Status:** Draft
**Aplicação:** Admin
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.3
**Data:** 18/09/2026

## Histórico de revisões

| Versão | Mudanças |
| ------ | -------- |
| 0.1 | Versão inicial. |
| 0.2 | Revisão completa com base nos specs de módulo em `apps/admin/doc/*/spec.md`. Corrige cardinalidade Segmento↔Funil (1:N → 1:1 obrigatório), Loja↔Time (N:N → 1:N), e as regras de bloqueio de Região, Promotora e Status de Proposta, que estavam incorretas ou trocadas entre entidades. **Promotora → Oferta usa a mesma regra de Produto → Oferta** (inativar ofertas ou substituir), confirmado pelo usuário como o comportamento correto — o spec `Promoters/spec.md` (RN-PRM-03, bloqueio total sem alternativa) está desatualizado e precisa ser corrigido separadamente. |
| 0.3 | Corrige **Região → Promotora/Loja**: inativação **não é bloqueada** (apenas a exclusão), só exige informar a quantidade de vínculos e o efeito de a entidade afetada tornar-se sem restrição regional se aquela era sua única Região — `Regions/spec.md` (RN-REG-02) está desatualizado. Corrige **Regra**: passa a admitir **inativação** (informando efeito nas novas/atuais associações de Oferta) e **reativação** (informando as Ofertas afetadas para decisão do gestor) — `Rules/spec.md` (RN-RGR-09) está desatualizado. |

## Como usar este documento

Este documento é a visão consolidada de relacionamentos entre entidades do Admin — cada relação remete ao módulo dono da regra (spec correspondente em `apps/admin/doc/<Módulo>/spec.md`), quando existe. Não substitui os documentos de módulo; existe para dar uma leitura rápida do grafo de dependências e evitar decisões inconsistentes entre módulos.

---

# 🏪 Loja

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Loja → Time** | `1 : N` | 🗑️ Ao excluir uma Loja, **excluir os Times em cascata** (RN-LOJ-09) — mas a exclusão da Loja só é possível se **não houver itens ativos em nenhum Time** (RN-LOJ-07), então os Times já estão vazios no momento da cascata. Time **nunca** pode ser transferido de Loja, é imutável após a criação (RN-LOJ-10). |
| **Loja → Segmento** | `N : N` | Não é vínculo de pertencimento — é uma associação com duas flags por Segmento: **Ativo** e **Recebe leads**. "Recebe leads" exige "Ativo" marcado (RN-LOJ-01). |
| **Loja → Região** | `N : N` | Loja nasce com valor explícito "Todas" (ausência de restrição = enxerga tudo). Loja sem Região vê todas as ofertas (RN-REG-04). |
| **Loja → Usuário** | `1 : N` (via Time) | Ao excluir a Loja, oferece **soft-delete** dos usuários associados exclusivamente a ela (RN-LOJ-08). |

---

# 🌎 Região

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Região → Promotora** | `N : N` | ⚠️ Ao inativar ou excluir, **informar a quantidade de Promotoras relacionadas**. A associação **não bloqueia a inativação**, apenas a **exclusão**. Ao inativar, informar que, se a Promotora afetada tiver **apenas** aquela Região, ela passa a ter acesso a **todas as regiões** (torna-se global). |
| **Região → Loja** | `N : N` | ⚠️ Ao inativar ou excluir, **informar a quantidade de Lojas relacionadas**. A associação **não bloqueia a inativação**, apenas a **exclusão**. Ao inativar, informar que, se a Loja afetada tiver **apenas** aquela Região, ela passa a ter acesso a **todas as regiões**. |
| **Região → Oferta** | *(sem vínculo direto)* | Oferta **não se associa a Região**. A região de uma Oferta é **derivada** da Promotora que a compõe (RN-REG-04 / RN-PRM-02): Promotora sem Região → oferta global; Promotora com Região(ões) → visível às Lojas com ao menos uma região em comum. |

---

# 📦 Produto

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Produto → Oferta** | `1 : N` | ⚠️ Ao inativar um Produto com Oferta vinculada, **informar a quantidade de ofertas relacionadas** e exigir escolha: **inativar todas as Ofertas afetadas** ou **substituir o Produto por outro Produto ativo** nessas Ofertas (RN-PRD-02). |
| **Produto → Oferta** | `1 : N` | 🔒 Exclusão definitiva só permitida se **não houver nenhuma Oferta vinculada** (RN-PRD-03). |

---

# 🏢 Promotora

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Promotora → Oferta** | `1 : N` | ⚠️ Ao inativar uma Promotora com Oferta vinculada, **informar a quantidade de ofertas relacionadas** e exigir escolha: **inativar todas as Ofertas afetadas** ou **substituir a Promotora por outra Promotora ativa** nessas Ofertas — mesma regra de Produto → Oferta. *(Spec `Promoters/spec.md`, RN-PRM-03, ainda descreve bloqueio total sem alternativa — está desatualizado; atualizar naquele documento.)* |
| **Promotora → Oferta** | `1 : N` | 🔒 A exclusão só é permitida quando **não houver ofertas relacionadas**. |
| **Promotora → Região** | `N : N` | Zero ou mais Regiões. Remover a **última** Região exige confirmação explícita: as Ofertas da Promotora passam a ser **globais** (RN-PRM-05). Associar a primeira Região **reduz** o alcance (contraintuitivo — a interface precisa avisar). |

---

# 🎁 Oferta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Oferta → Promotora** | `N : 1` | Obrigatório. Sem restrição de unicidade com Produto+Banco+Operação — duplicatas são permitidas livremente (RN-OFR-01). |
| **Oferta → Produto** | `N : 1` | Obrigatório. |
| **Oferta → Banco** | `N : 1` | Obrigatório, mas **Banco não é cadastro interno** — buscado via API do Banco Central. Não há tabela própria de Bancos no produto. |
| **Oferta → Operação** | `N : 1` | Obrigatório. **Operação é catálogo fixo do sistema** (Novo, Refinanciamento, Portabilidade, Portabilidade com Refinanciamento, Compra de Dívida) — sem CRUD, tenant não gerencia. Determina o valor-base da comissão (RN-OFR-07). |
| **Oferta → Regra** | `N : N` | Zero ou mais. A associação carrega dado próprio: os **valores de cada requisito** da Regra (a Regra só define Campo+Operador) e, opcionalmente, uma **sobrescrita** do comportamento padrão Bloqueia/Avisa. |
| **Oferta** | — | 🔒 Exclusão **não é bloqueada por nada** — nem Regra, nem simulação/proposta vinculada (snapshot imutável preserva histórico) — mas exige confirmação reforçada por ser ação crítica (RN-OFR-05). |

---

# 📋 Regra

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Regra → Requisito** | `1 : N` (mínimo 1, obrigatório) | Requisito carrega só **Campo + Operador** (template) — o valor é preenchido por Oferta, não pela Regra (RN-RGR-12). |
| **Regra → Oferta** | `N : N` | ⚠️ Ao excluir, **nenhum vínculo bloqueia** — a Regra é **desvinculada em lote de todas as Ofertas automaticamente**, com alerta informando quantas Ofertas serão afetadas, agrupadas por comportamento efetivo (Bloqueia/Avisa) de cada uma (RN-RGR-07, RN-RGR-10). Editar Campo/Operador de um requisito é **bloqueado se houver Oferta Ativa associada**; permitido com aviso se só houver Inativas (RN-RGR-13). |
| **Regra** | — | ⚠️ Regra **pode ser inativada** — a partir desta revisão, deixa de ser só criar/editar/excluir. Ao inativar: informar que a Regra **não poderá ser associada a novas Ofertas** e que passa a ser **desconsiderada em todas as Ofertas nas quais já está associada** (deixa de ser avaliada, sem removê-la do cadastro de cada Oferta). Ao reativar: **informar quais Ofertas serão afetadas** pela reativação, para que o gestor decida, por Oferta, se mantém a Regra associada (voltando a valer) ou a remove antes de reativar. |

---

# 🏷️ Segmento

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Segmento → Funil** | `1 : 1` (obrigatória) | 🔒 Todo Segmento nasce automaticamente com seu Funil, já populado com stages sugeridos, em status "Aguardando configuração" (RN-SEG-05). Não existe Segmento sem Funil, nem Funil sem Segmento. Segmento do Funil é **imutável** (RN-FNL-12). |
| **Segmento → Funil** | `1 : 1` | ⚠️ **Cascata parcial e informada**: inativar o Segmento inativa o Funil **automaticamente**, só se o Funil estiver **Ativo** (com aviso explícito antes) — RN-SEG-06. Reativar o Segmento **oferece** reativar o Funil, nunca automático — RN-SEG-07. |
| **Segmento → Funil** | `1 : 1` | 🔒 Exclusão do Segmento só é bloqueada se o Funil **já foi ativado alguma vez** (RN-SEG-04). Se o Funil nunca saiu de "Aguardando configuração", a exclusão é **permitida em cascata** (exclui os dois). |
| **Segmento → Produto / Promotora / Loja / Time** | *(sem vínculo direto)* | Produto, Promotora, Loja e Time **não se associam a Segmento**. A associação, quando existe, é sempre via Funil. |

---

# 🔄 Funil

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Funil → Stage** | `1 : N` | Precisa de ao menos um stage de cada **Tipo** (Novo, Andamento, Formalização, Pago, Cancelado) — RN-FNL-15. Remoção do último stage de um tipo é bloqueada. |
| **Funil → Fluxo** | `1 : N` | Fluxo é só um **registro-espelho** de automação da Huggy — o Corban não a executa (RN-FNL-04). Fluxo sem nenhum stage permitido não é removido, fica cadastrado sem exibição (RN-FNL-11). |
| **Funil → Tag** | `1 : N` | Também espelha cadastro da Huggy. Categoria (Audiência/Origem/Departamento) é enum fixo do sistema; tags dentro dela são livres (RN-FNL-05). |
| **Funil → Preço de Lead** | `1 : 2` (fixo: Interno e Externo) | Duas linhas fixas, não criáveis nem excluíveis (RN-FNL-07). Margem pode ser negativa; sistema sinaliza, não bloqueia (RN-FNL-08). |
| **Funil → Segmento** | `1 : 1` | Ver seção Segmento. |
| **Funil → Lead** | `1 : N` | Fora do escopo dos módulos especificados até agora (CRM/Leads sem spec própria) — mantido como relação aspiracional. Drenagem: inativar o Funil não remove leads em curso (tipo Novo/Andamento/Formalização); ele só some da visão das Lojas quando a última lead sair desses tipos (RN-FNL-14). |
| **Funil** | — | 🔒 Nunca é excluído por ação direta neste módulo. Um Funil já ativado alguma vez nunca se exclui, em hipótese alguma (RN-FNL-09). Só é excluído em cascata quando o Segmento pai é excluído, e só se nunca tiver saído de "Aguardando configuração". |
| **Stage → Fluxo** | `N : N` | Fluxo declara "Stages permitidos". |

---

# 📊 Status da Proposta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Status → Proposta** | `1 : N` | ⚠️ **Não bloqueia** inativação nem exclusão. Propostas vinculadas disparam um **fluxo de migração**: o gestor escolhe outro Status (do **mesmo Tipo** — Andamento/Pago/Cancelado, RN-STP-05) ou "Sem status" para elas, e a ação prossegue (RN-STP-02). |
| **Status → Proposta** | `1 : N` | ⚠️ Alterar o **Tipo** de um Status em uso **reclassifica retroativamente** todas as propostas vinculadas — ação permitida, mas exige aviso explícito (RN-STP-04). É uma saída indireta para o que a migração por tipo (RN-STP-05) normalmente impede. |

---

# ⚠️ Entidades sem spec/implementação própria ainda

- **Banco** — sem cadastro; vem da API do Banco Central (RN-RGR-03, RN-OFR).
- **Operação** — catálogo fixo do sistema, sem CRUD (spec de Produtos, §3).
- **Lead / Proposta (entidade completa)** — só o módulo de **Status de Proposta** tem spec; o fluxo de Lead/CRM e a entidade Proposta em si ainda não têm documento próprio.

---

# 📌 Pendências entre documentos

1. **`Promoters/spec.md` (RN-PRM-03) desatualizado** — descreve bloqueio total de inativação/exclusão sem alternativa. Deve ser corrigido para espelhar RN-PRD-02/RN-PRD-03 (inativar ofertas ou substituir a Promotora; exclusão bloqueada só com ofertas vinculadas).
2. **`Regions/spec.md` (RN-REG-02) desatualizado** — descreve bloqueio de inativação **e** exclusão por vínculo com Promotora/Loja. O comportamento correto é: inativação **livre**, apenas informando a quantidade de Promotoras/Lojas afetadas e o efeito colateral (a entidade afetada, se só tinha aquela Região, passa a ser global/sem restrição regional); só a **exclusão** é bloqueada por vínculo.
3. **`Rules/spec.md` (RN-RGR-09, RN-RGR-06 §6) desatualizado** — descreve Regra sem estados, ciclo de vida só criar → editar → excluir. O comportamento correto inclui **inativação**: Regra inativa não pode ser associada a novas Ofertas e é desconsiderada nas Ofertas onde já está associada; reativar exige informar as Ofertas afetadas para o gestor decidir se mantém ou remove a associação.
4. **RN-USR-05** (módulo de Usuários & Permissões) — ainda afirma que exclusão definitiva de usuário não existe; contradiz RN-LOJ-08 (soft-delete de usuários na cascata de exclusão de Loja). Pendência já registrada no doc de Lojas, não resolvida aqui.

---

_Fim do documento. Relacionamentos e Regras de Negócio v0.3 — visão consolidada, Admin._
