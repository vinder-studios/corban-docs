# Corban Admin · Funis de Vendas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Vendas
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.17
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.17 | **Cada Loja escolhe quais Origens recebe em cada Segmento** (Lojas v0.13, RN-LOJ-17). A RN-FNL-07 ganha a observação: as quatro linhas de preço são também as quatro Origens que a Loja liga ou desliga, e o preço de uma linha só é cobrado das Lojas que aceitam aquela Origem. Nenhuma regra do Funil muda |
| 0.1 | Versão inicial. |
| 0.2 | Fecha os itens abertos. Funil ganha **nome próprio** (sugerido a partir do Segmento). **Stage ganha o campo Tipo**, reaproveitando o enum de Status de Proposta — que passa a ter quatro valores, com a inclusão de **Novo**. Funil **não pode ser excluído**, apenas inativado, com regra de drenagem das leads em curso. Segmento do funil é **imutável** após a criação. Fluxo sem stage permitido fica cadastrado, sem exibição. |
| 0.3 | A tela de configuração passa de 3 para **5 abas**: a aba Board se divide em **Stages, Fluxos e Tags**, cada uma própria. Total: Informações gerais, Stages, Fluxos, Tags, Preço das leads. |
| 0.4 | Fecha o item aberto 1: **o Funil precisa ter ao menos um stage de cada Tipo** (Novo, Andamento, Pago, Cancelado) — RN-FNL-15. Bloqueia criação e remoção que violem a cobertura. |
| 0.5 | **O Funil deixa de ser criado manualmente — nasce automaticamente junto com o Segmento** (revoga RN-FNL-02), já populado com o conjunto de stages sugerido, em novo status **"Aguardando configuração"**. Removida a Aba/fluxo de criação de funil (RF-FNL-01 reescrito). RN-FNL-09 revisada: **Funil que já foi ativado nunca é excluído** — a exceção (Funil nunca ativado) é excluído apenas em cascata, disparada pelo módulo de Segmentos, nunca por ação direta aqui. Item aberto 1 fechado (Status de Proposta já atualizado). |
| 0.6 | Adiciona a entidade **Histórico de Preço de Lead** (RN-FNL-16) — registro append-only de custo/preço ao longo do tempo, insumo do bloco de gráfico no Dashboard do Admin. |
| 0.7 | **StageType deixa de ser compartilhado com Status de Proposta.** Passa a ser enum próprio, com **cinco** valores (ganha "Formalização"); "Andamento" renomeado para "Negociação". Adiciona os dois eventos automáticos que ligam Lead e Proposta: entrar no Stage de tipo Formalização cria a Proposta (RN-FNL-17); a Proposta atingir Pago/Cancelado move a Lead para o Stage correspondente, com desambiguação por Motivo quando há mais de um Stage do mesmo tipo terminal (RN-FNL-18). |
| 0.8 | Correção: a v0.7 deixou referências antigas a "Andamento" e "quatro tipos" em cinco pontos do documento — propagação da divisão StageType/StatusType estava incompleta. Corrigido por completo nesta versão. |
| 0.9 | Adiciona a **6ª aba — Layout de Lead**, remetendo ao módulo próprio (Card e Sheet, Server-Driven UI configurável pelo tenant, catálogo fechado de blocos). A tela de Configuração do Funil passa de 5 para 6 abas. |
| 0.10 | **Revoga parte de RN-FNL-04.** O Corban passa a poder **disparar** a execução de um Fluxo já registrado — via o botão "Ações" do Card da Lead (módulo Layout de Lead) —, não só reagir a eventos da Huggy. O que permanece de RN-FNL-04: o Corban continua sem modelar ou validar a lógica interna do que o Fluxo faz — só pede pra Huggy executar, e Huggy decide o resultado. |
| 0.11 | **Substitui Fluxo por Ação — reformulação de fundo.** Fluxo (genérico, registrado da Huggy) deixa de existir. Em seu lugar, cinco tipos de Ação fixos do sistema: Mover lead (dinâmica, sem configuração), Desqualificar, Finalizar, Reabrir e Formalizar (configuráveis, múltiplas instâncias, cada uma com Stage de destino, mensagem e Huggy-id opcionais). Formalizar cria a Proposta automaticamente ao mover a Lead para Formalização — absorve o que era RN-FNL-17. RN-FNL-18 (sincronização Proposta→Lead) permanece intacta, sem mudança. |
| 0.12 | **Preço de lead passa de duas para quatro linhas fixas, por Origem da lead:** Campanha, Orgânica, Prospecção e Carteira Ativa (RN-FNL-07). Interno vira Carteira Ativa e Externo vira Campanha; Orgânica e Prospecção são novas, esta última vinda do módulo Prospecção (Marketing). Recontato e Manual não têm linha porque não cobram (RN-LED-13, módulo de Leads). RN-FNL-06 passa a distinguir três "origens" (campanha, Origem de Venda, Origem da lead); RN-FNL-16 registra a Origem da lead no histórico. Registra o item aberto: linha de preço sem valor. |
| 0.13 | **Revisão completa do módulo.** **Um só stage de Novo, Formalização e Pago** (RN-FNL-15), porque o sistema precisa de um destino único para onde a lead nasce, para a Ação Formalizar e para a Proposta paga; Negociação e Cancelado continuam admitindo vários. **"Mover lead" passa a oferecer só Stages tipo Negociação à frente** (§3.3, RN-FNL-19), de modo que ninguém chegue a Formalização, Pago ou Cancelado sem passar pela Ação certa (Leads v0.8, RN-LED-11). **Preço de lead nasce em 0** nas quatro linhas, o que fecha o item aberto "linha sem valor" (RN-FNL-07, RN-FNL-16). **RN-FNL-18 passa a existir no corpo**: constava só do consolidado e do histórico, e era citada em Propostas, Leads e neste módulo. RN-FNL-13 corrigida (dizia que o enum era compartilhado com Status de Proposta e listava 4 tipos, sem Formalização); "quatro" → "cinco" em RN-FNL-02 e RN-FNL-15; RN-FNL-17 aponta o status em que a Proposta nasce (RN-STP-08); §8 cita a Ação Formalizar, e não mais "entrar no Stage". Consolidado ganha a RN-FNL-16. Corrige esta tabela (fora de ordem e com a linha da v0.5 sem o fechamento). Novo item aberto: Formalizar sem status Novo em Status de Proposta. |
| 0.14 | **Fecha o item aberto "Formalizar sem status Novo"**: Status de Proposta passa a nascer com um status padrão de cada tipo (Status de Proposta v0.7, RN-STP-10), então a Ação Formalizar sempre encontra um status Novo ativo. §9 sem pendências. Decisão do usuário. |
| 0.15 | **Reabrir passa a ter como destino só um Stage tipo Negociação** (RN-FNL-19), e não mais "qualquer não-terminal": levá-la a Formalização deixaria a Lead num Stage de Formalização sem Proposta (Reabrir não cria uma) e sem acesso do operador (RN-LED-10). A Lead reaberta volta ao pipeline em curso. Mesmo defeito que a v0.13 corrigiu em "Mover lead", achado na revisão de Layout de Lead. Decisão do usuário. |
| 0.16 | **Reabrir passa a valer só para Lead que nunca teve Proposta** (decisão do usuário, levantamento do CRM): a tabela dizia "volta ao pipeline, sem Proposta", mas nada impedia reabrir uma Lead cancelada por uma Proposta, o que levaria a uma segunda Proposta e violaria o 1:1 (Propostas, RN-PRP-01). |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-FNL) e regras de negócio (RN-FNL) próprios.

> **Este é o módulo mais amplo do Admin até aqui.** Reúne quatro domínios sob uma mesma tela, organizados em abas: identificação do funil, configuração do board (colunas, ações e tags), layout de Lead e precificação das leads entregues nele.

---

# 1. Objetivo do módulo

Configurar o funil de vendas de cada Segmento: as etapas pelas quais a lead avança, as automações que a movimentam, as tags que classificam a origem de campanha, e quanto a Loja paga por cada lead entregue ali.

---

# 2. Atores

- **Gestor comercial** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que o funil é tenant-wide.
- **Operador** — consome o funil no CRM (board kanban), sem configurá-lo aqui.

---

# 3. Entidades e conceitos

## 3.1. Funil de Vendas

**RN-FNL-01 — Um funil por Segmento, tenant-wide.** Cada Funil pertence a exatamente um Segmento, e cada Segmento tem exatamente um Funil — relação 1:1 obrigatória (RN-SEG-05, módulo de Segmentos). A configuração é **única para todas as Lojas** — não há variação por Loja. Uma Loja associa-se ao Funil (não ao Segmento), e é isso que lhe dá acesso ao pipeline daquele público.

**RN-FNL-02 — O Funil nasce automaticamente, junto com o Segmento.** *(Revoga a versão anterior desta regra, que previa criação manual e separada.)* Não existe fluxo de "criar funil" neste módulo — o Funil já existe desde o instante em que o Segmento é criado, disparado de lá (RN-SEG-05). Ele nasce **já populado** com o conjunto de stages sugerido (RN-FNL-03), cobrindo os cinco Tipos exigidos por RN-FNL-15, em status **"Aguardando configuração"**. O que este módulo oferece é a **configuração** de um Funil que já existe, nunca a criação de um do zero.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Nome próprio do funil, **sugerido automaticamente a partir do nome do Segmento** no momento da criação automática — o gestor pode alterá-lo depois. |
| Segmento | Sim | Qual Segmento este funil atende. Único, e **imutável** (RN-FNL-12). |
| Descrição | Não | Texto curto sobre o funil. |
| Status | Sim | **Aguardando configuração** / Ativo / Drenando / Inativo (ver §6). |

**RN-FNL-12 — O Segmento é imutável.** Não pode ser alterado depois de criado. Trocá-lo moveria todo o pipeline de um público para outro — leads de INSS passariam a ser de CLT sem que ninguém as tivesse movido.

## 3.2. Stage (coluna do board)

Etapa pela qual a lead avança. **Livremente definidas pelo gestor** — nome e ordem.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como a coluna aparece no board. Quando o Tipo é **Cancelado** e há mais de um Stage desse tipo, o Nome também funciona como o **Motivo** apresentado ao operador (RN-FNL-18) — não há campo separado. |
| Tipo | Sim | Enum próprio **StageType**: **Novo**, **Negociação**, **Formalização**, **Pago** ou **Cancelado** — ver RN-FNL-13. |
| Ordem | Sim | Posição da coluna no board. |

**RN-FNL-03 — O sistema sugere uma estrutura padrão, não a impõe.** Ao criar um funil, o sistema oferece um conjunto inicial de stages — **Novo, Negociação, Formalização, Venda, Desqualificado** — que o gestor pode aceitar, editar por completo ou descartar. Cobre os cinco Tipos, um a um (Venda → tipo Pago; Desqualificado → tipo Cancelado). É sugestão, não estrutura obrigatória: **não existem stages de sistema**, e qualquer um pode ser removido ou renomeado.

*Isso revoga a Especificação v1.2, que previa Entrada, Ganho e Perdido como etapas fixas em existência.*

**RN-FNL-15 — O funil precisa cobrir os cinco Tipos; Novo, Formalização e Pago admitem um só stage.** Não basta ter um stage terminal (o mínimo que evitaria o funil nunca drenar, RN-FNL-14) — é exigida cobertura completa: **Novo, Negociação, Formalização, Pago e Cancelado**, e a quantidade varia por tipo:

| Tipo | Quantidade | Por quê |
|---|---|---|
| **Novo** | **Exatamente um** | É onde toda lead nasce (RN-FNL-13) |
| **Formalização** | **Exatamente um** | Destino único da Ação Formalizar (RN-FNL-19) |
| **Pago** | **Exatamente um** | Destino único quando a Proposta é paga (RN-FNL-18) |
| **Negociação** | Um ou mais | O pipeline em curso pode ter quantas etapas o tenant quiser |
| **Cancelado** | Um ou mais | Cada stage pode ser um motivo (RN-FNL-18) |

Três pontos de aplicação:

- **Ao criar o funil**, não é possível salvar sem que a configuração de stages cubra os cinco tipos. O conjunto sugerido (RN-FNL-03) já satisfaz isso por construção.
- **Ao remover um stage**, se ele for o único do seu tipo, a remoção é bloqueada — mesmo que o stage esteja vazio. Essa checagem acontece **antes** da regra de destino para leads (RN-FNL-10): um stage que é o único do seu tipo não pode ser removido, então a pergunta "para onde vão as leads" nem chega a ser feita.
- **Ao criar um stage — ou mudar o Tipo de um stage existente — para Novo, Formalização ou Pago**, quando o funil já tem um stage desse tipo, a operação é bloqueada. Mudar o Tipo do único stage de um tipo para outro também é bloqueado, porque deixaria o tipo original sem cobertura. *(Decisão do usuário: um só stage de Novo, Formalização e Pago. A regra sobre mudar o Tipo é consequência.)*

**RN-FNL-13 — O Tipo do stage é o que o sistema entende; o nome é livre.** Sem stages de sistema, o produto precisa de outra forma de saber se uma lead está entrando, em curso, em formalização, convertida ou perdida — não dá para interpretar o nome que o tenant deu à coluna. Essa função passa a ser do **Tipo** (enum próprio, **StageType**):

| Tipo | Significado no board |
|---|---|
| **Novo** | Lead recém-chegada, ainda não trabalhada. **É onde toda lead nasce** |
| **Negociação** | Lead em curso, ainda sem proposta |
| **Formalização** | Lead com proposta em andamento — a Proposta é criada quando a lead vem para cá, pela Ação Formalizar (RN-FNL-17) |
| **Pago** | Lead convertida — virou produção |
| **Cancelado** | Lead perdida |

Um tenant pode ter três colunas de tipo Negociação com nomes próprios ("Primeiro contato", "Aguardando documento", "Reanálise") — para o sistema, as três significam "em curso, sem proposta ainda".

**O enum é próprio do Funil.** Status de Proposta tem o seu, **StatusType**, com quatro valores (sem Formalização), cadastrados pelo tenant. As duas entidades não compartilham enum; ligam-se pelos eventos de RN-FNL-17 e RN-FNL-18.

## 3.3. Ação

**Como a Lead se move entre Stages — sempre via Ação, nunca por drag-and-drop (RN-LED-11, módulo de Leads).** Cinco tipos, fixos do sistema — o tenant não cria tipos novos, só configura instâncias dos quatro configuráveis.

### Mover lead — dinâmica, sem configuração

Não tem cadastro: a cada Lead em Stage tipo **Novo ou Negociação**, o board calcula quais Stages **tipo Negociação** estão **à frente** do Stage atual (pela ordem configurada, RF-FNL-03) e oferece como destino. **Nunca move para trás**, e **nunca oferece** Formalização (só se chega lá pela Ação Formalizar, que cria a Proposta), Pago (só pela Proposta, RN-FNL-18) nem Cancelado (papel de Desqualificar e Finalizar). Se não houver Stage de Negociação à frente, a Ação não é oferecida. Sem mensagem, sem Huggy-id — é só reposicionamento.

### Desqualificar, Finalizar, Reabrir, Formalizar — configuráveis, múltiplas instâncias

Cada tipo pode ter **várias instâncias** cadastradas pelo tenant — por exemplo, dois "Desqualificar" diferentes ("Sem margem" → um Stage Cancelado; "Documentação inválida" → outro Stage Cancelado), cada um com sua própria mensagem e Huggy-id.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como a Ação aparece no Sheet de execução (módulo Layout de Lead). |
| Mensagem | Não | Enviada ao cliente ao executar — texto livre. |
| Huggy-id | Não | Referência a um Flow da Huggy, disparado como efeito colateral opcional (RN-FNL-04). |
| Stage de destino | Sim, com regra por tipo | Ver tabela abaixo — varia conforme o tipo de Ação. |

**RN-FNL-19 — Destino varia por tipo de Ação:**

| Tipo | Destino permitido | Semântica |
|---|---|---|
| **Mover lead** | Um Stage tipo Negociação à frente do atual (dinâmico) | Reposicionamento no pipeline, sem sair dele |
| **Desqualificar** | Um Stage tipo Cancelado | Cliente não atende a algum critério (ex.: sem margem) |
| **Finalizar** | Um Stage tipo Cancelado | Cliente sem interesse, ou parou de responder |
| **Reabrir** | Um Stage tipo Negociação | Só disponível quando a Lead está num Stage tipo Cancelado **e nunca teve Proposta** (foi descartada antes da Formalização) — volta ao pipeline em curso. Uma Lead cancelada por uma Proposta **não reabre**: a Proposta é terminal e é 1:1 com a Lead (Propostas, RN-PRP-01) |
| **Formalizar** | O Stage tipo Formalização | Move a Lead **e** cria a Proposta automaticamente (RN-FNL-17) |

**Desqualificar e Finalizar são semanticamente distintos, mesmo levando ambos a um Stage Cancelado** — a diferença é o motivo de negócio (critério não atendido vs. desinteresse/silêncio do cliente), refletida na Mensagem configurada para cada instância.

**RN-FNL-04 — O Huggy-id é efeito colateral opcional, nunca bloqueante.** Executar uma Ação sempre move a Lead no Corban, independentemente da Huggy. Se a Ação tiver um Huggy-id configurado, o Corban também solicita a execução daquele Flow — mas o resultado dessa solicitação não afeta se a Ação foi bem-sucedida no Corban.

**RN-FNL-17 — Formalizar cria a Proposta automaticamente.** *(Antes uma regra de gatilho por Stage; agora incorporada à própria definição da Ação Formalizar — como toda movimentação é via Ação, não há mais um "entrar em Formalização" que não seja através desta Ação específica.)* A Proposta nasce no **status ativo mais antigo do tipo Novo** (StatusType, RN-STP-08, módulo de Status de Proposta) — que sempre existe, porque o tenant nasce com um status padrão de cada tipo (RN-STP-10).

**RN-FNL-18 — A Proposta atingir Pago ou Cancelado move a Lead.** *(Reescrita no corpo na v0.13: a regra constava só do consolidado e do histórico.)* Quando o Status da Proposta passa a um tipo terminal, a Lead vinculada é movida automaticamente para o Stage do mesmo Tipo, sem Ação do operador (RN-PRP-03, módulo de Propostas). **Pago** → o Stage tipo Pago, que é único (RN-FNL-15), sempre direto. **Cancelado** → se o Funil tem **um** Stage tipo Cancelado, direto; se tem **mais de um**, o operador escolhe, e **a escolha é o Motivo**: o Nome do Stage faz esse papel (§3.2, RN-PRP-04). A regra só vale para Lead com Proposta; uma Lead sem Proposta é descartada pela Ação Desqualificar ou Finalizar (RN-LED-09).

**RN-FNL-20 — Stages tipo Pago não têm Ações associadas.** Nenhuma das cinco Ações pode ter um Stage tipo Pago como destino, e nenhuma Ação é oferecida a uma Lead que já está num Stage desse tipo — coerente com RN-FNL-18: só se chega a Pago pela Proposta, nunca por Ação direta na Lead.

> **Nota de evolução — trazer a automação para o sistema.** A dependência do Huggy-id como integração externa é decisão desta fase. Evolução prevista: o Corban executar sua própria lógica de automação, sem depender de um Flow externo configurado na Huggy.

## 3.4. Tag

**Classificador de card**, usado para marcar leads com informação de campanha. Também vem da Huggy.

| Campo | Obrigatório | Notas |
|---|---|---|
| Id | Sim | Identificador da tag na Huggy. |
| Nome | Sim | Como a tag aparece no card. |
| Categoria | Sim | **Audiência**, **Origem** ou **Departamento** — enum fixo do sistema. |

**RN-FNL-05 — Categoria é fixa; as tags dentro dela são livres.** As três categorias são do sistema e não podem ser criadas nem editadas pelo tenant. Dentro de cada uma, o gestor cadastra quantas tags quiser.

**RN-FNL-06 — "Origem" aqui é origem de campanha, não Origem de Venda nem Origem da lead.** São três coisas distintas, com nomes parecidos:

| Conceito | Onde vive | O que é |
|---|---|---|
| **Tag de categoria Origem** | Aqui, no Funil | De qual campanha de marketing a lead veio (ex.: "Campanha Black Friday", "Remarketing base") |
| **Origem de Venda** | Cadastro de Lead, no CRM | Por onde a venda chegou (ex.: "Indicação", "Porta a porta") |
| **Origem da lead** | Campo da Lead, definido pelo sistema (módulo de Leads, RN-LED-12) | Como a lead nasceu — Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa ou Manual; é o que define o preço (RN-FNL-07) |

Vale registrar porque a confusão é provável e o efeito seria modelar uma como a outra.

## 3.5. Preço de lead

**Quanto a Loja paga por cada lead entregue neste funil**, conforme a Origem da lead (módulo de Leads, RN-LED-12).

**RN-FNL-07 — Quatro linhas fixas, por Origem da lead.** A tabela tem exatamente quatro registros, não criáveis nem excluíveis — o gestor apenas preenche os valores de cada:

| Origem da lead | O que é |
|---|---|
| **Campanha** | Lead gerada por campanha paga externa (Facebook Ads, TikTok, Google Ads, etc.) |
| **Orgânica** | Contato espontâneo **novo** na Huggy — sem campanha e de pessoa ainda desconhecida do tenant |
| **Prospecção** | Lead gerada pela importação de uma base externa (módulo Prospecção, Marketing) |
| **Carteira Ativa** | Lead gerada pela mineração da base interna de clientes (módulo Carteira Ativa, Marketing) |

| Campo | Notas |
|---|---|
| Custo | O que o tenant gastou para adquirir a lead — por exemplo, a verba de anúncio (Campanha) ou o custo da base comprada (Prospecção). |
| Preço | O que a Loja paga ao tenant para receber a lead. |

**Recontato e Manual não aparecem na tabela:** não têm linha de preço e não cobram (RN-LED-13, módulo de Leads).

**As quatro linhas são também os tipos de lead que a Loja escolhe receber.** Em cada Segmento, a Loja liga ou desliga cada Origem (Lojas, RN-LOJ-17), e o preço de uma linha só é cobrado das Lojas que aceitam aquela Origem.

**Todas as linhas nascem com custo 0 e preço 0.** Zero é um valor válido (nada é gasto, nada é cobrado) e evita a linha vazia: o gestor edita quando quiser. O Funil nasce em "Aguardando configuração" (RN-FNL-02) e só passa a receber leads quando é ativado, então os zeros iniciais não geram cobrança indevida. *(Decisão do usuário, na revisão.)*

> **Migração.** O histórico de preço existente (Interno/Externo) é convertido: Interno → Carteira Ativa e Externo → Campanha. Orgânica e Prospecção começam com custo 0 e preço 0.

**RN-FNL-08 — A margem é a diferença, e pode ser negativa.** Preço menos custo é o que o tenant ganha por lead. Nada impede configurar preço abaixo do custo — é decisão comercial legítima (subsidiar a loja para ganhar volume), então o sistema não bloqueia, mas sinaliza (ver Microcopy).

> **Nota de evolução — integração com ferramentas de anúncio.** Nesta fase o custo é **informado manualmente** pelo gestor. Está prevista a integração com plataformas de anúncio (Facebook Ads, Google Ads, TikTok Ads) para que o custo real por lead seja importado automaticamente, tornando a precificação dinâmica em vez de estática. A ausência dessa integração é o que torna a versão atual viável a curto prazo.

---

# 4. Requisitos funcionais

## RF-FNL-01 — Listagem e configuração de funis

**Como** gestor comercial, **eu quero** ver os funis existentes e configurar cada um **para** que as leads de cada Segmento tenham um pipeline funcional.

**Comportamento esperado:** a listagem mostra **um funil por Segmento — sempre, sem exceção**, já que todo Segmento tem o seu (RN-FNL-01). Mostra o Segmento, o status do Funil e a quantidade de Lojas que o utilizam. **Não há ação de "criar funil"** — a lista é só de consulta e navegação para a configuração de cada um; a criação acontece automaticamente, disparada pelo cadastro de um Segmento novo (RN-FNL-02).

**Regras específicas:**

**RN-FNL-09 — Funil que já foi ativado nunca é excluído.** *(Revisão: a versão anterior dizia apenas "não há exclusão de funil" — agora existe uma exceção pontual.)* Um Funil que já passou por Ativo alguma vez (está Ativo, Drenando, ou Inativo após ter sido usado) não tem exclusão, em nenhuma circunstância — a descontinuação é só por inativação, com drenagem (RN-FNL-14). Um Funil que **nunca saiu de "Aguardando configuração"** pode ser excluído, mas **não por ação direta neste módulo** — só em cascata, quando o Segmento pai é excluído (RN-SEG-04, módulo de Segmentos). Este módulo não oferece um botão de excluir Funil isoladamente, em hipótese alguma.

## RF-FNL-02 — Aba Informações gerais

Nome (sugerido a partir do Segmento na criação automática, editável depois), Segmento (imutável, RN-FNL-12), descrição e status. O controle de status aqui é o mesmo Switch usado na Lista — permite inativar/reativar o Funil **diretamente**, independente de qualquer ação sobre o Segmento. A cascata vinda do Segmento (RN-SEG-06, RN-SEG-07) é um segundo caminho para o mesmo campo, não um campo separado.

## RF-FNL-03 — Aba Stages

Criar, renomear, reordenar e remover colunas. Ao criar o funil, o conjunto sugerido já vem preenchido (RN-FNL-03). Salvar e remover são validados contra a cobertura completa de tipos (RN-FNL-15) — a interface precisa indicar, por tipo, se o tipo está coberto; impedir a remoção do único stage de qualquer tipo antes mesmo de perguntar sobre destino de leads; e impedir criar (ou mudar o tipo de um stage para) um segundo stage de Novo, Formalização ou Pago (RN-FNL-15).

## RF-FNL-04 — Aba Ações

Cadastrar as instâncias dos quatro tipos configuráveis (Desqualificar, Finalizar, Reabrir, Formalizar) — Nome, Mensagem, Huggy-id, Stage de destino (RN-FNL-19). "Mover lead" não aparece aqui — é dinâmica, sem cadastro (§3.3).

## RF-FNL-05 — Aba Tags

Cadastrar as tags da Huggy: Id, nome e categoria (Audiência / Origem / Departamento).

**RN-FNL-10 — Remover um stage exige destino para as leads.** Se o stage contém leads, o gestor indica obrigatoriamente para qual stage elas vão, em lote. Mesmo padrão da RN022 da Especificação v1.2.

**RN-FNL-21 — Remover um stage que é destino de alguma Ação exige reatribuir a Ação.** Se o stage removido é o destino configurado de uma ou mais instâncias de Desqualificar/Finalizar/Reabrir/Formalizar, o gestor precisa apontar essas instâncias para outro Stage do mesmo Tipo antes de concluir a remoção — mesmo espírito de RN-FNL-10, aplicado a Ações em vez de leads.

## RF-FNL-06 — Aba Layout de Lead

Configuração do Card (board) e do Sheet de Detalhe da Lead — módulo próprio, **Corban Admin · Layout de Lead (Card e Sheet)**. Não redefinido aqui; esta aba só hospeda aquela configuração, por Funil (RN-LAY-02).

## RF-FNL-07 — Aba Preço das leads

Tabela de quatro linhas (Campanha, Orgânica, Prospecção e Carteira Ativa), com custo e preço editáveis em cada. As linhas já nascem com custo e preço 0 (RN-FNL-07).

**RN-FNL-16 — Cada mudança de valor gera um registro de histórico.** Toda vez que o gestor salva a aba Preço das leads com um custo ou preço **diferente** do valor anterior (para aquele Funil e Origem da lead), o sistema grava um registro em **Histórico de Preço de Lead** — entidade **append-only**, nunca editada nem excluída, com: Funil, Origem da lead, Custo, Preço, Data/hora. Os registros anteriores a esta versão, que guardam Interno/Externo, são convertidos (Interno → Carteira Ativa, Externo → Campanha).

Salvar sem alterar nenhum valor **não gera registro novo** — evita pontos redundantes num eventual gráfico de série temporal. Os valores iniciais (0 e 0) também **não** geram registro: o primeiro registro nasce quando o gestor salva um valor diferente do anterior. *(Consequência da decisão de nascer em zero.)* A aba Preço das leads em si continua mostrando só o valor **atual**; o histórico é consumido em outro lugar (Dashboard do Admin), não há tela de consulta ao histórico dentro deste módulo.

---

# 5. Regras de negócio (consolidado)

- **RN-FNL-01** — Um funil por Segmento, tenant-wide; Lojas associam-se ao Funil.
- **RN-FNL-02** — O Funil nasce automaticamente com o Segmento, já populado, em "Aguardando configuração".
- **RN-FNL-03** — O sistema sugere stages padrão, não os impõe; não há stage de sistema.
- **RN-FNL-04** — Huggy-id é efeito colateral opcional de uma Ação, nunca bloqueante.
- **RN-FNL-19** — Destino da Ação varia por tipo (Desqualificar/Finalizar → Cancelado; Reabrir → Negociação; Formalizar → Formalização). Reabrir só vale para Lead sem Proposta.
- **RN-FNL-20** — Stages tipo Pago não têm Ações associadas.
- **RN-FNL-21** — Remover stage que é destino de Ação exige reatribuir a Ação primeiro.
- **RN-FNL-05** — Categoria de tag é fixa; as tags dentro dela são livres.
- **RN-FNL-06** — Tag de categoria Origem (campanha) ≠ Origem de Venda ≠ Origem da lead.
- **RN-FNL-07** — Preço de lead tem quatro linhas fixas, por Origem da lead: Campanha, Orgânica, Prospecção e Carteira Ativa. Recontato e Manual não cobram. Todas as linhas nascem com custo e preço 0.
- **RN-FNL-08** — A margem pode ser negativa; o sistema sinaliza, não bloqueia.
- **RN-FNL-16** — Cada mudança de custo ou preço gera um registro append-only no Histórico de Preço de Lead; os valores iniciais (0 e 0) não geram.
- **RN-FNL-09** — Funil já ativado nunca é excluído; Funil nunca ativado só se exclui em cascata pelo Segmento pai (fora deste módulo).
- **RN-FNL-10** — Remover stage com leads exige destino.
- **RN-FNL-12** — O Segmento do funil é imutável após a criação.
- **RN-FNL-13** — O Tipo do stage (StageType, 5 valores) é o que o sistema entende; o nome é livre. Não compartilhado com Status de Proposta. A lead nasce no Stage Novo (único).
- **RN-FNL-17** — A Ação Formalizar cria a Proposta automaticamente ao mover a Lead para Formalização, no status ativo mais antigo do tipo Novo (RN-STP-08).
- **RN-FNL-18** — Proposta atingir Pago/Cancelado move a Lead ao Stage correspondente, com desambiguação por Nome (o Motivo) quando há mais de um Stage Cancelado; Pago é único.
- **RN-FNL-14** — Inativar drena: leads em curso seguem, e o funil some das Lojas quando esvaziar.
- **RN-FNL-15** — O funil cobre os cinco Tipos; um só stage de Novo, Formalização e Pago, e um ou mais de Negociação e Cancelado; a remoção do único stage de um tipo é bloqueada.

*RN-FNL-11: numeração sem uso; nenhum texto a cita.*

---

# 6. Estados e transições

**RN-FNL-14 — Funil não é excluído; é inativado, com drenagem.** Não existe exclusão de funil — a operação de descontinuação é a inativação, e ela acontece em duas fases:

| Fase | O que acontece |
|---|---|
| **Ao inativar** | O funil **para de receber leads novas**. As leads que já estão nele — de tipo **Novo**, **Negociação** ou **Formalização** (todos não-terminais) — continuam sendo trabalhadas normalmente, sem interrupção. O funil segue visível para as Lojas. |
| **Depois da drenagem** | Quando não restar nenhuma lead em nenhum Tipo não-terminal, o funil **deixa de ser exibido às Lojas**. |

A drenagem é o que evita o problema clássico de desligar um pipeline: leads em negociação não são abandonadas nem migradas à força — elas terminam o ciclo onde começaram.

**Funil:**
- **Aguardando configuração** — estado inicial, desde a criação automática. Nunca recebeu leads, nunca foi ativado. Já tem a estrutura sugerida de stages, mas não está disponível às Lojas.
- **Ativo** — recebe leads novas e é exibido às Lojas.
- **Inativo, drenando** — não recebe leads novas; as leads em curso seguem; ainda exibido às Lojas.
- **Inativo, drenado** — sem leads em curso; não exibido às Lojas.

Transições: aguardando configuração → ativo (livre — primeira ativação); ativo → inativo (livre, inicia a drenagem; pode ser direta, nesta tela, ou cascateada pela inativação do Segmento — RN-SEG-06); inativo → ativo (livre; pode ser direta, ou oferecida na reativação do Segmento — RN-SEG-07); inativo drenando → inativo drenado (automática, quando a última lead sai de qualquer Tipo não-terminal — Novo, Negociação ou Formalização).

**A diferença entre "Aguardando configuração" e "Inativo (drenado)" importa** — visualmente podem parecer o mesmo estado ("não ativo, sem leads"), mas a origem é diferente: um nunca foi usado; o outro já foi e voltou a ficar vazio. Manter os rótulos distintos evita que o gestor confunda um Funil recém-criado com um que já teve um ciclo de vida inteiro.

*Não há transição de "Aguardando configuração" (ou qualquer outro status) para "excluído" dentro deste módulo — a exclusão, quando possível, é sempre externa (RN-FNL-09).*

---

# 7. Limites do módulo

**Não** inclui: o board kanban operacional, onde as leads são trabalhadas (módulo de Leads, CRM); a associação de Lojas a Funis (módulo de Lojas); a execução das automações (Huggy); nem o cadastro de Segmentos.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Segmentos** — o evento de criação de um Segmento, que dispara a criação automática do Funil correspondente (RN-FNL-02); e o evento de inativação/reativação do Segmento, que pode cascatear para o Funil (RN-SEG-06, RN-SEG-07).
- **Huggy** — Huggy-id opcional das Ações configuráveis, e Id das Tags; o Flow associado (se houver) executa lá, sem bloquear a Ação no Corban (RN-FNL-04).

**Expõe para:**
- **Lojas** — a lista de Funis, para associação.
- **Leads (CRM)** — stages, ações e tags do funil da operação.
- **Créditos (CRM)** — custo e preço por lead entregue, para o débito automático.
- **Dashboard (Admin)** — o Histórico de Preço de Lead (RN-FNL-16), insumo do gráfico de custo × preço por Segmento.
- **Propostas (CRM)** — o evento da Ação Formalizar, que cria a Proposta (RN-FNL-17), e recebe de volta o evento de Status Pago/Cancelado (RN-FNL-18).
- **Layout de Lead (Admin)** — hospeda a 6ª aba (RF-FNL-06); a configuração em si vive no módulo próprio.

---

# 9. Itens abertos

Nenhuma pendência.

---

*Fim do documento. Funis de Vendas v0.17 — módulo da aplicação Admin, seção Vendas.*
