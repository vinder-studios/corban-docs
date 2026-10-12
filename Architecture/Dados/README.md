# SmartCorbans — Modelo de dados

**Status:** Draft
**Versão:** 0.2
**Data:** 10/10/2026
**Base:** C4 · Componentes da API v0.8, ADR-0003, ADR-0012, ADR-0023 e os documentos de Relacionamentos e Regras de Negócio (Admin v0.4, CRM v0.2, Marketing v0.2)

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.2 | Nomes de schema, tabela, coluna e valores de enum passam a **inglês**, em `snake_case` (decisão do usuário). **Acesso fica fora do modelo**: usuários, Cargos e permissões ficam com o Keycloak (decisão do usuário); os outros módulos referenciam o usuário pelo id do Keycloak. |
| 0.1 | Convenções e ordem dos módulos. Primeiro módulo: Acesso. |

## Como usar

> **Catálogo publicado em HTML:** Todos os 12 módulos de dados (15 schemas relacionais: 11 de domínio + 4 de apoio) e diagramas ER interativos estão disponíveis no [Catálogo de Persistência (HTML)](index.html).

Um documento por módulo da API, com o diagrama ER (Mermaid) e as tabelas. É o modelo **lógico** que os `DbContext`s implementam (ADR-0012); o físico (tipos exatos, índices finos) fecha nas migrations. Cada módulo é validado antes do seguinte. O texto dos documentos é em português; os nomes no banco, em inglês.

| # | Módulo | Schema | Documento | Situação |
|---|---|---|---|---|
| — | Acesso | — | [01-acesso](01-acesso/README.md) | **Fora do modelo** (Keycloak) |
| 1 | Estrutura | `structure` | [01-estrutura](01-estrutura/README.md) | Validado |
| 2 | Funis | `funnels` | [02-funis](02-funis/README.md) | Validado |
| 3 | Catálogo comercial | `catalog` | [03-catalogo](03-catalogo/README.md) | Validado |
| 4 | Clientes | `clients` | [04-clientes](04-clientes/README.md) | Validado |
| 5 | Leads e Distribuição | `leads` | [05-leads](05-leads/README.md) | Validado |
| 6 | Créditos | `credits` | [06-creditos](06-creditos/README.md) | Validado |
| 7 | Motor de Simulação | `simulation` | [07-simulacao](07-simulacao/README.md) | Validado |
| 8 | Propostas | `proposals` | [08-propostas](08-propostas/README.md) | Validado |
| 9 | Atendimento | `messaging` | [09-atendimento](09-atendimento/README.md) | Validado |
| 10 | Marketing | `marketing` | [10-marketing](10-marketing/README.md) | Validado |
| 11 | Desempenho | `performance` | [11-desempenho](11-desempenho/README.md) | Validado |
| 12 | Apoio: Auditoria, Webhooks, Jobs, Notificações | `audit`, `integration`, `jobs`, `notifications` | [12-apoio](12-apoio/README.md) | Validado |

## Convenções

1. **Um schema por módulo** (ADR-0012). Schema, tabela, coluna e valores de enum em **inglês**, `snake_case`; tabela no singular. Os termos seguem o glossário de `naming.md` do front (`store`, `team`, `segment`, `client`, `goal_value`…).
2. **Chave primária `id uuid`**, gerada pela aplicação em **UUID v7** (ordenável no tempo, sem ida ao banco antes do insert). Tabelas de associação usam chave composta.
3. **Referência dentro do módulo:** FK normal. **Referência a outro módulo:** só a coluna `<entity>_id`, indexada, **sem FK** (ADR-0023). Nos diagramas e tabelas aparece como *ref. lógica → `schema.table`*.
4. **Usuário:** colunas `*_user_id` (`operator_user_id`, `created_by_user_id`…) guardam o **id do usuário no Keycloak** (`sub`). Não há tabela de usuários no banco (Acesso fora do modelo).
5. **Datas** em `timestamptz` (UTC). Toda tabela de cadastro tem `created_at` e `updated_at`. A trilha completa das ações críticas vai para a Auditoria (RNF005).
6. **Enums** como `text` com `CHECK`, valores em inglês `snake_case` (`active`, `awaiting_setup`).
7. **Exclusão suave** (RN-SC-08): `deleted_at`. Unicidade que vale só entre registros vivos usa **índice único parcial** `WHERE deleted_at IS NULL`.
8. **Nome ou código único "ignorando maiúsculas e espaços"** (RN-STP-09, RN-CRG-05, RN-ORI-03) vira coluna gerada `<campo>_normalized = lower(trim(<campo>))` com o índice único.
9. **Sem `tenant_id`.** A instância é single-tenant (spec §1.1). O isolamento é por Loja: tabelas com dado de Loja têm `store_id` e entram no filtro de escopo (ADR-0012).
10. **Snapshot** quando o dado de outro módulo precisa ficar congelado (ADR-0023): colunas copiadas no próprio registro, com prefixo identificável (`offer_snapshot_*`).
11. **Dinheiro** em `numeric(14,2)`; percentuais em `numeric(7,4)`.
12. **Concorrência otimista** com a coluna de sistema `xmin` do PostgreSQL, mapeada pelo EF Core, nas tabelas editadas por mais de uma pessoa.
