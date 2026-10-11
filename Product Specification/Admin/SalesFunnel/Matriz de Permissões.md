# Corban Admin · Funis de Vendas — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Vendas
**Documento pai:** Corban Admin · Funis de Vendas (módulo v0.10)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | **Remove a ação `create`** — não existe mais fluxo manual de criação de funil; ele nasce automaticamente com o Segmento (RN-FNL-02). |

## Como usar este documento

Cobre o recurso deste módulo (`funnels`). Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos |
|---|---|---|
| `funnels` | `view` | `all` |
| `funnels` | `edit` | `all` |
| `funnels` | `manage_pricing` | `all` |
| `funnels` | `inactivate` | `all` |
| `funnels` | `reactivate` | `all` |

**Sem `create`.** Não existe ação de criar Funil — ele nasce automaticamente junto com o Segmento (RN-FNL-02, módulo v0.5). A permissão que efetivamente cria o Funil, de forma indireta, é `segments:create:all`, no catálogo de Segmentos.

**Sem `delete`.** Funil já ativado nunca tem exclusão. Um Funil nunca ativado só é excluído em cascata pela exclusão do Segmento pai — governada por `segments:delete:all`, não por nenhuma permissão deste módulo.

---

## 2. Por que `manage_pricing` é ação separada de `edit`

As abas Informações gerais, Stages, Fluxos, Tags e Layout de Lead são todas cobertas por `funnels:edit:all` — mudam a estrutura do pipeline (ou sua apresentação), não valores financeiros. A aba **Preço das leads** carrega custo e preço, informação sensível o bastante para merecer permissão própria, no mesmo espírito de outras permissões financeiras já isoladas no produto (ex.: indicadores financeiros do Dashboard, na Especificação v1.2).

Consequência: um Cargo pode ter `funnels:edit:all` (configura stages, fluxos, tags) sem `manage_pricing:all` (não vê nem altera custo/preço), ou o inverso — alguém do time financeiro que só mexe em preço, sem tocar na estrutura do funil.

---

## 3. Por que tudo em `all`

Funil é tenant-wide (RN-FNL-01) — a configuração é única para todas as Lojas, não pertence a nenhuma em particular.

---

## 4. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Funis de Vendas v0.1.*
