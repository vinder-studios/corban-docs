# Corban CRM · Simulações — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Simulações (módulo v0.13)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.2 | Simulações de origem Marketing (autor "Sistema") visíveis a quem tem `view` no escopo que alcança o Cliente. Nenhuma chave muda. |
| 0.1 | Versão inicial. |

## Como usar este documento

Cobre o recurso `simulations`. Formato da chave: `recurso:ação:escopo`, mesmo padrão já usado em Chat e Leads (CRM).

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `create` | `self`, `team`, `store`, `all` |
| `view` | `self`, `team`, `store`, `all` |

---

## 2. Notas sobre as ações

**`create` e `view` são separadas, mas frequentemente andam juntas.** `create` cobre a Página de Simulação (Nova simulação) e o Sheet contextual; `view` cobre o Histórico. Um Cargo pode ter só `view` — consulta o que já foi simulado, sem poder rodar novas.

**Escopos seguem o mesmo critério de `self` já fixado em Chat e Leads** — `self` = simulações que o próprio usuário criou (autor); `team`/`store`/`all` ampliam progressivamente. Simulações automáticas via Lead (RN-SIM-04, autor "Sistema") são visíveis a quem tem `view` no escopo que alcança a Lead correspondente. As de origem **Marketing** (RN-SIM-01, autor "Sistema") ficam no histórico do Cliente e são visíveis a quem tem `view` no escopo que alcança o Cliente.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Simulações v0.1.*
