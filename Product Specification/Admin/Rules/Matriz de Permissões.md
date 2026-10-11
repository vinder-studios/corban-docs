# Corban Admin · Regras — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Oferta comercial
**Documento pai:** Corban Admin · Regras (módulo v0.5)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`rules`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ação     | Escopos oferecidos |
| ------- | -------- | ------------------ |
| `rules` | `view`   | `all`              |
| `rules` | `create` | `all`              |
| `rules` | `edit`   | `all`              |
| `rules` | `delete` | `all`              |

**Sem `inactivate` e `reactivate`.** Regra não tem estado ativo/inativo (módulo §6) — o ciclo de vida é criar/editar/excluir. É o segundo módulo do produto assim, ao lado de Origem de Venda.

---

## 2. Por que tudo em escopo `all`

Regra é cadastro **tenant-wide** — não pertence a Loja nem a Time. Mesma estrutura dos demais cadastros do produto.

---

## 3. Notas sobre as ações

**`edit` afeta ofertas em produção.** Alterar requisitos ou comportamento de uma Regra vale imediatamente para todas as Ofertas que a utilizam (RN-RGR-08). Um Cargo com `rules:edit:all` altera, de fato, quem se qualifica para o quê — é permissão com efeito comercial direto, não configuração inerte.

**`delete` é a mais consequente do módulo.** Excluir uma Regra a desvincula de todas as Ofertas em lote (RN-RGR-07). Se a Regra era bloqueante, o resultado é **ampliar** o público de várias ofertas de uma vez (RN-RGR-10). É razoável reservá-la a Cargos mais restritos que `edit`.

**Sem ação própria para compor requisitos.** Adicionar, editar e remover requisitos faz parte de `rules:edit:all` — não tem chave separada, apesar de ser onde mora a lógica de negócio da Regra.

---

## 4. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Regras v0.1._
