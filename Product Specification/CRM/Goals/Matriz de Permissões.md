# Corban CRM · Metas — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Metas (módulo v0.3)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.3 | Acompanha a correção de campos do módulo (Início/Fim, Tipo travado) — sem mudança na matriz em si. |
| 0.2 | Acompanha a reescrita do módulo (Meta vira entidade com Título/Descrição, lista em vez de duas sub-seções) — sem mudança na matriz em si. |

## Como usar este documento

Cobre o recurso `goals`. Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `self`, `team`, `store` |
| `manage` | `team`, `store` |

---

## 2. Notas sobre as ações

**`view` tem `self`** — diferente da maioria dos recursos anexados deste porte, faz sentido aqui: todo Membro consulta a própria meta, mesmo sem nenhuma permissão de gestão. `self` = a Meta do próprio usuário (como Membro) e a do Time a que ele pertence, se houver.

**`manage` não tem `self`.** Configurar a meta de alguém — mesmo a própria, como Membro — é ação de gestão (`team` no mínimo). Ninguém define a própria meta sozinho.

**Sem escopo `all`.** Segue o mesmo critério de Membros, Times e Tabelas Multiplicadoras: sem visão/gestão cross-Loja no CRM.

**Uma permissão só cobre os dois Tipos** (Valor e Quantidade) — não há `goals:manage_value` separado de `goals:manage_quantity`. Continua valendo mesmo com Meta agora sendo uma lista de itens, não dois slots fixos: `manage` cobre criar e editar qualquer Meta, de qualquer Tipo.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Metas v0.3.*
