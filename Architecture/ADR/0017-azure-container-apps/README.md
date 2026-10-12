# ADR-0017 — API, Worker e migração no Azure Container Apps

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-13 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; pipeline de deploy do backend

## 1. Contexto

O backend fica na Azure (ADR-0004) e roda três coisas com o mesmo código (.NET 10, ADR-0005):

- a **API**, com ingresso público, WebSocket e réplica única (ADR-0009, ADR-0010);
- o **Worker**, processo contínuo sem ingresso (ADR-0007);
- o **job de migração**, executado uma vez (D-06, D-08).

## 2. Decisão

**Azure Container Apps**, num único ambiente (Container Apps Environment) integrado a uma VNet.

- **API:** Container App com ingresso HTTPS público e WebSocket; mínimo 1, máximo 1 réplica (ADR-0009); deploy por revisão, com tráfego só para a revisão saudável.
- **Worker:** Container App sem ingresso, uma réplica.
- **Migração:** Container Apps Job, executado manualmente.
- **Imagens** no Azure Container Registry; API e Worker publicados juntos, com a mesma versão dos módulos (ADR-0007).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Azure App Service | Mais simples para uma API .NET; WebSocket e deployment slots | Worker vira WebJob ou outro App Service; job de migração sem lugar natural | Três formas de hospedar o mesmo código |
| AKS (Kubernetes) | Controle total | Cluster para operar | Desproporcional para três contêineres |

## 4. Consequências

- **Ganhos:** um ambiente para os três; deploy sem indisponibilidade; rede privada, Managed Identity e Key Vault integrados; mesma imagem local, em homologação e em produção.
- **Custos e riscos aceitos:** montar imagens e manter o registry; a trava de réplica única precisa estar na configuração versionada do ambiente, não só no portal (ADR-0009).
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-13.

## 5. Quando revisar

Se o custo ficar desproporcional ao uso, ou se surgir requisito que o Container Apps não atenda.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-13 aceita pelo usuário. |
