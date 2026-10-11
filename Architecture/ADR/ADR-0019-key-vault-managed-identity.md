# ADR-0019 — Segredos no Key Vault, lidos por Managed Identity; banco sem senha

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-15 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; configuração da API, do Worker e do job de migração; ADR-0018

## 1. Contexto

O backend usa segredos de sistemas externos e do banco:

- o cliente com acesso à Admin API do Keycloak, que cria usuários e define senhas (ADR-0001, ADR-0002) — o mais sensível;
- as chaves do AbacatePay e da Huggy;
- os segredos que validam a origem dos webhooks (ADR-0011);
- o acesso ao PostgreSQL (ADR-0018).

## 2. Decisão

**Os segredos ficam no Azure Key Vault e são lidos por Managed Identity**; **o banco é acessado sem senha**, por autenticação Microsoft Entra com a mesma identidade.

- API, Worker e job de migração têm cada um a sua identidade gerenciada, com acesso só aos segredos de que precisam.
- Nenhum segredo no código, no repositório, no pipeline ou em variável de ambiente.
- Troca de segredo no Key Vault, sem novo deploy; todo acesso registrado.
- No ambiente local, segredos pelos user secrets do .NET, nunca commitados.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Segredos do Container Apps | Sem recurso a mais | Espalhados por app; sem registro central; troca em cada app | Pior controle para o segredo do Keycloak |
| Variáveis de ambiente no pipeline | Nada na Azure | Segredo passa pelo pipeline e por quem o acessa | Caminho mais fácil de vazar |
| Banco com usuário e senha no Key Vault | Padrão conhecido | Mais uma senha crítica para guardar e trocar | A identidade gerenciada elimina a senha |

## 4. Consequências

- **Ganhos:** nenhuma senha de banco existe; segredos externos centralizados e auditados; troca sem deploy.
- **Custos e riscos aceitos:** Key Vault e identidades para configurar; o ambiente local usa outro caminho; o papel do banco ligado à identidade precisa das permissões certas por schema (ADR-0012).
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-15; ADR-0018 deixa de prever a string de conexão no Key Vault.

## 5. Quando revisar

Se algum segredo precisar ser compartilhado com um sistema fora da Azure.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-15 aceita pelo usuário, com autenticação sem senha no banco. |
