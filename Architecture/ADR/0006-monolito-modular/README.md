# ADR-0006 — Uma API em monolito modular

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-02 (`C4/02-containers.md`)
**Afeta:** C4 · Containers (API), Componentes da API; base para P-03, P-07 e P-09

## 1. Contexto

O SmartCorbans é instância única (spec §1.1), feito por um time, com 12 módulos de domínio muito acoplados no fluxo do negócio (Componentes da API, §4):

- atribuir uma Lead checa o saldo e debita os Créditos da Loja na mesma operação (RN-SC-05);
- a Proposta paga grava o Contrato no Cliente e alimenta Metas e Rankings (RN-SC-06, RN-SC-07);
- inativar um usuário redistribui as Leads dele (RN-USR-05);
- Leads, Créditos e Simulação servem o CRM e o Marketing ao mesmo tempo (RN-SC-03).

O banco é relacional justamente para dar atomicidade a esses fluxos (ADR-0003).

## 2. Decisão

**Uma única API, organizada como monolito modular**: os 12 módulos rodam no mesmo processo e no mesmo deploy, separados por fronteiras explícitas no código.

- Cada módulo é dono das suas tabelas, num schema próprio, e só é acessado pelo seu contrato público (Componentes da API, §5).
- Fluxos que cruzam módulos e mexem em dinheiro usam transação local do PostgreSQL.
- O Worker hospeda os mesmos módulos, em processo separado (P-03).
- A forma dos projetos que garante as fronteiras é a P-09.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Microsserviços por módulo | Deploy e escala independentes | Transações distribuídas no fluxo de dinheiro; fila, rede e observabilidade para um time só; 12 módulos que conversam muito | Custo operacional e de consistência sem necessidade presente |
| Serviços por aplicação (Admin, CRM, Marketing) | Alinha deploy com as aplicações | O domínio não se divide por aplicação: Leads, Créditos e Simulação servem CRM e Marketing (RN-SC-03) | Corta o domínio no lugar errado |
| Monolito sem fronteiras de módulo | Mais simples no começo | Acoplamento cresce sem controle; impossível extrair um módulo depois | Perde o principal ganho de começar modular |

## 4. Consequências

- **Ganhos:** consistência por transação local; um deploy, um processo para observar; fronteiras verificadas pelo compilador; um módulo pode virar serviço depois, porque já só conversa pelo contrato.
- **Custos e riscos aceitos:** os módulos escalam juntos e uma falha grave de um afeta os outros; o processamento em lote fica no Worker para reduzir esse risco. A disciplina das fronteiras depende da P-09 e das regras de Componentes da API, §5.
- **O que muda nos documentos:** Containers e Componentes da API passam a citar esta ADR no lugar da P-02.

## 5. Quando revisar

Quando um módulo precisar de escala ou de ritmo de deploy muito diferente dos outros. O candidato mais provável é **Atendimento**, pelo volume de webhooks e mensagens da Huggy.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-02 aceita pelo usuário. |
