# Corban CRM · Créditos

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.7
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.7 | **O débito deixa de ter Produto**: o Funil não tem Produto e a Lead não tem Oferta no momento do débito. O débito passa a ser identificado por **Segmento (Funil) e Origem da lead**, o que de fato define o preço (RN-FNL-07), e o filtro do Extrato troca Produto por Segmento e Origem da lead. Mesma correção já feita em Admin · Relatórios v0.5. Decisão do usuário, no modelo de dados. |
| 0.6 | **Só Pix e AbacatePay** (decisão do usuário, D-04 em `Architecture/C4/01-contexto.md`). RF-CRD-03 passa a adicionar crédito só por Pix; Boleto vira nota de evolução. O gateway passa a ser o **AbacatePay**. Ajusta Objetivo, Atores, Extrato, Detalhes da operação, RN-CRD-08, estados e dependências. |
| 0.5 | O preço do débito passa a depender da **Origem da lead** (Campanha, Orgânica, Prospecção ou Carteira Ativa), e não mais de Interno/Externo; leads de Recontato e Manual não debitam (RN-LED-13). Corrige "módulo de Leads, a redigir", que já existe, e a referência do Extrato ("Funil/Tipo") para Funil e Origem da lead. |
| 0.4 | Fecha os dois itens abertos da v0.3: a flag "Pode receber lead sem saldo" **continua valendo**, intocada; RN-CRD-07 (Limite de gasto diário bloqueia leads ao ser atingido) **confirmada**, deixa de ser inferência. Resta só a nota técnica de concorrência (mantida a pedido). |
| 0.3 | **Reformulação de fundo.** A Loja deixa de ser só consulta — ganha **Adicionar crédito (Pix ou Boleto)** e **Limite de gasto diário** (editável, com indicador de gasto do dia). Extrato ganha filtro por Produto e Detalhes da operação (lead, para Débito; método de pagamento, para Crédito). Fecha 3 dos 4 itens abertos: Estorno entra nesta versão (RF-CRD-05); concorrência na checagem de saldo mantida como nota técnica pro backend; alerta de saldo baixo vira indicador no **Dashboard do CRM** (não Admin). Interpretação registrada, não confirmada: saldo negativo "não permitido" (resposta ao item de cobrança futura) lido como confirmação da regra padrão para Lojas sem a flag "Pode receber lead sem saldo" — a flag em si **não foi revogada** aqui, ver item aberto. |
| 0.2 | Adiciona a flag **"Pode receber lead sem saldo"** (campo da Loja, Admin — módulo de Lojas): quando marcada, a Loja recebe a lead mesmo sem saldo suficiente, e o débito é aplicado mesmo assim — **revoga RN-CRD-03** (saldo nunca negativo). Caso de uso: a Loja do próprio tenant operador. |
| 0.1 | Versão inicial. **Substitui a aplicação "Cash"** referenciada em módulos anteriores (Funis de Vendas) como consumidora de custo/preço de lead — aquilo era um nome provisório; este módulo é a implementação real, dentro do CRM. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-CRD) e regras de negócio (RN-CRD) próprios.

> **Contexto de acesso.** Como Tabela de Multiplicadores, este módulo existe no CRM porque é a Loja parceira gerenciando algo de si mesma. *(Correção da v0.2 — não é mais só consulta.)* A Loja agora **edita** o próprio Limite de gasto diário e **adiciona** o próprio saldo via Pix (RF-CRD-03/04) — sem depender do tenant operador para isso. A adição manual pelo tenant operador (Admin, módulo de Lojas) continua existindo, como segunda via, não mais exclusiva.

---

# 1. Objetivo do módulo

Manter o saldo em reais de cada Loja parceira, debitado automaticamente a cada lead entregue, com extrato completo dos lançamentos — e permitir que a própria Loja adicione crédito (Pix) e controle um limite de gasto diário.

---

# 2. Atores

- **Loja parceira (usuário do CRM)** — consulta saldo e extrato, adiciona crédito via Pix (RF-CRD-03), edita o próprio Limite de gasto diário (RF-CRD-04).
- **Tenant operador (usuário do Admin)** — adiciona saldo manualmente, no módulo de Lojas (RN-LOJ-11); aciona estorno (RF-CRD-05).

---

# 3. Entidades e conceitos

## 3.1. Saldo

Um saldo por Loja. Campos: **Valor** (R$), sempre ≥ 0 exceto para Lojas com a flag "Pode receber lead sem saldo" (RN-CRD-03) — e **Limite de gasto diário** (R$, opcional — RN-CRD-07), editável pela própria Loja.

## 3.2. Extrato

Ledger **append-only** — nenhum lançamento é editado ou excluído (estorno, quando necessário, gera um lançamento novo — RN-CRD-06, nunca apaga o original). Cada entrada:

| Campo | Notas |
|---|---|
| Loja | — |
| Tipo | Crédito ou Débito |
| Segmento e Origem da lead | Só em Débito — o Segmento (Funil) e a Origem da lead que definiram o preço (RN-FNL-07, módulo de Funis de Vendas). Vazios em Crédito. |
| Valor | — |
| Data/hora | — |
| Descrição/Referência | Para Débito: referência à lead entregue e ao Funil e à Origem da lead que originaram o preço. Para Crédito: origem — Pix, adição manual (com o usuário do Admin que adicionou), ou Estorno (com referência ao Débito original — RN-CRD-06). |
| Saldo resultante | Snapshot do saldo da Loja **após** aquele lançamento — permite auditoria sem precisar somar o extrato inteiro. |

## 3.3. Detalhes da operação (por lançamento)

Além dos campos do Extrato, cada lançamento tem um conjunto de detalhes próprio, consultável individualmente (RF-CRD-06):

- **Débito:** os dados da lead que gerou o débito — Nome do cliente/contato, Origem, Funil, data/hora de atribuição.
- **Crédito via Pix:** método, identificador da transação (ID no AbacatePay), status do pagamento, data de confirmação.
- **Crédito manual (Admin):** usuário do Admin que adicionou, nota opcional (já existente).
- **Estorno:** referência ao Débito original estornado, motivo (texto livre do Admin).

---

# 4. Requisitos funcionais

## RF-CRD-01 — Consulta de saldo e extrato

**Como** usuário da Loja parceira, **eu quero** ver meu saldo atual e o histórico de lançamentos **para** entender meu consumo e planejar recargas.

**Comportamento esperado:** saldo atual e Limite de gasto diário em destaque, com indicador do quanto já foi gasto hoje em relação ao limite (RN-CRD-07). Lista de lançamentos com filtro por período, por Segmento, por Origem da lead e por Tipo (Crédito/Débito). Cada lançamento abre os Detalhes da operação (RF-CRD-06).

## RF-CRD-02 — Débito automático por lead entregue

**Como** sistema, **eu quero** debitar o saldo da Loja a cada lead atribuída **para** que o consumo reflita o uso real, sem intervenção manual.

**Comportamento esperado:** toda vez que uma lead **que cobra** é atribuída a uma Loja (módulo de Leads, RN-LED-13), o sistema debita o preço configurado para a Origem da lead — Campanha, Orgânica, Prospecção ou Carteira Ativa — na aba Preço das leads do Funil correspondente (módulo de Funis de Vendas). Leads de Recontato e Manual não debitam. Gera um lançamento de Débito no Extrato, com o saldo resultante já refletindo a dedução.

**Regras específicas:**

**RN-CRD-01 — Débito só ocorre com saldo suficiente; sem saldo, a lead não é atribuída — exceto para Lojas com a flag "Pode receber lead sem saldo".** Antes de atribuir uma lead a uma Loja, o sistema verifica se o saldo cobre o preço daquela lead:

| Situação | Efeito |
|---|---|
| Saldo suficiente | Débito normal, lead atribuída. |
| Saldo insuficiente, sem a flag | Lead **não é atribuída** a esta Loja — o módulo de Leads tenta a próxima Loja elegível no rodízio. |
| Saldo insuficiente, **com a flag marcada** | Lead **é atribuída mesmo assim**, e o débito é aplicado normalmente — o saldo pode ficar **negativo** (RN-CRD-04). |

A flag é campo da Loja, definido no módulo de Lojas (Admin, aba Créditos) — não deste módulo.

**RN-CRD-02 — Adição de saldo é exclusiva do tenant operador, fora deste módulo.** Não existe, nesta versão, nenhuma ação de adicionar saldo dentro do CRM — nem para a própria Loja, nem para ninguém. A ação vive no Admin, módulo de Lojas (RN-LOJ-11).

**RN-CRD-03 — Saldo nunca é negativo, exceto para Lojas com a flag.** *(Revisão da versão anterior, que afirmava isso sem exceção.)* Para a grande maioria das Lojas, continua valendo — RN-CRD-01 impede o caminho que levaria a saldo negativo. A exceção é deliberada, e existe só para a Loja marcada com "Pode receber lead sem saldo" — tipicamente a Loja do próprio tenant operador, que não deveria travar por saldo.

**RN-CRD-04 — Saldo negativo não bloqueia nada, nesta versão.** Uma Loja com saldo negativo continua recebendo leads normalmente (se tiver a flag) e nenhuma outra funcionalidade é restringida por isso. Não há cobrança automática, alerta de inadimplência, nem trava — o saldo negativo é só um número acumulado no extrato, visível a quem consultar (ver item aberto §9.4).

## RF-CRD-03 — Adicionar crédito (Pix)

**Como** usuário da Loja parceira, **eu quero** adicionar saldo diretamente pelo CRM, via Pix **para** não depender do tenant operador para recarregar.

**Comportamento esperado:** o usuário informa o valor desejado e o sistema gera a cobrança Pix (QR Code e código copia-e-cola) no **AbacatePay**. Ao confirmar o pagamento (via integração com o AbacatePay, fora de escopo detalhar aqui), o sistema credita o saldo automaticamente e gera o lançamento de Crédito correspondente, com a origem identificada (RN-CRD-08).

## RF-CRD-04 — Editar Limite de gasto diário

**Como** usuário da Loja parceira, **eu quero** definir um teto de gasto diário **para** controlar o ritmo de consumo do saldo, independentemente do valor disponível.

**Comportamento esperado:** campo editável a qualquer momento, efeito imediato. Sem Limite definido, não há teto — só o saldo disponível governa (RN-CRD-01). *(Comportamento ao atingir o limite — RN-CRD-07, inferido, não confirmado explicitamente — ver item aberto.)*

## RF-CRD-05 — Estorno de débito (Admin)

**Como** usuário do Admin, **eu quero** estornar um débito aplicado indevidamente **para** corrigir o saldo da Loja sem apagar o histórico.

**Comportamento esperado:** a partir do Detalhe de um lançamento de Débito (RF-CRD-06), o Admin aciona "Estornar", informa um motivo, e o sistema gera um lançamento de **Crédito** no valor do débito original, referenciando-o (RN-CRD-06). O lançamento original nunca é editado nem excluído — ledger append-only (módulo §3.2).

## RF-CRD-06 — Detalhes da operação

**Como** usuário da Loja ou do Admin, **eu quero** ver o detalhe completo de um lançamento **para** entender exatamente o que o originou.

**Comportamento esperado:** ao clicar num lançamento do Extrato, abre o conjunto de detalhes próprio daquele tipo (módulo §3.3) — dados da lead (Débito) ou dados da operação de pagamento (Crédito).

**Regras específicas:**

**RN-CRD-06 — Estorno gera lançamento novo, nunca apaga o original.** Mesmo princípio de ledger append-only (§3.2) — o Débito original permanece no Extrato, e o Crédito de estorno aparece como entrada própria, com referência cruzada visível nos Detalhes de ambos.

**RN-CRD-07 — Limite de gasto diário, quando atingido, bloqueia novas leads pelo resto do dia** — confirmado. Soma dos Débitos do dia corrente (reiniciando à meia-noite) é comparada ao Limite configurado; ao atingir ou ultrapassar, novas leads **não são atribuídas** a esta Loja até o dia seguinte — mesmo mecanismo de bloqueio de RN-CRD-01, gatilho diferente.

**RN-CRD-08 — Origem do Crédito é sempre identificada.** Todo lançamento de Crédito registra como nasceu — Pix, adição manual (Admin) ou Estorno — nunca um Crédito "genérico" sem origem rastreável.

> **Nota de evolução, parcialmente superada nesta versão.** A adição de saldo por gateway de pagamento, cogitada como evolução futura na v0.2, passa a existir nesta versão (RF-CRD-03) — mantém-se como nota só a integração de fato com o AbacatePay (detalhes técnicos do Pix), que fica para quem for implementar.

> **Nota de evolução.** **Boleto** como segundo meio de pagamento na adição de crédito, também pelo AbacatePay. Fora desta versão (decisão D-04).

---

# 5. Regras de negócio (consolidado)

- **RN-CRD-01** — Débito condicionado a saldo suficiente; sem saldo, sem atribuição de lead (exceto Lojas com a flag "Pode receber lead sem saldo" — RN-CRD-03/04).
- **RN-CRD-02** — Adição de saldo manual continua existindo no Admin, mas deixou de ser exclusiva — Loja também adiciona, via Pix (RF-CRD-03).
- **RN-CRD-03** — Saldo nunca é negativo, exceto para Lojas com a flag.
- **RN-CRD-06** — Estorno gera lançamento novo, nunca apaga o original.
- **RN-CRD-07** — Limite de gasto diário, atingido, bloqueia novas leads pelo resto do dia — confirmado.
- **RN-CRD-08** — Origem do Crédito é sempre identificada.

---

# 6. Estados e transições

Sem estados de publicação — Saldo é um valor numérico contínuo, Extrato é um ledger append-only. Não há ciclo de vida além dos lançamentos em si.

**Crédito via Pix** tem um estado transitório, entre o pedido e a confirmação:

| Estado | O que significa |
|---|---|
| Aguardando pagamento | Cobrança Pix gerada, saldo ainda não creditado |
| Confirmado | Pagamento identificado pelo AbacatePay, saldo creditado, lançamento criado |
| Expirado/Cancelado | Prazo esgotado sem pagamento — sem efeito no saldo |

---

# 7. Limites do módulo

**Não** inclui: a ação de adicionar saldo manualmente (Admin, módulo de Lojas), a lógica de distribuição de leads quando o saldo ou o Limite de gasto diário é insuficiente (módulo de Leads), nem os detalhes técnicos da integração com o AbacatePay (Pix — RF-CRD-03 define o comportamento, não a implementação).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Funis de Vendas** (Admin) — o preço da lead, por Origem da lead, que determina o valor e a identificação (Segmento e Origem) de cada débito.
- **Leads** (CRM) — o evento de atribuição de lead, que dispara o débito; e os dados da lead exibidos em Detalhes da operação (RF-CRD-06).
- **Lojas** (Admin) — a ação de adicionar saldo manualmente (RN-LOJ-11), que gera lançamentos de Crédito.
- **AbacatePay** (externo, não modelado) — cobrança e confirmação do Pix (RF-CRD-03).

**Expõe para:**
- **Leads** (CRM) — a checagem de saldo suficiente e de Limite de gasto diário (RN-CRD-01, RN-CRD-07), pré-requisitos da atribuição.
- **Lojas** (Admin) — o Saldo e o Extrato, exibidos na aba Créditos daquele módulo.
- **Dashboard do CRM** (a redigir) — indicador de saldo baixo/zerado (item aberto §9.3, decisão do usuário: fica no Dashboard do CRM, não no Admin).

---

# 9. Itens abertos

1. **Concorrência na checagem de saldo.** Duas leads sendo atribuídas à mesma Loja ao mesmo tempo, quando o saldo só cobre uma — condição de corrida técnica, não decisão de produto. Mantido a pedido do usuário — precisa ser passado para o time de backend.

---

*Fim do documento. Créditos v0.5 — módulo da aplicação CRM.*
