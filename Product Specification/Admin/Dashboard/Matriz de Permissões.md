# Corban Admin · Dashboard — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Visão geral
**Documento pai:** Corban Admin · Dashboard (módulo v0.5)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | As permissões de Lojas e de Usuários passam a indicar que o escopo (`all` ou `store`) define o recorte mostrado (RN-DSH-05). O artefato apontava para o módulo v0.2. |

## Como usar este documento

**Este módulo não introduz nenhum recurso novo no catálogo de permissões.** O Dashboard é uma tela agregadora — cada bloco condiciona sua exibição a uma permissão de `view` já existente, de outro módulo:

| Bloco | Permissão consultada |
|---|---|
| Alerta "Funis aguardando configuração" | `funnels:view` (`all`, o único escopo oferecido) |
| Alerta "Ofertas com regras incompletas" | `offers:view` (`all`, o único escopo oferecido) |
| Alerta "Lojas sem time" | `stores:view` (`all` ou `store`; o escopo define o recorte — RN-DSH-05) |
| Gráfico de Preço de Lead | `funnels:view` **e** `funnels:manage_pricing` (dado financeiro — mesmo critério do módulo de Funis de Vendas) |
| Tabela de últimos usuários | `users:view` (`all` ou `store`; o escopo define o recorte — RN-DSH-05) |

A própria rota `dashboard` **não exige nenhuma permissão para abrir** — é a tela inicial do Admin, sempre acessível a qualquer usuário autenticado. O que varia é quantos blocos aparecem dentro dela (RN-DSH-04, módulo).

---

## 1. Nota sobre o Gráfico de Preço de Lead

Diferente dos três alertas (que só exigem `view`), o gráfico exige também `funnels:manage_pricing` — porque exibe **valores de custo e preço**, dado financeiro sensível. Um Cargo com `funnels:view` mas sem `manage_pricing` vê os alertas de Funil, mas não o gráfico.

---

*Fim do documento. Matriz de Permissões — Dashboard v0.2.*
