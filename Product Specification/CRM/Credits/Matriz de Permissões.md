# Corban CRM · Créditos — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Créditos (módulo v0.3)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial — só `view`, módulo era inteiramente de consulta. |
| 0.2 | Reescrita pós-reformulação do módulo — adiciona `add_credit` e `edit_daily_limit`. Estorno (RF-CRD-05) não ganha permissão aqui — é ação do Admin, fora deste recurso. |
| 0.3 | Acompanha Créditos v0.6: `add_credit` cobre só o Pix (AbacatePay); Boleto fica para evolução. |

## Como usar este documento

Cobre o recurso `credits`. Formato: `recurso:ação:escopo`.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `store` |
| `add_credit` | `store` |
| `edit_daily_limit` | `store` |

---

## 2. Notas sobre as ações

**Todas em `store`**, mesmo critério já usado em Tabelas Multiplicadoras e Membros — é gestão da Loja inteira, sem recorte menor.

**`add_credit` cobre o fluxo de Pix (RF-CRD-03)** — iniciar o pedido, ver o QR Code e o código copia-e-cola, não a confirmação em si (automática, via AbacatePay).

**`edit_daily_limit` é separada de `add_credit`** — um Cargo pode permitir adicionar crédito sem poder mexer no teto de gasto diário, ou vice-versa.

**Sem permissão para Estorno.** RF-CRD-05 é acionado pelo Admin (módulo de Lojas) — o resultado aparece no Extrato daqui, coberto por `view`, mas a ação de estornar não pertence a este recurso.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Créditos v0.2.*
