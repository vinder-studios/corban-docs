# ADR-0018 — Azure Database for PostgreSQL Flexible Server, com acesso privado

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-14 (`C4/04-implantacao.md`)
**Afeta:** C4 · Implantação; acesso operacional ao banco

## 1. Contexto

O banco é PostgreSQL (ADR-0003) e guarda dados pessoais e financeiros (RNF003). Só a API, o Worker e o job de migração acessam o banco, todos na VNet do Container Apps (ADR-0017).

## 2. Decisão

**Azure Database for PostgreSQL Flexible Server, com acesso privado pela VNet e sem IP público.**

- Backup automático e restauração para um momento específico.
- **Alta disponibilidade por zona desligada no início**, por custo; é a primeira coisa a ligar se a indisponibilidade do banco pesar.
- Acesso operacional (suporte, consulta manual) por caminho controlado (VPN, bastion ou regra temporária), nunca por IP público permanente.
- Acesso da API, do Worker e da migração **sem senha**, por Managed Identity com autenticação Microsoft Entra (ADR-0019).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Flexible Server com IP público e firewall | Acesso mais fácil para o time | Banco exposto à internet | Dados pessoais e financeiros (RNF003) |
| PostgreSQL em contêiner próprio | Custo menor no papel | Backup, atualização e recuperação por nossa conta | Operação sem ganho |

## 4. Consequências

- **Ganhos:** serviço gerenciado com backup e recuperação; banco fora da internet.
- **Custos e riscos aceitos:** sem alta disponibilidade por zona, uma falha do servidor deixa o sistema fora até a recuperação; acesso manual exige um caminho de rede a mais.
- **O que muda nos documentos:** Implantação passa a citar esta ADR no lugar da P-14.

## 5. Quando revisar

Quando a disponibilidade exigida pedir alta disponibilidade por zona, ou quando o volume pedir réplica de leitura para os relatórios.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-14 aceita pelo usuário. |
| 2026-10-10 | Aceita | Acesso ao banco passa a ser sem senha, pela ADR-0019. |
