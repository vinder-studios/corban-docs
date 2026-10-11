# ADR-0011 — Webhooks recebidos pela própria API

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-06 (`C4/02-containers.md`)
**Afeta:** C4 · Containers, Componentes da API (Webhooks), Implantação (entrada pública)

## 1. Contexto

Dois sistemas externos chamam o SmartCorbans por webhook:

- **Huggy:** criação de Lead e de Cliente, gatilho da Simulação automática (RN-SIM-04) e mensagem nova do Chat;
- **AbacatePay:** confirmação do pagamento Pix, que credita o saldo da Loja (RF-CRD-03).

O componente Webhooks valida a origem, ignora eventos repetidos e despacha ao módulo dono (Componentes da API, §3.1). A API roda com réplica única (ADR-0009) e os jobs ficam no PostgreSQL (ADR-0008).

## 2. Decisão

**Os webhooks entram por rotas públicas da própria API**, sem serviço dedicado.

- **Validação de origem** em toda chamada (assinatura ou segredo do provedor, guardado no Key Vault).
- **Idempotência:** cada evento é registrado pelo identificador do provedor; um evento repetido é aceito e ignorado.
- **Gravar e responder rápido.** A rota grava o evento e responde; o que for pesado vira job (ADR-0008), por exemplo a Simulação automática. Assim o provedor não estoura o tempo nem reenvia.
- **Efeito de negócio na mesma transação** quando for leve, por exemplo o pagamento confirmado que credita o saldo e grava o lançamento do Extrato.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Receptor dedicado (Function ou container) que grava numa fila | Absorve picos; a API pode cair sem perder webhook | Mais um serviço; fila entre receptor e API, contra a ADR-0008 | Volume esperado não justifica |
| Consultar os provedores periodicamente em vez de webhook | Sem rota pública | Atraso no Chat e na confirmação de pagamento; mais chamadas | Não atende ao tempo real do Chat |

## 4. Consequências

- **Ganhos:** nada a mais para implantar; o evento entra direto no módulo dono, com transação local.
- **Custos e riscos aceitos:** um pico de webhooks disputa a réplica única com o atendimento; se a API estiver fora do ar, a entrega depende da política de reenvio de cada provedor, que precisa ser confirmada na integração.
- **O que muda nos documentos:** Containers e Componentes da API passam a citar esta ADR no lugar da P-06.

## 5. Quando revisar

Quando o volume de webhooks da Huggy pesar na réplica única (mesmo sinal da ADR-0006 para Atendimento), ou se algum provedor não reenviar eventos perdidos.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-06 aceita pelo usuário. |
