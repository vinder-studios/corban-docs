# Corban Admin · Relatórios

**Status:** Draft
**Aplicação:** Admin
**Seção:** Análise
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.7
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. Relatórios do ponto de vista do tenant (escopo `all`). Define os 3 tipos compartilhados com o CRM (Produção, Funil e Conversão, Esteira de Propostas) e os 3 exclusivos do tenant (Venda de leads, Comissões a pagar, Ofertas e Elegibilidade). |
| 0.2 | Fecha os 4 itens abertos: Visões salvas são pessoais (confirmado); comissão passa a ser gravada no Pago (Propostas v0.10), então Comissões a pagar usa o valor gravado; caso "nenhuma Loja elegível" coberto pela Loja do tenant com a flag (premissa registrada em Leads v0.6) — métrica "demanda não atendida" descartada; marco do speed-to-lead confirmado. |
| 0.3 | **"Origem" passa a ser duas dimensões distintas:** Origem da lead (campo do sistema, módulo de Leads) e Origem de Venda (rótulo do tenant). O relatório de Funil e Conversão contava "leads por Origem de Venda", mas a origem que a v1.2 pedia (RF039) era o canal, hoje a Origem da lead. Venda de leads ganha a dimensão Origem da lead. Novas RN-REL-16 (as duas dimensões; Proposta sem Lead) e RN-REL-17 (Origem de Venda agrupada pelo rótulo, o que fecha o item aberto do rótulo órfão de Origem de Venda). |
| 0.4 | **"Time" é o Time atual do Operador** (nova RN-REL-19, decisão do usuário), declarado como regra, com a nota de que mudar de Time reatribui o histórico (mudanças são raras). **Lojas, Times e Operadores excluídos continuam nos relatórios**, marcados como excluídos (nova RN-REL-18, consequência do soft-delete de Lojas e Usuários). **"Regras que mais desqualificaram" passa a agrupar pela Regra gravada no snapshot** (RN-SIM-11), e não pela mensagem isolada (Regras v0.8: duas Regras podem ter a mesma mensagem). Declara a **Seção "Análise"**, a única que faltava entre os módulos do Admin. Atualiza a nota do prefixo (a colisão entre os Dashboards foi resolvida). Reordena esta tabela. |
| 0.5 | **Venda de leads deixa de ter a dimensão e o filtro "Produto"**: o Débito do Extrato não tem Produto (o Funil só tem preço por Origem da lead, e a Lead não tem Produto), então a dimensão não tinha fonte — escapou da revisão da v0.4 e foi achada no levantamento do Admin. **Nova RN-REL-20**: os relatórios de Propostas leem o snapshot da Oferta gravado na Proposta (decisão do usuário), e não a Oferta atual. |
| 0.6 | **O §3.2 deixa de chamar a configuração da Visão de "snapshot"**, o que contradizia a RN-REL-07 (o período relativo não é congelado): agora diz que o período relativo reabre acompanhando a data de hoje e o intervalo fixo reabre com as mesmas datas. O RF-REL-02 passa a prever o texto de apoio do diálogo de salvar, que informa isso. Achado na auditoria dos artefatos para o wireframe. |
| 0.7 | Acompanha Créditos v0.6: Créditos adicionados por origem passam a ser Pix e manual (sem Boleto nesta versão). |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-REL) e regras de negócio (RN-REL) próprios.

> **Este módulo é a fonte das definições compartilhadas.** Produção, Funil e Conversão e Esteira de Propostas são definidos **aqui**, na versão completa (escopo `all`, com a dimensão "Agrupar por Loja"). O módulo CRM · Relatórios referencia esses três tipos como **recorte** — limitados a `store` e abaixo, sem a dimensão Loja — no mesmo espírito de Membros (CRM) em relação a Usuários (Admin). Uma mudança num tipo compartilhado é feita aqui e vale nas duas aplicações.

> **Prefixo de regras.** Este módulo usa RN-REL / RF-REL. O módulo CRM · Relatórios usa o prefixo próprio RN-RLC, e os Dashboards ficaram em RN-DSH (Admin) e RN-DSC (CRM), de modo que nenhum identificador se repete entre as aplicações.

---

# 1. Objetivo do módulo

Dar ao tenant uma visão **global** — todas as Lojas — do resultado comercial, da operação de propostas, da venda de leads, das comissões devidas às Lojas e do desempenho das Ofertas, com filtros, agrupamento escolhível e Visões salvas.

---

# 2. Atores

- **Usuário do Admin** com permissão de relatórios — tipicamente gestão do tenant (dono, financeiro, comercial). Cada tipo de relatório tem permissão própria (§3.5), então o acesso a dado financeiro pode ser separado do acesso a dado operacional.

---

# 3. Entidades e conceitos

## 3.1. Tipo de relatório — mecânica comum

Os tipos são **fixos** — o usuário não cria tipos novos (RN-REL-01). Dentro de cada tipo, ele configura:

| Elemento | Notas |
|---|---|
| Período | Padrão: mês atual (RN-REL-04). Aceita períodos relativos ("mês atual", "últimos 30 dias") ou um intervalo fixo de datas. |
| Comparação | Com o período anterior equivalente — valores dos dois períodos, variação absoluta e percentual (RN-REL-04). |
| Filtros | Conjunto próprio de cada tipo (§3.3), combináveis entre si. |
| Agrupar por | Uma dimensão por vez, entre as que o tipo oferece (§3.3). Troca a granularidade da mesma tabela, sem trocar de relatório. |

**RN-REL-01 — Tipos são fixos; a personalização é filtro, agrupamento e Visão salva.** Não existe construtor livre de relatório — decisão consciente, já registrada na v1.2 ("não inclui construtor de relatórios customizados"). Cada tipo tem forma de dado própria (o Funil é passo a passo por Stage; a Venda de leads é um ledger), e generalizar isso numa grade única é onde a complexidade explodiria.

**RN-REL-02 — Cada tipo declara seus próprios filtros e dimensões de agrupamento.** Nem toda dimensão faz sentido em todo tipo (ex.: "Ator" só existe na Esteira de Propostas; "Regra" só em Ofertas e Elegibilidade).

**RN-REL-03 — No Admin, todo relatório é escopo `all`.** É a visão do tenant — todas as Lojas. Nenhum tipo do Admin oferece `store`, `team` ou `self`: esses recortes pertencem ao CRM (módulo CRM · Relatórios). A dimensão "Agrupar por Loja" existe só aqui.

**RN-REL-04 — Período padrão é o mês atual, sempre com comparação ao período anterior.** Herdado de RN059/RN064 da v1.2.

**RN-REL-05 — Exportação fiel ao que está na tela, e auditada.** CSV/XLSX com os mesmos filtros, agrupamento, colunas e comparação exibidos — cada exportação registra autor, momento, tipo e filtros (herdado de RN065 da v1.2). Relatórios trazem dado de clientes e financeiro; a trilha de exportação é exigência de LGPD (RNF003 da v1.2).

## 3.2. Visão salva

Combinação nomeada de um tipo com filtros, período e agrupamento — reaparece depois sem precisar reconfigurar.

| Campo | Obrigatório | Notas |
|---|---|---|
| Título | Sim | Ex.: "Produção por Loja — INSS". |
| Tipo | Sim, travado | O tipo de relatório de origem. Não muda depois de salva. |
| Filtros, Período, Agrupar por | Sim | A configuração no momento de salvar. **O período relativo ("mês atual", "últimos 30 dias") é salvo como relativo e reabre sempre acompanhando a data de hoje**; o intervalo fixo reabre com as mesmas datas (RN-REL-07). |
| Dono | Implícito | Quem salvou. |

**RN-REL-06 — Visão salva pertence a quem a criou.** Confirmado: Visões são pessoais — cada usuário vê e gerencia só as próprias, sem compartilhamento nesta versão.

**RN-REL-07 — Período relativo é salvo como relativo; intervalo fixo, como fixo.** *(Decisão própria, não pedida explicitamente.)* Uma Visão salva com "mês atual" mostra o mês corrente sempre que é reaberta — não congela o mês em que foi salva. Uma Visão salva com datas fixas reabre exatamente aquelas datas.

**RN-REL-08 — Reabrir uma Visão aplica a configuração salva; mudar algo não altera a Visão até salvar de novo.** O usuário pode ajustar filtros a partir de uma Visão e, se quiser manter, salvar por cima ou como nova Visão.

## 3.3. Os seis tipos

### Compartilhados com o CRM (definição única, aqui)

**Produção**

- **Base:** Propostas que chegaram a Pago no período.
- **Métricas:** Produção (R$) — soma do **valor base** (RN-OFR-07, módulo de Ofertas) — e Quantidade de Propostas Pagas.
- **Agrupar por:** Loja, Segmento, Time, Operador, Produto, Operação, Banco, Promotora, Origem da lead, Origem de Venda.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-09 — Produção usa o valor base, sem multiplicador.** *(Decisão própria.)* O multiplicador é configurado por cada Loja (módulo CRM · Tabelas Multiplicadoras) e serve para metas; somar valores multiplicados de Lojas diferentes misturaria escalas diferentes. No recorte do CRM, o "valor de meta" (multiplicado) pode aparecer como coluna adicional, porque ali é sempre uma Loja só.

**Funil e Conversão**

- **Base:** Leads criadas no período, por Funil.
- **Métricas:** Leads por Origem da lead; conversão por Stage e do funil inteiro (até Pago); tempo médio em cada Stage; tempo até o primeiro atendimento (speed-to-lead); motivos de perda.
- **Agrupar por:** Loja, Segmento/Funil, Origem da lead, Origem de Venda, Time, Operador.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-10 — Motivos de perda vêm de duas fontes.** Antes de existir Proposta: o nome da instância de Desqualificar ou Finalizar executada (Ações, módulo de Funis de Vendas). Depois: o Motivo do cancelamento da Proposta — o Stage tipo Cancelado escolhido (RN-PRP-04). O relatório mostra as duas fontes separadas, para não misturar "o cliente não tinha margem" com "a proposta foi cancelada no banco".

**RN-REL-11 — Speed-to-lead é medido até a primeira Ação executada.** Confirmado (a v1.2 deixava esse marco "a definir", RN066). Como toda movimentação de Lead acontece por Ação (RN-LED-11), a primeira Ação executada pelo responsável é o marco natural de "primeiro atendimento".

**Esteira de Propostas**

- **Base:** Propostas existentes no período — em andamento ou concluídas.
- **Métricas:** Quantidade por Status (os específicos do tenant, módulo de Status de Proposta); tempo entre os marcos — Data do contrato → Data de averbação → Data de pagamento ao cliente → Data de quitação; produtividade por Ator (Propostas por Consultor, Averbador, Emissor de Boleto e Digitador); cancelamentos por motivo; portabilidades por Banco de origem.
- **Agrupar por:** Loja, Status, Ator, Produto, Banco, Banco de origem.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-12 — Tempos só contam quando as duas datas existem, e o relatório mostra a cobertura.** As datas da operação são preenchidas manualmente e são opcionais (módulo de Propostas). Um tempo entre marcos só é calculado para Propostas com as duas datas preenchidas, e o relatório exibe a cobertura ("calculado sobre N de M propostas"), para que um número bom não esconda uma base pequena.

### Exclusivos do tenant

**Venda de leads**

- **Base:** lançamentos do Extrato de Créditos de todas as Lojas, no período (módulo CRM · Créditos).
- **Métricas:** Consumo — leads entregues (quantidade e valor dos Débitos), menos Estornos; Créditos adicionados, por origem (Pix, manual); Estornos (quantidade e valor); Lojas com saldo negativo e o valor em aberto.
- **Agrupar por:** Loja, Funil, Origem da lead, Origem do crédito.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-13 — Consumo e entrada de crédito são métricas separadas.** Consumo (Débitos por lead entregue) é a venda efetiva de leads. Crédito adicionado é dinheiro que entra antes do consumo. As duas não se somam nem se substituem. Créditos manuais (Admin) aparecem separados do Pix, porque podem não representar entrada de dinheiro (ex.: bonificação).

**Comissões a pagar**

- **Base:** Propostas que chegaram a Pago no período.
- **Métricas:** Comissão gravada no Pago (taxa da Oferta × valor base, RN-OFR-07 — snapshot de RN-PRP-05), somada por recorte.
- **Agrupar por:** Loja (principal), Oferta, Produto, Banco, Promotora.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-14 — É um relatório de valores devidos, não um controle de pagamento.** O produto não registra a quitação das comissões — gestão financeira (contas a pagar, conciliação) está fora do escopo desde a v1.2. O cálculo usa a comissão gravada no Pago (RN-PRP-05, módulo de Propostas), não a taxa atual da Oferta — editar a Oferta depois não altera comissões já devidas.

**Ofertas e Elegibilidade**

- **Base:** Simulações realizadas no período (módulo CRM · Simulações), de todas as Lojas.
- **Métricas:** volume de simulações (manual × automática via Lead); por Oferta, quantas vezes foi avaliada, aprovada e rejeitada; Regras que mais desqualificaram (a Regra gravada no snapshot, com a sua mensagem de erro — RN-SIM-11); conversão de Simulação em Proposta (Propostas com Simulação de referência).
- **Agrupar por:** Oferta, Promotora, Produto, Banco, Segmento, Regra, Loja.
- **Filtros:** os mesmos campos do agrupamento.

**RN-REL-15 — Ofertas e Elegibilidade lê o snapshot, não a configuração atual.** Como cada Simulação guarda o resultado da época (RN033 da v1.2, mantido em Simulações), o relatório mostra o que de fato aconteceu — se uma Regra foi editada depois, as rejeições antigas continuam atribuídas à versão que existia na hora. A Regra é identificada pelo que o snapshot grava (a Regra, a mensagem e os requisitos que falharam, RN-SIM-11), e não pela mensagem isolada: duas Regras podem ter a mesma mensagem, e uma Regra excluída continua aparecendo pelo nome gravado.

**RN-REL-16 — "Origem da lead" e "Origem de Venda" são dimensões distintas.** *Origem da lead* é o campo do sistema na Lead (Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa, Manual — módulo de Leads, RN-LED-12); *Origem de Venda* é o rótulo livre do tenant (módulo de Origem de Venda). Nos relatórios sobre **Propostas** (Produção e Esteira), a Origem da lead vem da Lead da Proposta; Proposta sem Lead aparece em "Sem lead" *(decisão desta revisão)*.

**RN-REL-17 — Origem de Venda é agrupada pelo rótulo.** Agrupa-se e filtra-se pelo texto do rótulo (RN-ORI-04, módulo de Origem de Venda): um rótulo órfão, de uma Origem excluída, e um cadastro de mesmo nome caem na mesma linha. Lead sem Origem de Venda aparece em "Sem origem de venda". O filtro lista os rótulos presentes nos dados do período, inclusive os excluídos.

**RN-REL-18 — Lojas, Times e Operadores excluídos continuam nos relatórios.** A exclusão de Loja (RN-LOJ-15, com os Times em cascata) e a de usuário (RN-USR-05) são soft-delete: o histórico permanece referenciando-os. Os relatórios do período em que atuaram os mostram normalmente, marcados como "(excluída)" ou "(excluído)", e os filtros listam os excluídos presentes nos dados do período — o mesmo tratamento do rótulo de Origem de Venda (RN-REL-17). *(Decisão desta revisão, consequência do soft-delete.)*

**RN-REL-19 — "Time" é o Time atual do Operador.** Em "Agrupar por Time" e no filtro de Time, o Time de cada registro é o **Time em que o Operador está hoje**, e não o da data do evento. Se o Operador mudou de Time, o histórico dele passa a aparecer no Time novo. Mudanças de Time são raras (informação do usuário), então a distorção é pequena; guardar o Time no momento do evento (lead atribuída, proposta paga) fica como evolução, caso incomode na prática. A **Loja** da Lead não muda depois da entrega, então não tem esse problema. *(Decisão do usuário.)*

**RN-REL-20 — Relatórios de Propostas leem o snapshot da Oferta gravado na Proposta.** Produção, Esteira de Propostas e Comissões a pagar agrupam e filtram por Produto, Promotora, Banco, Operação, Oferta e valor base **como estavam quando a Oferta foi associada à Proposta** (snapshot da Proposta, módulo de Propostas), e não pela Oferta atual: editar, inativar ou excluir uma Oferta depois não reescreve a história. Uma Oferta excluída continua aparecendo pelo nome gravado, marcada "(excluída)". *(Decisão do usuário: a Proposta carrega um snapshot da Oferta. A regra correspondente em Propostas está sendo escrita na revisão do CRM, e hoje o RN-PRP-05 ainda diz o contrário para o que vem antes do Pago.)*

## 3.4. Tela de entrada

A tela de Relatórios mostra os seis tipos como ponto de partida e, abaixo, a lista de Visões salvas do usuário (Título, Tipo). Detalhamento de telas fica para a Fase 2.

## 3.5. Acesso

Cada tipo é um sub-recurso de permissão próprio — mesmo padrão já usado em Propostas (`proposals_documents`, etc.) — permitindo, por exemplo, que um Cargo veja Produção sem ver Comissões a pagar. Todos com escopo único `all` (RN-REL-03). Exportar é ação separada de ver. Salvar Visão é consequência de poder ver o tipo — não exige permissão própria. Catálogo completo na Matriz de Permissões (Fase 2).

---

# 4. Requisitos funcionais

## RF-REL-01 — Consultar um relatório

**Como** usuário do tenant, **eu quero** abrir um tipo de relatório, ajustar período, filtros e agrupamento **para** responder a pergunta de negócio que tenho agora.

**Comportamento esperado:** escolhe o tipo, vê o resultado do mês atual com comparação ao período anterior, ajusta filtros e "Agrupar por" livremente; a tabela atualiza a cada mudança.

## RF-REL-02 — Salvar, reabrir e gerenciar Visões

**Como** usuário do tenant, **eu quero** salvar uma configuração de relatório com um nome **para** reabri-la depois sem reconfigurar.

**Comportamento esperado:** "Salvar como Visão" pede um Título. A Visão aparece na lista da tela de entrada; reabri-la aplica a configuração salva (RN-REL-07/08). O diálogo de salvar informa, em texto de apoio, como o período será guardado (relativo ou fixo). O dono pode renomear, salvar por cima ou excluir.

## RF-REL-03 — Exportar

**Como** usuário do tenant com permissão de exportação, **eu quero** baixar o relatório em CSV ou XLSX **para** trabalhar os dados fora do sistema.

**Comportamento esperado:** gera o arquivo fiel ao que está na tela e registra a exportação na trilha de auditoria (RN-REL-05).

---

# 5. Regras de negócio (consolidado)

- **RN-REL-01** — Tipos fixos; personalização é filtro, agrupamento e Visão salva.
- **RN-REL-02** — Cada tipo declara seus filtros e dimensões.
- **RN-REL-03** — No Admin, todo relatório é escopo `all`; "Agrupar por Loja" só existe aqui.
- **RN-REL-04** — Período padrão mês atual, com comparação ao anterior.
- **RN-REL-05** — Exportação fiel e auditada.
- **RN-REL-06** — Visão salva é pessoal.
- **RN-REL-07** — Período relativo salvo como relativo; fixo como fixo.
- **RN-REL-08** — Ajustes não alteram a Visão até salvar de novo.
- **RN-REL-09** — Produção usa valor base, sem multiplicador.
- **RN-REL-10** — Motivos de perda em duas fontes, exibidas separadas.
- **RN-REL-11** — Speed-to-lead medido até a primeira Ação executada.
- **RN-REL-12** — Tempos da Esteira só com as duas datas; cobertura exibida.
- **RN-REL-13** — Consumo e entrada de crédito separados; crédito manual à parte.
- **RN-REL-14** — Comissões a pagar é relatório de valor devido, calculado sobre a comissão gravada no Pago; não é controle de pagamento.
- **RN-REL-15** — Ofertas e Elegibilidade lê o snapshot das Simulações; a Regra é a gravada no snapshot, não a mensagem isolada.
- **RN-REL-16** — Origem da lead e Origem de Venda são dimensões distintas; Proposta sem Lead aparece em "Sem lead".
- **RN-REL-17** — Origem de Venda é agrupada pelo rótulo; lead sem ela aparece em "Sem origem de venda".
- **RN-REL-18** — Lojas, Times e Operadores excluídos (soft-delete) continuam nos relatórios, marcados como excluídos.
- **RN-REL-19** — "Time" é o Time atual do Operador, e não o da data do evento.
- **RN-REL-20** — Relatórios de Propostas leem o snapshot da Oferta gravado na Proposta; editar, inativar ou excluir a Oferta não reescreve a história.

---

# 6. Estados e transições

Relatório não tem ciclo de vida — é consulta computada. **Visão salva** existe ou não existe; sem estados intermediários.

---

# 7. Limites do módulo

**Não** inclui: construtor livre de relatório; agendamento ou envio automático; integração com ferramentas de BI; projeções; relatórios de atendimento (o Chat não grava mensagens — módulo CRM · Chat); Metas entre Lojas (cada Loja define as próprias Metas, com alvos diferentes — comparar percentuais entre Lojas mistura réguas); e controle de pagamento de comissões.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Propostas, Leads, Simulações, Créditos** (CRM) — dados de todos os relatórios.
- **Ofertas, Produtos, Promotoras, Regras, Segmentos, Origem de Venda, Status de Proposta, Funis de Vendas, Lojas** (Admin) — dimensões, valor base, comissão e motivos.

**Expõe para:**
- **CRM · Relatórios** — a definição dos três tipos compartilhados (Produção, Funil e Conversão, Esteira de Propostas), consumida como recorte `store` e abaixo, sem "Agrupar por Loja".

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Relatórios v0.6 — módulo da aplicação Admin.*
