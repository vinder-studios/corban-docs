# Corban Admin · Regiões — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Regiões (módulo v0.1)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`regions`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos |
|---|---|---|
| `regions` | `view` | `all` |
| `regions` | `create` | `all` |
| `regions` | `edit` | `all` |
| `regions` | `inactivate` | `all` |
| `regions` | `reactivate` | `all` |
| `regions` | `delete` | `all` |

---

## 2. Por que tudo em escopo `all`

Região é cadastro **tenant-wide** — não pertence a Loja nem a Time. Escopos `store`, `team` e `self` não têm significado. Mesma estrutura das Matrizes de Segmentos e Produtos, pelo mesmo motivo.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Regiões v0.1.*
