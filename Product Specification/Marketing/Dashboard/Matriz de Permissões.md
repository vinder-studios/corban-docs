# Corban Marketing · Dashboard — Matriz de Permissões

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing · Dashboard (módulo v0.3)
**Versão:** 0.1
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. Define os recursos do Marketing no catálogo de permissões, que o Documento de Produto do Marketing listava como "a consolidar". |

## Como usar este documento

**Este módulo introduz um único recurso no catálogo: `marketing_finance`.** O Dashboard é uma tela agregadora, e cada bloco condiciona sua exibição à permissão de `view` da fonte, definida em outro módulo do Marketing. Mesmo padrão do Dashboard do Admin.

**Aplicação do recurso: Marketing.** Formato da chave: `recurso:ação:escopo`.

| Bloco | Permissão consultada |
|---|---|
| 3.1 Visão geral de aquisição | `prospecting_lists:view` **ou** `sweeps:view` (soma só as fontes que o usuário vê) |
| 3.2 Prospecção | `prospecting_lists:view` |
| 3.3 Carteira Ativa | `sweeps:view` |
| 3.4 Conversão final | `prospecting_lists:view` **ou** `sweeps:view` (só as Leads das fontes que o usuário vê) |
| 3.5 Financeiro, e custos do ranking | `marketing_finance:view` **e** `view` da fonte (`prospecting_lists:view` para Custo da base e Resultado; as duas para a Receita de leads total) |
| Link do ranking para o detalhe (RF-DMB-03) | `view` do destino: `prospecting_lists:view` ou `sweeps:view` |

Quem não tem `view` de nenhuma fonte vê o estado "sem acesso" (RF-DMB-01).

A própria rota `dashboard` **não exige permissão para abrir**: é a tela inicial do Marketing, e quem entra na aplicação já tem alguma permissão nela (Usuários, RN-USR-16).

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos | O que cobre |
|---|---|---|---|
| `marketing_finance` | `view` | `all` | Ver Receita de leads, Custo da base, Resultado da base e os custos por Lead e por Proposta Paga (RN-DMB-04) |

---

## 2. Por que `marketing_finance` é separado

O bloco financeiro mostra **o que as Lojas pagaram** e **o que o tenant gastou nas bases**. É dado financeiro sensível, mesmo critério do Gráfico de Preço de Lead do Admin, que exige `funnels:manage_pricing`. Um Cargo com `prospecting_lists:view` e sem `marketing_finance:view` vê o volume e a conversão, mas não os valores em R$ de receita e custo.

**Só o escopo `all`**, porque o Dashboard não filtra por Loja (RN-DMB-03).

**O Dashboard não exige permissão no CRM.** Ele lê resultados agregados de Leads, Propostas e Créditos sem dar acesso a esses módulos.

---

## 3. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (Usuários, RN-USR-03).

---

*Fim do documento. Matriz de Permissões — Dashboard v0.1, companheira do módulo v0.3.*
