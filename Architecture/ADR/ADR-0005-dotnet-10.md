# ADR-0005 — Backend em .NET 10, com Clean Architecture e CQRS

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-01 (`C4/02-containers.md`)
**Afeta:** C4 · Containers (API, Worker), Componentes da API; base para P-07, P-08 e P-09

## 1. Contexto

A spec não define a stack do backend. O SmartCorbans tem uma API e um Worker que compartilham o mesmo código de domínio, com módulos isolados entre si (Componentes da API, §5). O time de backend da SmartTech já trabalha com .NET 10, Clean Architecture e CQRS nos serviços atuais.

## 2. Decisão

**API e Worker em .NET 10 (ASP.NET Core e Worker Service), com Clean Architecture e CQRS.**

- .NET 10 é a versão de suporte longo (LTS).
- Clean Architecture dentro de cada módulo; a forma dos projetos é decidida na P-09.
- CQRS separando comandos e consultas; a biblioteca é decidida na P-08.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| NestJS (Node.js) | Mesma linguagem do front; já usado pelo autor | Time de backend é .NET; isolar módulos por referência exige configuração extra | Troca de stack sem ganho para o time |
| Java / Spring | Ecossistema maduro | Fora da experiência do time | Custo de aprendizado |

## 4. Consequências

- **Ganhos:** o time usa a stack que já domina; SignalR, Npgsql e Container Apps têm bom suporte; isolamento entre projetos verificado pelo compilador.
- **Custos e riscos aceitos:** front e back em linguagens diferentes; contratos da API precisam de um caminho para virar tipos TypeScript (por exemplo, geração a partir do OpenAPI).
- **O que muda nos documentos:** Containers e Componentes da API passam a citar esta ADR no lugar da P-01.

## 5. Quando revisar

No fim do suporte do .NET 10, ou se o time de backend mudar de stack.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-01 aceita pelo usuário. |
