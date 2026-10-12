# Corban CRM · Leads

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.12
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.12 | **Acompanha Lojas v0.13: a Loja escolhe quais tipos de lead recebe em cada Segmento.** A elegibilidade de Loja (RN-LED-02) passa a exigir, além do Segmento "Ativo", que a **Origem da lead esteja ligada** na Loja (Lojas, RN-LOJ-17). O Recontato e o cadastro Manual não passam por essa escolha. Os dois itens abertos da v0.11 estão fechados: a Origem de Venda das Leads do Marketing (Origem de Venda v0.8, RN-ORI-06 e 07) e a apresentação do valor potencial e do resumo das Ofertas (Layout de Lead v0.7, RN-LAY-13). A Loja com "Pode receber lead sem saldo" tem todas as Origens sempre ligadas (RN-LOJ-18), o que mantém a garantia de destino de última instância |
| 0.11 | **Levantamento do Marketing, decisões do usuário.** (1) **Nova RN-LED-16: a Lead do Marketing leva o rastreio e o resumo das Ofertas**: referência imutável à Lista ou à Varredura que a gerou, valor potencial e resumo das Ofertas elegíveis com os requisitos não avaliados (Marketing, RN-MKT-01, 03 e 08). São campos do sistema, somente leitura. (2) A **Origem de Venda** da Lista ou da Varredura é **copiada** à Lead na injeção (antes, a Lead do Marketing nascia sem ela). (3) O **valor potencial** passa a ter duas fontes: digitado pelo operador (Lead manual) ou calculado pelo sistema (Lead do Marketing, somente leitura). (4) RF-LED-03 e a RN-LED-12 deixam de remeter a "importação de base externa" genérica e apontam para Lista de Prospecção e Varredura. (5) §8 e §9 atualizados. |
| 0.10 | **Levantamento do CRM, decisões do usuário.** (1) **Nova RN-LED-15: lead parada** = mais de 3 dias sem mudar de Stage (o Dashboard prometia um critério "já definido em Leads" que não existia). (2) **Reabrir só vale para Lead que nunca teve Proposta** (RN-LED-11). (3) O campo "convênio" vira **Promotora**, `Select` opcional que pré-preenche a Simulação. (4) A Origem de Venda aparece também no bloco Dados de Marketing do Sheet. (5) O RF-LED-01 deixa de mandar "arrastar o cartão" e de listar o que o cartão mostra (o dono é o Layout de Lead); "Fluxos" vira "Ações". (6) Recontato confirmado, incluindo Loja e operador **excluídos**. (7) A Lead nasce no Stage tipo Novo (único). (8) §8 lista Usuários e Membros (redistribuição) e Chat; sai o "a redigir". Reordena e fecha este histórico. |
| 0.9 | **Corrige a tabela de Ações (RN-LED-11)** para ficar coerente com a RN-LED-10: Desqualificar, Finalizar e Formalizar só aparecem para Lead em Stage tipo **Novo ou Negociação** (diziam "qualquer Stage não-terminal", o que incluía Formalização, onde o operador já não executa Ações). **Reabrir** passa a levar só a um Stage tipo **Negociação** (Funis v0.15, RN-FNL-19). Achados na revisão de Layout de Lead. |
| 0.8 | **"Mover lead" passa a oferecer só Stages tipo Negociação à frente** (RN-LED-11), nunca Formalização, Pago ou Cancelado, que têm Ação própria. Decorre da revisão de Funis v0.13 (RN-FNL-19). |
| 0.7 | **Introduz a Origem da lead**, campo do sistema que substitui o "canal" da RN042 e define a cobrança: Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa e Manual (RN-LED-12). Só as quatro primeiras com linha de preço cobram (RN-LED-13); o Recontato volta à Loja e ao operador anteriores, sem rodízio nem débito (RN-LED-14). Antes, o tipo que definia o preço (Interno/Externo) não tinha dono neste módulo. O cadastro manual ganha o campo opcional Origem de Venda (módulo de Origem de Venda), que o módulo prometia referenciar e nunca referenciou. RF-LED-03 passa a cobrir também as leads injetadas pelos módulos de Marketing. |
| 0.6 | Registra a premissa que cobre o caso "nenhuma Loja elegível": a Loja do próprio tenant tem a flag "Pode receber lead sem saldo" e funciona como destino de última instância. Caso considerado improvável, não modelado. |
| 0.5 | Fecha o item aberto: Fluxo virou **Ação** (módulo de Funis de Vendas, v0.11) — cinco tipos fixos (Mover lead, Desqualificar, Finalizar, Reabrir, Formalizar), cada configurável já com Stage de destino próprio. Resolve de vez o mecanismo de RN-LED-11. |
| 0.4 | Fecha o item aberto: movimentação de Stage acontece **sempre** via execução de Ação (nunca drag-and-drop) — unifica o descarte manual pré-Formalização (RN-LED-09, revisada) com o mesmo mecanismo. Adiciona RN-LED-10: operador perde acesso à Lead ao entrar em Formalização, exceto Documentos e Chat. Revela lacuna no Fluxo (Funis de Vendas): falta o Stage de destino — item aberto novo. |
| 0.3 | Corrige RF-LED-04, que descrevia um mecanismo obsoleto (operador movendo a Lead manualmente para Ganho/Perdido com motivo, herdado direto da v1.2) — hoje a Lead só chega a um Stage terminal via a Proposta (RN-FNL-17/18, módulo de Funis de Vendas), exceto no caso de descarte **antes** de existir Proposta, cadastrado aqui pela primeira vez (RN-LED-09). Corrige referência a "Andamento" em §6. |
| 0.2 | Fecha os três itens abertos: a flag do operador vive no módulo de **Usuários** (seção de acesso, junto do campo Time); sem limite de leads ativas por operador nesta versão; rodízio real — quem recebeu por último vai para o fim da fila, em ambas as camadas de distribuição. |
| 0.1 | Versão inicial. Substitui e amplia o Módulo 6 (Leads) da Especificação v1.2 — aquele documento previa distribuição só entre operadores, dentro de um único tenant. Este módulo introduz uma camada anterior: distribuição entre **Lojas parceiras**, condicionada a saldo (módulo CRM · Créditos). |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-LED) e regras de negócio (RN-LED) próprios.

> **O fluxo de uma lead, do nascimento ao operador, tem duas camadas de distribuição — não uma.** A Especificação v1.2 só previa a segunda:
>
> 1. **Distribuição entre Lojas** (nova, este módulo) — a lead nasce tenant-wide, sem Loja, e é oferecida por rodízio às Lojas elegíveis (mesmo Segmento) que tenham saldo — ou que possam operar sem saldo (RN-LOJ-12).
> 2. **Distribuição entre operadores, dentro da Loja escolhida** (herdada e adaptada da v1.2, RF027) — uma vez que a lead pertence a uma Loja, ela é atribuída por rodízio a um operador elegível daquele Segmento.
>
> Cada Loja só enxerga suas próprias leads — nunca as de outra Loja, mesmo estando no mesmo Funil (que é estrutura tenant-wide, módulo de Funis de Vendas).

---

# 1. Objetivo do módulo

Gerir o pipeline comercial em funil kanban — da captação da lead (canais digitais, landing pages, módulos de Marketing ou cadastro manual) à sua conclusão como ganho ou perda — passando pelas duas camadas de distribuição (Loja, depois operador), com rastreabilidade completa.

---

# 2. Atores

- **Operador** — trabalha as leads que chegam até ele: atende, simula, movimenta, registra desfechos.
- **Gestor de Loja** — supervisiona o funil da própria Loja, acompanha o rodízio interno de operadores.
- **Sistema** — executa as duas camadas de distribuição automaticamente.

---

# 3. Entidades e conceitos

## 3.1. Lead

Intenção de negócio em andamento. Nasce sempre em um Funil (via Segmento), passa pela distribuição entre Lojas, depois pela distribuição entre operadores.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome, telefone | Sim | Dados mínimos — mesmo padrão da v1.2 (RF024). |
| CPF, **Promotora**, observações | Não | A Promotora é um `Select` opcional das Promotoras ativas, e pré-preenche a Promotora da Simulação (RN-SIM-06). |
| **Valor potencial** | Não | Estimativa do valor da operação. Na Lead **manual**, é digitado pelo operador. Na Lead do **Marketing**, é definido pelo sistema na injeção e é somente leitura (RN-LED-16). É uma referência, não uma Simulação. |
| Segmento / Funil | Sim | Determina o conjunto de Lojas elegíveis; a Lead nasce no Stage **tipo Novo** do Funil, que é único (RN-FNL-13, RN-FNL-15). |
| **Origem da lead** | Sim, definida pelo sistema | Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa ou Manual (RN-LED-12). Substitui o "canal" da RN042 da v1.2; o detalhe (canal, página, lista, varredura ou autor) é registrado como complemento. Define se a lead cobra (RN-LED-13). |
| **Origem de Venda** | Não | Rótulo do tenant, escolhido pelo operador num Combobox criável (módulo de Origem de Venda, Admin). Distinta da Origem da lead. Aparece no cadastro manual e, editável, no bloco Dados de Marketing do Sheet (Layout de Lead, RN-LAY-07). Na Lead do Marketing, nasce **copiada** da Lista ou da Varredura, se ela tiver (RN-LED-16); o operador pode alterá-la ou limpá-la depois. |
| **Lista ou Varredura de origem** | Sim, nas Leads de Prospecção e Carteira Ativa | Referência imutável à Lista de Prospecção ou à Varredura que injetou a Lead (RN-LED-16). Somente leitura. Vazia nas demais Origens. |
| **Resumo das Ofertas elegíveis** | Sim, nas Leads do Marketing | Ofertas elegíveis na pré-análise, com os requisitos **não avaliados** (RN-LED-16). Somente leitura. Vazio nas demais Origens. |
| **Loja** | Não, até a distribuição resolver | Vazia entre o nascimento e a primeira distribuição bem-sucedida (RN-LED-01). |
| **Operador responsável** | Não, até a distribuição interna resolver | Vazio entre a lead cair na Loja e a distribuição interna (RN-LED-04) resolver. |
| Stage | Sim | Sempre em exatamente um Stage do Funil (herdado de RN-FNL, módulo de Funis de Vendas). |

**RN-LED-01 — Sem Loja é estado transitório, não permanente.** Diferente da v1.2 (onde "pool sem responsável" era um estado estável, RN046), aqui uma lead sem Loja está em trânsito — o sistema está, naquele instante, percorrendo o rodízio de Lojas elegíveis. Não existe uma tela de "pool tenant-wide" onde leads ficam paradas esperando (decisão tomada ao fechar Créditos: sem a flag de saldo, a lead simplesmente segue pra próxima Loja da fila, não pausa em lugar nenhum).

**RN-LED-12 — Origem da lead é definida pelo sistema, uma vez, e não muda.** Lista fixa do sistema, que o tenant não edita. Substitui o "canal digital, landing page ou manual" da RN042 da v1.2: o detalhe (qual canal da Huggy, qual landing page, qual Lista de Prospecção ou Varredura, qual autor) continua sendo registrado, como complemento da Origem. Nas Origens do Marketing, o complemento é a **Lista ou Varredura de origem** (RN-LED-16).

| Origem da lead | Como nasce | Distribuição entre Lojas | Cobra? |
|---|---|---|---|
| **Campanha** | Contato da Huggy ou de landing page originado de campanha externa | Sim | Sim |
| **Orgânica** | Contato espontâneo **novo** na Huggy — sem campanha e de pessoa ainda desconhecida do tenant | Sim | Sim |
| **Recontato** | Contato espontâneo na Huggy de pessoa **já conhecida** do tenant, sem campanha | **Não** — volta à Loja e ao operador anteriores (RN-LED-14) | **Não** |
| **Prospecção** | Injetada pelo módulo Prospecção (Marketing), a partir de uma Lista de Prospecção (base externa importada) | Sim | Sim |
| **Carteira Ativa** | Injetada pelo módulo Carteira Ativa (Marketing), a partir de uma Varredura da base interna de Clientes | Sim | Sim |
| **Manual** | Cadastro feito por um operador (RN-LED-07) | Não | Não |

É distinta do cadastro **Origem de Venda** (rótulo livre do tenant, escolhido pelo operador — módulo de Origem de Venda, Admin) e da Tag de categoria **Origem** de campanha de marketing (RN-FNL-06, módulo de Funis de Vendas). *(Decisão desta revisão: a Origem da lead não muda depois de definida.)*

**RN-LED-13 — Só cobra a Origem que tem linha de preço.** O débito (módulo de Créditos, RN-CRD-01) só acontece quando a Origem da lead tem linha de preço no Funil (RN-FNL-07, módulo de Funis de Vendas): **Campanha, Orgânica, Prospecção e Carteira Ativa**. **Recontato e Manual não têm linha de preço e não debitam.** O preço cobrado é o da linha daquela Origem, no Funil da lead. A regra é do tipo de lead, não da Loja: uma lead que não cobra não cobra de ninguém.

**RN-LED-14 — Recontato: o que é, para onde vai e por que não cobra.**
- **Definição.** Contato espontâneo (sem campanha) feito na Huggy por uma pessoa **já conhecida** do tenant — que já existe como Cliente ou já teve uma Lead —, **sem limite de prazo**. Como o sistema identifica isso é detalhe de implementação, fora deste documento.
- **Contato de campanha não é Recontato.** Se a pessoa já é conhecida mas o contato vem de campanha, a Origem é **Campanha** e a lead cobra normalmente.
- **Destino.** Volta à **Loja e ao operador que a atenderam por último**, sem rodízio e sem débito.
- *(Decisões confirmadas pelo usuário:)* se o operador não puder receber (inativo, excluído, removido, flag desmarcada), a lead fica com a mesma Loja e segue a distribuição interna normal (RN-LED-04, RN-LED-05); se a Loja não puder receber (inativa, excluída, ou sem o Segmento), a lead entra no rodízio entre Lojas **sem cobrança** e sem a checagem de saldo (RN-LED-02), bastando a Loja operar o Segmento, **sem a escolha de tipos de lead** (o Recontato não tem interruptor, Lojas, RN-LOJ-17); e o Recontato **não altera a fila do rodízio** (RN-LED-08), pois não é uma atribuição por rodízio.

**RN-LED-16 — A Lead do Marketing leva o rastreio e o resumo das Ofertas.** Toda Lead de Origem **Prospecção** ou **Carteira Ativa** nasce, na injeção, com quatro informações gravadas pelo sistema (Marketing, RN-MKT-01, 03 e 08):
- **A Lista ou a Varredura de origem**, imutável como a Origem da lead. É a ligação que o Dashboard do Marketing usa para medir resultado.
- **O valor potencial**: o maior valor pré-aprovado entre as Ofertas elegíveis. É referência, não Simulação: não garante aprovação nem condição.
- **O resumo das Ofertas elegíveis**, com os requisitos **não avaliados** (campos que a base não trouxe), para o operador confirmar na primeira Simulação.
- **A Origem de Venda**, copiada da Lista ou da Varredura, quando ela tiver.

Os três primeiros são **somente leitura** para qualquer perfil e não mudam depois da injeção. A Origem de Venda é um rótulo do tenant e segue editável (RN-ORI-06). A pré-análise **não cria Simulação** (Marketing, RN-MKT-02): a primeira Simulação da Lead é feita pelo operador no CRM. Onde cada informação aparece (cartão e Sheet) é definido no Layout de Lead.

**RN-LED-15 — Lead parada.** Uma Lead está **parada** quando fica mais de **3 dias** sem mudar de Stage — critério único e fixo nesta versão (configurá-lo por Funil fica como evolução). O Dashboard (Minha carteira, RN-DSC-01) a destaca. *(Decisão do usuário, levantamento do CRM.)*

## 3.2. Distribuição entre Lojas

**RN-LED-02 — Elegibilidade de Loja: Segmento + saldo (ou flag).** Uma Loja é elegível para receber uma lead quando: opera o Segmento daquela lead **e tem ligada a Origem da lead** (Aba Segmentos da Loja, "Ativo" + a Origem ligada — módulo de Lojas, RN-LOJ-17), **e** tem saldo suficiente para o preço daquela lead (nas origens que cobram, RN-LED-13) **ou** tem a flag "Pode receber lead sem saldo" marcada (RN-LOJ-12). **Premissa operacional:** o tenant mantém a própria Loja com a flag "Pode receber lead sem saldo" (RN-LOJ-12), e ela é a única Loja que pode receber leads sem saldo — funciona como destino de última instância. Por isso o caso de nenhuma Loja elegível é considerado improvável e não é modelado nesta versão. A garantia vale enquanto a Loja do tenant operar o Segmento da lead; as Origens dessa Loja ficam sempre ligadas (Lojas, RN-LOJ-18), então ela é elegível para qualquer Origem distribuída.

**RN-LED-03 — Rodízio entre Lojas elegíveis, sem critério de Região.** Região **não** desqualifica uma Loja nesta versão (diferente da visibilidade de Ofertas, RN-REG-04, que é um mecanismo diferente e não se aplica aqui). Ao encontrar a primeira Loja elegível na fila, o sistema tenta o débito (módulo de Créditos, RN-CRD-01): se o débito for bem-sucedido, a lead pertence a essa Loja; se a Loja não tinha saldo e não tinha a flag, o sistema segue pra próxima Loja elegível na fila.

**RN-LED-08 — Rodízio real: quem recebeu por último vai para o fim da fila.** Vale para as duas camadas de distribuição (Lojas e operadores). Não é ordem fixa de cadastro — a cada atribuição bem-sucedida, o destinatário (Loja ou operador) volta ao fim da respectiva fila, dando a vez aos demais elegíveis na próxima rodada. O **Recontato** não conta: não passa pelo rodízio e não altera a fila (RN-LED-14).

## 3.3. Distribuição entre operadores (dentro da Loja)

**RN-LED-04 — Elegibilidade de operador: Ativo + flag + Time Ativo + Time do Segmento.** Um operador é elegível para receber uma lead quando, simultaneamente: está com status Ativo (módulo de Usuários); tem a flag **"Pode receber lead"** marcada — campo definido no módulo de Usuários, na seção de acesso, junto do Time (RN-USR-14); pertence a um Time com status Ativo; e esse Time tem o Segmento daquela lead associado (módulo de Lojas, campo Segmento do Time).

**Sem limite de leads ativas por operador nesta versão.** Diferente de RF027 da v1.2 (que previa limite como parte do critério), aqui a elegibilidade não considera quantas leads o operador já tem em andamento — um operador com 50 leads ativas segue tão elegível quanto um com nenhuma.

Rodízio real entre os elegíveis (RN-LED-08).

**RN-LED-05 — Sem operador elegível, a lead fica no pool da Loja.** Diferente da camada anterior (onde não há pool tenant-wide), aqui sim existe pool — mas escopado à própria Loja, visível para atribuição manual e auto-assunção por quem tiver permissão, mesmo padrão de RN046/RF028 da v1.2.

---

# 4. Requisitos funcionais

## RF-LED-01 — Board kanban do funil

**Como** operador ou gestor de Loja, **eu quero** visualizar e movimentar as leads da minha Loja num board kanban **para** trabalhar o pipeline de forma visual.

**Comportamento esperado:** o board exibe os Stages do Funil daquele Segmento como colunas (estrutura tenant-wide, módulo de Funis de Vendas) — mas **só as leads da própria Loja** aparecem, nunca as de outra Loja no mesmo Funil. Cada cartão segue o **layout configurado no Funil** (Layout de Lead, RN-LAY-03 — o Layout é o dono do que o cartão mostra). **Não se arrasta o cartão**: toda movimentação é por Ação (RN-LED-11), e Pago e Cancelado só se alcançam pela Proposta (RN-FNL-18) ou pela Ação de descarte.

**RN-LED-06 — Isolamento de Loja é absoluto, mesmo dentro do mesmo Funil.** Duas Lojas operando o mesmo Segmento compartilham a estrutura do Funil (mesmos Stages, Ações, Tags), mas nunca compartilham leads. O isolamento de operação é feito estritamente entre as Lojas.

## RF-LED-02 — Cadastro manual de lead

**Como** operador, **eu quero** cadastrar manualmente uma lead **para** registrar contatos que chegam fora dos canais digitais.

**Comportamento esperado:** nasce já na Loja e no Time de quem cadastrou — **pula as duas camadas de distribuição** (RN-LED-07). Mesmo formulário mínimo da v1.2 (RF024). A Origem da lead é **Manual**, definida pelo sistema; o formulário tem ainda o campo opcional **Origem de Venda** (Combobox criável, módulo de Origem de Venda); o mesmo campo está, editável, no bloco Dados de Marketing do Sheet da Lead (Layout de Lead, RN-LAY-07).

**RN-LED-07 — Cadastro manual não passa por nenhuma distribuição.** Quem cadastra já trouxe a lead — atribuir por rodízio (a outra Loja, ou a outro operador) não faria sentido. A lead nasce diretamente com Loja e operador responsável preenchidos. Tem Origem da lead **Manual** e não debita (RN-LED-13).

## RF-LED-03 — Entrada por canais digitais, landing pages e módulos de Marketing

**Como** sistema, **eu quero** transformar contatos de canais digitais e landing pages, e as leads injetadas pelos módulos de Marketing, em leads tenant-wide **para** que a distribuição entre Lojas decida o destino.

**Comportamento esperado:** landing pages e integração com Huggy continuam **tenant-wide** (não pertencem a nenhuma Loja específica — módulo §3, referenciado). A lead nasce sem Loja e entra imediatamente na distribuição (RN-LED-02, RN-LED-03).

A **Origem da lead** é definida pelo sistema na entrada (RN-LED-12): contato de campanha → **Campanha**; contato espontâneo novo na Huggy → **Orgânica**; contato espontâneo de pessoa já conhecida → **Recontato**, que não passa pela distribuição entre Lojas (RN-LED-14). Como o sistema distingue esses casos é detalhe de implementação, fora deste documento. As leads injetadas pelos módulos de Marketing chegam já qualificadas, no Stage tipo Novo do Funil do Segmento alvo, com Origem **Prospecção** (Lista de Prospecção) ou **Carteira Ativa** (Varredura), carregando o rastreio e o resumo das Ofertas (RN-LED-16), e entram na distribuição com cobrança (RN-LED-13). O Cliente delas pertence ao tenant e a Loja só passa a acessá-lo quando a Lead é distribuída a ela (Clientes, RN-CLI-04).

## RF-LED-04 — Desfecho da lead

**Comportamento esperado — dois caminhos, conforme existir Proposta ou não:**

- **Com Proposta (Lead já passou por Formalização):** o desfecho da Lead **não é ação direta** — acontece como consequência da Proposta chegar a Pago ou Cancelado (RN-FNL-17/18, módulo de Funis de Vendas). O operador trabalha a Proposta, não a Lead, para concluir a negociação.
- **Sem Proposta (Lead ainda não chegou em Formalização):** RN-FNL-18 não se aplica — não há Proposta para sincronizar. O operador pode descartar a Lead diretamente, movendo-a para um Stage tipo Cancelado, com motivo obrigatório (RN-LED-09) — herdado em espírito de RF030/RN051 da v1.2, mas só válido nesta janela.

**RN-LED-09 — Descarte antes de existir Proposta também acontece via Ação, não por mecanismo separado.** Uma Lead sem Proposta pode ser movida para um Stage tipo Cancelado executando a Ação correspondente (RN-LED-11) — não é um "botão de descartar" à parte, é o mesmo mecanismo unificado de movimentação.

**RN-LED-10 — Operador perde acesso à Lead ao entrar em Formalização, exceto Documentos e Chat.** A partir do momento em que a Lead atinge um Stage tipo Formalização, o operador que a trabalhava **não executa mais Ações** sobre ela — o card fica bloqueado para esse fim. As únicas exceções: adicionar Documentos (bloco "Documentos da Proposta", módulo Layout de Lead) e atendimento via Chat. Dali em diante, o avanço da Lead depende da Proposta chegar a Pago ou Cancelado (RN-FNL-17/18, módulo de Funis de Vendas) — trabalho que acontece no módulo de Propostas, não mais na Lead.

**RN-LED-11 — Toda movimentação de Stage acontece via execução de Ação — nunca drag-and-drop.** O board não permite arrastar o card entre colunas. Cinco tipos de Ação, fixos do sistema (módulo de Funis de Vendas, §3.3):

| Ação | Quando aparece disponível | O que faz |
|---|---|---|
| **Mover lead** | Lead em Stage tipo Novo ou Negociação | Avança para um Stage tipo Negociação à frente (nunca para trás, e nunca Formalização, Pago ou Cancelado, que têm Ação própria) — dinâmica, sem configuração; só aparece se houver Stage de Negociação à frente |
| **Desqualificar** | Lead em Stage tipo Novo ou Negociação | Move para um Stage tipo Cancelado configurado — cliente não atende a algum critério |
| **Finalizar** | Lead em Stage tipo Novo ou Negociação | Move para um Stage tipo Cancelado configurado — cliente sem interesse ou silencioso |
| **Formalizar** | Lead em Stage tipo Novo ou Negociação | Move para o Stage tipo Formalização **e** cria a Proposta automaticamente (RN-FNL-17) |
| **Reabrir** | Lead num Stage tipo Cancelado **que nunca teve Proposta** | Move de volta para um Stage tipo Negociação configurado. Uma Lead cancelada por Proposta **não reabre** (RN-PRP-03) |

Enquanto o operador tem acesso à Lead (antes de Formalização, RN-LED-10), ele executa as Ações disponíveis — acessadas pelo botão "Ações" do Card (módulo Layout de Lead, RN-LAY-10) — e assim movimenta a Lead. Depois de Formalização, o avanço é só via Proposta (RN-FNL-18).

---

# 5. Regras de negócio (consolidado)

- **RN-LED-01** — Sem Loja é estado transitório (trânsito no rodízio), não um pool visível.
- **RN-LED-02** — Elegibilidade de Loja: Segmento ativo + Origem da lead ligada, e saldo suficiente ou flag de exceção.
- **RN-LED-03** — Rodízio simples entre Lojas elegíveis; Região não desqualifica.
- **RN-LED-04** — Elegibilidade de operador: Ativo + flag "pode receber lead" (módulo de Usuários) + Time Ativo + Time do Segmento; sem limite de leads ativas.
- **RN-LED-05** — Sem operador elegível, pool escopado à Loja.
- **RN-LED-06** — Isolamento de Loja é absoluto, mesmo dentro do mesmo Funil.
- **RN-LED-07** — Cadastro manual pula as duas distribuições.
- **RN-LED-08** — Rodízio real: último a receber vai para o fim da fila, nas duas camadas.
- **RN-LED-09** — Descarte pré-Formalização acontece via Ação, mesmo mecanismo unificado.
- **RN-LED-10** — Operador perde acesso à Lead em Formalização, exceto Documentos e Chat.
- **RN-LED-11** — Movimentação de Stage é sempre via Ação; nunca drag-and-drop.
- **RN-LED-12** — Origem da lead é do sistema, fixa, definida uma vez: Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa ou Manual.
- **RN-LED-13** — Só cobra a Origem que tem linha de preço (Campanha, Orgânica, Prospecção, Carteira Ativa); Recontato e Manual não debitam.
- **RN-LED-14** — Recontato: pessoa já conhecida, contato espontâneo; volta à Loja e ao operador anteriores, sem rodízio nem débito.
- **RN-LED-15** — Lead parada: mais de 3 dias sem mudar de Stage.
- **RN-LED-16** — A Lead do Marketing leva a Lista ou Varredura de origem, o valor potencial e o resumo das Ofertas (somente leitura) e a Origem de Venda copiada.

---

# 6. Estados e transições

**Lead:**
- **Em distribuição entre Lojas** — transitório, sem Loja (RN-LED-01).
- **Na Loja, em distribuição entre operadores** — tem Loja, sem operador (pool da Loja).
- **Atribuída** — tem Loja e operador; posicionada em algum Stage não-terminal (Novo, Negociação ou Formalização — StageType, módulo de Funis de Vendas).
- **Concluída** — Stage do tipo Pago ou Cancelado (módulo de Funis de Vendas, RN-FNL-13).

---

# 7. Limites do módulo

**Não** inclui: a configuração do Funil em si (Admin), a checagem de saldo (módulo de Créditos — este módulo só consome o resultado), nem simulações/propostas (módulos próprios).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Funis de Vendas** (Admin) — Stages, Tipos, Ações, Tags e as quatro linhas de Preço de lead (RN-FNL-07).
- **Marketing** (Prospecção e Carteira Ativa) — as leads injetadas, com a Origem da lead, a referência à Lista ou Varredura, o valor potencial e o resumo das Ofertas (RN-LED-16).
- **Origem de Venda** (Admin) — a lista do Combobox opcional e o rótulo copiado da Lista ou Varredura.
- **Segmentos, Lojas** (Admin) — elegibilidade de Loja (Aba Segmentos: Ativo e Origens ligadas) e de operador (Time/Segmento).
- **Créditos** (CRM) — checagem e débito de saldo, gatilho da distribuição entre Lojas.
- **Usuários** (Admin) — status Ativo do operador e a flag "Pode receber lead" (RN-USR-14).

**Expõe para:**
- **Marketing** (Dashboard) — a Origem da lead, a referência à Lista ou Varredura e o desfecho (Pago, Cancelado) para medir conversão.
- **Créditos** — o evento de atribuição de lead a uma Loja, que dispara o débito, junto com a Origem da lead que define o preço (RN-LED-13).
- **Clientes, Simulações, Propostas, Chat** (CRM) — o contexto da lead.
- **Usuários** (Admin) e **Membros** (CRM) — a contagem e a redistribuição das leads em andamento na inativação de um usuário (RN-USR-05).

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Leads v0.12 — módulo da aplicação CRM.*
