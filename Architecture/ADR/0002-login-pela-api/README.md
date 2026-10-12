# ADR-0002 — Login pela API, com a tela própria do Shell Público

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** D-09 (`C4/02-containers.md`) — ADR retroativa
**Afeta:** C4 · Containers, Componentes da API (Endpoints, Acesso), Componentes do Admin, CRM e Marketing (pacote Auth); Shell Público

## 1. Contexto

As três aplicações têm uma tela de login própria, desenhada no Shell Público. O provedor de identidade é o Keycloak (ADR-0001). É preciso decidir quem fala com o Keycloak no login: a aplicação web, por redirecionamento, ou a API.

## 2. Decisão

**A tela de login envia e-mail e senha à API, que autentica no Keycloak e devolve os tokens.** As aplicações web não falam com o Keycloak.

- A API usa o fluxo de credenciais diretas do Keycloak (Direct Access Grant) num cliente dedicado ao SmartCorbans.
- Renovação de token e logout também passam pela API.
- O pacote Auth do monorepo só conhece a API.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Redirecionar ao Keycloak (Authorization Code + PKCE), com tema próprio | Padrão recomendado de OIDC; a senha nunca passa pela API; MFA e SSO prontos | Perde a tela do Shell Público; exige tema no Keycloak | A tela própria já está desenhada e é a decisão de produto |
| Aplicação web chama o token endpoint do Keycloak direto | Sem passo na API | Expõe o Keycloak e o cliente às três SPAs | Duas portas de entrada em vez de uma |

## 4. Consequências

- **Ganhos:** uma única porta de entrada (a API); a tela de login é a do design system; as SPAs não precisam saber que o Keycloak existe.
- **Custos e riscos aceitos:** a senha transita pela API, que precisa de limite de tentativas e não pode registrar o corpo da requisição de login; o fluxo de credenciais diretas é desaconselhado pelas boas práticas atuais do OAuth, então MFA e SSO vão exigir rever esta decisão.
- **O que muda nos documentos:** já refletido em Containers v0.2 e nos Componentes.

## 5. Quando revisar

Quando MFA ou SSO corporativo entrarem no escopo: os dois funcionam melhor com redirecionamento ao Keycloak.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Registrada retroativamente a partir de D-09. |
