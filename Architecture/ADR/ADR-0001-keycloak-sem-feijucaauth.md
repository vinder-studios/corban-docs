# ADR-0001 — Keycloak atual como provedor de identidade, sem a FeijucaAuth

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** D-01 e D-07 (`C4/01-contexto.md`) — ADR retroativa
**Afeta:** C4 · Contexto, Containers, Componentes da API (Acesso, Adaptadores externos), Implantação; Admin · Usuários; Especificação Geral §4.2

## 1. Contexto

O SmartCorbans precisa de login por e-mail e senha, gestão de sessão e criação de usuários por quem tem permissão, sem cadastro público (spec §4.2). A Smartconsig já usa um **Keycloak**, acessado hoje por uma abstração própria, a **FeijucaAuth**. A FeijucaAuth foi construída para dar ao sistema atual a capacidade de trabalhar **multi-tenant**, com uma abordagem **multi-realm** (um realm por tenant), o que o Keycloak sozinho não resolvia na época.

Hoje os usuários estão em **um realm por parceiro**.

Dois fatos mudaram esse cenário: o SmartCorbans é **instância única** (spec §1.1; parceiros são Lojas, não tenants), e o Keycloak passou a oferecer o recurso de **Organizações**, que resolve multi-tenant dentro de um único realm. Todos os usuários do sistema atual vão migrar (D-06), e recriar credenciais sem recuperação de senha (D-03) deixaria os usuários sem acesso no primeiro dia.

## 2. Decisão

O SmartCorbans usa **o mesmo Keycloak e a mesma base de identidade de hoje**, falando com ele **diretamente**, sem a FeijucaAuth.

- **Um único realm** para o SmartCorbans.
- **Cada Loja é uma Organização** do Keycloak, inclusive a Loja da própria Smartconsig. O usuário com Loja é membro da Organização dela; usuário sem Loja (Marketing, backoffice, nível tenant, RN-USR-15) não é membro de nenhuma.
- **O SmartCorbans é a fonte da verdade** de Loja, vínculo e permissões. A Organização espelha a Loja: criar Loja cria a Organização; vincular ou desvincular usuário muda a participação; inativar ou excluir a Loja desativa a Organização. Quem faz isso é a API, pelo adaptador do Keycloak. Permissão e escopo continuam decididos na API, não no token.
- **Os realms atuais são consolidados** nesse realm único durante a migração, com as senhas já criptografadas (a exportação de realm do Keycloak as preserva). Usuários e senhas continuam valendo; a migração de Usuários do SmartCorbans leva só os dados de negócio (Cargo, Loja, Time).
- Só a API fala com o Keycloak (ver ADR-0002), pelo módulo Acesso e pelo adaptador do Keycloak.
- SSO corporativo fica fora desta versão; quando entrar, é configurado no próprio Keycloak.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Manter a FeijucaAuth | Já existe e funciona | Foi feita para multi-tenant por multi-realm, que o SmartCorbans não precisa; camada a mais para manter, com divergências conhecidas entre documentação e comportamento | O motivo de existir acabou: não há multi-tenant, e se houver, o Keycloak resolve com Organizações |
| Novo provedor de identidade | Começar limpo | Migrar credenciais, sem recuperação de senha na v1 | Usuários ficariam sem acesso na virada |
| Identidade própria na API | Controle total | Reimplementar segurança de senha, sessão e bloqueio | Risco alto sem ganho |

## 4. Consequências

- **Ganhos:** nenhuma migração de credencial; menos uma camada para manter; SSO e MFA futuros já têm onde morar.
- **Custos e riscos aceitos:** a consolidação dos realms precisa resolver **e-mails repetidos entre realms** antes da carga, porque o e-mail é único na instância (RN-USR-04); entra no plano de migração (D-08). Manter Loja e Organização sincronizadas é responsabilidade da API, e uma falha no meio deixa as duas divergentes, então a operação precisa ser repetível sem efeito duplicado. O SmartCorbans depende da disponibilidade do Keycloak atual (sem ele não há login, spec RNF004); a API precisa de um cliente com permissão na Admin API do Keycloak para criar usuário, definir senha e revogar sessão.
- **O que muda nos documentos:** já refletido em Contexto v0.5, Containers v0.2, Componentes da API v0.4 e spec v2.4.

## 5. Quando revisar

- Se o Keycloak atual mudar de dono ou de hospedagem de forma que o SmartCorbans não controle, ou se a Smartconsig decidir aposentá-lo.
- Se o produto voltar a ser **multi-tenant** (spec §11): a solução é o recurso de **Organizações** do Keycloak, e não reintroduzir uma abstração própria.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Registrada retroativamente a partir de D-07. |
| 2026-10-10 | Aceita | Revisão do contexto pelo usuário: motivo de existir da FeijucaAuth (multi-tenant por multi-realm) e o recurso de Organizações do Keycloak. |
| 2026-10-10 | Aceita | Decisão complementada pelo usuário: realm único, cada Loja é uma Organização, consolidação dos realms por parceiro na migração. |
