# Corban CRM — Documento de Produto

**Status:** Draft
**Aplicação:** CRM (segunda aplicação do Corban, ao lado de Admin e Marketing)
**Versão:** 0.2
**Data:** [a definir]
**Módulos:** Leads v0.12 · Clientes v0.11 · Simulações v0.13 · Propostas v0.13 · Chat v0.8 · Configurações v0.5 · Rankings v0.5 · Relatórios v0.3 · Membros v0.5 · Times v0.4 · Metas v0.8 · Tabelas Multiplicadoras v0.6 · Créditos v0.6 · Dashboard v0.5

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.2 | Acompanha a Especificação Geral v2.4: o gateway de pagamento passa a ser o **AbacatePay**, só com Pix (Créditos v0.6); Membros v0.5 (sem estado Convidado). |
| 0.1 | Versão inicial. Documento guarda-chuva da aplicação CRM, no mesmo formato do Documento de Produto do Marketing e do Admin: o que a aplicação é, quem a usa, o fluxo de ponta a ponta, as regras que atravessam os módulos, o que ela troca com as outras aplicações, o roadmap e o que está em aberto. **Não cria regra nova**: consolida o que os 14 módulos já definem e aponta para a regra de origem. As regras compartilhadas aqui usam o prefixo RN-CRM. |

---

# 1. O que é o CRM

O CRM é a aplicação em que a **Loja opera o dia a dia comercial**: recebe Leads, atende, simula as condições de crédito, formaliza Propostas, acompanha metas e controla o saldo que paga pelas Leads. Ele não configura a parte comercial (isso é do Admin) e não gera demanda em massa (isso é do Marketing): o CRM **consome** o Funil, as Ofertas e as Regras do Admin, **recebe** as Leads do Marketing e **fecha o ciclo** com a Proposta Paga.

Os módulos se organizam em cinco **seções** da navegação:

| Seção | Módulo | Para que serve | Especificação |
|---|---|---|---|
| **Visão geral** | **Dashboard** | Tela inicial: carteira de Leads, resultados próprios, progresso de Meta e alerta de Créditos. | `corban-crm-dashboard-v0_5` |
| **Operação** | **Leads** | O board kanban do funil: da captação ao desfecho, com a distribuição entre Lojas e operadores. | `corban-crm-leads-v0_12` |
| | **Simulações** | Calcular e registrar as condições de crédito de uma Lead ou Cliente. | `corban-crm-simulacoes-v0_13` |
| | **Propostas** | Formalizar e acompanhar a Proposta até Pago ou Cancelado. | `corban-crm-propostas-v0_13` |
| | **Clientes** | A ficha consolidada do Cliente, com histórico de contratos. | `corban-crm-clientes-v0_11` |
| | **Chat** | A conversa do canal digital (Huggy) no contexto da Lead. | `corban-crm-chat-v0_8` |
| | **Configurações** | A Loja edita os próprios dados e os Segmentos e tipos de lead que recebe. | `corban-crm-configuracoes-v0_5` |
| **Desempenho** | **Rankings** | Rankings de Membros e Times, com Modo TV. | `corban-crm-rankings-v0_5` |
| | **Relatórios** | Produção, funil, esteira, metas e custo de leads da Loja. | `corban-crm-relatorios-v0_3` |
| **Gestão** | **Membros** | Os usuários da Loja, com Cargo, Time e permissões adicionais. | `corban-crm-membros-v0_5` |
| | **Times** | Os Times da Loja e sua composição. | `corban-crm-times-v0_4` |
| **Financeiro** | **Tabela de Multiplicadores** | O multiplicador por Oferta, que pesa as Propostas nas metas. | `corban-crm-tabelas-multiplicadoras-v0_6` |
| | **Créditos** | Saldo, Extrato, adição de crédito e limite de gasto diário. | `corban-crm-creditos-v0_6` |
| *(anexada)* | **Metas** | Metas de Valor e de Quantidade para Times e Membros. Não tem destino próprio: vive dentro do Detalhe do Time e do Detalhe do Membro. | `corban-crm-metas-v0_8` |

**Dependência de integração.** O Chat espelha a conversa da Huggy, e a Simulação automática via Lead é disparada por evento da Huggy. As mensagens não são persistidas pelo Corban.

**Fora da v1:** integrações externas de simulação (CLT via Jaéconsig), ver §6.

---

# 2. Atores e acesso

- **Operador** — trabalha as Leads que chegam até ele: atende, simula, movimenta, registra desfechos.
- **Gestor de Loja** — supervisiona o funil, as metas e o saldo da própria Loja, e gerencia Membros e Times dela.
- **Tenant operador** — a Loja do próprio tenant, destino de última instância da distribuição, e os usuários com visão `all` nos recursos operacionais.
- **Sistema** — distribui as Leads entre Lojas e operadores, debita o saldo, cria Clientes e Propostas, grava Simulações automáticas e calcula as metas.
- **Cliente final** — externo ao sistema; fala pela Huggy e aparece na ficha de Cliente.

O acesso é por **permissão**, nunca por papel: cada Loja monta seus Cargos dentro do limite `store` (Membros, RN-MBR-02 e 03). O **escopo** de cada permissão define o alcance dos dados, em quatro níveis cumulativos: `self` (o que é do próprio usuário), `team` (+ o Time dele), `store` (+ a Loja inteira) e `all` (+ todas as Lojas, visão do tenant operador). Catálogo de permissões (`recurso:ação:escopo`):

| Recurso | Ações | Escopos | Módulo |
|---|---|---|---|
| `leads` | `view`, `execute_actions` · `create` · `reassign` | `self`…`all` · `store` · `team`…`all` | Leads |
| `clients` | `view`, `edit` · `export` | `self`…`all` · `store`, `all` | Clientes |
| `clients_documents` | `view`, `manager` | `self`…`all` | Clientes |
| `simulations` | `create`, `view` | `self`…`all` | Simulações |
| `proposals` | `view` · `create` | `self`…`all` · `store` | Propostas |
| `proposals_*` (documentos, atores, datas, linha do tempo, Oferta, e os painéis por Status: Novo, Negociação, Pago) | `view`, `manager` | `self`…`all` (Pago: `team`…`all`) | Propostas |
| `chats` | `view`, `reply` | `self`…`all` | Chat |
| `credits` | `view`, `add_credit`, `edit_daily_limit` | `store` | Créditos |
| `multipliers` | `view`, `edit` | `store` | Tabelas Multiplicadoras |
| `members`, `members_sessions` | `view`, `create`, `edit`, `manage_access`, `manage_permissions`, `inactivate`, `reactivate` · `view`, `manage` | `store` | Membros |
| `crm_teams` | `view`, `create`, `edit`, `manage_members`, `inactivate`, `delete` | `store` | Times |
| `goals` | `view` · `manage` | `self`, `team`, `store` · `team`, `store` | Metas |
| `rankings` | `view`, `create`, `edit`, `delete`, `manage_tv_link` | `store` | Rankings |
| `crm_reports_production`, `_funnel`, `_proposals_pipeline`, `_goals` · `_lead_cost` | `view`, `export` | `self`, `team`, `store` · `store` | Relatórios |
| `store_profile`, `store_segments` | `edit` | `store` | Configurações |

O Dashboard não tem recurso próprio: cada bloco depende do `view` do módulo de origem. As permissões que mexem em dinheiro (`add_credit`, `edit_daily_limit`) e em acesso (`manage_access`, `manage_permissions`) são separadas da edição comum. O CRM aparece no app switcher para quem tem acesso a mais de uma aplicação; o usuário cai no Dashboard ao entrar.

---

# 3. Fluxo de ponta a ponta

1. **Entrada.** A Lead nasce por um de quatro caminhos: campanha ou canal orgânico (pela Huggy, inclusive mensagem recebida sem Lead existente), **Recontato** de um cliente já conhecido, injeção do **Marketing** (Prospecção ou Carteira Ativa), ou **cadastro manual** pela Loja. A Origem da lead é definida pelo sistema, uma vez.
2. **Cliente.** Quando a Lead reúne os dados mínimos, o Cliente nasce, ou é associado se o CPF já existe. O Marketing também cria Clientes, às vezes sem CPF e sem Loja.
3. **Distribuição entre Lojas.** Em rodízio entre as Lojas elegíveis: a que opera o Segmento, tem ligada a Origem da lead e tem saldo (ou a flag "Pode receber lead sem saldo"). O débito acontece ao atribuir.
4. **Distribuição entre operadores.** Dentro da Loja, em rodízio entre os operadores elegíveis. Sem operador elegível, a Lead fica no pool da Loja.
5. **Atendimento.** O operador conversa pelo **Chat**, executa **Ações** que movem a Lead pelos Stages e faz **Simulações** contra as Ofertas do Admin.
6. **Formalização.** A Ação Formalizar cria a **Proposta**, que guarda a Oferta escolhida. A partir daí o operador deixa de executar Ações sobre a Lead.
7. **Desfecho.** A Proposta chega a **Pago** ou **Cancelado** e move a Lead. O Pago grava a atribuição (Operador, Time, Cargo), calcula o Valor de meta (valor base × multiplicador) e gera um **Contrato** no histórico do Cliente.
8. **Acompanhamento.** As **Metas**, os **Rankings**, o **Dashboard** e os **Relatórios** leem o Pago; os **Créditos** mostram o custo das Leads.

Uma Lead de Recontato volta à Loja e ao operador anteriores, sem rodízio nem débito. O cadastro manual pula as duas camadas de distribuição.

---

# 4. Regras compartilhadas (RN-CRM)

As regras abaixo atravessam vários módulos. Cada uma aponta para a regra de origem; em caso de divergência, **vale o módulo**.

**RN-CRM-01 — O escopo é cumulativo e o isolamento de Loja é absoluto.** `self` ⊂ `team` ⊂ `store` ⊂ `all`. Uma Loja nunca vê Leads, Propostas, Conversas ou Créditos de outra, mesmo dentro do mesmo Funil (Leads, RN-LED-06). O escopo `all` serve para trabalhar Leads e Propostas e é exclusivo do tenant operador; **Relatórios e Rankings nunca oferecem `all`** (Relatórios, RN-RLC-01; Rankings, RN-RNK-01). O relatório nunca mostra um registro que o usuário não poderia abrir no módulo de origem (RN-RLC-02).

**RN-CRM-02 — A Lead é distribuída em duas camadas, por rodízio real.** Primeiro entre Lojas, depois entre operadores. A Loja é elegível quando opera o Segmento, tem a Origem da lead ligada e tem saldo suficiente (nas Origens que cobram) ou a flag "Pode receber lead sem saldo". O operador é elegível quando está Ativo, tem a flag "Pode receber lead", tem Time Ativo e o Time é do Segmento. Quem recebeu por último volta ao fim da fila, e a Região não desqualifica a Loja (Leads, RN-LED-02 a 05, 08; Usuários, RN-USR-14).

**RN-CRM-03 — A Origem da lead define se há cobrança.** Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa e Manual; só Campanha, Orgânica, Prospecção e Carteira Ativa têm linha de preço e cobram (Leads, RN-LED-12, 13). A Origem não muda depois de definida. A Lead do Marketing leva a referência à Lista ou Varredura de origem, o valor potencial e o resumo das Ofertas, em modo de referência (RN-LED-16).

**RN-CRM-04 — A Lead só se move por Ação.** Não existe arrastar o card entre colunas: toda mudança de Stage é a execução de uma Ação, e o descarte antes da Proposta também é uma Ação. Ao entrar em Formalização, o operador perde o acesso às Ações, mantendo Documentos e Chat. Formalizar cria a Proposta, e Pago ou Cancelado na Proposta move a Lead de volta (Leads, RN-LED-09 a 11; Funis de Vendas, RN-FNL-17, 18; Propostas, RN-PRP-03).

**RN-CRM-05 — O Cliente é do tenant; a Loja só o acessa.** O Cliente existe no tenant e pode existir sem nenhuma Lead e sem Loja. A Loja o acessa por **associação**, via distribuição de Lead. Um CPF que só outras Lojas conhecem não revela nenhum dado. Conversas e Propostas na ficha são escopadas à Loja de quem consulta (Clientes, RN-CLI-03, 04, 09, 10). O CPF já existente associa, não duplica, e o CPF pode ser preenchido depois, uma única vez (RN-CLI-02, 11).

**RN-CRM-06 — O Cliente guarda o histórico de contratos.** Cada Proposta que chega a Pago gera um Contrato no histórico do Cliente; o Marketing também grava Contratos importados. O histórico é de uso interno e alimenta a Carteira Ativa do Marketing (Clientes, RN-CLI-13; Propostas, RN-PRP-18).

**RN-CRM-07 — A Simulação usa o mesmo motor em três origens e não consome Créditos.** Manual, Automática via Lead e Marketing: o cálculo é o mesmo, avaliado contra as Ofertas e Regras do Admin. A Simulação de origem Marketing é gravada na injeção da Lead com Oferta aprovada. "Refazer" gera sempre um novo item no histórico. A Simulação é visível a quem tem acesso ao Cliente (Simulações, RN-SIM-01, 03, 09).

**RN-CRM-08 — A Proposta congela a Oferta e define o que conta.** A Proposta guarda uma única Oferta como snapshot, com o multiplicador, gravado ao associá-la. Proposta não debita saldo. Ao chegar a Pago, grava a atribuição e o Valor de meta (Propostas, RN-PRP-05, 06, 07). O Pago e o Cancelado travam a edição da Oferta e dos dados gerais; Documentos, Datas e Comentário seguem editáveis (RN-PRP-15).

**RN-CRM-09 — As metas só contam o Pago, pela atribuição gravada nele.** Promoção, inativação ou troca de Time não retiram vendas já fechadas. A Meta de Valor soma o Valor de meta e a de Quantidade conta 1 por Proposta. Uma Meta tem Prazo (Início e Fim) e gera histórico ao encerrar; a Meta individual de quem deixa de ser elegível é encerrada, nunca excluída (Metas, RN-MET-03, 04, 10, 12). Os Rankings usam a mesma atribuição, por Loja, só com Membros e Times Ativos (Rankings, RN-RNK-01, 02, 13). O Relatório de Metas usa o mesmo realizado calculado em Metas, sem recalcular (Relatórios, RN-RLC-05).

**RN-CRM-10 — O saldo é debitado ao atribuir a Lead; o estorno é do Admin.** O débito só ocorre com saldo suficiente. O saldo nunca é negativo, exceto na Loja com a flag. O limite de gasto diário, quando atingido, bloqueia novas Leads pelo resto do dia. O estorno gera um lançamento novo, nunca apaga o original, e é feito no Admin (Créditos, RN-CRD-01, 03, 06, 07; Lojas, RN-LOJ-13).

**RN-CRM-11 — O Chat espelha a Huggy e respeita o escopo.** As mensagens são espelhadas, não persistidas. O acesso é inteiramente por escopo (`self`, `team`, `store`, `all`); responder é permissão à parte. Após 24 horas da última mensagem do cliente só se envia Template, e a Conversa de Lead concluída fica somente leitura (Chat, RN-CHT-03, 05, 10). Lead sem conversa na Huggy tem o botão de Chat desabilitado (RN-CHT-11).

**RN-CRM-12 — O Gestor só concede o que possui, e só até `store`.** Os Membros nascem vinculados à Loja de quem os cadastra. Só os Cargos sem nenhuma linha de escopo `all` são listados, e as permissões adicionais ficam limitadas a `store` e abaixo (Membros, RN-MBR-01 a 04; Times, RN-TIM-03).

**RN-CRM-13 — A Loja edita um recorte de si mesma.** Em Configurações, a Loja edita Nome, Endereço, Logo e os interruptores de Segmento e tipos de lead, escrevendo no mesmo registro de Admin · Lojas (Configurações, RN-CFG-01, 02). Desligar um Segmento é bloqueado enquanto houver Leads em andamento dele, e a Loja com a flag "Pode receber lead sem saldo" não desliga tipos de lead (RN-CFG-05, 07).

---

# 5. O que o CRM troca com as outras aplicações

| Com | O que o CRM recebe | O que o CRM entrega |
|---|---|---|
| **Admin** | Funis de Vendas (Stages, Ações, Tags, preço da lead), Layout de Lead, Ofertas e Regras, Produtos, Promotoras, Regiões, Segmentos, Status de Proposta, Origem de Venda, Usuários, Cargos, Lojas. | Leads, Propostas, Simulações e Créditos, como dados para os Relatórios do Admin; a redistribuição das Leads em andamento na inativação de um usuário; o recorte da Loja editado em Configurações. |
| **Marketing** | Leads injetadas (Origem Prospecção ou Carteira Ativa), com rastreio, valor potencial e resumo das Ofertas; Clientes e Contratos importados; a Simulação de origem Marketing. | Clientes e o histórico de contratos (base da Carteira Ativa); o desfecho da Lead (Pago, Cancelado) e as Propostas, que alimentam o Dashboard do Marketing; o rodízio e a cobrança das Leads injetadas. |
| **Huggy** (externa) | Mensagens, o evento de criação automática de Lead e o gatilho da Simulação automática. | Envio de mensagens e Templates; Ações do Funil podem acionar um fluxo da Huggy, sem bloquear a Ação no Corban. |
| **AbacatePay** (externo) | A confirmação do Pix na adição de crédito. | O pedido de cobrança Pix. |

---

# 6. Notas de evolução (roadmap)

Itens conhecidos, fora da v1.

## Integrações externas de simulação

CLT via Jaéconsig, e qualquer outra integração externa de simulação, ficam para quando o produto tiver um módulo de integrações. Hoje só o motor interno está ativo (Simulações, RN-SIM-08).

## Contabilização de metas fora do Pago

Será avaliada a possibilidade de contar a meta a partir de outro Status ou Stage além do Pago, por exemplo contando a atividade (entrada em Negociação) separadamente da conversão. Hoje a única fonte de contagem é o Pago (Metas, RN-MET-03).

## Dashboard configurável

Ordem e presença dos blocos do Dashboard são fixas. Permitir que cada usuário reordene ou oculte blocos é evolução futura (Dashboard, nota de evolução).

## Limitação conhecida da Esteira de Propostas

Com escopo `self`, a Esteira só alcança as Propostas de Leads do próprio usuário; Propostas em que ele atua só como Ator nem sempre entram (Relatórios, §8.1).

---

# 7. Itens abertos

Nenhum. Os 14 módulos do CRM não têm decisão de produto em aberto. Os itens que ainda aparecem nas seções de itens abertos dos módulos são de natureza técnica (por exemplo, a concorrência na checagem de saldo de Créditos, que foi repassada ao time de backend).

---

*Fim do documento. CRM — Documento de Produto v0.1. Os 14 módulos têm Fase 1 e Fase 2 completas.*
