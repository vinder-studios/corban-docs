# ADR-0013 — CQRS e eventos de domínio com MediatR 12.x

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-08 (`C4/03-componentes-api.md`), definida pelo usuário na revisão dos Componentes (substituindo o LiteBus)
**Afeta:** C4 · Componentes da API (todos os módulos, Endpoints, regra 3 da §5)

## 1. Contexto

O backend usa CQRS (ADR-0005). Os módulos precisam de despacho de comandos e consultas a partir dos Endpoints e do Worker, e de eventos de domínio no mesmo processo para efeitos colaterais entre módulos, por exemplo "Proposta paga" → Desempenho e Clientes (Componentes da API, §5, regra 3). Os serviços .NET atuais da SmartTech usam LiteBus.

## 2. Decisão

**MediatR, fixado na linha 12.x**, para comandos, consultas e eventos de domínio (notificações do MediatR), sempre no mesmo processo.

- A versão fica travada em 12.x, a última com licença Apache 2.0; as versões seguintes exigem licença comercial.
- Comportamentos transversais (validação, transação, log) entram como pipeline behaviors.
- Eventos entre módulos são publicados pelo projeto `Contracts` do módulo de origem (ADR-0014).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| LiteBus | Já usado nos serviços atuais | — | Decisão do usuário pelo MediatR |
| MediatR 13+ | Versões novas e suporte | Licença comercial | Custo sem necessidade |
| Despacho próprio | Sem dependência | Reimplementar pipeline e notificações | Esforço sem ganho |

## 4. Consequências

- **Ganhos:** biblioteca amplamente conhecida; pipeline behaviors para regras transversais; eventos de domínio sem infraestrutura.
- **Custos e riscos aceitos:** a linha 12.x não recebe evolução; atualizar exige reavaliar a licença.
- **O que muda nos documentos:** Componentes da API, §6, passa a citar esta ADR.

## 5. Quando revisar

Se uma falha de segurança na 12.x exigir atualização, ou se a licença comercial passar a valer a pena.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-08 definida pelo usuário. |
