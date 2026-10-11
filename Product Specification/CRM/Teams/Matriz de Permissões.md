# Corban CRM · Times — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM · Grupo Gestão
**Documento pai:** Corban CRM · Times (módulo v0.3)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre o recurso `crm_teams`. Formato da chave: `recurso:ação:escopo`.

> **Por que `crm_teams`, não `teams`.** O Admin já declara `teams` (módulo de Lojas, Matriz de Permissões) — mesma entidade Time, mas com alcance diferente: lá `teams` oferece `all`, aqui só `store` existe, e este módulo soma uma capacidade que o Admin não tem (associar Membro pela tela do Time, RF-TIM-02). Mesmo critério já usado para `members`/`users`: nome de recurso distinto por aplicação até a pendência de arquitetura (prefixo `app:` vs. tabela recurso→aplicação, registrada no guia de acompanhamento) ser resolvida.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `store` |
| `create` | `store` |
| `edit` | `store` |
| `manage_members` | `store` |
| `inactivate` | `store` |
| `delete` | `store` |

---

## 2. Notas sobre as ações

**Todas em `store`, sem `all`/`team`/`self`** — mesmo critério de Membros e Tabelas Multiplicadoras: é gestão da Loja inteira, sem recorte menor definido.

**`manage_members` é ação própria, separada de `edit`.** Cobre RF-TIM-02 (associar/desassociar Membro) — a capacidade que não existe no Admin. `edit` cobre só Nome e Segmento do Time.

**Sem ação de reativar.** Diferente de Membros (que tem `inactivate`/`reactivate` distintos), o ciclo de vida do Time é o de Lojas (Admin) — Ativo/Inativando/Inativo — e reativar durante a drenagem (RN-LOJ-04) é uma consequência de `inactivate`, não uma permissão própria: quem pode inativar também pode reverter antes da drenagem terminar.

**`delete` inclui a escolha de migrar membros ou redistribuir (RN-LOJ-05)** — não é uma ação separada.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Times v0.1.*
