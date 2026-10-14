# ADR-0026 — Keycloak só para autenticação; usuários, permissões e escopo no SmartCorbans

**Status:** Proposta
**Data:** 2026-10-11
**Decisores:** Rafael da Conceição; revisão do time de backend
**Origem:** Modelo de dados · Acesso (`Dados/01-acesso.md`, suspenso) e `Dados/01-estrutura.md` §6, item 2
**Afeta:** ADR-0001 (substitui a parte "Loja = Organização"); ADR-0002; C4 · Componentes da API (Acesso, Endpoints e autorização, Adaptadores externos); Modelo de dados (Acesso volta ao modelo; Estrutura); front (`@corban/permissions`, Shell)

## 1. Contexto

O produto tem quatro escopos cumulativos: `all`, `store`, `team` e `self` (spec §5; Cargos §3.1). A ADR-0001 previa espelhar cada Loja numa **Organização** do Keycloak. Durante a modelagem de dados, chegou a ser cogitado levar também Cargos, permissões e Time para o Keycloak.

Três forças pesam contra pôr escopo e permissão no Keycloak:

- **Organizations cobre só um dos quatro níveis.** `team` precisaria de outro mecanismo, `self` é por usuário e `all` é a ausência de restrição. Metade do escopo ficaria no Keycloak e metade na API.
- **O escopo é lido o tempo todo, inclusive fora de requisição.** Distribuição de Leads (operadores ativos, com Time do Segmento e "Pode receber lead", RN-LED-04), filtro de escopo do EF Core (ADR-0012), Metas e Rankings por Cargo e o Worker (sem usuário logado) leem Loja, Time e Cargo do banco.
- **Mudança de Cargo vale na hora** (RN-CRG-02). Permissão dentro do token só mudaria na renovação.

Ao mesmo tempo, há duas necessidades práticas: o **front** precisa saber as permissões do usuário para exibir só as telas e ações permitidas, e o **backend .NET** quer validar o acesso de forma declarativa nos endpoints, como o `[Authorize(Roles = ...)]` faz com claims do token.

## 2. Decisão

**O Keycloak cuida só da autenticação. Usuários, Cargos, permissões e escopo vivem no SmartCorbans (módulo Acesso), e a API os resolve a cada requisição.**

**Keycloak**
- E-mail e senha, sessões, emissão e validação do token, usuário habilitado ou não; MFA e SSO no futuro.
- O token carrega a **identidade** (`sub`), não as permissões.
- **Sem Organizations**: a Loja não é espelhada no Keycloak. O realm único e a consolidação dos realms atuais na migração (ADR-0001) continuam.

**SmartCorbans (módulo Acesso, schema `access`)**
- Usuário com nome, e-mail, status, Owner, Cargo, Loja, Time e "Pode receber lead", ligado ao Keycloak pelo `keycloak_id`.
- Cargos e suas linhas de permissão (`recurso:ação:escopo`); permissões adicionais por usuário.
- Os demais módulos referenciam o **usuário interno** (`*_user_id`).
- **Consistência com o Keycloak** pelo adaptador, no módulo Acesso: criar usuário cria no Keycloak e no banco, com compensação se uma das partes falhar; inativar desabilita no Keycloak e revoga as sessões (RN-USR-05); mudar o e-mail reflete nos dois.

**Backend: autorização declarativa sem permissões no token**
- Um **`IClaimsTransformation`**, depois da validação do JWT, carrega as permissões efetivas do usuário (Cargo ∪ adicionais) do módulo Acesso, com **cache em memória**, e as adiciona como claims ao `ClaimsPrincipal` da requisição.
- Os endpoints usam um atributo próprio, **`[RequirePermission("offers", "view")]`**, sobre policies (`IAuthorizationRequirement` + handler). `[Authorize(Roles = ...)]` e `User.IsInRole` também funcionam, porque as claims estão lá.
- O endpoint checa **`recurso:ação`**; o **escopo** é aplicado pelo filtro do EF Core, com Loja e Time do banco (ADR-0012).
- Mudou Cargo, linhas do Cargo ou permissões adicionais: o cache do(s) usuário(s) afetado(s) é invalidado. Com réplica única (ADR-0009), a invalidação é local.

**Front: permissões pela API, atualizadas em tempo real**
- A **resposta do login** (que já passa pela API, ADR-0002) devolve, além dos tokens, o perfil do usuário (nome, Loja, Time) e as **permissões efetivas**.
- **`GET /me/access`** devolve o mesmo, para recarregar a página; o front guarda em cache (TanStack Query) e o `@corban/permissions` lê dali.
- Quando as permissões do usuário mudam, a API envia o evento **"permissões alteradas"** pelo hub de tempo real (ADR-0025). O front invalida a consulta e o menu se ajusta sem novo login.
- No front, a permissão só **esconde**; quem protege é a API.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Loja = Organização no Keycloak (ADR-0001) | Recurso pronto do Keycloak | Cobre só `store`; espelhamento a manter; escopo dividido entre dois lugares | Não atende `team`, `self`, `all` |
| Cargos e permissões como papéis do Keycloak, no token | `[Authorize(Roles)]` direto; front lê do token | Duas fontes de verdade; mudança só na renovação do token (fere RN-CRG-02); JWT grande com dezenas de permissões; cópia local necessária para distribuição e Worker | Mais complexidade e inconsistência do que ganho |
| Permissões no token via mapper customizado do Keycloak que consulta a API | Fonte única no banco; permissões no token | Mapper em Java (SPI) para manter; continua desatualizado até renovar; token grande | O `IClaimsTransformation` dá o mesmo resultado no backend sem SPI |
| API emite token próprio (troca do token do Keycloak) | Controle total do conteúdo | Mais um emissor de token, rotação de chaves, revogação | Complexidade sem necessidade |

## 4. Consequências

- **Ganhos:** uma fonte de verdade para quem pode o quê e sobre quais dados; escopo nos quatro níveis consultável pelo banco, inclusive no Worker; mudança de Cargo com efeito imediato na API e na tela; Keycloak trocável sem mexer no modelo de acesso; auditoria (RNF005) na mesma transação.
- **Custos e riscos aceitos:** dois cadastros a manter coerentes (Keycloak e banco), com compensação e testes no adaptador; uma leitura de permissões por requisição, absorvida pelo cache; o front depende de uma chamada (`/me/access`) além do token.
- **O que muda nos documentos:** ADR-0001 passa a "Substituída em parte pela ADR-0026" (sai "Loja = Organização"); Modelo de dados: Acesso volta (schema `access`, com base no `01-acesso.md`), sai `keycloak_organization_id` de `structure.store`, e `*_user_id` passa a apontar para `access.user`; C4 · Componentes da API: Acesso com `IClaimsTransformation` e `/me/access`, adaptador do Keycloak sem Organizations; Componentes de Admin, CRM e Marketing: Permissões lê de `/me/access` e reage ao evento de tempo real.

## 5. Quando revisar

Se a API ganhar réplicas (o cache de permissões precisa de invalidação distribuída, junto com a ADR-0009 e a ADR-0010); se o SSO corporativo exigir que grupos do provedor externo definam Cargos.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-11 | Proposta | Proposta na revisão do módulo Acesso, para comentários do time de backend antes de aceitar. |
