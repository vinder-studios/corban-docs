# Corban Admin · Ofertas — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Oferta comercial
**Documento pai:** Corban Admin · Ofertas (módulo v0.12)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.2 | O texto sobre o que `offers:edit:all` cobre passa a incluir o Segmento. A matriz de permissões em si não muda. |
| 0.1 | Versão inicial. |

## Como usar este documento

Cobre o recurso `offers`. Formato da chave: `recurso:ação:escopo`.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `all` |
| `create` | `all` |
| `edit` | `all` |
| `manage_rules` | `all` |
| `manage_pricing` | `all` |
| `inactivate` | `all` |
| `reactivate` | `all` |
| `delete` | `all` |

---

## 2. Por que tudo em `all`

Oferta é cadastro tenant-wide — não pertence a Loja nem a Time. A visibilidade por Loja (via Região da Promotora) é regra de negócio aplicada no consumo (Simulações), não escopo de permissão administrativa (mesmo raciocínio já registrado em Regiões, RN-REG-04, e reforçado em Promotoras).

## 3. Por que `manage_rules` e `manage_pricing` são ações próprias, separadas de `edit`

Mesmo padrão já usado em Cargos (`manage_permissions`) e Funis (`manage_pricing`):

- **`manage_rules`** — associar Regras, preencher valores de requisitos e sobrescrever comportamento (RN-OFR-03, RN-RGR-05) é decisão de elegibilidade, sensível o bastante para não estar embutida em "editar dados da oferta".
- **`manage_pricing`** — taxa, prazo e comissão são dados financeiros, mesmo tratamento já dado a indicadores financeiros em outros módulos.

Um Cargo pode ter `offers:edit:all` (mexe em Segmento/Promotora/Produto/Banco/Operação) sem ter `manage_pricing:all` nem `manage_rules:all` — um gestor de catálogo comercial que não define preço nem elegibilidade, por exemplo.

## 4. Por que `delete` existe sem par de "bloqueio"

Diferente de quase todo o resto do produto, `offers:delete:all` não tem regra de bloqueio (RN-OFR-05) — mas segue restrita a `all` e a confirmação reforçada, por ser ação crítica mesmo sem vínculo bloqueante.

---

## 5. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Ofertas v0.2.*
