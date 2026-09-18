# Corban Admin · Usuários — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Acesso
**Documento pai:** Corban Admin · Usuários (módulo v0.1)
**Versão:** 0.1
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                       |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | **Cisão da Matriz de Permissões de Usuários & Permissões (v0.3).** Cobre os recursos `users` e `sessions`. Sem mudança de conteúdo em relação à v0.3 anterior. |

## Como usar este documento

Cobre os recursos `users` e `sessions`. Formato da chave, definido no módulo de Cargos (§3.1): `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso    | Ação                 | Escopos oferecidos |
| ---------- | -------------------- | ------------------ |
| `users`    | `view`               | `all`, `store`     |
| `users`    | `create`             | `all`              |
| `users`    | `edit`               | `all`              |
| `users`    | `manage_access`      | `all`              |
| `users`    | `manage_permissions` | `all`              |
| `users`    | `inactivate`         | `all`              |
| `users`    | `reactivate`         | `all`              |
| `sessions` | `view`               | `all`, `store`     |
| `sessions` | `manage`             | `all`              |

---

## 2. Notas sobre as ações

**`users:edit` vs. `users:manage_access` vs. `users:manage_permissions`.** Três sensibilidades diferentes, três ações: editar dados cadastrais (nome, e-mail); definir Cargo e Time do usuário; e conceder permissões adicionais avulsas a ele. A última é a mais sensível — permite dar a alguém acesso que o Cargo dele não prevê.

**`users:inactivate` / `users:reactivate` são ações distintas, sem `delete`.** Usuário não tem exclusão definitiva na v1 (RN-USR-05, módulo) — _mas ver a pendência registrada naquele módulo sobre a exceção introduzida em Lojas_. O par inactivate/reactivate cobre o ciclo completo do estado do usuário.

**`sessions:manage` cobre a revogação (RF-USR-02).** Não há ação para o usuário comum sobre as próprias sessões — a revogação das próprias acontece indiretamente (troca de senha, logout), fora do catálogo deste módulo.

---

## 3. Escopo `store` em `users:view` e `sessions:view`

Estas são as duas únicas ações do módulo oferecidas em escopo mais restrito que `all`. `users:view:store` permite a um usuário consultar os demais usuários da Loja do seu Time — típico de um Cargo de gestão intermediária, que precisa enxergar a própria equipe sem poder alterá-la. Todas as ações de escrita são oferecidas apenas em `all`.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (módulo, RN-USR-03) — nunca é resolvido por escopo ou linha da matriz.

---

_Fim do documento. Matriz de Permissões — Usuários v0.1._
