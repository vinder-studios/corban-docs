# Corban CRM · Propostas

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.14
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.14 | **Valor liberado e Valor financiado** (§3.1) existem em qualquer Operação, junto do Valor da dívida; o valor base é um deles conforme a Operação (Ofertas v0.16, RN-OFR-07). O "Valor do contrato" passa a se chamar Valor financiado. Decisão do usuário. |
| 0.13 | **Campos do contrato confirmados pelo usuário** (Valor, Parcela e Prazo, §3.1). |
| 0.12 | **Valor, Parcela e Prazo do contrato** (campos novos, opcionais, §3.1) e **geração do Contrato** no histórico do Cliente quando a Proposta chega a Pago (nova RN-PRP-18), para a Carteira Ativa do Marketing. A importação de Propostas passa a aceitar essas três colunas, e a referência ao "padrão de cargas do Marketing" aponta para a Lista de Prospecção. Decisão do usuário: o sistema passa a guardar o histórico de contratos e propostas do Cliente; os campos são propostos [confirmar]. |
| 0.11 | **Levantamento do CRM, decisões do usuário.** (1) **A Proposta guarda uma única Oferta como snapshot, gravado ao associá-la**, com o multiplicador junto; a taxa de comissão congela na associação; a Oferta pode ficar vazia em Novo e é obrigatória para sair de Novo; Duplicar copia a Oferta atual (RN-PRP-05, RN-PRP-07). (2) **A atribuição é gravada no Pago** (Operador, Time e Cargo), para Metas, Rankings e relatórios não perderem vendas quando alguém é promovido ou inativado. (3) **Propostas não se excluem** (RN-PRP-08; sai o RF-PRP-08). (4) **Pago e Cancelado só editam Documentos, Datas e Comentário** (RN-PRP-15). (5) **Novo campo Operador** e **Origem de Venda** (a Tag "Origem"), `self` = Operador ou Ator (RN-PRP-16/17). (6) O Status é o do cadastro do tenant, e nasce no status ativo mais antigo do tipo Novo. (7) Busca por CPF só entre Clientes da Loja. Corrige o aviso de abertura (a Proposta tem quatro caminhos) e reordena este histórico. |
| 0.10 | **Comissão passa a ser gravada no Pago**, junto com o multiplicador (RN-PRP-05). Motivo: o relatório Comissões a pagar (Admin · Relatórios) recalcularia Propostas antigas se a taxa da Oferta mudasse depois. Novo campo "Comissão aplicada". |
| 0.9 | **Corrige resíduo da v0.6**, encontrado ao analisar o CRM para Relatórios: a v0.6 registrou a remoção do campo "Tabela Multiplicadora usada", mas a tabela de campos, o ator Operador e dois itens do consolidado continuaram citando-o. Removido em todos os pontos — cada Oferta já é a "tabela" (módulo Tabelas Multiplicadoras), não há escolha de tabela. |
| 0.8 | Corrige RN-PRP-13: Operação é campo próprio da Oferta de novo (módulo de Ofertas v0.9), não mais "do Produto". Adiciona **Banco de origem** (RN-PRP-14) — obrigatório quando a Operação é Portabilidade ou Portabilidade com Refinanciamento, capturado na criação manual e na importação, exibido como badge na linha de Tags. |
| 0.7 | RN-PRP-09 ganha a regra de travamento: cada Ator trava individualmente assim que atribuído — diferente de Documentos e Datas, que continuam editáveis em qualquer Status. |
| 0.6 | Corrige a Tag "Nome da tabela multiplicadora": não existe uma entidade "Tabela" separada para referenciar (módulo Tabelas Multiplicadoras v0.2, revertido) — cada Oferta já É, em si, uma "tabela". Remove o campo "Tabela Multiplicadora usada" e RN-MUT-04 (renumerado); a Tag mostra a identificação da própria Oferta (Nome + taxa), não um nome de coleção separado. |
| 0.5 | **Correção real de inferência errada.** Lead deixa de ser vínculo obrigatório — vira **opcional**. O vínculo de verdade é com **Cliente** (existente ou dados capturados inline, mesmo padrão de Simulações RN-SIM-05). Nem toda Proposta nasce do board de Leads: criação manual, Duplicar e Importação podem gerar Propostas **sem nenhuma Lead**. RN-PRP-01 e RN-PRP-03 revisadas para essa condicionalidade. |
| 0.4 | **Reformulação de fundo, motivada pela especificação do Card de Proposta.** Ganha destino de topo próprio (nunca foi só contextual — era engano tratar como tal). Ganha quatro Atores complementares ao operador (Consultor, Averbador, Emissor de Boleto, Digitador). Ganha novas formas de criação — manual, Duplicar, Importação — **além** da automática (RN-PRP-02 continua intacta para o caminho automático). Ganha Excluir. Ganha datas de operação, Val. Dívida, Documentos da Proposta (distintos de Documentos do Cliente), linha do tempo de eventos, Comentário opcional na mudança de Status, e Tags computadas (Tabela Multiplicadora, Operação, Produto, Banco, Origem — da Lead). Referencia Tabelas Multiplicadoras (plural), não mais o módulo singular. |
| 0.3 | Fecha os dois itens abertos: **sem reabertura de Proposta** — Pago e Cancelado são definitivamente terminais; **sem múltiplos Stages tipo Pago** — só Cancelado pode ter mais de um, RN-PRP-04 nunca se estende a Pago. |
| 0.2 | Adiciona o campo **Oferta** à Proposta (RN-PRP-07) — durante a negociação, várias Ofertas podem estar em consideração (vindas da Simulação); o operador seleciona qual delas embasa a Proposta única. RN-PRP-01 não muda: continua sendo uma Proposta por Lead. Substitui o modo demonstração da Digitação de Proposta (Módulo 5, v1.2) — Proposta entra como funcionalidade plena. |
| 0.1 | Versão inicial. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRP) e regras de negócio (RN-PRP) próprios.

> **A Proposta nasce por quatro caminhos:** o automático (o gatilho é a Lead entrar em Formalização, RN-FNL-17, módulo de Funis de Vendas) e três manuais — criação manual, Duplicar e Importação (RF-PRP-03/04/05) —, nenhum deles exigindo Lead. **Propostas não se excluem:** uma criada por engano se cancela (RN-PRP-08).

---

# 1. Objetivo do módulo

Formalizar e acompanhar a Proposta de crédito — do nascimento (automático, ao entrar a Lead em Formalização, ou manual/duplicado/importado) até o desfecho (Pago ou Cancelado), que move a Lead de volta quando aplicável.

---

# 2. Atores

- **Operador** (Vendedor) — trabalha a Proposta: atualiza o Status, escolhe a Oferta, registra o motivo ao cancelar. É o mesmo operador responsável pela Lead (módulo de Leads).
- **Consultor, Averbador, Emissor de Boleto, Digitador** — Atores complementares, atribuídos manualmente por Proposta (RN-PRP-09). Não substituem o Operador — cada um cobre uma etapa específica da formalização.
- **Sistema** — cria a Proposta automaticamente quando a Lead entra em Formalização (RN-PRP-02), e sincroniza o Status com o Stage da Lead nos dois sentidos.

---

# 3. Entidades e conceitos

## 3.1. Proposta

| Campo | Obrigatório | Notas |
|---|---|---|
| Lead | **Não** | Opcional — presente quando a Proposta nasceu do caminho automático (Formalização) ou foi manualmente associada a uma. Quando presente, é 1:1: aquela Lead não tem outra Proposta (RN-PRP-01). |
| **Cliente** | Sim, de alguma forma | O vínculo real da Proposta. Cliente cadastrado (referência) **ou** dados capturados inline (CPF, Nome completo, Telefone — mesmo padrão de RN-SIM-05, módulo de Simulações) quando não está cadastrado. Toda Proposta tem um Cliente, com ou sem Lead. |
| **Operador** | Sim | O responsável pela Proposta: o da Lead, quando há; senão, quem a criou (manual, Duplicar) ou quem a importou. É o Operador que recebe o crédito de Produção, Quantidade e Valor de meta (RN-PRP-16); os Atores não. |
| **Origem de Venda** | Não | Rótulo do tenant (módulo de Origem de Venda, Admin), `Combobox` criável. **Copiada da Lead** na criação, quando há, e **depois independente** dela; sem Lead, é capturada no cadastro manual ou na importação. É a Tag "Origem" do Card (RN-PRP-13). |
| Número da proposta | Não | Identificador, quando aplicável — pode ficar em branco ("-") até ser preenchido/atribuído. |
| Simulação de referência | Não | Se a Lead teve simulação, pré-preenche o valor (herdado de RF030 da v1.2). |
| Status | Sim | O **Status** do cadastro do tenant (módulo de Status de Proposta); o **Tipo** dele — Novo, Negociação, Pago ou Cancelado — comanda o comportamento. Nasce no **status ativo mais antigo do tipo Novo** (RN-STP-08). |
| Oferta | Para sair de Novo | A Oferta que embasa esta Proposta — a Proposta guarda **uma só**, como **snapshot** gravado ao associá-la (RN-PRP-05, RN-PRP-07): Oferta, Produto, Promotora, Banco, Operação e a taxa de comissão. Pode ficar **vazia enquanto a Proposta está em Novo**; é obrigatória para sair de Novo. Determina o valor base (RN-OFR-07), a Operação e o Produto/Banco exibidos como Tag. |
| Banco de origem | Condicional | Obrigatório quando a Operação da Oferta é Portabilidade ou Portabilidade com Refinanciamento (RN-PRP-14) — de qual banco o contrato está sendo portado. |
| Multiplicador aplicado | Sim, junto com a Oferta | Snapshot do multiplicador da Loja para a Oferta, gravado **no mesmo momento do snapshot da Oferta** (RN-PRP-05, RN-MUT-04) e regravado a cada troca de Oferta. |
| Valor de meta | Calculado | `Valor base (RN-OFR-07) × Multiplicador aplicado` — calculado quando a Proposta chega a Pago, e só conta para Metas a partir daí (RN-MET-03). |
| Comissão aplicada | Taxa na associação; valor no Pago | A **taxa** de comissão fica congelada junto com o snapshot da Oferta; o **valor** resultante (taxa × valor base, RN-OFR-07) é gravado quando o Status chega a Pago (RN-PRP-05). |
| Motivo (se Cancelado) | Condicional | Obrigatório só quando há mais de um Stage do tipo Cancelado no Funil (RN-PRP-04). |
| **Atribuição no Pago** | Sim, no fechamento | Gravados quando a Proposta chega a Pago: o **Operador**, o **Time** e o **Cargo** dele naquele momento. Metas, Rankings, Dashboard e os relatórios baseados em Pago leem esta atribuição, e não o cadastro atual: promoção, inativação ou troca de Time depois não movem a venda (RN-PRP-05). |
| Comentário (mudança de Status) | Não | Sempre opcional, em qualquer mudança de Status — RN-PRP-10. |
| Atores | Não | Consultor, Averbador, Emissor de Boleto, Digitador — RN-PRP-09. |
| **Datas da operação** | Não | Data do contrato, Data de averbação, Data de pagamento ao cliente, Data de quitação, Data de retorno de saldo. |
| Valor liberado, Valor financiado | Não | Valores da operação. Existem em **qualquer** Operação, e o operador preenche os que se aplicam. O **valor base** (RN-OFR-07) é um deles, conforme a Operação da Oferta: Valor liberado no Novo; Valor financiado em Refinanciamento, Portabilidade e Portabilidade com Refinanciamento. O Valor financiado é o valor do contrato. |
| Parcela e Prazo (meses) | Não | Dados do contrato fechado. Alimentam o Contrato do histórico do Cliente (RN-PRP-18); sem Parcela e Prazo, a Carteira Ativa não consegue analisar o Cliente. Editáveis em qualquer Status, como as Datas. |
| Valor da dívida | Não | "Val. Dívida" no Card — relevante para Operações de Portabilidade/Refinanciamento. |
| Resíduo (data + valor) | Não | — |
| Documentos da Proposta | Não | Distintos de Documentos do Cliente (módulo de Clientes) — exclusivos desta negociação, não aparecem em outras Propostas do mesmo Cliente (RN-PRP-11). |
| Linha do tempo | Calculado | Feed cronológico de eventos desta Proposta — mudanças de Status, documentos anexados, Atores atribuídos, cada um com autor e data (RN-PRP-12). |

**RN-PRP-01 — Quando há Lead, é 1:1 — mas Proposta pode existir sem nenhuma Lead.** Se uma Proposta está vinculada a uma Lead, aquela Lead não tem outra Proposta — mesmo quando várias Ofertas estão sendo consideradas (RN-PRP-07), é sempre uma Proposta só por Lead, com uma Oferta selecionada. **Mas nem toda Proposta tem Lead**: criação manual, Duplicar e Importação (RF-PRP-03/04/05) podem gerar Propostas vinculadas só a um Cliente, sem passar pelo board de Leads em nenhum momento.

**RN-PRP-07 — A Proposta guarda uma única Oferta, que pode ser substituída.** Durante a negociação, mais de uma Oferta pode ser considerada (tipicamente as elegíveis retornadas pela Simulação), mas a Proposta carrega **uma só**, a escolhida pelo operador. A escolha preenche o campo Oferta e grava o snapshot (RN-PRP-05). **Trocar a Oferta** é permitido em Novo e Negociação (dentro do Editar) e grava um **novo** snapshot, que substitui o anterior; em Pago e Cancelado a Oferta fica travada (RN-PRP-15). A Oferta pode ficar vazia em Novo, mas é obrigatória para sair de Novo.

**RN-PRP-02 — Nascimento automático continua existindo, sem alteração — mas deixou de ser o único caminho.** A Proposta ainda nasce automaticamente quando a Lead entra num Stage de tipo Formalização (RN-FNL-17, módulo de Funis de Vendas) — esse caminho é intocado, e é o único que sempre vem com Lead. **Além dele**, agora existem: criação manual (RF-PRP-03), Duplicar (RF-PRP-04) e Importação (RF-PRP-05) — nenhum dos três exige Lead. Todos exigem um Cliente (RN-PRP-01, campo Cliente).

**RN-PRP-09 — Atores são atribuídos manualmente, complementando o Operador — e travam individualmente assim que definidos.** Consultor, Averbador, Emissor de Boleto e Digitador são campos próprios da Proposta, cada um atribuível a um Membro da Loja — independentes do Operador (que segue sendo o responsável pela Lead, módulo de Leads). Nenhum é obrigatório. **Cada Ator trava assim que atribuído** — não editável depois, independentemente do Status da Proposta ou dos demais Atores (cada campo trava por si, não em bloco). Diferente de Documentos e Datas (RN-PRP-11 e a seção de Datas), que continuam editáveis em qualquer Status. O Ator **não recebe crédito** de Produção nem de Quantidade (RN-PRP-16).

**RN-PRP-10 — Comentário na mudança de Status é sempre opcional.** Diferente do Motivo (obrigatório só em Cancelado com ambiguidade, RN-PRP-04), o Comentário é um campo de texto livre, nunca obrigatório, disponível em qualquer mudança de Status.

**RN-PRP-11 — Documentos da Proposta são exclusivos da negociação, distintos de Documentos do Cliente.** Um documento anexado à Proposta não aparece em outras Propostas do mesmo Cliente — ao contrário de Documentos do Cliente (módulo de Clientes), que aparecem em todas as negociações dele. Mesma distinção já registrada no módulo Layout de Lead (RN-LAY-06).

**RN-PRP-12 — Linha do tempo é gerada automaticamente, não editável.** Todo evento relevante da Proposta (mudança de Status, documento anexado, Ator atribuído) gera uma entrada na linha do tempo — autor, data/hora, descrição. Consultável, nunca editável diretamente.

## 3.2. Sincronização com a Lead — só quando ela existe

**RN-PRP-03 — Status de tipo Pago ou Cancelado move a Lead automaticamente, quando há Lead vinculada.** Consequência de RN-FNL-18 (módulo de Funis de Vendas): ao mudar o Status da Proposta para um Status de tipo Pago ou Cancelado, o sistema move a Lead correspondente para o Stage do mesmo Tipo no Funil — **só quando a Proposta tem Lead** (RN-PRP-01). Proposta sem Lead (criação manual/Duplicar/Importação, sem associação) simplesmente não dispara esse evento — não existe Stage para sincronizar. **Uma Lead cancelada por uma Proposta não reabre** (Funis, RN-FNL-19): a Proposta é terminal e é 1:1 com a Lead (RN-PRP-01).

**RN-PRP-04 — Cancelar com múltiplos Stages do tipo exige Motivo — regra exclusiva de Cancelado.** Se o Funil da Lead tem mais de um Stage de tipo Cancelado (caso raro — ex.: "Cancelado" vs. "Desqualificado"), o operador que muda o Status para Cancelado **precisa escolher** entre os Stages disponíveis daquele Tipo — a escolha é o próprio Motivo, não um campo à parte (o Nome do Stage já cumpre esse papel, RN-FNL-18). Com um único Stage do tipo, a mudança de Status é direta, sem esse passo extra. **Não se estende a Pago** — há exatamente um Stage tipo Pago por Funil (Funis, RN-FNL-15); mudar para Pago é sempre direto, sem escolha de destino.

## 3.3. Multiplicador de Meta

**RN-PRP-05 — A Oferta é congelada ao ser associada; a atribuição e os valores finais, no Pago.** Quando a Oferta é associada à Proposta (e a cada troca), a Proposta grava um snapshot: a Oferta, o Produto, a Promotora, o Banco, a Operação, a **taxa de comissão** e o **multiplicador** da Loja para aquela Oferta (RN-MUT-04). Editar, inativar ou excluir a Oferta, ou mudar o multiplicador, depois da associação **não altera** a Proposta; uma troca de Oferta grava outro snapshot. **No momento do Pago**, a Proposta grava ainda o Valor de meta (valor base × multiplicador), o valor da comissão (taxa congelada × valor base) e a **atribuição**: o Operador, o **Time** e o **Cargo** dele naquele instante. Metas, Rankings, Dashboard e os relatórios baseados em Pago leem essa atribuição, e não o cadastro atual — promoção, inativação ou troca de Time depois não retiram a venda do Time nem do Membro. Antes do Pago, o Valor de meta não existe. *(Decisões do usuário, levantamento do CRM: snapshot da Oferta na associação; multiplicador gravado junto; atribuição fixada no Pago.)*

## 3.4. Sem consumo de Créditos

**RN-PRP-06 — Proposta não debita saldo.** O débito já aconteceu quando a Lead foi atribuída à Loja (módulo de Créditos, RN-CRD-01) — nenhuma ação sobre a Proposta gera novo lançamento no Extrato.

## 3.5. Tags do Card (computadas, sem campo próprio)

**RN-PRP-13 — Tags, todas derivadas de campos já existentes — cinco fixas, mais uma condicional.** Identificação da Oferta selecionada (o que aparecia como "Nome da tabela multiplicadora" — cada Oferta já é, em si, uma "tabela" no módulo Tabelas Multiplicadoras, sem entidade separada para nomear), **Operação (campo próprio da Oferta — módulo de Ofertas v0.9, não mais derivada do Produto)**, Produto, Banco (da Oferta), e **Origem** — a **Origem de Venda** da própria Proposta (campo seu, copiado da Lead na criação e independente depois; sem Lead, capturada no cadastro manual ou na importação — módulo de Origem de Venda). **Sexta Tag, condicional:** Banco de origem (RN-PRP-14), só quando a Operação for Portabilidade ou Portabilidade com Refinanciamento. Nenhuma delas é a entidade Tag de Funis de Vendas (categorias Audiência/Origem/Departamento) — conceitos distintos, já registrado anteriormente.

**RN-PRP-14 — Banco de origem é obrigatório em Portabilidade e Portabilidade com Refinanciamento.** Quando a Operação da Oferta selecionada for uma dessas duas, a Proposta exige informar **de qual banco o contrato está sendo portado** — campo próprio, buscado via API do Banco Central (mesmo padrão já usado em Regras e Ofertas para campos de Banco). Não se aplica a Novo nem Refinanciamento puro. Capturado na criação manual (RF-PRP-03) e na importação (RF-PRP-05) — linha/formulário sem esse campo preenchido, quando a Operação exige, é rejeitado (importação) ou bloqueia salvar (manual). Exibido como badge na linha de Tags (RN-PRP-13).

**RN-PRP-18 — O Pago gera um Contrato no histórico do Cliente.** Quando a Proposta chega a Pago, o sistema grava um Contrato no histórico do Cliente (Clientes, RN-CLI-13) com Segmento, Operação, Banco, Valor, Parcela, Prazo e Data de pagamento ao cliente da Proposta. O Contrato **acompanha** as edições posteriores desses campos na Proposta. O histórico é de uso interno do tenant e não aparece para as Lojas.

**RN-PRP-08 — Proposta não se exclui, em nenhum Status.** Nem o gestor pode excluí-la. Uma Proposta criada por engano se resolve **cancelando-a** (Status de tipo Cancelado, RN-PRP-04): ela permanece no histórico, nos relatórios e na Lead, quando há. *(Decisão do usuário, levantamento do CRM.)*

**RN-PRP-15 — O que se edita depende do Tipo do Status.** Em **Novo** e **Negociação**, tudo é editável (inclusive trocar a Oferta, RN-PRP-07), respeitando a trava individual dos Atores (RN-PRP-09). Em **Pago** e **Cancelado**, só **Documentos**, **Datas** e **Comentário**; o resto fica travado. *(Decisão do usuário.)*

**RN-PRP-16 — O Operador recebe o crédito da venda.** Produção, Quantidade, Valor de meta e Metas são creditados **só ao Operador** da Proposta (o da Lead, ou quem a criou ou importou); os Atores (Consultor, Averbador, Emissor de Boleto, Digitador) não contam. *(Consequência de `self` incluir o Ator, RN-PRP-17.)*

**RN-PRP-17 — `self` em Propostas são as Propostas em que o usuário é o Operador ou um Ator.** O escopo `self` alcança as Propostas das Leads atribuídas ao usuário, as que ele criou (sem Lead) **e** aquelas em que foi atribuído como Consultor, Averbador, Emissor de Boleto ou Digitador. Os escopos `team`, `store` e `all` herdam esse recorte, ampliado. *(Decisão do usuário; antes só constava nas Variações.)*

---

# 4. Requisitos funcionais

## RF-PRP-01 — Acompanhamento da Proposta

**Como** operador, **eu quero** ver e atualizar o Status da Proposta vinculada à minha Lead **para** formalizar a negociação até o desfecho.

**Comportamento esperado:** a partir da Lead (já em Formalização), o operador acessa a Proposta, atualiza o Status conforme o processo avança. Ao mover para Pago ou Cancelado, a sincronização com o Funil (RN-PRP-03) acontece imediatamente.

## RF-PRP-02 — Cancelamento com motivo

Coberto em RN-PRP-04. A tela de mudança de Status para Cancelado, quando há ambiguidade de Stage, exige a escolha antes de confirmar.

## RF-PRP-03 — Criação manual de Proposta

**Como** operador, **eu quero** criar uma Proposta manualmente para um Cliente **para** cobrir casos que não passaram pelo board de Leads (ex.: negociação iniciada direto por telefone, sem lead registrada).

**Comportamento esperado:** destino de topo (RF-PRP-06) oferece "Nova proposta" — o operador informa os dados do Cliente (o CPF busca entre os Clientes **associados à Loja do operador**; se não achar, captura inline — mesmo padrão de RN-SIM-05, Simulações; um CPF que só outras Lojas conhecem não revela nenhum dado, RN-CLI-04) e, **opcionalmente**, associa a uma Lead existente sem Proposta ainda. Sem Lead associada, a Proposta segue seu ciclo de Status normalmente, só sem o efeito de mover nenhum card no board (RN-PRP-03 não se aplica). Ao escolher uma Oferta cuja Operação é Portabilidade ou Portabilidade com Refinanciamento, o campo Banco de origem passa a obrigatório (RN-PRP-14). O formulário tem ainda o campo opcional **Origem de Venda** (`Combobox` criável); a Oferta pode ficar vazia (RN-PRP-07); o **Operador** é quem cria.

## RF-PRP-04 — Duplicar Proposta

**Como** operador, **eu quero** duplicar uma Proposta existente **para** reaproveitar a maior parte dos dados numa negociação nova.

**Comportamento esperado:** a partir de uma Proposta existente, "Duplicar" copia os campos preenchíveis (Cliente, Origem de Venda, datas e a **Oferta atual**, com um **novo snapshot** — se a Oferta não existir mais, o campo fica vazio para o operador escolher), **sem os Atores** (cada negociação tem os seus papéis) para uma Proposta nova, **sem Lead** por padrão (a cópia não herda a Lead da original, mesmo que ela tivesse uma — evita duas Propostas competindo pela mesma Lead, RN-PRP-01). Status sempre reinicia em Novo; linha do tempo começa vazia.

## RF-PRP-05 — Importação de Propostas

**Como** gestor de Loja, **eu quero** importar Propostas em lote **para** trazer uma base existente para o sistema.

**Comportamento esperado:** herdado do mesmo padrão de importação já usado nas Listas do Marketing (Prospecção, RF-PRS-01) — envio de arquivo, mapeamento de colunas, validação linha a linha, resumo final. Cada linha traz os dados do Cliente (obrigatório) — Lead é opcional, associada só se a coluna de referência for preenchida e apontar para uma Lead sem Proposta. Linhas cuja Oferta tem Operação Portabilidade ou Portabilidade com Refinanciamento **exigem** a coluna de Banco de origem preenchida (RN-PRP-14) — sem ela, a linha é rejeitada, com relatório de erro. A coluna Origem de Venda é opcional, assim como as colunas Valor, Parcela e Prazo do contrato; o Operador das linhas importadas é o gestor que importa.

## RF-PRP-06 — Lista de Propostas (destino de topo)

**Como** operador ou gestor, **eu quero** uma tela dedicada listando as Propostas ao meu alcance **para** localizar e trabalhar sem precisar navegar pela Lead toda vez.

**Comportamento esperado:** lista de Cards de Proposta (colapsados por padrão — Fase 2 detalha a estrutura visual completa, com base na especificação do Card), cada card expansível. Filtros por Status, Loja/Time (conforme escopo), período, Ator.

## RF-PRP-07 — Gestão de Documentos da Proposta

Herdado do mesmo padrão de upload já usado no produto (ex.: importações) — filepicker com drag-and-drop, tabela com Descrição, Data de envio, Operador, ações de Baixar e Excluir. Distintos de Documentos do Cliente (RN-PRP-11).

## RF-PRP-08 — sem uso

Propostas não se excluem (RN-PRP-08); a numeração fica sem uso.

---

# 5. Regras de negócio (consolidado)

- **RN-PRP-01** — Uma Lead tem no máximo uma Proposta, qualquer que seja a origem de criação.
- **RN-PRP-02** — Nascimento automático continua existindo, sem alteração; deixou de ser o único caminho.
- **RN-PRP-03** — Status de tipo Pago/Cancelado move a Lead automaticamente (RN-FNL-18); Lead cancelada por Proposta não reabre.
- **RN-PRP-04** — Cancelar com múltiplos Stages do tipo exige escolha de Motivo (= Stage); nunca se estende a Pago.
- **RN-PRP-05** — A Oferta e o multiplicador são congelados ao associar a Oferta; no Pago, gravam-se o Valor de meta, a comissão e a atribuição (Operador, Time, Cargo).
- **RN-PRP-06** — Proposta não consome Créditos.
- **RN-PRP-07** — A Proposta guarda uma única Oferta, substituível em Novo e Negociação; pode ficar vazia em Novo.
- **RN-PRP-08** — Proposta não se exclui, em nenhum Status; criada por engano, cancela-se.
- **RN-PRP-09** — Atores (Consultor, Averbador, Emissor de Boleto, Digitador) complementam o Operador.
- **RN-PRP-10** — Comentário na mudança de Status é sempre opcional.
- **RN-PRP-11** — Documentos da Proposta são exclusivos da negociação, distintos de Documentos do Cliente.
- **RN-PRP-12** — Linha do tempo gerada automaticamente, não editável.
- **RN-PRP-13** — Seis Tags computadas (identificação da Oferta, Operação, Produto, Banco, Origem de Venda, e Banco de origem quando aplicável).
- **RN-PRP-14** — Banco de origem obrigatório em Portabilidade e Portabilidade com Refinanciamento.
- **RN-PRP-18** — Ao chegar a Pago, a Proposta gera um Contrato no histórico do Cliente.
- **RN-PRP-15** — O que se edita depende do Tipo do Status: Pago e Cancelado só Documentos, Datas e Comentário.
- **RN-PRP-16** — O Operador recebe o crédito da venda; os Atores não contam.
- **RN-PRP-17** — `self` = Propostas em que o usuário é Operador ou Ator.

---

# 6. Estados e transições

**Proposta:** segue o **StatusType** do módulo de Status de Proposta — Novo → Negociação → (Pago | Cancelado), com os status intermediários livres, definidos pelo tenant dentro de cada Tipo.

Transições Pago e Cancelado são **terminais e definitivas** e disparam RN-PRP-03. Não há transição de volta a partir delas — confirmado, sem reabertura de Proposta em nenhuma hipótese nesta versão. Em Pago e Cancelado só Documentos, Datas e Comentário seguem editáveis (RN-PRP-15).

---

# 7. Limites do módulo

**Não** inclui: o disparo automático em si (evento do módulo de Funis de Vendas — este só reage), a configuração das Tabelas Multiplicadoras (módulo próprio), a gestão de Membros que podem ser Atores (módulo de Membros), nem o cálculo/consumo do valor de meta em Metas (módulo de Metas — este só grava o snapshot e a atribuição).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Funis de Vendas** (Admin) — dispara a criação automática (RN-FNL-17) e recebe o evento de sincronização (RN-FNL-18).
- **Status de Proposta** (Admin) — o StatusType e os status configurados pelo tenant.
- **Ofertas, Produtos** (Admin) — valor base (RN-OFR-07) e Operação (campo da Oferta).
- **API do Banco Central** — busca do Banco de origem (RN-PRP-14), mesmo padrão já usado em Regras e Ofertas.
- **Tabelas Multiplicadoras** (CRM) — o multiplicador vigente por Oferta, no momento do Pago.
- **Simulações** (CRM) — a simulação de referência e as Ofertas em consideração, se houver.
- **Clientes** (CRM) — Documentos do Cliente, distintos dos Documentos da Proposta.
- **Membros** (CRM) — quem pode ser atribuído como Ator.
- **Leads** (CRM) — a Lead de origem, quando há, e a Origem de Venda copiada dela na criação.
- **Origem de Venda** (Admin) — a lista do `Combobox` do campo Origem de Venda (RN-PRP-13).
- **Usuários** (Admin) — a Proposta acompanha a Lead na redistribuição por inativação, e os Atores permanecem (RN-USR-05, RN-PRP-09).

**Expõe para:**
- **Clientes** (CRM) e **Marketing** (Carteira Ativa) — o Contrato gerado no Pago (RN-PRP-18), com Valor, Parcela, Prazo e Data de pagamento ao cliente.
- **Metas, Rankings e Dashboard** (CRM) — o Valor de meta (valor base × multiplicador) e a atribuição (Operador, Time, Cargo) gravados no Pago.
- **Relatórios** (CRM) — produção e motivos de cancelamento. Também **Relatórios (Admin)** — a comissão gravada no Pago, base de Comissões a pagar.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Propostas v0.14 — módulo da aplicação CRM.*
