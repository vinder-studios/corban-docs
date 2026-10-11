# Corban Admin · Produtos — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Produtos (módulo v0.1)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`products`). Formato da chave: `recurso:ação:escopo`. Tabela compacta — uma linha por `recurso:ação`, com os escopos oferecidos.

---

## 1. Matriz

| Recurso    | Ação     | Escopos oferecidos |
| ---------- | -------- | ------------------ |
| `products` | `view`   | `all`              |
| `products` | `manage` | `all`              |

---

## 2. Por que tudo em escopo `all`

Produto é cadastro **tenant-wide**: não pertence a Loja nem a Time, e não tem dono individual. Os escopos `store`, `team` e `self` não têm significado aqui. Um Cargo ou vê o cadastro de Produtos inteiro, ou não vê nada dele — e, nesse caso, o destino de navegação nem é renderizado.

Mesma estrutura da Matriz de Segmentos, pelo mesmo motivo.

---

## 3. Notas sobre as ações

**`inactivate` / `reactivate` / `delete` coexistem.** Produto tem exclusão definitiva (RN-PRD-03) além da inativação. Três ações distintas, concedíveis separadamente — é razoável um Cargo poder inativar mas não excluir.

**Sem ação para Operação.** Operação é catálogo fixo do sistema, sem CRUD — não há recurso `operations` no catálogo de permissões do tenant.

---

## 4. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Produtos v0.1._
