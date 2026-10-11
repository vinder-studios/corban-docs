# ADR-0008 — Fila de jobs no próprio PostgreSQL, sem broker

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-04 (`C4/02-containers.md`)
**Afeta:** C4 · Containers (banco), Componentes da API (Jobs, Persistência), Implantação (Worker)

## 1. Contexto

Os módulos registram trabalho e o Worker executa (ADR-0007). Os jobs não podem rodar duas vezes: uma injeção de Leads duplicada debitaria o saldo das Lojas em dobro (RN-SC-05, RN-SC-09). A Varredura Recorrente precisa de agendamento (RN-CRA-08). O volume esperado é moderado: rotinas diárias e lotes disparados por gestores de Marketing, não um fluxo contínuo de eventos. O banco é PostgreSQL (ADR-0003).

## 2. Decisão

**Os jobs ficam numa tabela do próprio PostgreSQL**, sem broker de mensagens.

- **Enfileirar junto com o dado de negócio, na mesma transação.** Exemplo: aprovar a injeção e criar o job de injeção são uma operação só; ou as duas acontecem, ou nenhuma.
- **Consumo com trava por linha** (`FOR UPDATE SKIP LOCKED`): cada job é pego por um único executor, mesmo com várias réplicas do Worker.
- **Agendamentos** (Varredura Recorrente, virada diária do Limite de gasto) também ficam no banco.
- **Novas tentativas e falha final registradas**, com o erro, para o job não sumir em silêncio.
- **A implementação** (própria ou biblioteca sobre o PostgreSQL, como o Hangfire) é escolhida na fase de esqueleto; não muda esta decisão.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Azure Service Bus | Feito para mensageria; escala alta; dead-letter nativa | Gravar no banco e publicar viram duas operações, exigindo outbox; mais um serviço pago e operado | Complexidade sem volume que justifique |
| Jobs só em memória no Worker | Nada a mais | Perde jobs ao reiniciar; não coordena réplicas | Inaceitável para injeção e débito |

## 4. Consequências

- **Ganhos:** consistência entre dado de negócio e job; nenhuma execução dupla; zero infraestrutura nova; histórico de jobs consultável em SQL.
- **Custos e riscos aceitos:** o banco recebe a carga da fila; a tabela de jobs precisa de limpeza periódica dos concluídos; não serve para volume muito alto de mensagens.
- **O que muda nos documentos:** Containers, Componentes da API e Implantação passam a citar esta ADR no lugar da P-04.

## 5. Quando revisar

Quando o volume de jobs ou de webhooks pesar no banco, ou quando outro sistema precisar consumir eventos do SmartCorbans. O caminho é o Azure Service Bus com outbox.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-04 aceita pelo usuário. |
