# ADR-0014 — Módulo como pasta, com um projeto .NET por camada

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-09 (`C4/03-componentes-api.md`), definida pelo usuário na revisão dos Componentes
**Afeta:** C4 · Componentes da API (todos os módulos, regras de fronteira); estrutura da solução do backend

## 1. Contexto

O backend segue Clean Architecture (ADR-0005) num monolito modular (ADR-0006). As fronteiras entre módulos e entre camadas precisam ser garantidas, e não só combinadas, para que um módulo possa virar serviço depois.

## 2. Decisão

**Cada módulo é uma pasta da solução, com um projeto .NET por camada:**

- `<Módulo>.Domain`
- `<Módulo>.Application`
- `<Módulo>.Infrastructure`
- `<Módulo>.Contracts` — o contrato público: interfaces, DTOs e eventos de integração.

**Referências permitidas:** Application → Domain; Infrastructure → Application e Domain; **outro módulo → só `Contracts`**. Os hosts da API e do Worker referenciam os projetos de cada módulo só para registrar as dependências.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Um projeto por módulo, com pastas por camada | Menos projetos | Camadas separadas só por convenção; precisa de testes de arquitetura | Isolamento por referência é mais forte |
| Projetos por camada para a solução toda | Estrutura clássica | Mistura módulos dentro de cada camada | Apaga a fronteira entre módulos |

## 4. Consequências

- **Ganhos:** o compilador barra um módulo que use o Domain ou a Infrastructure de outro, e uma camada que pule a de cima; extrair um módulo para serviço é mover a pasta.
- **Custos e riscos aceitos:** cerca de quatro projetos por módulo (perto de 50 no total); build mais lento e mais arquivos de projeto para manter.
- **O que muda nos documentos:** Componentes da API, §5 e §6, passam a citar esta ADR.

## 5. Quando revisar

Se o número de projetos tornar o build ou a manutenção pesados demais para o ganho de isolamento.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-09 definida pelo usuário. |
