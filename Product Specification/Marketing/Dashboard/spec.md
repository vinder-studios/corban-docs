# Corban Marketing · Dashboard

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing — Documento de Produto
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.3 | **As permissões do Dashboard passam a ter nome** (Matriz de Permissões do Dashboard v0.1). Cada bloco depende do `view` da fonte (`prospecting_lists:view`, `sweeps:view`), e o bloco financeiro depende de `marketing_finance:view`. RN-DMB-03 e 04 e o RF-DMB-01 acompanham |
| 0.2 | **Levantamento do Marketing, decisões do usuário.** (1) **A amarração com o CRM passa a ser a Origem da lead mais a referência imutável à Lista ou Varredura**, e não mais a Tag de categoria "Origem" (RN-DMB-01). (2) **"Campanha de Varredura" vira "Varredura".** (3) **Valor potencial** passa a vir do campo que viaja com a Lead (RN-MKT-08), e o **custo** vem do Custo da base da Lista, campos que antes não existiam. (4) **Novo bloco financeiro**, sob permissão própria: **Receita de leads** (o que as Lojas pagam pelas Leads injetadas), Custo da base e Resultado da base (RN-DMB-04 e 05). (5) O que antes se chamava "Receita Gerada" passa a **Produção gerada**, para não colidir com a Receita de leads. (6) O Dashboard **agrega**; o resultado detalhado de cada Varredura vive em Carteira Ativa (RF-CRA-02). (7) Novo filtro por Origem de Venda; leitura em português do Brasil. |
| 0.1 | Versão inicial. Indicadores dos motores de Prospecção e Carteira Ativa; preparada para Inbound e Ads. |

## Como usar este documento

Doc de módulo: blocos visuais, requisitos funcionais (RF-DMB) e regras de negócio (RN-DMB) próprios.

> **O centro de comando da Geração de Demanda.** Este módulo não gere entidades próprias: é um agregador de leitura. Cruza o volume que o Marketing injeta com o desfecho comercial que acontece no CRM (Propostas Pagas) e com o que as Lojas pagam pelas Leads, e permite medir a eficácia das bases e das varreduras.

---

# 1. Objetivo do módulo

Dar ao Gestor de Marketing uma visão única do volume e da qualidade das Leads geradas pela Prospecção e pela Carteira Ativa, da conversão delas até o pagamento e, para quem tem permissão, do retorno financeiro.

---

# 2. Atores

- **Gestor de Marketing (tenant)** — consome os blocos para avaliar suas Listas e Varreduras.
- **Owner / gestão financeira do tenant** — vê também o bloco financeiro.

---

# 3. Blocos de indicadores

## 3.1. Visão geral de aquisição

- **Leads geradas:** soma das Leads injetadas pela Prospecção e pela Carteira Ativa no período.
- **Valor potencial injetado:** soma do **valor potencial** das Leads injetadas (RN-MKT-08).

## 3.2. Prospecção

- **Funil de higienização:** linhas importadas ➔ inválidas e bloqueadas ➔ descartadas na pré-análise ➔ qualificadas ➔ injetadas.
- **Taxa de aproveitamento:** % das linhas importadas que virou Lead.
- **Ranking de Listas:** Listas que mais geraram Propostas Pagas no CRM (com Leads, Pagas e, para quem tem permissão financeira, custo por Lead e por Proposta Paga).

## 3.3. Carteira Ativa

- **Base varrida × oportunidades:** Clientes analisados ➔ elegíveis ➔ recusados (não perturbe, carência, Lead ativa, Proposta em andamento) ➔ Leads injetadas.
- **Ranking de Varreduras:** as Varreduras que mais geram valor potencial e Propostas Pagas.

## 3.4. Conversão final (conexão com o CRM)

- **Status das Leads geradas:** distribuição atual (Novo, Negociação, Formalização, Pago, Cancelado) das Leads de Prospecção e Carteira Ativa.
- **Produção gerada:** valor de produção (R$) das Propostas Pagas das Leads de Prospecção e Carteira Ativa.

## 3.5. Financeiro *(permissão própria)*

- **Receita de leads:** quanto as Lojas pagaram (créditos debitados) pelas Leads injetadas no período.
- **Custo da base:** soma do Custo da base das Listas do período (Prospecção, §3.1).
- **Resultado da base:** Receita de leads − Custo da base, por Lista.

---

# 4. Requisitos funcionais

## RF-DMB-01 — Exibir o Dashboard

**Como** Gestor de Marketing, **eu quero** ver os indicadores ao entrar na aplicação **para** ter o pulso da geração de demanda.

**Comportamento esperado:** A tela inicial do Marketing carrega os blocos do §3 que as permissões do usuário habilitam. Sem permissão de visualização em nenhuma fonte (Listas ou Varreduras), mostra o estado "sem acesso". Sem nenhuma Lista ou Varredura ainda, o estado vazio leva à primeira importação ou Varredura.

## RF-DMB-02 — Filtros globais

**Como** Gestor de Marketing, **eu quero** filtrar o Dashboard por período, Segmento e Origem de Venda **para** isolar uma análise (ex.: "como foi a geração para INSS neste mês?").

**Comportamento esperado:** Barra superior com **Período** (padrão: mês atual), **Segmento** (Segmentos ativos do Admin) e **Origem de Venda** (opcional). Todos os blocos recalculam ao mudar um filtro.

## RF-DMB-03 — Detalhar a Lista ou a Varredura

**Como** Gestor de Marketing, **eu quero** abrir uma Lista ou Varredura a partir do ranking **para** ver o resultado e os motivos de descarte.

**Comportamento esperado:** Os itens dos rankings levam ao detalhe da Lista (Prospecção, RF-PRS-03) ou ao Resultado da Varredura (Carteira Ativa, RF-CRA-02).

---

# 5. Regras de negócio (consolidado)

- **RN-DMB-01 — Amarração pela Origem da lead e pela referência.** A ligação entre as Leads do Marketing e os resultados do CRM (Propostas Pagas, produção) é a **Origem da lead (Prospecção ou Carteira Ativa)** mais a **referência à Lista ou Varredura**, gravadas no momento da injeção (RN-MKT-01). Ambas são definidas pelo sistema e **não mudam**.
- **RN-DMB-02 — O período é o da injeção.** O filtro considera a data em que a Lead foi injetada, e não a do pagamento. Uma Lead gerada em outubro e paga em novembro conta como conversão de outubro.
- **RN-DMB-03 — Acesso por permissão.** Cada bloco aparece conforme a permissão de visualização da fonte (`prospecting_lists:view` para Listas, `sweeps:view` para Varreduras), como detalha a Matriz de Permissões. O Dashboard não filtra por Loja, porque o Marketing atua acima das Lojas, e não exige permissão no CRM para ler os resultados agregados de Leads, Propostas e Créditos.
- **RN-DMB-04 — O bloco financeiro é permissão própria.** Receita de leads, Custo da base, Resultado e os custos do ranking só aparecem para quem tem `marketing_finance:view`, além da visualização da fonte.
- **RN-DMB-05 — Como se calculam os valores.** **Receita de leads** é a soma do preço debitado nas Leads de Marketing (Créditos, Extrato); Lead que não cobrou (Leads, RN-LED-02, rodízio sem cobrança) conta zero. **Custo por Lead** = Custo da base ÷ Leads injetadas da Lista, e **custo por Proposta Paga** = Custo da base ÷ Propostas Pagas da Lista. Lista sem Custo da base fica fora do cálculo de custo e do Resultado, e o Dashboard avisa quantas ficaram de fora. Varredura não tem custo de base.

---

# 6. Estados e transições

Sem ciclo de vida de entidade. Os blocos leem, em tempo real, Listas e Varreduras (Marketing), Leads e Propostas (CRM) e Créditos (CRM).

---

# 7. Limites do módulo

**Não** inclui: editar Listas ou Varreduras (o módulo apenas leva até elas); o controle de saldo das Lojas (Créditos); e o custo de anúncios (Google Ads, Meta), que só entra com os módulos de Inbound e Ads. Não inclui exportação nem comparação com períodos anteriores nesta versão.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Prospecção e Carteira Ativa (Marketing)** — volume, descartes, recusas, valor potencial e custo.
- **Leads e Propostas (CRM)** — status, Propostas Pagas e produção das Leads geradas.
- **Créditos (CRM)** — o que as Lojas pagaram pelas Leads.
- **Segmentos e Origem de Venda (Admin)** — filtros.

**Expõe para:** nada. É um módulo de leitura terminal.

---

*Fim do documento. Dashboard v0.3 — módulo da aplicação Marketing.*
