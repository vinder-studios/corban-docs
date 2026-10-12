# ADR-0009 — API com réplica única, sem escalonamento automático

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** revisão da seção de escala de `C4/04-implantacao.md` (substitui "2+ réplicas com afinidade de sessão")
**Afeta:** C4 · Implantação; ADR-0010 (tempo real); qualquer estado mantido em memória pela API

## 1. Contexto

O diagrama de implantação previa duas ou mais réplicas da API, por disponibilidade e não por carga. Na revisão:

- o SmartCorbans é instância única (spec §1.1), com volume de requisições que não pede escala horizontal no início;
- o processamento pesado fica no Worker (ADR-0007);
- o Azure Container Apps já publica sem indisponibilidade com uma réplica: a nova revisão só recebe tráfego quando está saudável;
- várias réplicas trazem um custo escondido: tudo que vive na memória do processo deixa de ser compartilhado, a começar pelo tempo real do Chat (ADR-0010).

## 2. Decisão

**A API roda com uma réplica fixa: mínimo 1, máximo 1, escalonamento automático desligado.**

- Deploy por revisão do Container Apps, sem indisponibilidade.
- Falha do container: reinício automático, com indisponibilidade de segundos.
- Enquanto esta ADR valer, a API pode guardar estado em memória do processo (conexões do tempo real, cache, contadores de limite de tentativas de login), **sabendo que isso precisa ser revisto ao sair dela**.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Duas ou mais réplicas com afinidade de sessão | Continua atendendo se uma réplica cair | Exige backplane para o tempo real e estado compartilhado para cache e limites; mais custo | Disponibilidade extra sem necessidade presente |
| Escalonamento automático por carga | Absorve picos | Uma segunda réplica criada automaticamente quebra o tempo real em silêncio | Risco escondido maior que o ganho |

## 4. Consequências

- **Ganhos:** nenhum backplane nem estado distribuído; operação e custo mínimos; comportamento previsível.
- **Custos e riscos aceitos:** indisponibilidade de segundos se o container cair; um pico além da capacidade de uma réplica deixa a API lenta, sem escalar sozinha.
- **Regra para quem opera:** **não ligar o escalonamento automático nem aumentar as réplicas sem antes substituir esta ADR.** Sair dela exige, juntos: backplane do tempo real (ADR-0010), cache e limites fora da memória do processo.
- **O que muda nos documentos:** Implantação, §5, passa a citar esta ADR.

## 5. Quando revisar

- Quando a carga medida (CPU, memória ou latência) se aproximar do limite de uma réplica.
- Quando a disponibilidade exigida não tolerar os segundos de um reinício.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Decisão do usuário ao revisar a P-05. |
