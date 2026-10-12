# Corban Marketing · Carteira Ativa

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing — Documento de Produto
**Versão:** 0.9
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.9 | Corrige a descrição do estado **Ativa** (§6), que dizia "rodando todo dia": a Recorrente reavalia a base na frequência escolhida e só a injeção é diária (RN-CRA-08). |
| 0.8 | Acompanha o Documento de Produto v0.9: a Varredura **notifica** quando entra em Aguardando aprovação, em Falha e quando uma execução da Recorrente falha (RN-MKT-10). §7 deixa claro que só a injeção não notifica. §8 passa a expor os eventos ao sino do Shell. |
| 0.7 | **Frequências confirmadas pelo usuário:** Semanal (7 dias), Quinzenal (15) e Mensal (30). Item 6 resolvido. |
| 0.6 | **Decisões do usuário.** (1) A varredura **grava Simulação na injeção**, para o Cliente com Oferta aprovada (RN-CRA-02, RN-CRA-04; Produto, RN-MKT-02). (2) **Base legal e declaração saem do escopo inicial** (campo e etapa do assistente). (3) Saem os itens abertos que são detalhe de implementação ou de migração (§9, itens 5 e 7). |
| 0.5 | **Decisões do usuário.** (1) O motor lê o **histórico de contratos** do Cliente (Clientes v0.9, RN-CLI-13), e não mais o último snapshot de Simulação; a **idade máxima** sai da Varredura, e a simulação em lote continua silenciosa (RN-CRA-02). (2) A Varredura "Contínua" passa a se chamar **Recorrente** e tem **frequência definida na criação**, sem execução diária (RN-CRA-08). (3) **Filtro de base** sobre o último Contrato, pela Data de pagamento ao cliente; as Canceladas ficam fora (decisão do usuário, recomendação aceita). (4) A Carga de base não existe mais. As opções de frequência e o cálculo do prazo restante são propostos [confirmar]. |
| 0.4 | **Acompanha as decisões da Prospecção v0.3:** cálculo do valor potencial em lote (parcela = margem, prazo máximo) e Região na pré-análise (qualquer Região) deixam de ser itens abertos (§9, itens 2 e 4). |
| 0.3 | **Novo estado Processando pré-análise** (§6), como na Lista de Prospecção. A pré-análise de uma base inteira leva tempo, e o módulo não dizia em que estado a Varredura fica enquanto ela roda. Rascunho, a edição que muda o universo (RN-CRA-09) e o reprocessamento passam por ele antes de Aguardando aprovação. Achado ao escrever a Fase 2 |
| 0.2 | **Levantamento do Marketing, decisões do usuário.** (1) A entidade "Campanha de Varredura" passa a se chamar **Varredura**, para não colidir com a evolução Campanhas (disparos em massa, fora da v1). (2) **Origem corrigida:** a Lead nasce com a **Origem da lead Carteira Ativa** (Leads, RN-LED-12) e a referência à Varredura, e não mais com a Tag de categoria "Origem". (3) **Dados da pré-análise definidos:** último snapshot de Simulação do Cliente e Propostas Pagas (RN-CRA-02). O Segmento do Cliente é o das Leads e Propostas dele. (4) **Injeção controlada:** nova aprovação na primeira execução, limite diário obrigatório na Contínua, estados Aguardando aprovação, Falha e Cancelada, transições e regras de edição (RN-CRA-06 a 09). (5) **Duplicidade completa:** Lead ativa, Proposta em andamento e carência (RN-MKT-04). (6) **Conformidade:** Lista de bloqueio e declaração de base legal. (7) O RF-CRA-02 vira **Resultado da Varredura**, por Varredura, e o Dashboard agrega. (8) Texto reescrito em português do Brasil; sai a promessa de "notificação aos operadores", que o CRM não tem. As RN-CRA foram renumeradas. |
| 0.1 | Versão inicial. Varredura ativa da base de Clientes do tenant para gerar oportunidades por simulação em lote interna. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-CRA) e regras de negócio (RN-CRA) próprios. As regras compartilhadas com a Prospecção (RN-MKT) estão no Documento de Produto do Marketing e são só referenciadas aqui.

> **O motor de Farming (Cultivo).** Enquanto a Prospecção caça contatos novos, a Carteira Ativa trabalha o maior ativo do tenant: os Clientes que já estão dentro de casa. Varre a base, cruza o histórico de contratos de cada Cliente com as Ofertas e Regras vigentes e entrega a oportunidade "esquecida" no funil das Lojas.

---

# 1. Objetivo do módulo

Minerar a base de Clientes do tenant: encontrar quem, pelo histórico e pelas condições vigentes, está elegível a uma nova operação (ex.: Refinanciamento, Portabilidade) e injetar essas oportunidades como Leads, de forma controlada.

---

# 2. Atores

- **Gestor de Marketing (tenant)** — configura as Varreduras, aprova a injeção, pausa e acompanha o resultado.
- **Sistema** — executa a pré-análise e a injeção em segundo plano.

---

# 3. Entidades e conceitos

## 3.1. Varredura

Define a regra pela qual o sistema garimpa a base de Clientes.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Identificador interno (ex.: "Refin INSS acima de 12 meses"). |
| Segmento alvo | Sim | Fatia da base varrida e Funil de destino. A base é formada pelos **Clientes com Contrato naquele Segmento** (RN-CRA-01). |
| Filtro de base | Sim | Ver abaixo. |
| Operações a simular | Sim | Operações que o motor tenta aprovar (ex.: Refinanciamento, Portabilidade). |
| Recorrência | Sim | **Única** (roda uma vez) ou **Recorrente** (roda na frequência escolhida). |
| Frequência | Recorrente: sim | **Semanal** (a cada 7 dias), **Quinzenal** (15) ou **Mensal** (30), definida na criação (RN-CRA-08). |
| Limite diário de leads | Recorrente: sim · Única: não | Máximo de Leads injetadas por dia (RN-CRA-07). |
| Origem de Venda | Não | Rótulo do tenant, copiado às Leads geradas (RN-MKT-01). |
| Status | Sim | Ver §6. |

**Filtro de base.** Recortes sobre o **último Contrato** do Cliente no Segmento alvo (Clientes, RN-CLI-13): **Operação** e tempo desde a **Data de pagamento ao cliente** (ex.: "pago há mais de 360 dias"). Propostas Canceladas não entram, porque não geram Contrato, e Clientes sem Contrato ficam fora da base.

## 3.2. Pré-análise da base

**RN-CRA-01 — Base e Segmento do Cliente.** O Cliente não tem Segmento próprio. Para a Varredura, o Segmento do Cliente é o dos **Contratos** dele. A Varredura lê o histórico de contratos dos Clientes do tenant, **de todas as Lojas**; o conteúdo lido é de uso interno do motor e não é exposto às Lojas além do resumo que viaja com a Lead (RN-MKT-08).

**RN-CRA-02 — Simulação em lote silenciosa.** Para cada Cliente que atende ao Filtro de base, o motor roda uma simulação em segundo plano, usando o **último Contrato** dele no Segmento: Operação, Banco, Valor, Parcela e o **prazo restante** (Prazo menos o tempo decorrido desde a Data de pagamento). Os campos que o Contrato não traz (margem, data de nascimento etc.) ficam "não avaliados" (RN-MKT-03). Contrato sem Parcela ou sem Prazo deixa o Cliente em "Sem dados para análise". O motor avalia as Ofertas ativas e vigentes das Operações escolhidas e as Regras de cada uma. Durante a varredura a avaliação **não grava Simulação**; ela é gravada na injeção, para o Cliente com Oferta aprovada (RN-CRA-04, RN-MKT-02).

**RN-CRA-03 — Qualificação.** O Cliente se qualifica se for elegível a ao menos uma Oferta testada. Campo sem valor não desclassifica (RN-MKT-03).

## 3.3. Injeção e proteção

**RN-CRA-04 — Lead requalificada.** O Cliente qualificado ganha uma nova Lead, associada à ficha dele, no Stage tipo Novo do Funil do Segmento alvo, com **Origem da lead Carteira Ativa**, a referência à Varredura e a Origem de Venda da Varredura (RN-MKT-01). Leva o valor potencial e o resumo das Ofertas (RN-MKT-08), e a avaliação é **gravada como Simulação de origem Marketing**, ligada à Lead (Simulações, RN-SIM-01).

**RN-CRA-05 — Duplicidade e bloqueio.** Antes de cada injeção, vale a duplicidade completa (Lead ativa no Segmento, Proposta em andamento e carência — RN-MKT-04) e a Lista de bloqueio (RN-MKT-05). A recusa é contada no resultado e **não gera Lead**.

**RN-CRA-06 — Entrada no funil.** A Lead entra no rodízio de Lojas e cobra como qualquer Lead de Origem com preço (Leads, RN-LED-02, 03 e 13). A **Loja que receber passa a ter o Cliente associado** (Clientes, RN-CLI-04).

**RN-CRA-07 — Aprovação e ritmo.** A **primeira execução** de toda Varredura exige aprovação do gestor (RN-MKT-07). A Varredura **Recorrente** exige **limite diário** de Leads, que é o ritmo da injeção: a execução avalia a base e as Leads qualificadas saem em fila, no máximo N por dia. O que sobrar quando chegar a próxima execução é reavaliado junto. A Varredura **Única** pode ter limite opcional.

**RN-CRA-08 — Como a Recorrente roda.** Na **frequência escolhida na criação** (a cada 7, 15 ou 30 dias), à meia-noite (horário de Brasília), a Varredura **reavalia os Clientes que atendem ao Filtro de base naquele dia**. A primeira execução acontece à meia-noite seguinte à aprovação, e as seguintes 7, 15 ou 30 dias depois. Quem já foi injetado fica em carência (RN-MKT-04), e quem foi recusado por colisão volta a ser avaliado na próxima execução. **Não há execução diária:** reavaliar a base inteira é um processo pesado, e a frequência existe para controlar o custo. *(Decisão do usuário.)*

**RN-CRA-09 — Editar exige reaprovar o que muda o universo.** Uma Recorrente só se edita **Pausada**. Mudar o Segmento, o Filtro de base ou as Operações **refaz a pré-análise e volta a Varredura a Aguardando aprovação**. Mudar só o nome, a frequência, o limite diário ou a Origem de Venda não exige reaprovação.

---

# 4. Requisitos funcionais

## RF-CRA-01 — Configurar a Varredura

**Como** Gestor de Marketing, **eu quero** configurar os parâmetros de uma varredura **para** encontrar clientes elegíveis a novas operações.

**Comportamento esperado:** Wizard: nome e Segmento alvo → Filtro de base (com a **contagem estimada** de Clientes que o recorte alcança) → Operações a simular → recorrência (com a frequência, quando Recorrente) e limite diário → Origem de Venda. Ao concluir, a Varredura roda a pré-análise e fica em **Aguardando aprovação**.

## RF-CRA-02 — Resultado da Varredura

**Como** Gestor de Marketing, **eu quero** ver o resultado de cada Varredura **para** medir quanto a base antiga está gerando.

**Comportamento esperado:** Por Varredura, e por execução nas Recorrentes: **Clientes na base ➔ atendem ao filtro ➔ sem dados para análise ➔ elegíveis ➔ recusados (não perturbe, carência, Lead ativa, Proposta em andamento) ➔ Leads injetadas ➔ valor potencial**. O Dashboard do Marketing apenas agrega e leva a esta tela (RF-DMB-03).

## RF-CRA-03 — Aprovar a injeção

**Como** Gestor de Marketing, **eu quero** revisar e aprovar a primeira execução **para** não despejar a base inteira nas Lojas sem querer.

**Comportamento esperado:** Em **Aguardando aprovação**, mostra quantas Leads seriam injetadas, o valor potencial e os recusados. O gestor aprova ou cancela. Aprovada, a Única passa a **Executando** e a Recorrente a **Ativa**.

## RF-CRA-04 — Gerir Varreduras

**Como** Gestor de Marketing, **eu quero** pausar, retomar, editar e cancelar Varreduras **para** ajustar a torneira de Leads.

**Comportamento esperado:** Lista de Varreduras com status, recorrência, Leads geradas e última execução. Pausar e retomar nas Recorrentes; editar conforme RN-CRA-09; cancelar uma Varredura em Aguardando aprovação, Executando ou Pausada. **Varredura com Leads injetadas não se exclui**; Rascunho e Cancelada sem injeção podem ser descartadas.

---

# 5. Regras de negócio (consolidado)

- **RN-CRA-01** — A base é o Cliente com Contrato no Segmento; o Segmento do Cliente é o dos Contratos dele.
- **RN-CRA-02** — Simulação em lote silenciosa sobre o último Contrato do Cliente; sem Parcela ou Prazo, "Sem dados para análise". Não grava Simulação na varredura; grava na injeção, se houver Oferta aprovada.
- **RN-CRA-03** — Elegível a ao menos uma Oferta testada qualifica.
- **RN-CRA-04** — A Lead nasce com Origem Carteira Ativa e referência à Varredura.
- **RN-CRA-05** — Duplicidade completa e Lista de bloqueio antes de cada injeção.
- **RN-CRA-06** — Rodízio com cobrança; a Loja que receber passa a ter o Cliente associado.
- **RN-CRA-07** — Primeira execução aprovada; limite diário obrigatório na Recorrente.
- **RN-CRA-08** — A Recorrente reavalia a base na frequência escolhida (7, 15 ou 30 dias), às 00h de Brasília.
- **RN-CRA-09** — Editar a Recorrente só Pausada; mudar o universo exige reaprovação.

---

# 6. Estados e transições

| Estado | Significado | Sai para |
|---|---|---|
| **Rascunho** | Configuração iniciada. | Processando pré-análise · descartada |
| **Processando pré-análise** | Rotina em segundo plano; Varredura trancada. | Aguardando aprovação · Falha |
| **Aguardando aprovação** | Pré-análise pronta, nada injetado. | Executando (Única) · Ativa (Recorrente) · Cancelada · Processando pré-análise (reprocessar ou editar) |
| **Executando** | Única injetando conforme o ritmo. | Concluída · Cancelada · Falha |
| **Ativa** | Recorrente em vigor: reavalia a base na frequência escolhida e injeta a fila no limite diário (RN-CRA-07, RN-CRA-08). | Pausada · Cancelada · Falha |
| **Pausada** | Recorrente parada pelo gestor. Pode ser editada. | Ativa · Processando pré-análise (se mudou o universo) · Cancelada |
| **Concluída** | Única terminou. | — |
| **Falha** | Erro estrutural; as execuções anteriores ficam no resultado. | Ativa ou Executando (nova tentativa) · Cancelada |
| **Cancelada** | Encerrada pelo gestor. As Leads já injetadas continuam. | — |

---

# 7. Limites do módulo

**Não** inclui: a importação de dados externos (Prospecção); o envio de mensagens aos clientes (Huggy ou evolução futura); o atendimento (CRM); e **notificações** de lead injetada (a Lead aparece no board e no pool da Loja).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Clientes (histórico de contratos), Propostas e Leads (CRM)** — a base e os Contratos (Clientes, RN-CLI-13; Propostas, RN-PRP-18).
- **Ofertas e Regras (Admin)** — o motor de elegibilidade.
- **Segmentos e Funis de Vendas (Admin)** — destino da Lead.
- **Prospecção (Marketing)** — Lista de bloqueio, carência e os Contratos importados (RN-PRS-15).

**Expõe para:**
- **Leads (CRM)** — o evento de injeção, que dispara o rodízio.
- **Dashboard (Marketing)** — volume, valor potencial e conversão das Varreduras.
- **Notificações (Shell Privado)** — Varredura em Aguardando aprovação, em Falha e execução da Recorrente com falha (RN-MKT-10).

---

# 9. Itens abertos

1. *(Resolvido na v0.5.)* **Data de referência:** a Data de pagamento ao cliente; as Canceladas ficam fora do Filtro.
2. *(Removido na v0.5.)* A idade máxima do snapshot deixou de existir. O valor potencial em lote está resolvido (Prospecção v0.3; Produto, RN-MKT-08).
3. *(Resolvido na v0.5.)* Só é minerável o Cliente com Contrato (de Proposta Paga ou importado pela Lista).
4. *(Resolvido na v0.4.)* **Região:** a pré-análise considera qualquer Região (Prospecção v0.3, §9, item 1).
5. *(Removido na v0.6: desempenho é detalhe de implementação.)*
6. *(Resolvido na v0.7.)* **Opções de frequência:** Semanal, Quinzenal e Mensal (7, 15 e 30 dias).
7. *(Removido na v0.6: migração do histórico é detalhe de implementação.)*

---

*Fim do documento. Carteira Ativa v0.9 — módulo da aplicação Marketing.*
