# Corban Admin · Cargos — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Acesso
**Documento pai:** Corban Admin · Cargos (módulo v0.1)
**Versão:** 0.1
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                 |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 0.1    | **Cisão da Matriz de Permissões de Usuários & Permissões (v0.3).** Cobre só o recurso `roles`, que já vinha isolado. Sem mudança de conteúdo em relação à v0.3 anterior. |

## Como usar este documento

Cobre o recurso `roles`. Formato da chave, definido no módulo (§3.1): `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ação                 | Escopos oferecidos |
| ------- | -------------------- | ------------------ |
| `roles` | `view`               | `all`              |
| `roles` | `create`             | `all`              |
| `roles` | `edit`               | `all`              |
| `roles` | `manage_permissions` | `all`              |
| `roles` | `inactivate`         | `all`              |
| `roles` | `delete`             | `all`              |

---

## 2. Notas sobre as ações

**`edit` vs. `manage_permissions`.** Editar nome/descrição do Cargo é diferente de editar as linhas de permissão que ele carrega — a segunda ação afeta imediatamente todos os usuários que têm aquele Cargo (RN-CRG-02).

**`inactivate` e `delete` coexistem.** RN-CRG-01: um Cargo em uso só pode ser excluído após migrar seus usuários para outro Cargo; alternativamente pode ser inativado (bloqueia novas atribuições, mantém as existentes). Dois estados-alvo diferentes, duas ações.

---

## 3. Escopo — sempre `all`

Cargo é cadastro tenant-wide — não pertence a Loja nem a Time. Todas as ações são oferecidas apenas em `all`.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (módulo de Usuários, RN-USR-03) — nunca é resolvido por escopo ou linha da matriz, e não é um Cargo.

---

_Fim do documento. Matriz de Permissões — Cargos v0.1._
