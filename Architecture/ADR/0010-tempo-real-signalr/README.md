# ADR-0010 — Tempo real com SignalR na API, sem backplane

**Status:** Aceita (escopo estendido pela ADR-0015)
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-05 (`C4/02-containers.md`)
**Afeta:** C4 · Containers, Componentes da API (Tempo real, Atendimento), Componentes do CRM (Tempo real), Implantação

## 1. Contexto

O Chat do CRM precisa avisar mensagem nova em tempo real (CRM · Chat). As mensagens não são persistidas no SmartCorbans e a Huggy avisa só a API, por webhook. O CRM também recebe outras notificações (Lead atribuída, Proposta paga). O Marketing não usa tempo real (P-11). A API roda com réplica única (ADR-0009).

## 2. Decisão

**Tempo real por WebSocket com SignalR, hospedado na própria API, sem backplane.**

- Só o CRM se conecta.
- O evento só avisa que algo mudou; o CRM busca o dado na API e invalida as consultas afetadas (Componentes do CRM, §5).
- Toda mensagem respeita o escopo do destinatário: o hub só entrega a quem pode ver a Lead, a Loja ou o Time do evento (RNF002).
- Com réplica única, o webhook da Huggy e a conexão do operador estão sempre no mesmo processo, então não há backplane.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Azure SignalR Service | Escala conexões fora da API; resolve várias réplicas | Serviço pago sem volume que justifique | Desnecessário com uma réplica |
| `LISTEN/NOTIFY` do PostgreSQL como backplane | Sem serviço novo | Código a mais para um problema que não existe com uma réplica | Fica como caminho de revisão |
| Consulta periódica no CRM | Nenhuma infraestrutura | Atraso perceptível no Chat; muitas requisições vazias | Não atende ao requisito do Chat |

## 4. Consequências

- **Ganhos:** nada além do ASP.NET Core; tempo real simples de testar e operar.
- **Custos e riscos aceitos:** em cada deploy ou reinício as conexões caem e o cliente SignalR reconecta sozinho; ao reconectar, o CRM recarrega as consultas abertas para não perder eventos do intervalo.
- **O que muda nos documentos:** Containers, Componentes e Implantação passam a citar esta ADR no lugar da P-05.

## 5. Quando revisar

Junto com a ADR-0009: se a API ganhar mais de uma réplica, entra um backplane (`LISTEN/NOTIFY` do PostgreSQL ou Azure SignalR Service) na mesma mudança.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-05 aceita pelo usuário, com a ADR-0009. |
| 2026-10-10 | Aceita | Escopo estendido ao Marketing pela ADR-0015 ("Só o CRM se conecta" passa a "CRM e Marketing"). |
