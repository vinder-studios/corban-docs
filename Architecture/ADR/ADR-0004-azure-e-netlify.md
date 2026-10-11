# ADR-0004 — Backend na Azure, aplicações web na Netlify

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** D-11 (`C4/02-containers.md`) — ADR retroativa
**Afeta:** C4 · Containers, Implantação; API (CORS)

## 1. Contexto

É preciso definir onde rodam a API, o Worker, o banco e as três aplicações web.

## 2. Decisão

**API, Worker e banco na Azure; Admin, CRM e Marketing na Netlify.** Os serviços específicos da Azure estão no diagrama de implantação, como premissas (P-12 a P-18).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Tudo na Azure (Static Web Apps para as SPAs) | Um provedor só; mesmo domínio possível | Menos prático para os sites do monorepo | Decisão do usuário pela Netlify no front |
| VPS Linux própria | Custo baixo e previsível | Operação manual de banco, backup e escala | Mais operação do que o time quer manter |

## 4. Consequências

- **Ganhos:** deploy simples dos sites, com previews por branch; serviços gerenciados para banco e containers.
- **Custos e riscos aceitos:** SPAs e API em domínios diferentes, então a API precisa de CORS para os três domínios; dois provedores para operar.
- **O que muda nos documentos:** já refletido em Containers v0.2 e Implantação v0.1.

## 5. Quando revisar

Se o custo da Azure ficar desproporcional ao uso, ou se a Netlify deixar de atender os builds do monorepo.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Registrada retroativamente a partir de D-11. |
