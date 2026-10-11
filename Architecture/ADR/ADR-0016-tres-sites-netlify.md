# ADR-0016 — Um site na Netlify por aplicação, em subdomínios

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-12 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; API (CORS); pipeline de deploy do front

## 1. Contexto

As aplicações web ficam na Netlify (ADR-0004). O monorepo trata Admin, CRM e Marketing como aplicações independentes, cada uma com build e deploy próprios e sem compartilhar runtime (`FrontEnd/docs/architecture/monorepo.md`). O UX define que cada aplicação é uma entrada própria, com endereço próprio (Documento de UX, §2).

## 2. Decisão

**Três sites na Netlify, um por aplicação, cada um num subdomínio próprio** (`admin.<domínio>`, `crm.<domínio>`, `marketing.<domínio>`).

- Os subdomínios ficam **no mesmo domínio-pai da API** (`api.<domínio>`).
- Cada site compila só o seu app, com o filtro do Turborepo; build de um app não dispara deploy dos outros.
- Regra de SPA em cada site (`/* /index.html 200`).
- A API libera no CORS só essas três origens.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Um site, apps em caminhos (`/admin`, `/crm`) | Um domínio; sem CORS entre apps | Deploy único para os três; caminho base e regras de SPA por app | Contraria a independência que o monorepo já assume |

## 4. Consequências

- **Ganhos:** deploy e rollback independentes por app; previews por branch em cada site; endereço próprio por aplicação, como o UX define.
- **Custos e riscos aceitos:** três sites para configurar; três origens no CORS.
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-12.

## 5. Quando revisar

Se o custo ou o limite de sites da Netlify pesar, ou se os apps passarem a compartilhar runtime.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-12 aceita pelo usuário. |
