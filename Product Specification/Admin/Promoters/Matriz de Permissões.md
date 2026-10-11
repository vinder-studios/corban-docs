# Corban Admin · Promotoras — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Promotoras (módulo v0.2)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre apenas o recurso deste módulo (`promoters`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso     | Ação         | Escopos oferecidos |
| ----------- | ------------ | ------------------ |
| `promoters` | `view`       | `all`              |
| `promoters` | `create`     | `all`              |
| `promoters` | `edit`       | `all`              |
| `promoters` | `inactivate` | `all`              |
| `promoters` | `reactivate` | `all`              |
| `promoters` | `delete`     | `all`              |

---

## 2. Por que tudo em escopo `all`

Promotora é cadastro **tenant-wide** — não pertence a Loja nem a Time. Mesma estrutura das Matrizes de Segmentos, Produtos e Regiões.

**Nota importante:** a Região associada a uma Promotora **não** é um escopo de permissão. Ela restringe quais Lojas enxergam as ofertas derivadas dela (RN-PRM-02) — isso é regra de negócio, não controle de acesso. Um Cargo com `promoters:view:all` vê todas as Promotoras, de todas as Regiões, independentemente da Loja em que o usuário atue.

---

## 3. Notas sobre as ações

**Sem ação própria para associar Regiões.** Associar ou remover Regiões de uma Promotora é parte de `promoters:edit:all` — não tem chave separada. Vale notar que essa ação tem consequência sobre a visibilidade de ofertas (RN-PRM-05), o que é mais impacto do que um "editar" comum sugere.

---

## 4. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Promotoras v0.1._
