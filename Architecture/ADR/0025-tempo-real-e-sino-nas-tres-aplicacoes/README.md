# ADR-0025 — Tempo real nas três aplicações e sino único por usuário

**Status:** Aceita
**Data:** 2026-10-11
**Decisores:** Rafael da Conceição
**Origem:** Modelo de dados · Apoio (`Dados/12-apoio.md`), revisão de usabilidade das notificações
**Afeta:** ADR-0010 e ADR-0015 (escopo estendido); C4 · Containers, Componentes da API e do Admin; Shell Privado §6; modelo de dados de Notificações

## 1. Contexto

A ADR-0015 ligou ao hub de tempo real o CRM e o Marketing, e o sino era lido por aplicação. Um usuário do tenant (escopo `all`) que processa uma Lista no Marketing e vai para o Admin não fica sabendo que a Lista terminou ou falhou até voltar ao Marketing: o sino do Admin não mostra o aviso, e o Admin nem recebe eventos. As três aplicações compartilham o mesmo Shell e o mesmo usuário; o sino responde "o que precisa da minha atenção", não "o que precisa de atenção nesta aplicação".

## 2. Decisão

**O sino é único por usuário, igual nas três aplicações, e as três se conectam ao hub de tempo real.**

- **Sino único:** mostra as notificações de todas as aplicações em que o usuário tem acesso, cada item com o **selo da aplicação de origem**. O item leva à tela da ação, mesmo em outra aplicação (pelo app switcher). O indicador de não lidas conta todas.
- **Admin no hub:** o Admin passa a se conectar ao hub, para receber notificações na hora. Eventos de tela (invalidar consultas) continuam só onde há tela que os use.
- Valem as regras da ADR-0010: o evento só avisa, o dado vem da API; o hub respeita escopo e permissão; réplica única, sem backplane (ADR-0009).
- No modelo de dados, a coluna `app` da notificação serve ao selo e ao link, não a filtro.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Sino por aplicação (como estava) | Cada app só vê o seu | O aviso fica preso na app de origem; o usuário multi-app perde o que precisa agir | Usabilidade ruim, apontada pelo usuário |
| Sino único sem o Admin no hub (Admin lê ao carregar) | Uma conexão a menos | No Admin, o aviso só aparece ao recarregar | Atraso justamente no caso do usuário multi-app |

## 4. Consequências

- **Ganhos:** nenhum aviso fica preso numa aplicação; um só comportamento de sino no produto.
- **Custos e riscos aceitos:** mais conexões no hub (todas as sessões das três apps); o link de uma notificação pode mudar de aplicação, o que o Shell precisa tratar.
- **O que muda nos documentos:** ADR-0010 e ADR-0015 (escopo); Shell Privado §6 (sino único, selo de aplicação); C4 Containers e Componentes da API e do Admin (Admin ligado ao Tempo real); Dados · Apoio (uso da coluna `app`).

## 5. Quando revisar

Junto com a ADR-0009 e a ADR-0010, se a API ganhar réplicas ou se o número de conexões pesar no hub.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-11 | Aceita | Proposta na revisão das notificações; aceita pelo usuário. |
