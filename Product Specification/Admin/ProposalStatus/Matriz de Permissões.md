# Corban Admin · Status de Proposta — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Status de Proposta (módulo v0.2)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`proposal_statuses`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso             | Ação         | Escopos oferecidos |
| ------------------- | ------------ | ------------------ |
| `proposal_statuses` | `view`       | `all`              |
| `proposal_statuses` | `create`     | `all`              |
| `proposal_statuses` | `edit`       | `all`              |
| `proposal_statuses` | `inactivate` | `all`              |
| `proposal_statuses` | `reactivate` | `all`              |
| `proposal_statuses` | `delete`     | `all`              |

---

## 2. Por que tudo em escopo `all`

Status de Proposta é cadastro **tenant-wide** — não pertence a Loja nem a Time. Mesma estrutura de Segmentos, Produtos, Regiões e Promotoras.

Vale notar o contraste com **Origem de Venda**, o outro cadastro consumido no meio de um fluxo de trabalho: lá `create` é oferecido em `store`, porque a criação acontece durante o atendimento. Aqui não — Status de Proposta é estrutura do processo comercial, definida uma vez pelo tenant, não algo que se cria no meio de uma proposta.

---

## 3. Notas sobre as ações

**`edit` carrega mais peso do que aparenta.** Inclui alterar o **tipo** de um status, que reclassifica retroativamente todas as propostas vinculadas (RN-STP-04) — uma proposta contada como conversão pode deixar de sê-lo. É a única permissão de "editar cadastro" do produto com efeito sobre números de produção já apurados.

**`inactivate` e `delete` disparam migração, não bloqueio.** Diferente dos demais cadastros, quem tem essas permissões pode mover propostas de um status para outro no processo (RN-STP-02). Na prática, são permissões de manipulação de dados de proposta, não só de cadastro.

---

## 4. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Status de Proposta v0.1._
