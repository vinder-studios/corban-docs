# SmartCorbans — Especificação Geral do Produto

**Status:** Draft
**Versão:** 2.6
**Data:** [a definir]
**Autor:** [a definir]
**Aprovado por:** [a definir]
**Substitui:** `corban-especificacao-v1` (v1.2) e as v2.0 a v2.2
**Documentos de Produto das aplicações:** Admin v0.2 · CRM v0.1 · Marketing v0.8

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 2.6 | Inclui o **ViaCEP** na §4.4, que já constava em CRM · Clientes (RN-CLI-07). |
| 2.5 | **Auditoria decidida (RNF005).** Lista única de ações críticas por critério (acesso, dinheiro, regra comercial, dados pessoais e estrutura), conteúdo do registro e consulta sem tela na v1; a tela de auditoria vira nota de evolução (§11). Fecha o último item aberto da §12. Corrige a referência do RNF003 (RN-REL-05). Decisão do usuário. |
| 2.4 | **Acesso, pagamento e transição decididos** (C4 · Contexto, D-01 a D-07). (1) **Keycloak** é o provedor de identidade, o mesmo já usado hoje, acessado diretamente, sem a abstração FeijucaAuth; **SSO corporativo não entra** agora. (2) **Senha inicial definida por quem cadastra**, sem troca obrigatória e sem recuperação de senha ou e-mails de cadastro nesta versão. (3) **AbacatePay** é o provedor de pagamento, **só Pix**. (4) A **migração é completa** (configuração base, Leads, Clientes, Usuários etc.), com o detalhe num plano de migração próprio. Inclui a **API do Banco Central** na §4.4, que já constava nos módulos. Atualiza §3.2, §4.2, §4.4, RNF004, §8, §10 e §11; os itens 1 e 2 da §12 são fechados e o antigo item 3 passa a item 1. |
| 2.3 | **Duas notas de evolução novas (§11):** IA para inteligência de negócio e leilão CLT. Decisão do usuário. |
| 2.2 | **Remove a §1.3 (problemas que a evolução resolve)** e as menções a resolver problemas do sistema atual. O item aberto correspondente sai da §12 e os itens seguintes são renumerados. A §1.4 e a §1.5 passam a §1.3 e §1.4. |
| 2.1 | **Instância única (single-tenant).** O SmartCorbans **não é multi-tenant**: é uma instância da Smartconsig, e os **parceiros da Smartconsig entram como Lojas**. Consequências: o isolamento que importa é **entre Lojas** (RNF002 reescrito); o **Console da Plataforma sai do escopo** (não há tenants a provisionar); o termo **tenant** passa a significar a própria organização Smartconsig. O impacto nos documentos das aplicações está em §14. |
| 2.0 | **Nova especificação geral, com o nome SmartCorbans.** (1) O produto passa a se chamar **SmartCorbans** e é apresentado como **proposta de evolução do sistema que a Smartconsig usa hoje**. (2) A Parte I da v1.2 é reescrita sobre o modelo atual: três aplicações (Admin, CRM e Marketing), Cargos no lugar de Grupos, Loja e Time no lugar de Operação, Segmento com Funil próprio, Oferta, Regras, Origem da lead, distribuição em duas camadas, Créditos e Proposta. (3) A **Parte II da v1.2 (módulos 1 a 11, RF001–RF043) é aposentada**: o detalhe de cada módulo vive hoje nos documentos de módulo, e cada aplicação tem o seu Documento de Produto. (4) Os RNFs são atualizados (§6) e a numeração **RNF001–RNF006 é mantida**. (5) O mapa de módulos passa de 11 para 32 (§9). **Não cria regra de módulo**: aponta para o documento de origem. |
| 1.2 | Versão anterior, ainda com o nome Corban. Ver `corban-especificacao-v1`. |

---

# 1. Visão geral

## 1.1. O que é o SmartCorbans

O **SmartCorbans** é uma plataforma SaaS de gestão comercial para **correspondentes bancários** que operam crédito consignado. Ele reúne, em um só sistema, a **estrutura da operação** (quem acessa o quê, quais Lojas e Funis existem, quais Ofertas são vendidas), o **dia a dia comercial** (Leads, atendimento, Simulações, Propostas, metas e saldo) e a **geração de demanda** (Prospecção e Carteira Ativa).

O sistema é **single-tenant**: é uma instância única, operada pela **Smartconsig**. Os **parceiros da Smartconsig são tratados como Lojas** dentro dessa instância, isolados entre si pelo escopo de acesso. Neste documento e nos de módulo, **tenant** é a própria organização Smartconsig (a dona da instância), e não um cliente entre vários.

## 1.2. Contexto: uma evolução do sistema atual

O SmartCorbans é uma **proposta de evolução do sistema que a Smartconsig usa hoje**, sem ser uma cópia do que existe.

Isso orienta duas leituras do documento:

- **O ponto de partida é o negócio como ele funciona hoje**, e não o sistema como ele foi construído. Onde o modelo do sistema atual e o do negócio divergem, a especificação segue o negócio.
- **O SmartCorbans é entregue como evolução**, não como produto novo no vazio: a transição da Smartconsig entra nos pré-requisitos de lançamento (§10).

## 1.3. Personas

**Sérgio — dono da Smartconsig (Owner).** Define a estrutura da operação: Cargos, Lojas (inclusive as dos parceiros), Segmentos, Funis e Ofertas. Precisa saber o que cada Loja produz e quanto custa a Lead. Usa o **Admin** e os relatórios globais.

**Marina — gestora de Loja.** Lidera Times de operadores, acompanha metas, funil e saldo da Loja, e cuida dos Membros. Usa o **CRM**, dentro do escopo `store`.

**Diego — operador de vendas.** Recebe Leads pela distribuição, conversa pelo Chat, simula contra as Ofertas, formaliza a Proposta e acompanha a própria meta. Usa o **CRM**, normalmente com escopo `self`.

**Gestor de Marketing.** Importa bases, cria Listas e Varreduras, aprova a injeção de Leads e acompanha o retorno. Usa o **Marketing**, acima das Lojas.

Os nomes são figuras de referência, e não papéis do sistema. O acesso é sempre por permissão (§5).

## 1.4. Promessa de valor

O SmartCorbans acompanha o negócio do primeiro contato à Proposta Paga: a demanda é gerada ou captada, a Lead é distribuída à Loja e ao operador certos, é atendida e simulada contra as Ofertas do tenant, vira Proposta e, quando paga, alimenta metas, rankings e relatórios. Cada pessoa vê o sistema pela sua lente: o dono configura e acompanha o todo, a gestora de Loja conduz o Time, o operador trabalha a sua carteira.

---

# 2. Estrutura do produto

## 2.1. As três aplicações

| Aplicação | Papel | Quem usa | Documento de Produto |
|---|---|---|---|
| **Admin** | O tenant **configura a estrutura** da operação. Define as regras do jogo. | Owner e gestores do tenant | `corban-admin-produto-v0_2` |
| **CRM** | A Loja **opera o dia a dia comercial**. Consome o que o Admin define. | Gestores de Loja e operadores | `corban-crm-produto-v0_1` |
| **Marketing** | O tenant **gera demanda** acima das Lojas e entrega Leads ao CRM. | Gestor de Marketing | `corban-marketing-produto-v0_8` |

As três aplicações compartilham o mesmo **Shell** (sidebar, header, app switcher, notificações, tema e menu de conta), descrito em `corban-shell-privado-v0_4`. O app switcher aparece para quem tem acesso a mais de uma aplicação.

Não há aplicação de provisionamento de tenants: sendo uma instância única, o **Console da Plataforma** da v1.2 deixa de existir (§4.3).

## 2.2. Como as aplicações se encaixam

- **Admin → CRM e Marketing.** O Admin fornece o Funil, as Ofertas, as Regras, os cadastros, os Usuários e as Lojas. Os outros dois **consomem**, nunca redefinem.
- **Marketing → CRM.** O Marketing injeta Leads (Origem Prospecção ou Carteira Ativa) e cria Clientes. O CRM **devolve** o desfecho e o histórico de contratos, que alimentam a Carteira Ativa.
- **O mesmo motor.** As Simulações do CRM e a pré-análise em lote do Marketing avaliam as mesmas Ofertas e Regras, com o mesmo resultado.

## 2.3. Fluxo de ponta a ponta

1. **Configuração (Admin).** O tenant monta Cargos, Usuários, cadastros-base, Segmentos (cada um com seu Funil), Regras, Ofertas, Lojas e Times.
2. **Entrada (CRM e Marketing).** A Lead nasce por campanha ou canal orgânico (Huggy), por Recontato, por injeção do Marketing ou por cadastro manual.
3. **Distribuição.** Primeiro entre as Lojas elegíveis, depois entre os operadores da Loja, em rodízio, com cobrança conforme a Origem.
4. **Atendimento.** O operador conversa pelo Chat, executa Ações que movem a Lead pelos Stages do Funil e simula contra as Ofertas.
5. **Formalização.** A Ação Formalizar cria a Proposta, que guarda a Oferta escolhida.
6. **Desfecho.** A Proposta chega a Pago ou Cancelado. O Pago grava a atribuição, calcula o Valor de meta e gera o Contrato no histórico do Cliente.
7. **Acompanhamento.** Metas, Rankings, Dashboards e Relatórios leem o Pago. A Carteira Ativa do Marketing lê o histórico de contratos e reinicia o ciclo.

---

# 3. Escopo

## 3.1. O que entra

- Acesso por **Cargos** e permissões, com escopo por Loja e Time.
- **Estrutura comercial**: Lojas, Times, Segmentos, Funis de Vendas, Layout de Lead, Produtos, Promotoras, Regiões, Status de Proposta e Origem de Venda.
- **Oferta comercial**: Ofertas e Regras de elegibilidade, avaliadas pelo motor interno de Simulação.
- **Operação comercial**: Leads em kanban, Clientes, Simulações, Propostas, Chat (Huggy), Configurações da Loja, Membros e Times.
- **Desempenho**: Metas, Rankings (com Modo TV), Tabela de Multiplicadores, Dashboards e Relatórios.
- **Financeiro da Lead**: preço por Origem, Créditos por Loja, Extrato, limite diário, adição de crédito e estorno.
- **Geração de demanda**: Prospecção (com Lista de bloqueio) e Carteira Ativa.

## 3.2. O que não entra

Por decisão consciente, o SmartCorbans **não inclui** nesta versão:

- **Integrações externas de simulação**, como CLT via Jaéconsig e a consulta de dados de INSS via Consignado Rápido. Hoje só o motor interno está ativo (CRM · Simulações, RN-SIM-08). Entram com o módulo de Integrações (§11).
- **Landing Pages**, **Anúncios** (Meta, Google e TikTok) e o **hub de Integrações** do Marketing (§11).
- **Campanhas** no sentido de disparos de mensagem em massa.
- **Multi-tenant**: outros correspondentes contratando a plataforma, e o **Console da Plataforma** que os provisionaria.
- **Autenticação multifator (MFA)** e **SSO corporativo**.
- **Recuperação de senha, troca obrigatória de senha e e-mails do ciclo de acesso** (cadastro, convite, recuperação). A senha inicial é definida por quem cadastra (Admin · Usuários, RF-USR-03).
- **Boleto** na adição de crédito. Nesta versão, só Pix (CRM · Créditos, RF-CRD-03).
- **Aplicativo móvel nativo**. A aplicação é web e mobile-first (RNF001).
- **Esteira bancária** de ponta a ponta (envio da Proposta aos bancos, averbação e assinatura digital). A Proposta registra o ciclo, mas não se integra aos bancos.
- **Gestão financeira completa** (contas a pagar e receber, conciliação, comissionamento e repasse). O financeiro cobre o saldo de Créditos da Loja e o retorno do Marketing.
- **Base legal, declarações e política de retenção de dados.** Saíram do escopo inicial por decisão do usuário (Marketing, RN-MKT-06).
- **Portal ou aplicativo do cliente final.**

## 3.3. Decisões estruturantes

**Instância única; parceiros são Lojas.** O SmartCorbans atende apenas a Smartconsig. Os parceiros dela são Lojas, e o que os separa é o escopo de acesso, não instâncias distintas. A Loja da própria Smartconsig (tenant operador) é o destino de última instância da distribuição.

**Acesso por permissão, nunca por papel.** Permissões têm a forma `recurso:ação:escopo`. O **Cargo** é um conjunto nomeado de permissões, e cada usuário tem **um Cargo** mais **permissões adicionais** (que somam, nunca subtraem). O **Owner** do tenant é papel de sistema, com bypass total. O acesso a uma aplicação depende de ter ao menos uma permissão dela (Admin · Usuários, RN-USR-16).

**Loja e Time organizam a operação.** A **Loja** é a unidade de venda: tem Região, Segmentos que opera, tipos de lead que recebe e saldo de Créditos. O **Time** pertence a uma Loja e escolhe um Segmento, o que lhe dá acesso ao Funil. Uma Loja nunca vê Leads, Propostas, Conversas ou Créditos de outra.

**Um Segmento, um Funil.** O Segmento é o público pagador atendido (por exemplo, INSS). Cada Segmento nasce com o seu Funil, que define Stages, Ações, Tags e o preço da lead.

**A Lead só se move por Ação.** Não há arrastar o card entre colunas: toda mudança de Stage é a execução de uma Ação do Funil.

**A Origem da lead define a cobrança.** São seis Origens (Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa e Manual), definidas pelo sistema uma única vez. Só quatro têm linha de preço e cobram.

**Duas camadas de distribuição, por rodízio.** Primeiro entre Lojas elegíveis (opera o Segmento, tem a Origem ligada, tem saldo ou a flag "Pode receber lead sem saldo"), depois entre operadores elegíveis da Loja. O débito ocorre ao atribuir.

**A Regra define a condição; a Oferta define o valor.** A Oferta combina Promotora, Produto, Operação (Novo, Refinanciamento, Portabilidade, Portabilidade com Refinanciamento) e Banco, com taxa, prazo e comissão. As Regras são reutilizáveis, e o valor de cada requisito é definido na Oferta.

**Cliente é do tenant; a Loja só o acessa por associação.** O Cliente existe no tenant, pode existir sem Lead e sem Loja, e é identificado por CPF (preenchido depois, no caso do Marketing). A Loja o acessa quando uma Lead dele é distribuída a ela.

**Simulação sempre contextualizada, em três origens.** Manual, Automática via Lead (disparada pela Huggy) e Marketing (gravada na injeção). O cálculo é o mesmo, e a Simulação não consome Créditos. Fica no histórico do Cliente.

**A Proposta congela a Oferta e define o que conta.** O Pago grava a atribuição (Operador, Time, Cargo), calcula o Valor de meta (valor base × multiplicador) e gera o Contrato. As metas contam só o Pago.

**Exclusão suave.** Loja, Time e Usuário excluídos somem das listas, mas o histórico (Extrato, Leads, Propostas, Metas, Relatórios) continua apontando para eles.

---

# 4. Plataforma e integrações

## 4.1. Shell compartilhado

Sidebar, header, app switcher, notificações, toasts, tema, menu de conta e estados transversais (carregando, vazio, erro, sem acesso) são comuns às três aplicações. A navegação oculta o que o usuário não pode usar. As diretrizes de interface estão em `corban-ux-v0_2` e `corban-design-system-v0_5`.

## 4.2. Acesso e autenticação

O acesso é por e-mail e senha, sem cadastro público: os usuários são criados dentro do tenant por quem tem permissão. A identidade fica no **Keycloak**, o mesmo que a Smartconsig já usa hoje, agora acessado diretamente pelo SmartCorbans, sem a abstração FeijucaAuth. Usuários e senhas existentes continuam valendo.

- **Senha inicial.** Quem cadastra o usuário define a senha e a comunica pelo canal que preferir. O usuário acessa de imediato, sem troca obrigatória no primeiro login, e pode trocar a própria senha quando quiser (Admin · Usuários, RF-USR-03).
- **Sem e-mails do ciclo de acesso.** Não há convite, recuperação de senha nem troca obrigatória nesta versão (§11).
- **Sem SSO corporativo** nesta versão (§11).
- A sessão e a gestão de sessões ativas estão em Admin · Usuários.

## 4.3. Sem Console da Plataforma

O Console da Plataforma (provisionar, suspender e reativar tenants) existia por causa do multi-tenant. Com a instância única, **deixa de ser necessário**: a criação do Owner e a carga inicial são tarefas de implantação (§10), e as Lojas dos parceiros são criadas no Admin · Lojas. Se um dia o produto for oferecido a outros correspondentes, a decisão volta a ser tomada (§11).

## 4.4. Integrações e serviços externos

| Serviço | Função no produto | Situação |
|---|---|---|
| **Huggy** | Plataforma de atendimento omnichannel. Origina Leads por campanha ou canal orgânico, espelha a conversa no Chat (as mensagens não são persistidas pelo SmartCorbans) e dispara a Simulação automática via Lead. Ações do Funil podem acionar um fluxo da Huggy, sem bloquear a Ação. | Ativa |
| **AbacatePay** | Provedor de pagamento: gera a cobrança e confirma o **Pix** na adição de crédito. Boleto fica para depois (§11). | Ativa |
| **Keycloak** | Provedor de identidade: login por e-mail e senha e sessão. É o mesmo usado hoje, sem a abstração FeijucaAuth. | Ativa (existente) |
| **SSO corporativo** | Login federado, via Keycloak. | Evolução (§11) |
| **API do Banco Central** | Lista oficial de bancos, usada em Ofertas, Regras e Propostas. Não há cadastro de Bancos no produto. | Ativa |
| **ViaCEP** | Consulta de endereço pelo CEP na ficha do Cliente (CRM · Clientes RN-CLI-07). Falha na consulta não bloqueia o cadastro. | Ativa |
| **Jaéconsig** | Simulação CLT. | Evolução (§11) |
| **Consignado Rápido** | Consulta de dados do benefício (INSS). | Evolução (§11) |
| **Meta, Google e TikTok Ads** | Anúncios do Marketing. | Evolução (§11) |

---

# 5. Acesso e permissões

- A permissão tem a forma **`recurso:ação:escopo`**. O catálogo vive em cada aplicação (Admin v0.2 §2, CRM v0.1 §2, Marketing v0.8 §2).
- O **escopo** tem quatro níveis cumulativos: `self` (o que é do usuário), `team` (+ o Time), `store` (+ a Loja) e `all` (+ todas as Lojas, visão do tenant).
- O escopo `all` serve para **trabalhar** Leads e Propostas e é exclusivo do tenant operador. **Relatórios e Rankings do CRM nunca oferecem `all`.**
- O **Gestor de Loja só concede o que possui, e só até `store`**: seus Membros nascem na própria Loja, e só são listados Cargos sem escopo `all`.
- Ações que mexem em **dinheiro** (`add_credit`, `manage_pricing`, `manage_credits`, `refund`, `approve` no Marketing) e em **acesso** (`manage_access`, `manage_permissions`) têm permissão **separada** da edição comum.
- A mudança de Cargo ou de permissões **vale imediatamente**.
- Dashboards, relatórios e visibilidade são **orientados por permissão**. "Visão do dono" e "visão do gerente" são arranjos típicos, não perfis fixos.

---

# 6. Requisitos não funcionais

São bloqueadores: o SmartCorbans não entra em uso com cliente real sem que estejam atendidos.

**RNF001 — Interface responsiva, mobile-first.** As três aplicações são desenhadas com o celular como caso base. Tabelas densas viram listas de cards, detalhes abrem em sheet (bottom-sheet no mobile) e a sidebar vira drawer (`corban-ux-v0_2`).

**RNF002 — Isolamento estrito entre Lojas.** Como os parceiros da Smartconsig são Lojas da mesma instância, uma Loja nunca acessa Leads, Propostas, Conversas, Créditos ou dados de Clientes de outra (CRM, RN-CRM-01 e RN-CRM-05). O isolamento é validado por testes automatizados antes de cada release, com atenção a Relatórios, Rankings, exportações e ao Chat, onde um vazamento entre parceiros seria o risco principal.

**RNF003 — Proteção de dados pessoais.** O sistema trata dados pessoais e financeiros (CPF, contratos, condições de crédito). O acesso a dado sensível é controlado por permissão, e as exportações são auditadas (Relatórios, RN-REL-05; RNF005). A base legal e a política de retenção estão fora do escopo inicial (§3.2).

**RNF004 — Degradação graciosa das integrações.** A indisponibilidade da Huggy ou do AbacatePay não derruba o restante do sistema. O usuário recebe mensagem clara do que está indisponível e, quando houver, um caminho alternativo (por exemplo, a adição manual de saldo pelo Admin com o AbacatePay fora). O **Keycloak** é a exceção: sem ele não há login, então sua disponibilidade é requisito de operação, não integração degradável.

**RNF005 — Auditoria de ações críticas.** Ações críticas registram uma trilha **imutável**: nenhum registro é alterado nem apagado.

**Critério.** É crítica a ação que muda **acesso**, **dinheiro** ou **regra comercial**, ou que **expõe ou remove dados pessoais**. O trabalho do dia a dia (Ações na Lead, Simulações, mensagens) não entra: já tem histórico no próprio registro (linha do tempo da Proposta, Extrato de Créditos, histórico do Cliente).

| Grupo | Ações auditadas | Módulos |
|---|---|---|
| **Acesso** | Criar, editar, inativar, reativar e excluir usuário; mudar Cargo, Loja e Time; permissões adicionais; redefinir a senha de outro usuário; revogar sessões; criar, editar, mudar permissões, inativar e excluir Cargo | Admin · Usuários (RN-USR-17), Cargos (RN-CRG-03); CRM · Membros |
| **Dinheiro** | Adicionar saldo manual; estornar débito; ligar ou desligar "Pode receber lead sem saldo"; alterar o preço da lead; alterar o limite de gasto diário; editar a Tabela de Multiplicadores; trocar a Oferta de uma Proposta; aprovar, pausar, retomar ou cancelar injeção do Marketing | Admin · Lojas, Funis de Vendas; CRM · Créditos, Tabela de Multiplicadores, Propostas; Marketing · Prospecção, Carteira Ativa |
| **Regra comercial** | Criar, editar, inativar, reativar e excluir Oferta (inclui Regras associadas, valores, preço e comissão); o mesmo para Regra | Admin · Ofertas, Regras |
| **Dados pessoais** | Exportar Relatórios e Clientes; importar Lista; alterar a Lista de bloqueio | Admin e CRM · Relatórios (RN-REL-05); CRM · Clientes; Marketing · Prospecção |
| **Estrutura** | Excluir Loja (com a cascata de Times e usuários) | Admin · Lojas |

**Conteúdo do registro.** Autor, data e hora, ação, item afetado (tipo e identificador), valores **antes e depois** dos campos alterados e a Loja envolvida, quando houver. Na exportação, também o tipo e os filtros (RN-REL-05).

**Consulta.** Sem tela na v1: a trilha é gravada e consultada pelo suporte técnico. A tela de auditoria no Admin é evolução (§11). Esta é a regra única; os módulos apontam para ela.

**RNF006 — Desempenho do board kanban.** O funil permanece fluido com o volume real: como referência inicial, ao menos 1.000 Leads ativas por Funil sem degradação perceptível de navegação, filtro e Ações. [números a calibrar com o cliente]

---

# 7. Regras transversais do produto (RN-SC)

Regras que atravessam as três aplicações. Cada uma aponta para o documento de origem; **em caso de divergência, vale o módulo**.

**RN-SC-01 — O Admin define, o CRM e o Marketing consomem.** Funil, Ofertas, Regras, cadastros, Usuários e Lojas são mantidos no Admin. Os outros dois não os redefinem (Admin, RN-ADM-11; CRM, §5; Marketing, §1). A exceção é a **Loja**, que edita um recorte de si mesma pelo CRM, escrevendo no mesmo registro (CRM, RN-CRM-13).

**RN-SC-02 — O acesso é por permissão e o isolamento de Loja é absoluto.** Ver §5 (Admin, RN-ADM-01; CRM, RN-CRM-01 e RN-CRM-12).

**RN-SC-03 — Um motor, três pontos de uso.** A Simulação manual, a automática via Lead e a pré-análise do Marketing avaliam as mesmas Ofertas e Regras (CRM, RN-CRM-07; Marketing, RN-MKT-02).

**RN-SC-04 — A Origem da lead é definida pelo sistema, uma vez, e define a cobrança.** Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa e Manual; só Campanha, Orgânica, Prospecção e Carteira Ativa cobram e têm linha de preço (CRM, RN-CRM-03; Admin, RN-ADM-07).

**RN-SC-05 — O saldo é debitado ao atribuir a Lead e o estorno é do Admin.** Nunca negativo, exceto na Loja com a flag "Pode receber lead sem saldo" (CRM, RN-CRM-10; Admin, RN-ADM-08).

**RN-SC-06 — O Pago é a única fonte de contagem.** Metas, Rankings, Relatórios e o Dashboard do Marketing leem o Pago, pela atribuição gravada nele (CRM, RN-CRM-08 e RN-CRM-09).

**RN-SC-07 — O Cliente é do tenant, nasce de várias fontes e guarda o histórico de contratos.** O Marketing e o CRM criam Clientes; o CPF existente associa, não duplica (CRM, RN-CRM-05 e RN-CRM-06; Marketing, RN-MKT-09).

**RN-SC-08 — Excluir é exclusão suave e o histórico permanece** (Admin, RN-ADM-04).

**RN-SC-09 — Nada é injetado pelo Marketing sem aprovação, e cada injeção revalida duplicidade e bloqueio** (Marketing, RN-MKT-04, 05 e 07).

---

# 8. Glossário

**Correspondente bancário (corban).** Empresa que intermedeia produtos de crédito entre bancos e o consumidor. É o cliente da plataforma.

**Tenant.** A organização dona da instância: a Smartconsig. Há um único tenant.

**Owner.** Papel de sistema, único, com permissões plenas (bypass do catálogo).

**Cargo.** Conjunto nomeado de permissões, montado pelo tenant. Cada usuário tem um.

**Permissão.** Autorização no formato `recurso:ação:escopo`.

**Escopo.** Alcance de uma permissão: `self`, `team`, `store` ou `all`.

**Loja.** Unidade de venda: a própria Smartconsig (tenant operador) ou um parceiro dela. Tem Região, Segmentos, tipos de lead e saldo de Créditos.

**Time.** Grupo de Membros de uma Loja, vinculado a um Segmento.

**Membro / Usuário.** Pessoa com acesso ao sistema. Membro é o Usuário visto pelo CRM, dentro da Loja.

**Segmento.** Público pagador atendido (por exemplo, INSS). Cada Segmento tem um Funil.

**Funil de Vendas.** Pipeline de um Segmento, com Stages, Ações, Tags e preço da lead.

**Stage.** Etapa do Funil. O Tipo é o que o sistema entende; o nome é livre.

**Ação.** Operação que move a Lead entre Stages. É a única forma de movê-la.

**Lead.** Intenção de negócio em andamento no Funil de um Segmento.

**Origem da lead.** Como a Lead nasceu: Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa ou Manual.

**Origem de Venda.** Rótulo de por onde a venda chegou, gerido no ponto de uso.

**Cliente.** Registro da pessoa no tenant, com histórico de contratos.

**Contrato.** Registro de uma Proposta Paga (ou importada pelo Marketing) no histórico do Cliente.

**Promotora.** Entidade pagadora da Oferta; carrega as Regiões.

**Produto.** Instrumento de crédito comercializado.

**Região.** Recorte geográfico que qualifica a Oferta visível a cada Loja.

**Operação.** Tipo da Oferta: Novo, Refinanciamento, Portabilidade ou Portabilidade com Refinanciamento. Catálogo fixo do sistema.

**Oferta.** Unidade comercial avaliada pelo motor: Promotora, Produto, Operação e Banco, com taxa, prazo, comissão e Regras.

**Regra.** Condição de elegibilidade reutilizável (Campo + Operador), sem valor; o valor é definido na Oferta.

**Simulação.** Cálculo e registro das condições de crédito para um Cliente ou Lead, em três origens: Manual, Automática via Lead e Marketing.

**Proposta.** Formalização de uma Lead, com uma Oferta congelada. Termina em Pago ou Cancelado.

**Valor de meta.** Valor base da Oferta multiplicado pelo multiplicador, gravado no Pago.

**Meta.** Objetivo de Valor ou de Quantidade, para Times e Membros, com Prazo.

**Créditos.** Saldo da Loja, debitado ao receber Lead com cobrança.

**Prospecção.** Módulo do Marketing que importa bases externas, pré-analisa e distribui Leads.

**Lista.** Base importada na Prospecção.

**Carteira Ativa.** Módulo do Marketing que varre a base de Clientes e injeta Leads de quem está elegível.

**Varredura.** Execução da Carteira Ativa, Única ou Recorrente.

**Lista de bloqueio.** CPFs e telefones que não podem receber injeção do Marketing.

**Huggy.** Plataforma omnichannel de atendimento usada pelo cliente.

**Keycloak.** Provedor de identidade do SmartCorbans (login e sessão).

**AbacatePay.** Provedor de pagamento da adição de crédito (Pix).

---

# 9. Mapa de módulos

O SmartCorbans tem **32 módulos** em três aplicações. A especificação de cada um está no documento indicado; o detalhe de telas, estados, permissões e microcopy está nos artefatos da Fase 2 de cada módulo.

## 9.1. Admin (15)

Cargos · Usuários · Dashboard · Lojas · Funis de Vendas · Layout de Lead · Segmentos · Produtos · Promotoras · Regiões · Status de Proposta · Origem de Venda · Ofertas · Regras · Relatórios.
Ver `corban-admin-produto-v0_2`, §1.

## 9.2. CRM (14)

Dashboard · Leads · Simulações · Propostas · Clientes · Chat · Configurações · Rankings · Relatórios · Membros · Times · Tabela de Multiplicadores · Créditos · Metas (anexada ao Detalhe do Time e do Membro).
Ver `corban-crm-produto-v0_1`, §1.

## 9.3. Marketing (3)

Prospecção · Carteira Ativa · Dashboard.
Ver `corban-marketing-produto-v0_8`, §1.

## 9.4. Fora das três aplicações

Acesso e autenticação (§4.2).

---

# 10. Pré-requisitos de lançamento

Antes do primeiro cliente real usar o SmartCorbans em produção, estes itens, transversais e bloqueadores, devem estar completos:

**Integração com a Huggy.** Autorização do cliente e configuração da conta, para a entrada de Leads, o Chat e o gatilho da Simulação automática.

**AbacatePay.** Conta configurada para a adição de crédito por Pix.

**Estrutura inicial do tenant.** Cargos, Usuários, cadastros-base, Segmentos com Funis configurados, Regras, Ofertas, Lojas e Times da Smartconsig cadastrados e validados pelo cliente. Sem Ofertas completas, o motor não opera.

**Migração do sistema atual.** Migração completa da base de dados do sistema que a Smartconsig usa hoje (configuração base, Leads, Clientes, Usuários etc.), numa carga única. O mapeamento e a execução ficam num plano de migração próprio. Credenciais não migram: continuam no Keycloak.

**Keycloak.** O SmartCorbans autenticando direto no Keycloak atual, sem a abstração FeijucaAuth, validado em produção.

**Implantação da instância.** Criação do Owner da Smartconsig e das Lojas iniciais, sem intervenção manual em banco de dados além da implantação.

**Ambiente de produção validado.** Inclui a suíte automatizada de isolamento entre Lojas (RNF002) executada com sucesso.

---

# 11. Notas de evolução (roadmap)

Direção prevista, **sem compromisso de escopo**. Cada item vira um módulo com documento próprio quando entrar em especificação.

- **IA para inteligência de negócio.** Uso de inteligência artificial sobre os dados do SmartCorbans (Leads, Propostas, Simulações, Clientes, Metas, Créditos) para apoiar decisões de negócio. Escopo, casos de uso e permissões ainda não definidos.
- **Leilão CLT.** Evolução voltada ao público CLT, que hoje não tem simulação nem Segmento específico no produto. Mecânica, atores e relação com o módulo de Integrações (CLT via Jaéconsig) ainda não definidos.
- **Módulo de Integrações (Admin).** Onde o tenant configura as conexões com sistemas externos. É o ponto de partida da simulação externa (CLT via Jaéconsig e consulta de dados de INSS via Consignado Rápido) e do hub técnico do Marketing (Admin, §6; CRM, §6).
- **Marketing V2 — Landing Pages.** Páginas de conversão que criam Cliente e Lead, com Origem Campanha ou Orgânica.
- **Marketing V3 — Integrações e Anúncios.** OAuth2 com Meta, Google e TikTok; painel de anúncios com UTM; o gasto de anúncios alimenta o bloco financeiro do Dashboard do Marketing, sem alterar o preço da lead (Marketing, §6).
- **Lista de bloqueio fora do Marketing.** Estender o "não perturbe" a Leads manuais, Huggy e landing pages (Marketing, §6).
- **Contabilização de metas fora do Pago**, por exemplo contando a atividade (entrada em Negociação) separadamente da conversão (CRM, §6).
- **Dashboard configurável.** Cada usuário reordena ou oculta blocos (CRM, §6).
- **Tela de auditoria no Admin**, com permissão própria, para consultar a trilha do RNF005.
- **Autenticação multifator (MFA)** e **SSO corporativo** (via Keycloak).
- **E-mails do ciclo de acesso:** convite de cadastro, recuperação de senha e troca obrigatória de senha no primeiro login.
- **Boleto** na adição de crédito (AbacatePay).
- **Multi-tenant e Console da Plataforma**, se a plataforma for oferecida a outros correspondentes.
- **Esteira bancária e gestão financeira completa**, hoje fora do escopo (§3.2).

---

# 12. Itens abertos

Só entram aqui decisões de produto.

Nenhum item aberto. *(O último, a lista de ações críticas da auditoria, foi decidido na v2.5: RNF005.)*

*(Pendente de confirmação em módulo: a validade de 7 dias da Lista, Marketing · Prospecção, RN-PRS-09, marcada como [proposto].)*

---

# 13. O que mudou em relação à v1.2

| v1.2 | Hoje |
|---|---|
| Nome **Corban** | **SmartCorbans**. Os arquivos continuam com o prefixo `corban-` até a renomeação. |
| **Multi-tenant**, com Console da Plataforma | **Instância única** da Smartconsig; parceiros são **Lojas**; sem Console. |
| Um produto de 11 módulos | **Três aplicações** (Admin, CRM e Marketing), 32 módulos. |
| **Grupos** de permissões, com usuário em vários grupos | **Cargos**: um por usuário, mais permissões adicionais. |
| **Operação** como eixo (times, recursos, funil, metas) | **Loja** e **Time**; **Segmento** com **Funil** próprio. |
| Oferta = banco + produto + convênio, com **modalidades** (empréstimo novo, cartão, compra de dívida, CLT) | Oferta = Promotora + Produto + **Operação** + Banco; **Região** via Promotora; sem CLT. |
| Regra com tipo bloqueante ou de aviso | Regra = Campo + Operador, **todos os requisitos precisam passar**; valor na Oferta. |
| Lead por Huggy, landing page ou manual; distribuição por operação | **Seis Origens**, **duas camadas** de distribuição, cobrança por Origem. |
| Lead movida por arrastar | **Só por Ação**. |
| Ganho e Perdido registrados na Lead | **Proposta** (Pago e Cancelado), **Valor de meta** e **Contrato**. |
| Simulação CLT (Jaéconsig) e consulta INSS (Consignado Rápido) | **Fora**; evolução com o módulo de Integrações. |
| Chat e digitação de proposta em modo demonstração | **Chat** funcional (espelha a Huggy); a Proposta substitui a digitação. |
| Cliente com importação no CRM | Cliente criado pelo CRM e pelo Marketing; **importação via Prospecção**. |
| Sem Marketing | **Prospecção** e **Carteira Ativa**. |
| Sem financeiro da Lead | **Créditos**, preço da lead e estorno. |
| Dashboard e Relatórios únicos | Um por aplicação, por escopo. |
| LGPD reativa, base legal e retenção | **Fora do escopo inicial.** |
| Parte II com RF001–RF043 e RN001–RN069 | **Aposentada**. As regras vivem nos módulos; a numeração antiga não é reutilizada. |

---

# 14. Impactos da instância única nos documentos das aplicações

**Aplicados em 10/10/2026.** Os documentos de módulo e o Documento de Produto do Admin já não citam o Console da Plataforma nem o isolamento entre tenants: o Owner nasce na implantação (Admin, §2; Usuários, RN-USR-03 e §8), o e-mail é único na instância (RN-USR-04), os status iniciais vêm da implantação (Status de Proposta, RN-STP-10) e o isolamento é entre Lojas (CRM · Leads, RN-LED-06). O termo **tenant** continua, com o sentido de "a organização Smartconsig".
---

*Fim do documento. SmartCorbans — Especificação Geral v2.5. Três aplicações, 32 módulos, RNF001–RNF006, RN-SC-01 a RN-SC-09.*
