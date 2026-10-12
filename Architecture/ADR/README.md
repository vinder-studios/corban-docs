# SmartCorbans — Registro de decisões de arquitetura (ADR)

> **Catálogo publicado:** Todas as decisões estão disponíveis em formato interativo com busca e filtros no [Catálogo de ADRs (HTML)](index.html).

Uma decisão por arquivo, no modelo `_modelo.md`.

**Regras**
1. ADR aceita não é editada no mérito. Mudar a decisão = nova ADR que a substitui; a antiga passa a "Substituída por ADR-XXXX". Correção de texto é permitida.
2. Premissa (P-xx, documentos C4) vira ADR quando é decidida, aceita ou rejeitada. Nos C4, "premissa P-xx" passa a citar a ADR.
3. Só decisões arquiteturais. Produto fica na spec; convenções de código, na skill de arquitetura do monorepo.
4. A seção "Quando revisar" é obrigatória.
5. Numeração sequencial, nunca reaproveitada.

| ADR | Título | Status | Origem |
|---|---|---|---|
| [ADR-0001](0001-keycloak-sem-feijucaauth/README.md) | Keycloak atual como provedor de identidade, sem a FeijucaAuth | Aceita | D-07 |
| [ADR-0002](0002-login-pela-api/README.md) | Login pela API, com a tela própria do Shell Público | Aceita | D-09 |
| [ADR-0003](0003-postgresql/README.md) | PostgreSQL como banco de dados | Aceita | D-10 |
| [ADR-0004](0004-azure-e-netlify/README.md) | Backend na Azure, aplicações web na Netlify | Aceita | D-11 |
| [ADR-0005](0005-dotnet-10/README.md) | Backend em .NET 10, com Clean Architecture e CQRS | Aceita | P-01 |
| [ADR-0006](0006-monolito-modular/README.md) | Uma API em monolito modular | Aceita | P-02 |
| [ADR-0007](0007-worker-separado/README.md) | Worker em processo separado, com o mesmo código de domínio | Aceita | P-03 |
| [ADR-0008](0008-fila-de-jobs-no-postgresql/README.md) | Fila de jobs no próprio PostgreSQL, sem broker | Aceita | P-04 |
| [ADR-0009](0009-api-replica-unica/README.md) | API com réplica única, sem escalonamento automático | Aceita | Revisão da Implantação |
| [ADR-0010](0010-tempo-real-signalr/README.md) | Tempo real com SignalR na API, sem backplane | Aceita (estendida pelas ADR-0015 e ADR-0025) | P-05 |
| [ADR-0011](0011-webhooks-na-api/README.md) | Webhooks recebidos pela própria API | Aceita | P-06 |
| [ADR-0012](0012-ef-core-dbcontext-por-modulo/README.md) | EF Core com Npgsql, um DbContext por módulo | Aceita | P-07 |
| [ADR-0013](0013-mediatr-12/README.md) | CQRS e eventos de domínio com MediatR 12.x | Aceita | P-08 |
| [ADR-0014](0014-modulo-pasta-projeto-por-camada/README.md) | Módulo como pasta, com um projeto .NET por camada | Aceita | P-09 |
| [ADR-0015](0015-tempo-real-no-marketing/README.md) | Tempo real também no Marketing, para notificações e andamento | Aceita (estendida pela ADR-0025) | P-11 (rejeitada) |
| [ADR-0016](0016-tres-sites-netlify/README.md) | Um site na Netlify por aplicação, em subdomínios | Aceita | P-12 |
| [ADR-0017](0017-azure-container-apps/README.md) | API, Worker e migração no Azure Container Apps | Aceita | P-13 |
| [ADR-0018](0018-postgresql-flexible-server-privado/README.md) | Azure Database for PostgreSQL Flexible Server, com acesso privado | Aceita | P-14 |
| [ADR-0019](0019-key-vault-managed-identity/README.md) | Segredos no Key Vault, lidos por Managed Identity; banco sem senha | Aceita | P-15 |
| [ADR-0020](0020-nat-gateway-sob-demanda/README.md) | NAT Gateway com IP de saída fixo só quando exigido | Aceita | P-16 |
| [ADR-0021](0021-regiao-brazil-south/README.md) | Recursos da Azure na região Brazil South | Aceita | P-17 |
| [ADR-0022](0022-homologacao-mesma-forma/README.md) | Homologação com a mesma forma de produção, descrita em código | Aceita | P-18 |
| [ADR-0023](0023-sem-fk-entre-modulos/README.md) | Sem chave estrangeira entre schemas de módulos diferentes | Aceita | Modelo de dados |
| [ADR-0024](0024-armazenamento-de-arquivos-blob/README.md) | Arquivos no Azure Blob Storage privado, entregues por URL assinada | Aceita | Modelo de dados |
| [ADR-0025](0025-tempo-real-e-sino-nas-tres-aplicacoes/README.md) | Tempo real nas três aplicações e sino único por usuário | Aceita | Revisão das notificações |

## Premissas

Todas as premissas P-01 a P-18 foram decididas: viraram ADR, foram rejeitadas com a alternativa registrada em ADR (P-11 → ADR-0015) ou viraram convenção da skill (P-10).

## Confirmações futuras

Fatos externos que algumas ADRs assumem e que serão confirmados mais adiante, sem bloquear o esqueleto.

| Confirmação | ADR |
|---|---|
| A Huggy e o Keycloak têm ambiente de testes separado para a homologação | ADR-0022 |
| A Huggy e o AbacatePay reenviam webhooks quando a API está fora do ar | ADR-0011 |
