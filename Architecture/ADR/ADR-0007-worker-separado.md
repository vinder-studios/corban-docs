# ADR-0007 — Worker em processo separado, com o mesmo código de domínio

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-03 (`C4/02-containers.md`)
**Afeta:** C4 · Containers (Worker), Componentes da API (Jobs), Implantação; base para P-04

## 1. Contexto

Várias tarefas do SmartCorbans rodam fora de uma requisição:

- a Varredura Recorrente, à meia-noite de Brasília, na frequência escolhida (RN-CRA-08);
- a pré-análise em lote da Prospecção e da Carteira Ativa, que pode simular milhares de Clientes (RN-CRA-02);
- a importação e validação de Listas;
- a injeção de Leads aprovadas, respeitando o limite diário (RN-CRA-07);
- a virada diária do Limite de gasto das Lojas (RN-CRD-07).

A API atende o dia a dia do CRM, inclusive o tempo real do Chat, e roda com duas ou mais réplicas (Implantação, §5).

## 2. Decisão

**Um Worker em processo separado da API, hospedando os mesmos módulos de domínio** (ADR-0006).

- Os módulos registram o trabalho como jobs; o Worker os executa (Componentes da API, componente Jobs).
- O Worker usa o mesmo motor de Simulação da API, com o mesmo resultado (RN-SC-03).
- Começa com uma réplica; a fila com trava por linha permite mais réplicas depois sem duplicar trabalho (Implantação, §5).
- Não tem ingresso público.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Jobs dentro da API (`BackgroundService` ou agendador no mesmo processo) | Um processo a menos | Com várias réplicas, o mesmo job agendado pode rodar em todas sem trava; lote pesado deixa o CRM lento | Mistura carga em lote com atendimento interativo |
| Azure Functions | Escala por demanda; cobra por execução | Código de domínio em outro modelo de hospedagem; limite de tempo de execução para pré-análises longas; mais um tipo de serviço | Mais complexidade sem ganho para o volume esperado |

## 4. Consequências

- **Ganhos:** lote e agendamento isolados do atendimento; recursos e réplicas próprios; mesma regra de negócio da API, sem duplicar código.
- **Custos e riscos aceitos:** mais um processo para implantar e monitorar, no mesmo ambiente do Container Apps; o Worker precisa da mesma versão dos módulos que a API, então os dois são publicados juntos.
- **O que muda nos documentos:** Containers, Componentes da API e Implantação passam a citar esta ADR no lugar da P-03.

## 5. Quando revisar

Quando o volume pedir escala independente por tipo de job, por exemplo vários Workers para a pré-análise e um só para as rotinas diárias.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-03 aceita pelo usuário. |
