# Corban Admin · Relatórios — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin
**Documento pai:** Corban Admin · Relatórios (módulo v0.4)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Só atualiza o ponteiro: a matriz em si não muda. O artefato apontava para o módulo v0.2. |

## Como usar este documento

Seis sub-recursos, um por tipo (módulo §3.5) — não um recurso `reports` genérico, porque um Cargo pode ver um tipo sem ver outro (ex.: Produção sem Comissões a pagar). Formato: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ações | Escopo |
|---|---|---|
| `reports_production` | `view`, `export` | `all` |
| `reports_funnel` | `view`, `export` | `all` |
| `reports_proposals_pipeline` | `view`, `export` | `all` |
| `reports_lead_sales` | `view`, `export` | `all` |
| `reports_commissions` | `view`, `export` | `all` |
| `reports_offers_eligibility` | `view`, `export` | `all` |

---

## 2. Notas sobre as ações

**Só `all`, em todos os seis** — RN-REL-03: no Admin, todo relatório é a visão do tenant inteiro. Não há recorte menor aqui.

**`export` é ação separada de `view`.** Mesmo critério já usado em todo o produto (RN065/RNF003 da v1.2) — exportar dado financeiro e de clientes é mais sensível que só visualizar.

**Salvar Visão não tem permissão própria.** É consequência de ter `view` no tipo — quem vê um tipo pode salvar a própria configuração dele como Visão (RF-REL-02).

**`reports_commissions` e `reports_lead_sales` são os mais sensíveis** — valor devido a cada Loja e receita de venda de leads. Esperado que, na prática, só o Owner e o financeiro do tenant tenham esses dois.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Relatórios v0.2.*
