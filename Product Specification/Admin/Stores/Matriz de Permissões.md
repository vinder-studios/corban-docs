# Corban Admin · Lojas — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Vendas
**Documento pai:** Corban Admin · Lojas (módulo v0.6)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Adiciona `manage_credits` e `refund` ao recurso `stores` — Aba Créditos (Adicionar saldo, Estornar), nunca coberta aqui antes. |

## Como usar este documento

Cobre dois recursos: `stores` (Loja) e `teams` (Time) — entidades distintas, com Cargos podendo governar uma sem a outra (ex.: um gestor de RH que só mexe em Times, não em dados cadastrais de Loja). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz — `stores`

| Ação | Escopos oferecidos |
|---|---|
| `view` | `all`, `store` |
| `create` | `all` |
| `edit` | `all`, `store` |
| `manage_segments` | `all`, `store` |
| `manage_credits` | `all`, `store` |
| `refund` | `all`, `store` |
| `inactivate` | `all`, `store` |
| `reactivate` | `all`, `store` |
| `delete` | `all` |

## 2. Matriz — `teams`

| Ação | Escopos oferecidos |
|---|---|
| `view` | `all`, `store`, `team` |
| `create` | `all`, `store` |
| `edit` | `all`, `store` |
| `inactivate` | `all`, `store` |
| `reactivate` | `all`, `store` |
| `delete` | `all`, `store` |

---

## 3. Por que `create` e `delete` de Loja são exclusivos de `all`

Abrir ou fechar uma unidade de venda é decisão de tenant, não de quem já trabalha numa Loja específica — não existe "gerente de loja que cria a própria loja". `delete` também carrega a cascata de soft-delete de usuários (RN-LOJ-08), sensível demais para escopo `store`.

## 4. Por que `teams:view` chega a `team`

É o único par recurso:ação deste módulo — e um dos poucos do produto — que oferece os três escopos não-`all`. Faz sentido: um operador comum, sem nenhuma permissão de gestão, ainda precisa saber quem são os colegas do próprio Time (para redistribuição informal, por exemplo). `store` e `all` seguem a hierarquia normal (gestor de loja vê todos os times dela; gestor de tenant vê tudo).

## 5. `stores:manage_segments`, `stores:manage_credits` e `stores:refund` são ações próprias, separadas de `edit`

Mesma lógica já usada em outros módulos (ex.: `roles:manage_permissions` separado de `roles:edit`): mexer nas flags "Ativo"/"Recebe leads" por Segmento, adicionar saldo ou estornar um débito têm efeito operacional ou financeiro direto — mais sensíveis que editar nome ou endereço da Loja. `refund` é a mais sensível das três (reverte uma transação já aplicada) — por isso separada de `manage_credits`, não incluída nela: um Cargo pode adicionar saldo sem poder estornar.

---

## 6. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Lojas v0.2.*
