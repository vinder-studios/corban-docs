# Corban CRM · Rankings — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Rankings (módulo v0.4)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial — só `view` (ranking fixo, sem CRUD). |
| 0.2 | Ranking vira entidade configurável — adiciona `create`, `edit`, `delete`. |
| 0.3 | Remove `pair_tv`/`manage_tvs` (pareamento removido) — substitui por `manage_tv_link`. |

## Como usar este documento

Cobre o recurso `rankings`. Formato: `recurso:ação:escopo`.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `store` |
| `create` | `store` |
| `edit` | `store` |
| `delete` | `store` |
| `manage_tv_link` | `store` |

---

## 2. Notas sobre as ações

**Todas em `store`**, mesmo critério de sempre neste módulo — Rankings são da Loja inteira.

**`create`/`edit`/`delete` cobrem o CRUD do Ranking em si** (Título, Tipo, Critério, Cargos elegíveis, Exibir no Modo TV) — separadas de `view`, que só consulta.

**`manage_tv_link` cobre ver, copiar e gerar um novo link do Modo TV** — ação única, independente do CRUD de Ranking.

**O Modo TV em si não tem permissão própria** — acessado pelo link com token, sem sessão de usuário.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Rankings v0.3.*
