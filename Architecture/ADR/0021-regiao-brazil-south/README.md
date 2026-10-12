# ADR-0021 — Recursos da Azure na região Brazil South

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-17 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; todos os recursos da Azure (ADR-0017, ADR-0018, ADR-0019)

## 1. Contexto

O SmartCorbans trata dados pessoais e financeiros de brasileiros (CPF, contratos, condições de crédito — RNF003), e os usuários estão no Brasil. Base legal e retenção estão fora do escopo inicial (spec §3.2), mas o lugar onde os dados ficam é decisão de arquitetura. **O sistema atual da Smartconsig já roda em Brazil South.**

## 2. Decisão

**Todos os recursos da Azure do SmartCorbans ficam em Brazil South**, a mesma região do sistema atual.

- Backup do PostgreSQL na mesma região. Se um dia houver cópia em outra região, ela vai para **Brazil Southeast**, para os dados continuarem no país.
- Os sites na Netlify usam CDN global; isso não muda a decisão, porque são estáticos e não guardam dados pessoais.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Região dos EUA (East US) | Mais barata; todos os serviços disponíveis | Dados pessoais e financeiros fora do país; latência maior; região diferente do sistema atual | Dados no país e continuidade com o que já existe |

## 4. Consequências

- **Ganhos:** dados no Brasil; menor latência; mesma região do sistema atual, o que facilita a migração da base (D-06) e reduz custo de tráfego entre regiões durante a carga.
- **Custos e riscos aceitos:** preço maior que nas regiões dos EUA; algum serviço novo pode demorar a chegar à região.
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-17.

## 5. Quando revisar

Se algum serviço necessário não existir em Brazil South.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-17 aceita pelo usuário; o sistema atual já usa Brazil South. |
