# ADR-0020 — NAT Gateway com IP de saída fixo só quando exigido

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-16 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; integrações com Keycloak, Huggy, AbacatePay e API do Banco Central

## 1. Contexto

A API e o Worker chamam o Keycloak (ADR-0001), a Huggy, o AbacatePay e a API do Banco Central. Sem NAT Gateway, o IP de saída do Container Apps (ADR-0017) não é fixo. Se algum desses serviços liberar acesso só para IPs cadastrados, o SmartCorbans precisa de IP fixo.

## 2. Decisão

**Sem NAT Gateway no início.** Ele entra, na VNet que já existe, **quando algum sistema externo exigir liberação por IP**.

- Entrar com o NAT é mudança de configuração da rede, sem mudança de código.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| NAT Gateway desde o início | IP fixo pronto para qualquer liberação | Custo mensal sem necessidade comprovada | Pagar antes de precisar |

## 4. Consequências

- **Ganhos:** nenhum custo de rede a mais enquanto não houver exigência.
- **Custos e riscos aceitos:** se a exigência só aparecer durante a integração, entra uma tarefa de rede no meio do caminho.
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-16.

## 5. Quando revisar

Quando o Keycloak, a Huggy ou o AbacatePay exigirem liberação por IP.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-16 aceita pelo usuário. |
| 2026-10-10 | Aceita | Removida a condição sobre o Keycloak: nenhum externo exige liberação por IP hoje (confirmado pelo usuário). |
