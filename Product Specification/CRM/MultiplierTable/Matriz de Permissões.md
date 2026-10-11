# Corban CRM · Tabelas Multiplicadoras — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Tabelas Multiplicadoras (módulo v0.4)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Esclarece que `edit` cobre Multiplicador **e** Status (Ativa/Inativa) — uma permissão só, sem separar. |

## Como usar este documento

Cobre o recurso `multipliers`. Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `store` |
| `edit` | `store` |

---

## 2. O que `edit` cobre

`edit` cobre as duas coisas editáveis no Sheet — **Multiplicador e Status** (Ativa/Inativa). Não há permissão separada para inativar: os dois são ajustes da mesma configuração.

## 2a. Por que só `store`, sem `self`/`team`/`all`

O Multiplicador é uma configuração **da Loja inteira**, não de um Time ou usuário específico — não há recorte menor que faça sentido. E não é dado tenant-wide (`all`) — cada Loja só configura e vê a própria tabela, nunca a de outra.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Tabelas Multiplicadoras v0.2.*
