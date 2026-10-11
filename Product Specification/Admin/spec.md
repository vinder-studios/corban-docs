# Corban Admin — Documento de Produto

**Status:** Draft
**Aplicação:** Admin (primeira aplicação do Corban, ao lado de CRM e Marketing)
**Versão:** 0.3
**Data:** [a definir]
**Módulos:** Cargos v0.4 · Usuários v0.7 · Dashboard v0.5 · Lojas v0.15 · Funis de Vendas v0.17 · Layout de Lead v0.7 · Segmentos v0.8 · Produtos v0.5 · Promotoras v0.4 · Regiões v0.7 · Status de Proposta v0.7 · Origem de Venda v0.8 · Ofertas v0.15 · Regras v0.9 · Relatórios v0.7

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.3 | Fecha o item aberto 1: a lista de ações críticas da auditoria passa a ser regra única na Especificação Geral (RNF005, v2.5). RN-ADM-02 aponta para ela. |
| 0.2 | **Módulo de Integrações como nota de evolução** (§6): passa a ser o ponto de partida das integrações externas de simulação (CLT via Jaéconsig) e do hub técnico do Marketing (Meta, Google e TikTok). Decisão do usuário. |
| 0.1 | Versão inicial. Documento guarda-chuva da aplicação Admin, no mesmo formato do Documento de Produto do Marketing: o que a aplicação é, quem a usa, o fluxo de ponta a ponta, as regras que atravessam os módulos, o que ela entrega às outras aplicações, o roadmap e o que está em aberto. **Não cria regra nova**: consolida o que os 15 módulos já definem e aponta para a regra de origem. As regras compartilhadas aqui usam o prefixo RN-ADM. |

---

# 1. O que é o Admin

O Admin é a aplicação em que o **tenant configura a estrutura da própria operação**: quem tem acesso e com quais permissões, quais Lojas e Times existem, quais públicos e funis de venda são atendidos, e quais Ofertas comerciais o motor de simulação avalia. Ele não atende clientes nem negocia (isso é do CRM) e não gera demanda (isso é do Marketing). O Admin **define as regras do jogo**; as outras duas aplicações as **consomem**.

Os módulos se organizam em seis **seções** da navegação:

| Seção | Módulo | Para que serve | Especificação |
|---|---|---|---|
| **Acesso** | **Cargos** | Conjuntos nomeados de permissões, que o tenant monta livremente. | `corban-admin-cargos-v0_4` |
| | **Usuários** | Quem existe no sistema, com Cargo, Loja, Time e permissões adicionais; sessões ativas. | `corban-admin-usuarios-v0_6` |
| **Visão geral** | **Dashboard** | Tela inicial: alertas de configuração, evolução de custo e preço da lead, últimos usuários. | `corban-admin-dashboard-v0_5` |
| **Vendas** | **Lojas** | Unidades de venda, seus Times, os Segmentos e tipos de lead que cada Loja recebe, e o saldo de Créditos. | `corban-admin-lojas-v0_14` |
| | **Funis de Vendas** | O pipeline de cada Segmento: etapas, Ações, tags e preço da lead. Inclui, como 6ª aba, o **Layout de Lead**. | `corban-admin-funis-vendas-v0_17` |
| | **Layout de Lead** | A composição do Card e do Sheet de detalhe da Lead, por Funil. | `corban-admin-layout-lead-v0_7` |
| **Cadastros** | **Segmentos** | Os públicos pagadores atendidos; cada Segmento nasce com seu Funil. | `corban-admin-segmentos-v0_8` |
| | **Produtos** | Os instrumentos de crédito comercializados. | `corban-admin-produtos-v0_5` |
| | **Promotoras** | As entidades pagadoras, que carregam o recorte geográfico das Ofertas. | `corban-admin-promotoras-v0_4` |
| | **Regiões** | Os recortes geográficos, usados como qualificador. | `corban-admin-regioes-v0_7` |
| | **Status de Proposta** | Os status do ciclo da Proposta, em quatro tipos que o sistema reconhece. | `corban-admin-status-proposta-v0_7` |
| | **Origem de Venda** | O rótulo de por onde a venda chegou; cadastro gerido no ponto de uso, sem tela própria. | `corban-admin-origem-venda-v0_8` |
| **Oferta comercial** | **Ofertas** | A unidade comercial que o motor de simulação avalia. | `corban-admin-ofertas-v0_15` |
| | **Regras** | As condições de elegibilidade, reutilizáveis entre Ofertas. | `corban-admin-regras-v0_9` |
| **Análise** | **Relatórios** | Visão global (todas as Lojas) de produção, funil, esteira, venda de leads, comissões e desempenho das Ofertas. | `corban-admin-relatorios-v0_7` |

**O que o Admin não tem.** Não há módulo de "estrutura organizacional" separado: a necessidade é coberta por Lojas, Segmentos, Funis de Vendas e Times (CRM).

---

# 2. Atores e acesso

- **Tenant / Owner** — papel de sistema, único por tenant, com permissões plenas. Não passa pela checagem do catálogo de permissões (bypass total), é criado na implantação e não é editável, excluível nem atribuível por usuários comuns (Usuários, RN-USR-03).
- **Gestor do tenant** — qualquer usuário cujo Cargo (mais as permissões adicionais) inclua permissões do Admin. Não há papéis fixos além do Owner: cada tenant monta os seus Cargos.
- **Gestor de Loja** — nos poucos pontos em que o Admin oferece escopo `store` (Lojas, Times e Usuários da própria Loja), um usuário com esse recorte opera só a sua Loja.
- **Sistema** — cria o Funil junto com o Segmento, aplica a cascata de inativação e exclusão, grava o histórico de preço de lead e as trilhas de auditoria.

O acesso é por **permissão**, nunca por papel (RN-USR-16): o usuário entra no Admin quando suas permissões efetivas incluem ao menos uma permissão da aplicação; Loja e Time só definem o **alcance**. O Admin aparece no app switcher para quem tem acesso a mais de uma aplicação. A navegação oculta o que o usuário não pode usar, e a rota sem permissão mostra o estado "sem acesso" do Shell.

Quase todos os recursos do Admin têm escopo `all`, porque o Admin atua sobre o tenant inteiro. Catálogo de permissões da aplicação (`recurso:ação:escopo`):

| Recurso | Ações | Escopos | Módulo |
|---|---|---|---|
| `roles` | `view`, `create`, `edit`, `manage_permissions`, `inactivate`, `delete` | `all` | Cargos |
| `users` | `view` · `create`, `edit`, `manage_access`, `manage_permissions`, `inactivate`, `reactivate` | `all`, `store` · `all` | Usuários |
| `sessions` | `view` · `manage` | `all`, `store` · `all` | Usuários |
| `stores` | `view`, `edit`, `manage_segments`, `manage_credits`, `refund`, `inactivate`, `reactivate` · `create`, `delete` | `all`, `store` · `all` | Lojas |
| `teams` | `view` · `create`, `edit`, `inactivate`, `reactivate`, `delete` | `all`, `store`, `team` · `all`, `store` | Lojas (Times) |
| `segments` | `view`, `create`, `edit`, `inactivate`, `reactivate`, `delete` | `all` | Segmentos |
| `funnels` | `view`, `edit`, `manage_pricing`, `inactivate`, `reactivate` | `all` | Funis de Vendas |
| `offers` | `view`, `create`, `edit`, `manage_rules`, `manage_pricing`, `inactivate`, `reactivate`, `delete` | `all` | Ofertas |
| `products`, `promoters`, `regions` | `view`, `create`, `edit`, `inactivate`, `reactivate`, `delete` | `all` | Produtos, Promotoras, Regiões |
| `rules` | `view`, `create`, `edit`, `inactivate`, `reactivate`, `delete` | `all` | Regras |
| `proposal_statuses` | `view`, `create`, `edit`, `inactivate`, `reactivate`, `delete` | `all` | Status de Proposta |
| `sale_origins` | `view` (acesso universal) · `create` · `delete` | — · `all`, `store` · `all` | Origem de Venda |
| `reports_production`, `reports_funnel`, `reports_proposals_pipeline`, `reports_lead_sales`, `reports_commissions`, `reports_offers_eligibility` | `view`, `export` | `all` | Relatórios |

Dashboard e Layout de Lead não têm recurso próprio: cada bloco do Dashboard depende do `view` do módulo de origem, e o Layout de Lead é a 6ª aba de Funis de Vendas, sob as permissões de `funnels`. Ações que mexem em dinheiro ou em acesso têm permissão **separada** da edição comum (`manage_pricing`, `manage_credits`, `refund`, `manage_access`, `manage_permissions`).

---

# 3. Fluxo de ponta a ponta

A ordem abaixo é a de configurar um tenant do zero; cada passo habilita o seguinte.

1. **Acesso.** O Owner cria os **Cargos** (conjuntos de permissões) e os **Usuários**, cada um com exatamente um Cargo e, se for o caso, Loja, Time e permissões adicionais.
2. **Cadastros-base.** O tenant cadastra **Regiões**, **Produtos** e **Promotoras** (a Promotora recebe suas Regiões), os **Status de Proposta** (o tenant já nasce com um de cada tipo) e os **Segmentos** que atende.
3. **Funil.** Cada Segmento criado gera automaticamente o seu **Funil de Vendas**, que fica "Aguardando configuração" até o gestor definir etapas, Ações, tags e as quatro linhas de preço de lead. O **Layout de Lead** define como a Lead aparece no board e no detalhe.
4. **Oferta comercial.** Cadastram-se as **Regras** de elegibilidade e depois as **Ofertas**, que combinam Promotora, Produto, Operação e Banco com a parametrização financeira e as Regras (com os valores de cada requisito).
5. **Lojas e Times.** Cada **Loja** é criada com sua Região, Segmentos que opera e tipos de lead que recebe; os **Times** escolhem o Segmento, o que lhes dá acesso ao Funil. Os usuários são vinculados a Loja e Time.
6. **Operação.** Com isso o CRM funciona: as Leads entram nos Funis, são distribuídas às Lojas e aos operadores, simuladas contra as Ofertas e viram Propostas. O Marketing consome as mesmas Ofertas e Regras.
7. **Acompanhamento.** O **Dashboard** do Admin sinaliza o que ficou por configurar (Funis aguardando configuração, Ofertas com regras incompletas, Lojas sem Time), e os **Relatórios** mostram o resultado global.

O gestor volta a este fluxo sempre que a operação muda: um novo Segmento, uma Oferta nova, uma Loja que entra, um usuário que sai.

---

# 4. Regras compartilhadas (RN-ADM)

As regras abaixo atravessam vários módulos. Cada uma aponta para a regra de origem; em caso de divergência, **vale o módulo**.

**RN-ADM-01 — Acesso por permissão; Owner com bypass; o efeito é imediato.** Toda tela e ação do Admin dependem de permissão (`recurso:ação:escopo`). O Owner não passa pelo catálogo. Mudar o Cargo ou as permissões de alguém vale imediatamente, e as permissões adicionais somam, nunca subtraem (Usuários, RN-USR-01, 03, 13; Cargos, RN-CRG-02).

**RN-ADM-02 — Toda gestão de acesso é auditada** (lista completa de ações auditadas na Especificação Geral, RNF005). Mudanças em Cargos, Usuários e permissões geram trilha de auditoria (Cargos, RN-CRG-03; Usuários, RN-USR-17). Quem pode gerenciar as permissões de um Cargo pode conceder qualquer acesso, inclusive a si mesmo (RN-CRG-04); por isso `manage_permissions` é permissão à parte.

**RN-ADM-03 — Os cadastros-base são livres do tenant e seguem o mesmo ciclo.** Segmentos, Produtos, Promotoras, Regiões e Status de Proposta são cadastros do tenant, com inativar, reativar e excluir. O que **bloqueia ou cascateia** depende do cadastro:

| Cadastro | Ao inativar | Ao excluir |
|---|---|---|
| **Produto** | Bloqueado enquanto houver Oferta (RN-PRD-02). | Bloqueado nas mesmas condições (RN-PRD-03). |
| **Promotora** | Não bloqueia: inativar ofertas ou substituir (conforme RN-PRD-02 para Produto). | Bloqueado enquanto houver Oferta. |
| **Região** | Não bloqueia inativação; exige informar a quantidade de vínculos e o efeito. | Mesma cascata e mesma confirmação (RN-REG-03). |
| **Segmento** | Cascateia para o Funil, se ele estiver Ativo, avisando antes (RN-SEG-06). | Bloqueado se o Funil já foi ativado ou se houver Oferta; do contrário exclui Segmento e Funil e remove as associações com Lojas e Times (RN-SEG-04, 08, 09). |
| **Status de Proposta** | Propostas vinculadas pedem migração para status do mesmo tipo (RN-STP-02, 05). O último ativo de um tipo não pode ser inativado (RN-STP-07). | Mesma migração (RN-STP-03). |
| **Regra** | Informar efeito nas novas/atuais associações de Oferta (reativação informa Ofertas afetadas). | Não bloqueia: desvincula das Ofertas em lote, com alerta que diz a consequência (RN-RGR-07, 10). |
| **Origem de Venda** | — | Sem bloqueio; o histórico preserva o rótulo como texto (RN-ORI-01, 04). |

O **Código** dos cadastros é editável após a criação; ver §6.

**RN-ADM-04 — Excluir Loja, Time e Usuário é exclusão suave, e o histórico permanece.** A Loja, o Time e o usuário excluídos somem das listas e das seleções, mas Extrato, Leads, Propostas, Metas e Relatórios continuam apontando para eles, marcados como excluídos. O usuário só é excluído em cascata da exclusão da Loja, e o Código da Loja e o e-mail do usuário ficam liberados (Lojas, RN-LOJ-15, 08, 09; Usuários, RN-USR-04, 05).

**RN-ADM-05 — Um Segmento, um Funil.** O Funil nasce junto com o Segmento, é único por Segmento e tenant-wide, e o Segmento não muda depois. Funil que já foi ativado nunca é excluído: é inativado, com drenagem das Leads (Funis de Vendas, RN-FNL-01, 02, 09, 12, 14). O **Tipo** do stage é o que o sistema entende; o nome é livre (RN-FNL-13).

**RN-ADM-06 — A Loja e o Time ligam a operação ao Funil.** O Segmento do **Time** é o que dá acesso ao Funil (Lojas, RN-LOJ-02). A **Loja** escolhe, por Segmento, se está Ativa e **quais tipos de lead recebe**, e a Loja com "Pode receber lead sem saldo" é o destino de última instância e recebe todos os tipos (RN-LOJ-01, 17, 18). Desligar o Segmento é bloqueado enquanto houver Leads em andamento daquele Segmento na Loja (RN-LOJ-16).

**RN-ADM-07 — O preço da lead tem quatro linhas, por Origem, e tem histórico.** Cada Funil define o custo e o preço da lead para quatro Origens (Campanha, Orgânica, Prospecção e Carteira Ativa); a margem pode ser negativa, e cada mudança de valor gera um registro de histórico (Funis de Vendas, RN-FNL-07, 08, 16). Mexer em preço exige `manage_pricing`, e o gráfico do Dashboard exige essa permissão (Dashboard, §3.2).

**RN-ADM-08 — Saldo e estorno são ações do Admin.** Adicionar saldo manualmente e estornar são ações da tela de Lojas; o estorno nunca é do CRM (Lojas, RN-LOJ-11, 12, 13). O interruptor "Pode receber lead sem saldo" habilita saldo negativo e só é editável com `stores:manage_credits` em escopo `all`.

**RN-ADM-09 — A Oferta combina quatro peças e é avaliada por Regras.** Uma Oferta é Promotora + Produto + Operação + Banco, com taxa, prazo e comissão, mais as Regras que a limitam (Ofertas, RN-OFR-01). Quem vê qual Oferta depende da **Região da Promotora** (RN-OFR-02, RN-PRM-02, RN-REG-04); o Segmento determina a elegibilidade na simulação (RN-OFR-08). A Operação é um catálogo fixo do sistema: Novo, Refinanciamento, Portabilidade e Portabilidade com Refinanciamento. O valor base da comissão depende da Operação (RN-OFR-07).

**RN-ADM-10 — A Regra define a condição; a Oferta define o valor.** A Regra é cadastrada uma vez, com requisitos no formato Campo + Operador, sem valor; o valor de cada requisito é definido na Oferta (Regras, RN-RGR-12). Todos os requisitos precisam passar (RN-RGR-01). Editar uma Regra em uso afeta as Ofertas imediatamente (RN-RGR-08). A Oferta só fica completa com todos os valores preenchidos, e uma Oferta **ativa** nunca está incompleta (Ofertas, RN-OFR-03; Regras, RN-RGR-13). A **mensagem de erro** da Regra é a explicação geral lida por quem avalia o cliente (RN-RGR-11).

**RN-ADM-11 — O mesmo motor serve a quem simula e a quem prospecta.** As Ofertas e Regras do Admin são avaliadas pelas Simulações do CRM e pela pré-análise em lote do Marketing, com o mesmo resultado. A Proposta guarda um snapshot da Oferta ao associá-la, e os relatórios leem esse snapshot, não a configuração atual (Relatórios, RN-REL-15, 20).

**RN-ADM-12 — Os relatórios do Admin são globais e têm tipos fixos.** Todo relatório do Admin é escopo `all`. Os tipos são fixos; a personalização é filtro, agrupamento e Visão salva, que é pessoal. A exportação é fiel à tela e auditada. Lojas, Times e Operadores excluídos continuam nos relatórios (Relatórios, RN-REL-01 a 08, 18).

---

# 5. O que o Admin entrega às outras aplicações

| Para | O que o Admin fornece | Módulo de origem |
|---|---|---|
| **CRM · Leads** | Stages, Tipos, Ações e Tags do Funil; as quatro linhas de preço; elegibilidade de Loja (Segmento, tipos de lead, saldo) e de operador (Time, flag "Pode receber lead"); lista de Origens de Venda. | Funis de Vendas, Lojas, Usuários, Origem de Venda |
| **CRM · Layout da Lead** | A composição do Card e do Sheet de detalhe, por Funil. | Layout de Lead |
| **CRM · Simulações** | A Oferta completa (parametrização e Regras com valores), Produtos, Promotoras, Segmentos e a Região da Loja, que filtra as Ofertas visíveis. | Ofertas, Regras, Produtos, Promotoras, Regiões |
| **CRM · Propostas** | Status e seus tipos, valor base e comissão da Oferta, Origem de Venda e a ação Formalizar, que cria a Proposta. | Status de Proposta, Ofertas, Funis de Vendas |
| **CRM · Créditos** | O preço da lead (débito) e as ações Adicionar saldo e Estornar. | Funis de Vendas, Lojas |
| **CRM · Membros, Times, Configurações** | As entidades Usuário, Cargo, Loja e Time; a Loja edita um recorte dela mesma pelo CRM, escrevendo no mesmo registro. | Usuários, Cargos, Lojas |
| **CRM · Relatórios** | Os três tipos de relatório compartilhados, com a mesma mecânica, sem a dimensão Loja. | Relatórios |
| **Marketing** | Ofertas e Regras (motor da pré-análise, com a mensagem de erro como motivo de descarte), Segmentos e Funis (destino da Lead), Origem de Venda (rótulo da Lista e da Varredura). | Ofertas, Regras, Segmentos, Funis de Vendas, Origem de Venda |
| **Todas as aplicações** | As permissões efetivas de cada usuário (Cargo + adicionais) e o status ativo ou inativo, base de toda visibilidade e ação. | Usuários, Cargos |

Dois pontos em que o Admin lê ou escreve em dados que são do CRM, por decisão de produto: a tela de **inativação de usuário** mostra a contagem de Leads em andamento e exige a redistribuição (Usuários, RN-USR-05), e a **Loja** é editável tanto no Admin quanto, em um recorte, no CRM.

---

# 6. Notas de evolução (roadmap)

Itens conhecidos, fora da v1.

## Autenticação multifator

O MFA é evolução conhecida, fora da v1, dado o caráter sensível dos dados tratados (Especificação, §1.6).

## Módulo de Integrações

O Admin ganhará um **módulo de Integrações**, ainda não modelado, onde o tenant configura as conexões do Corban com sistemas externos. Ele é o ponto de partida de duas famílias de integração:

- **Simulação externa.** Qualquer integração externa de simulação, por exemplo CLT via Jaéconsig, entra por aqui. Hoje só o motor interno está ativo (CRM, Simulações, RN-SIM-08).
- **Marketing (hub técnico).** Credenciais e fluxo OAuth2 com Meta Business, Google Ads e TikTok Ads, previstos para a V3 do Marketing (Marketing, Documento de Produto, §6).

Quando o módulo existir, o **Código dos cadastros** (Segmentos, Produtos, Promotoras e Regiões), hoje editável após a criação, passa a ser uma ação de risco se algum sistema externo referenciar um cadastro por código. A reavaliação acontece junto com a definição do módulo.

## Lista de bloqueio fora do Marketing

Ver Marketing, Documento de Produto, §6. Não altera o Admin.

---

# 7. Itens abertos

1. *(Resolvido na v0.3.)* **Auditoria como requisito transversal:** a lista única de ações críticas está na Especificação Geral, RNF005 (v2.5).

---

*Fim do documento. Admin — Documento de Produto v0.3. Os 15 módulos têm Fase 1 completa. A Fase 2 está completa em todos, exceto o Layout de Lead (o wireframe existe fora deste fluxo) e a Origem de Venda, que tem formato reduzido porque não tem tela própria.*
