# Corban Admin · Origem de Venda — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Origem de Venda (módulo v0.4)
**Versão:** 0.2
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`sale_origins`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso        | Ação     | Escopos oferecidos                    |
| -------------- | -------- | ------------------------------------- |
| `sale_origins` | `view`   | — _(sem permissão: acesso universal)_ |
| `sale_origins` | `create` | `all`, `store`                        |
| `sale_origins` | `delete` | `all`                                 |

**Sem `edit`, `inactivate` nem `reactivate.`** A entidade tem um campo só (nome) e nenhum estado — o ciclo de vida é criar/excluir, sem edição nem inativação (módulo §6).

---

## 2. Três níveis de acesso deliberadamente diferentes

Este módulo é o único da seção em que as três ações têm tratamentos distintos. Cada um responde a um risco diferente:

**`view` — sem permissão, acesso universal.** Qualquer usuário autenticado enxerga a lista de Origens. Não há chave no catálogo para isso. A razão: o campo vive dentro do cadastro de lead, e esconder a lista de alguém que cadastra leads produziria leads sem origem — o oposto do que o cadastro existe para garantir.

**`create` — `all` e `store`.** A Origem é criada no ponto de uso, no meio de um atendimento. Exigir escopo `all` significaria que só um administrador do tenant poderia criar uma origem nova, quebrando justamente o fluxo que este módulo existe para permitir. Um usuário com `create:store` cria Origens que **todo o tenant** passa a ver — o escopo qualifica quem age, não restringe o dado (RN-ORI-05).

**`delete` — apenas `all`.** Aqui o escopo `store` **não** é oferecido, deliberadamente: excluir uma Origem afeta todas as Lojas, inclusive as que a estão usando. Dar esse poder a um usuário de escopo de loja seria deixá-lo apagar algo alheio. Criar é barato e reversível; excluir não é.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Origem de Venda v0.2._
