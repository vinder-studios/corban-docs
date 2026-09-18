# Corban Admin · Lojas — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Vendas
**Documento pai:** Corban Admin · Lojas (módulo v0.2)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre dois recursos: `stores` (Loja) e `teams` (Time) — entidades distintas, com Cargos podendo governar uma sem a outra (ex.: um gestor de RH que só mexe em Times, não em dados cadastrais de Loja). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz — `stores`

| Ação              | Escopos oferecidos |
| ----------------- | ------------------ |
| `view`            | `all`, `store`     |
| `create`          | `all`              |
| `edit`            | `all`, `store`     |
| `manage_segments` | `all`, `store`     |
| `inactivate`      | `all`, `store`     |
| `reactivate`      | `all`, `store`     |
| `delete`          | `all`              |

## 2. Matriz — `teams`

| Ação         | Escopos oferecidos     |
| ------------ | ---------------------- |
| `view`       | `all`, `store`, `team` |
| `create`     | `all`, `store`         |
| `edit`       | `all`, `store`         |
| `inactivate` | `all`, `store`         |
| `reactivate` | `all`, `store`         |
| `delete`     | `all`, `store`         |

---

## 3. Por que `create` e `delete` de Loja são exclusivos de `all`

Abrir ou fechar uma unidade de venda é decisão de tenant, não de quem já trabalha numa Loja específica — não existe "gerente de loja que cria a própria loja". `delete` também carrega a cascata de soft-delete de usuários (RN-LOJ-08), sensível demais para escopo `store`.

## 4. Por que `teams:view` chega a `team`

É o único par recurso:ação deste módulo — e um dos poucos do produto — que oferece os três escopos não-`all`. Faz sentido: um operador comum, sem nenhuma permissão de gestão, ainda precisa saber quem são os colegas do próprio Time (para redistribuição informal, por exemplo). `store` e `all` seguem a hierarquia normal (gestor de loja vê todos os times dela; gestor de tenant vê tudo).

## 5. `stores:manage_segments` é ação própria, separada de `edit`

Mesma lógica já usada em outros módulos (ex.: `roles:manage_permissions` separado de `roles:edit`): mexer nas flags "Ativo"/"Recebe leads" por Segmento tem efeito operacional direto (liga/desliga entrada de leads) — mais sensível que editar nome ou endereço da Loja.

---

## 6. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Lojas v0.1._
