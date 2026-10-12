# ADR-0022 — Homologação com a mesma forma de produção, descrita em código

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-18 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; pipeline de deploy; integrações (Huggy, AbacatePay, Keycloak)

## 1. Contexto

Várias decisões de produção só se comportam de verdade num ambiente parecido com ela: rede privada até o banco (ADR-0018), banco sem senha por identidade gerenciada (ADR-0019), três sites em subdomínios com CORS na API (ADR-0016), login pela API até o Keycloak (ADR-0002) e webhooks dos externos (ADR-0011).

## 2. Decisão

**Um ambiente de homologação com a mesma forma de produção**, em resource group próprio, com recursos menores.

- **Infraestrutura como código** (Bicep ou Terraform, escolha do esqueleto): os dois ambientes vêm da mesma descrição, com parâmetros por ambiente. A trava de réplica única da ADR-0009 fica versionada aí.
- **Front:** cada site da Netlify gera preview por branch, apontando para a API de homologação.
- **Integrações em ambiente de teste:** sandbox do AbacatePay, conta de testes da Huggy e realm de testes no Keycloak. Se a Huggy ou o Keycloak não tiverem ambiente separado, isso se resolve antes de a homologação receber webhooks.
- **Dados:** nenhuma cópia da base de produção sem anonimização (RNF003).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Só produção e ambiente local | Sem custo extra | Problemas de rede, identidade e webhook só aparecem em produção | Risco alto num sistema que mexe com dinheiro |

## 4. Consequências

- **Ganhos:** a topologia é testada antes de publicar; os dois ambientes não divergem, porque vêm do mesmo código.
- **Custos e riscos aceitos:** custo de um segundo ambiente; manter contas de teste nos externos; escrever a infraestrutura em código desde o esqueleto.
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-18.

## 5. Quando revisar

Se o custo da homologação pesar: ela pode ser desligada fora do horário de uso.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-18 aceita pelo usuário. |
