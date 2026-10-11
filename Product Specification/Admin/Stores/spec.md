# Corban Admin · Lojas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Vendas
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.15
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.15 | Acompanha Créditos v0.6: a Loja adiciona saldo pelo CRM **só via Pix** (AbacatePay). Ajusta a aba Créditos e RN-LOJ-11. |
| 0.14 | **Pendência cruzada resolvida.** A RN-USR-05 de Usuários (v0.6) já prevê a exclusão de usuário em cascata da Loja (soft-delete); sai o aviso do topo e o item 1 de itens abertos. |
| 0.13 | **O lojista escolhe quais tipos de lead recebe em cada Segmento** (decisão do usuário). (1) O interruptor "Recebe leads" por Segmento deixa de existir e é **substituído por um interruptor por Origem da lead**: Campanha, Orgânica, Prospecção e Carteira Ativa (nova RN-LOJ-17). Cada Funil tem leads de Origens diferentes, com preços diferentes (Funis, RN-FNL-07), e a Loja decide quais aceita. (2) A RN-LOJ-01 passa a exigir "Ativo" para ligar qualquer Origem, e ao marcar "Ativo" as quatro nascem **ligadas**. (3) **Nova RN-LOJ-18:** a Loja com "Pode receber lead sem saldo", o destino de última instância (Leads, RN-LED-02), tem as Origens sempre ligadas e travadas. (4) O Segmento do Time passa a exigir "Ativo" com ao menos um tipo de lead ligado. (5) **Migração:** quem tinha "Recebe leads" ligado fica com as quatro Origens ligadas; quem tinha desligado, com as quatro desligadas. Acompanha Leads v0.12 e Configurações v0.5 |
| 0.12 | **A RN-LOJ-15 passa a dizer que o saldo não bloqueia a exclusão**: o diálogo de exclusão mostra o saldo como aviso quando é diferente de zero (decisão do usuário; a RN-CRD-09, de Créditos, já a citava e a spec de Lojas não a tinha). |
| 0.11 | **Excluir um Time passa a ser soft-delete** (RN-LOJ-05): a atribuição das vendas no Pago e as Metas continuam apontando para o Time, marcado "(excluído)". **Nova RN-LOJ-16:** desmarcar "Ativo" de um Segmento é bloqueado enquanto houver leads em andamento nele (hoje os operadores perderiam o acesso às leads que ainda trabalham). Decisões do usuário, levantamento do CRM. |
| 0.10 | **O campo "Pode receber lead sem saldo" (RN-LOJ-12) passa a ter lugar na spec**: linha na tabela de campos da Loja, interruptor na Aba Créditos (RF-LOJ-03), e a regra de quem o edita (`stores:manage_credits` com escopo `all`; somente leitura com `store`). Antes, a RN-LOJ-12 só dizia "campo booleano da Loja", e nenhuma tela nem permissão o previa. Ligar pede confirmação descritiva. Achado na auditoria dos artefatos para o wireframe. |
| 0.9 | **A Lista ganha o filtro "Sem Time ativo"** (RF-LOJ-01), alvo do alerta "Lojas sem time" do Dashboard do Admin: o alerta prometia abrir a Lista já filtrada, e o filtro não existia (achado na revisão do Dashboard). O RF-LOJ-01 passa a descrever os filtros da Lista, que só existiam nos artefatos, e a incluir a exclusão no "Como". |
| 0.8 | Corrige o que a v0.7 dizia do documento de anotações: a seção 7 (vínculo Time↔Grupo) **também** foi superada, pelo módulo de Cargos v0.4 (Cargo é atribuído direto ao usuário e não se associa a Time), e só a seção 6 segue aberta. |
| 0.7 | **A exclusão da Loja passa a ser soft-delete** (nova RN-LOJ-15): some das listagens, e o histórico (Extrato, leads, propostas, relatórios) permanece, marcado como "Loja excluída"; o Código fica liberado. Os Times excluídos em cascata também. **Nova RN-LOJ-14**: a regra do chip "Todas" e da confirmação da primeira Região constava só nos artefatos. Consolidado ganha as RN-LOJ-11 a 15. Corrige a remissão do aviso do topo (RN-LOJ-11 → RN-LOJ-08) e a menção a atendimentos em RN-LOJ-03 e 06, que passa a remeter a RN-USR-05. §8 lista a distribuição de leads e o filtro de Ofertas por Região. O documento de anotações passa a "parcialmente superado". Segmento excluído remove a associação com a Loja e limpa o Time (RN-SEG-09, Segmentos v0.6). Reordena esta tabela. |
| 0.6 | **Acompanha a reformulação de Créditos (CRM, v0.4).** Corrige RN-LOJ-11, que afirmava exclusividade — adicionar saldo deixou de ser ação só desta tela, já que a Loja também adiciona via Pix/Boleto (RF-CRD-03, CRM). Adiciona **Estornar débito** (RF-LOJ-05) — ação que vive aqui, não no CRM, operando sobre o Extrato já definido em Créditos. Aba Créditos passa a exibir também o Limite de gasto diário da Loja (somente leitura — quem edita é a própria Loja, RF-CRD-04). |
| 0.5 | Acompanha Usuários v0.4 (RN-USR-15): o usuário passa a ter **vínculo direto com a Loja**, independente do Time. Escopo `store` inclui os usuários vinculados diretamente à Loja; RN-LOJ-08 e a dependência com Usuários ajustadas. |
| 0.4 | Aba Créditos ganha o campo **"Pode receber lead sem saldo"** (RN-LOJ-12) — habilita saldo negativo para esta Loja (RN-CRD-03/04, módulo CRM · Créditos). |
| 0.3 | Adiciona a **4ª aba — Créditos**: o operador do tenant visualiza saldo e extrato (entidade dona: módulo CRM · Créditos) e adiciona saldo manualmente (RN-LOJ-11). É a única ação de escrita sobre Saldo em todo o produto. |
| 0.2 | Corrige os campos do Time: **Nome** e **Segmento** apenas — "Loja" não é um campo editável, é o vínculo de criação, e é **imutável** (RN-LOJ-10). Fecha os itens abertos 1 e 2 da v0.1: exclusão de Loja **exclui os Times em cascata**, confirmado; Time **não pode ser transferido de Loja**. |
| 0.1 | Versão inicial. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-LOJ) e regras de negócio (RN-LOJ) próprios. Cobre **Loja** e **Time**, que vivem na mesma tela de configuração.

> **Este documento substitui e revoga as seções 1 a 5 de `corban-anotacoes-operacoes-loja.md`.** Aquele documento antecipava um modelo de "Operação-instância por Loja" que nunca chegou a existir — hoje sabemos que quem carrega o pipeline é o **Funil** (tenant-wide, 1:1 com Segmento, módulo Funis de Vendas), e é nele que Loja e Time se associam, não numa instância própria por Loja. O documento de anotações fica marcado como **parcialmente superado**: as seções 1 a 5 (Loja contém Operações) são revogadas aqui; a seção 7 foi superada pelo módulo de Cargos (v0.4), e só a seção 6, que não trata de Loja nem de Operação, segue aberta.

> **Dependência cruzada:** este módulo introduz a exclusão de usuário em cascata da exclusão de Loja (soft-delete, RN-LOJ-08), já prevista na RN-USR-05 do módulo de Usuários (v0.6).

---

# 1. Objetivo do módulo

Estruturar as unidades de venda do tenant. A Loja é a unidade organizacional visível ao mercado — endereço, CNPJ, identidade visual — e decide quais Segmentos opera. Dentro dela, Times organizam os operadores e decidem, cada um, a qual Funil (via Segmento) têm acesso.

---

# 2. Atores

- **Gestor de estrutura** — usuário cujas permissões efetivas incluem a gestão deste módulo.
- **Gestor de Time** — pode ter permissão restrita ao escopo `store` (só a própria Loja) ou `team` (só o próprio Time).
- **Operador** — consome a estrutura (Loja, Time) para saber a que Funis tem acesso; não a gerencia.

---

# 3. Entidades e conceitos

## 3.1. Loja

Cadastro **livre do tenant**. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | — |
| Código | Sim | Identificador curto. Único no tenant, formato livre, editável após a criação — mesmo padrão dos demais cadastros. A unicidade vale entre Lojas não excluídas (RN-LOJ-15). |
| Endereço | Sim | — |
| CNPJ | Não | Opcional nesta primeira versão. |
| Logo | Não | Imagem. |
| Regiões | Sim, com padrão | Zero ou mais Regiões (módulo de Regiões). **Nasce com o valor explícito "Todas"** — um chip visível no campo, não um campo vazio — que corresponde à ausência de restrição regional (RN-REG-04, módulo de Regiões: Loja sem Região vê todas as ofertas). Mostrar "Todas" de forma explícita, e não em branco, evita que o campo pareça esquecido. Comportamento em RN-LOJ-14. |
| Pode receber lead sem saldo | Não (padrão: desmarcado) | Booleano. Habilita saldo negativo (RN-LOJ-12). Editado na **Aba Créditos**, por quem tem `stores:manage_credits` com escopo `all`; com `store`, aparece somente leitura. |
| Status | Sim | Ativa / Inativa (ver §6). |

## 3.2. Aba Segmentos (da Loja)

Não é uma entidade própria — é a associação entre a Loja e cada Segmento do tenant, com um interruptor **Ativo** e um interruptor por **Origem da lead**, com dependência entre eles:

| Flag | O que controla |
|---|---|
| **Ativo** | O Segmento aparece como operado por esta Loja. |
| **Tipos de lead** (um interruptor por Origem da lead: Campanha, Orgânica, Prospecção e Carteira Ativa) | A Loja recebe leads novas daquele Segmento **só das Origens ligadas** (RN-LOJ-17). |

**RN-LOJ-01 — Os tipos de lead exigem "Ativo".** Não é possível ligar uma Origem num Segmento que não está "Ativo" nesta Loja — não faria sentido receber leads de um Segmento que ela nem opera. A interface impede a combinação inválida (desabilita os interruptores de Origem até "Ativo" estar marcado), em vez de validar depois. **Ao marcar "Ativo", as quatro Origens nascem ligadas.** Se o Segmento for excluído do tenant, a associação some desta aba (RN-SEG-09).

**RN-LOJ-17 — Cada Loja escolhe quais tipos de lead recebe em cada Segmento.** O Funil de um Segmento recebe leads de Origens diferentes, com preços diferentes (Funis, RN-FNL-07), e a Loja liga ou desliga cada uma de forma independente: **Campanha, Orgânica, Prospecção e Carteira Ativa**, as Origens que passam pela distribuição entre Lojas e têm linha de preço. **Recontato e Manual não aparecem**: o Recontato volta à Loja anterior e a Manual nasce na própria Loja (Leads, RN-LED-12 e 14). Uma Loja é elegível para uma lead quando o Segmento está "Ativo" **e** a Origem daquela lead está ligada (Leads, RN-LED-02). O efeito é imediato e reversível, e não afeta as leads em andamento. Desligar as quatro equivale a pausar o Segmento: ele segue "Ativo" e os Times continuam com acesso ao Funil (RN-LOJ-16), mas a Loja não recebe leads novas dele. Na interface o grupo se chama **"Tipos de lead"**. *(Decisão do usuário.)*

**RN-LOJ-18 — A Loja com "Pode receber lead sem saldo" recebe todos os tipos.** Essa Loja (RN-LOJ-12), tipicamente a do próprio tenant, é o destino de última instância da distribuição (Leads, RN-LED-02). Em todo Segmento "Ativo" dela, as Origens ficam **ligadas e travadas**, com `Tooltip` explicando. Ao ligar a flag, as Origens dos Segmentos "Ativos" são ligadas; ao desligá-la, continuam ligadas e passam a poder ser alteradas. *(Decisão do usuário.)*

**RN-LOJ-16 — Desmarcar "Ativo" é bloqueado enquanto houver leads em andamento daquele Segmento na Loja.** O Segmento do Time é o que dá acesso ao Funil (RN-LOJ-02): se a Loja deixasse de operar o Segmento com leads em andamento, o Time viraria "sem Segmento" e os operadores perderiam o acesso às leads que ainda trabalham. A interface bloqueia a ação, com a orientação de desligar os tipos de lead do Segmento e concluir ou redistribuir as leads em andamento antes. *(Decisão do usuário, levantamento do CRM; vale também para a mesma aba no CRM · Configurações.)*

## 3.3. Time

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | — |
| Segmento | Não | No máximo um. Restrito aos Segmentos que a própria Loja tem **"Ativo" e com ao menos um tipo de lead ligado** (RN-LOJ-01, RN-LOJ-02, RN-LOJ-17) — um Time não pode escolher um Segmento que a Loja não opera de fato. |
| Status | Sim | Ativo / Inativando / Inativo (ver §6). |

**A Loja não é um campo do Time.** É o contexto em que o Time é criado (a Aba Times só existe dentro da configuração de uma Loja) — não um campo editável no formulário, e **imutável** (RN-LOJ-10): um Time nunca muda de Loja depois de criado.

**RN-LOJ-02 — O Segmento do Time é o que dá acesso ao Funil.** Um Time com Segmento selecionado acessa o Funil daquele Segmento (via a relação 1:1 Segmento↔Funil, já fechada no módulo de Funis de Vendas). Um Time **sem** Segmento existe normalmente, mas não acessa nenhum Funil — útil para times administrativos ou de suporte que não trabalham leads diretamente. Se o Segmento for excluído do tenant, o Time fica sem Segmento (RN-SEG-09).

## 3.4. Escopos `store`, `team` e `self`

Os três escopos usados em todo o catálogo de permissões do produto (além de `all`) se ancoram nesta estrutura:

| Escopo | Alcance |
|---|---|
| `store` | Todos os dados da Loja inteira — todos os Times dela e os usuários vinculados diretamente à Loja (RN-USR-15), com ou sem Time. |
| `team` | Só os dados do Time do usuário. |
| `self` | Só os dados do próprio usuário. |

Afetam **visibilidade de leads, pipeline e qualquer outro recurso do sistema** cujo catálogo de permissões ofereça esses escopos — a aplicação concreta é decidida em cada módulo consumidor (Leads, Simulações, etc.), não aqui. Este módulo só define a estrutura (Loja, Time) da qual esses escopos derivam.

---

# 4. Requisitos funcionais

## RF-LOJ-01 — Gestão de lojas (CRUD)

**Como** gestor de estrutura, **eu quero** criar, consultar, editar, inativar e excluir Lojas **para** refletir as unidades de venda do tenant.

Tela de configuração com quatro abas: **Informações gerais**, **Segmentos**, **Times**, **Créditos**. A listagem permite busca por nome ou código e filtros por status, por Região e pela situação dos Times ("Sem Time ativo": Lojas ativas sem nenhum Time ativo — alvo do alerta do Dashboard do Admin).

## RF-LOJ-02 — Ciclo de vida do Time

**RN-LOJ-03 — Inativar um Time tem dois modos.** Ao inativar, o Time para de receber itens novos (leads e demais itens de trabalho — categorias em RN-USR-05). O gestor escolhe entre:

- **Inativação imediata** — exige redistribuir, na hora, todos os itens ativos daquele Time para outro Time **da mesma Loja**.
- **Inativação após finalizar** — o Time entra em estado **Inativando**: para de receber itens novos, mas os que já estão em andamento seguem até o fim. Quando o último for concluído, o Time passa a **Inativo** automaticamente.

**RN-LOJ-04 — Reativar durante a drenagem cancela a inativação agendada.** Se o gestor reativa um Time que está em **Inativando**, ele volta a **Ativo** e recomeça a receber itens novos — a inativação automática que estava agendada é cancelada, não só adiada.

**RN-LOJ-05 — Excluir um Time.** O gestor escolhe entre migrar os membros do Time para outro Time (da mesma Loja), ou, se preferir não migrar, precisa redistribuir os itens ativos daquele Time para outro — mesmo mecanismo de RN-LOJ-03. **A exclusão é soft-delete**, como a de Loja e a de Usuário (RN-LOJ-15): o Time some das listas e das seleções, mas o histórico (Metas e vendas atribuídas a ele no Pago) continua apontando para ele, com a marca "(excluído)". As Metas do Time ficam preservadas como histórico.

## RF-LOJ-04 — Gestão de saldo (aba Créditos)

**Como** operador do tenant, **eu quero** ver o saldo de cada Loja e adicionar créditos **para** manter a operação delas funcionando.

**Comportamento esperado:** a aba mostra o saldo atual, o Limite de gasto diário (somente leitura — quem edita é a própria Loja, RF-CRD-04 do módulo CRM · Créditos), o interruptor **"Pode receber lead sem saldo"** (RN-LOJ-12) e o extrato completo (entidades **Saldo** e **Extrato**, definidas e detalhadas naquele módulo — este módulo só consome e adiciona, não redefine). Ação "Adicionar saldo": valor + nota opcional, gera um lançamento de Crédito imediatamente refletido no saldo — **um dos dois caminhos de adicionar saldo**, o outro sendo a própria Loja via Pix (RF-CRD-03, CRM). Cada lançamento do Extrato abre os mesmos Detalhes da operação já definidos em Créditos (RF-CRD-06) — não redefinidos aqui. Um campo próprio da Loja, **"Pode receber lead sem saldo"** (`Switch`), também vive nesta aba.

**Regras específicas:**

**RN-LOJ-11 — Adicionar saldo manualmente é ação exclusiva desta tela — mas não a única forma de adicionar saldo.** *(Revisão: a versão anterior afirmava exclusividade total, hoje falsa.)* Nenhuma outra tela do **Admin** permite adicionar saldo. No CRM, porém, a própria Loja adiciona saldo via Pix (RF-CRD-03, módulo CRM · Créditos) — segundo caminho, fora desta tela. Débitos continuam sempre automáticos, por lead entregue (RN-CRD-01); estorno de débito é RF-LOJ-05, abaixo.

**RN-LOJ-12 — "Pode receber lead sem saldo" habilita saldo negativo.** Campo booleano da Loja. Quando marcado, a Loja continua recebendo leads mesmo com saldo insuficiente — o débito é aplicado normalmente e o saldo pode ficar negativo (RN-CRD-01, RN-CRD-03, módulo CRM · Créditos). Uso típico: a Loja do próprio tenant operador. Efeito sobre os tipos de lead: RN-LOJ-18. Desmarcado por padrão para Lojas novas. **Onde se edita:** na Aba Créditos, ao lado do saldo, e só por quem tem `stores:manage_credits` com escopo `all` — com `store`, o interruptor aparece **somente leitura**, porque o efeito é financeiro e um gestor de Loja não deve poder autorizar o próprio saldo negativo. **Ligar** pede confirmação descritiva (a Loja passa a poder ficar com saldo negativo); **desligar** é direto *(a confirmação é escolha desta revisão)*. Permanece como única exceção à regra padrão — confirmado pelo usuário, não revogado pela introdução do Limite de gasto diário.

## RF-LOJ-05 — Estornar débito

**Como** operador do tenant, **eu quero** estornar um débito aplicado indevidamente à Loja **para** corrigir o saldo sem apagar o histórico.

**Comportamento esperado:** a partir do Detalhe de um lançamento de Débito no Extrato (aba Créditos, RF-CRD-06), ação "Estornar" — pede um motivo (texto livre) e, ao confirmar, gera um lançamento de **Crédito** no valor do débito original, referenciando-o. Mesmo princípio de ledger append-only já estabelecido em Créditos (RN-CRD-06) — o lançamento original nunca é editado nem excluído.

**Regras específicas:**

**RN-LOJ-13 — Estorno é exclusivo desta tela, nunca do CRM.** Diferente de Adicionar saldo (que agora tem dois caminhos — RN-LOJ-11), Estornar continua existindo só aqui — a Loja nunca estorna o próprio débito, só consulta o resultado no Extrato dela.

## RF-LOJ-03 — Ciclo de vida da Loja

**RN-LOJ-06 — Inativar uma Loja.** Ela para de receber itens novos (leads e demais itens de trabalho — categorias em RN-USR-05) em todos os seus Times. Diferente do Time, não há dois modos aqui — é sempre "para de receber novos, os em andamento seguem".

**RN-LOJ-07 — Excluir uma Loja é bloqueado enquanto houver itens ativos.** O gestor precisa inativar primeiro e aguardar os processos em andamento se encerrarem, em qualquer Time da Loja, antes de poder excluí-la.

**RN-LOJ-08 — Exclusão da Loja oferece excluir os usuários exclusivos dela.** Ao excluir, a interface pergunta se o gestor quer excluir também os usuários que estão associados **apenas** a esta Loja (pelo vínculo direto com a Loja ou via Time, considerando o Cargo; um usuário tem no máximo uma Loja e um Time — módulo de Usuários). Se sim, exibe a lista completa desses usuários, pré-marcada, para o gestor confirmar ou desmarcar individualmente antes de aplicar. **Exclusão de usuário aqui é soft-delete**: some das listagens do sistema, mas o registro permanece no banco — contradiz RN-USR-05, que precisa ser atualizada (ver nota no topo deste documento).

**RN-LOJ-09 — Excluir a Loja exclui os Times dela em cascata, também de forma suave (RN-LOJ-15).** Confirmado. Como a exclusão da Loja já exige que não haja itens ativos em nenhum Time (RN-LOJ-07), os Times dela estão necessariamente vazios de trabalho no momento da exclusão.

**RN-LOJ-10 — O Time não pode ser transferido de Loja.** A Loja é definida na criação do Time e nunca muda — não existe ação de "mover Time para outra Loja" em nenhuma tela do produto.

**RN-LOJ-14 — A Loja nasce com "Todas"; a primeira Região específica pede confirmação.** *(Regra que constava só na Descrição, na Microcopy e na Matriz de Estados; escrita no módulo na v0.7, espelhando a RN-PRM-06 de Promotoras.)* A Loja nasce **sem Região específica**, mostrada como o chip **"Todas"**, que equivale a não ter restrição regional (RN-REG-04: a Loja sem Região vê todas as ofertas). O campo de Regiões não existe no cadastro, só na Configuração. Ao **escolher a primeira Região específica** de uma Loja existente, a interface pede uma **confirmação leve**, porque a Loja passa a ver só as ofertas de promotoras com Região em comum (ou sem Região) e deixa de ver as demais; as Regiões seguintes não pedem confirmação. Ao **remover a última Região específica**, o campo volta a "Todas" automaticamente, sem confirmação (é ampliar, não restringir). Uma Região **inativada ou excluída** (RN-REG-02) é removida da Loja, e a Loja volta a "Todas" se era a última.

**RN-LOJ-15 — Excluir uma Loja é exclusão suave (soft-delete), como a dos usuários.** A Loja excluída some das listagens e das seleções (inclusive da distribuição de leads), mas o **registro permanece**: o Extrato de Créditos (append-only, RN-CRD-06), as leads e propostas já concluídas e os relatórios continuam referenciando-a, marcada como excluída. Vale também para os Times excluídos em cascata (RN-LOJ-09). Não há ação de restaurar nesta versão. O **Código** de uma Loja excluída fica liberado para uso por outra Loja (a identidade é o registro, não o código). *(Decisão do usuário; a liberação do Código e o efeito nos Times são escolha desta revisão.)* **A exclusão (e a inativação) não é bloqueada por saldo:** quando o saldo da Loja é diferente de zero, o diálogo de exclusão mostra o saldo como **aviso**, e o Extrato permanece (Créditos, RN-CRD-09). *(Decisão do usuário, levantamento do CRM.)*

---

# 5. Regras de negócio (consolidado)

- **RN-LOJ-01** — Os tipos de lead exigem "Ativo" no mesmo Segmento; ao ativar, as quatro Origens nascem ligadas.
- **RN-LOJ-02** — Segmento do Time dá acesso ao Funil correspondente; sem Segmento, sem Funil.
- **RN-LOJ-03** — Inativar Time: imediata (com redistribuição) ou após finalizar (drenagem automática).
- **RN-LOJ-04** — Reativar Time em drenagem cancela a inativação agendada.
- **RN-LOJ-05** — Excluir Time: migra membros ou redistribui itens; a exclusão é soft-delete (histórico e Metas preservados).
- **RN-LOJ-16** — Desmarcar "Ativo" de um Segmento é bloqueado enquanto houver leads em andamento nele.
- **RN-LOJ-06** — Inativar Loja para recebimento de itens novos, sem modo imediato/drenagem — só um comportamento.
- **RN-LOJ-07** — Excluir Loja bloqueado com itens ativos em qualquer Time.
- **RN-LOJ-08** — Exclusão da Loja oferece soft-delete dos usuários exclusivos dela.
- **RN-LOJ-09** — Exclusão da Loja cascade-exclui os Times (soft-delete, RN-LOJ-15).
- **RN-LOJ-10** — Time não pode ser transferido de Loja; imutável após a criação.
- **RN-LOJ-11** — Adicionar saldo manualmente é exclusivo desta tela no Admin, mas não o único caminho (a Loja também adiciona, no CRM).
- **RN-LOJ-12** — "Pode receber lead sem saldo" habilita saldo negativo; desmarcado por padrão. Editado na Aba Créditos, só com `manage_credits` em `all` (somente leitura com `store`); ligar pede confirmação.
- **RN-LOJ-13** — Estornar débito é exclusivo desta tela, nunca do CRM.
- **RN-LOJ-14** — A Loja nasce com "Todas"; a primeira Região específica pede confirmação leve; remover a última repõe "Todas".
- **RN-LOJ-15** — Excluir a Loja é soft-delete: o histórico permanece, e o Código é liberado. Saldo diferente de zero não bloqueia: o diálogo mostra o saldo como aviso.
- **RN-LOJ-17** — Cada Loja escolhe, por Segmento, quais tipos de lead recebe (Campanha, Orgânica, Prospecção, Carteira Ativa); a elegibilidade exige a Origem ligada.
- **RN-LOJ-18** — A Loja com "Pode receber lead sem saldo" recebe todos os tipos: Origens ligadas e travadas.

---

# 6. Estados e transições

**Loja:**
- **Ativa** — opera Segmentos, recebe leads conforme a Aba Segmentos.
- **Inativa** — não recebe itens novos em nenhum Time.
- **Excluída** — bloqueada enquanto houver item ativo (RN-LOJ-07); **soft-delete**, como o dos usuários associados (RN-LOJ-15): some das listagens, e o histórico permanece.

**Time:**
- **Ativo** — recebe itens novos, conforme o Segmento associado.
- **Inativando** — não recebe itens novos; itens em curso seguem; volta a Ativo se reativado (RN-LOJ-04).
- **Inativo** — sem itens em curso, não recebe novos. Alcançado por inativação imediata (com redistribuição) ou pelo fim da drenagem.

---

# 7. Limites do módulo

**Não** inclui: a configuração do Funil em si (módulo Funis de Vendas), o cadastro de Segmentos e Regiões (módulos próprios), nem a atribuição de Cargo a usuário (módulo de Usuários & Permissões) — este módulo só define a estrutura Loja/Time à qual o usuário se associa.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Segmentos** — lista de Segmentos do tenant, para a Aba Segmentos da Loja.
- **Funis de Vendas** — o Funil correspondente ao Segmento escolhido no Time é o que efetivamente concede acesso ao pipeline.
- **Regiões** — lista de Regiões, para o campo da Loja.

**Expõe para:**
- **Usuários** — a lista de Lojas e a de Times, para os campos Loja e Time do usuário; os escopos `store`/`team` derivam desta estrutura.
- **Leads / pipeline (CRM)** — quais Funis cada Time efetivamente alcança e, para a distribuição de leads entre Lojas, a flag "Ativo" e os tipos de lead ligados por Segmento (RN-LOJ-17) e o campo "Pode receber lead sem saldo" (RN-LOJ-12).
- **Ofertas e Simulações (CRM)** — a Região da Loja, que filtra as Ofertas visíveis (RN-REG-04, RN-OFR-02).

**Depende também de:**
- **Créditos** (CRM) — dono das entidades Saldo, Extrato e Detalhes da operação; este módulo só as exibe e escreve nelas via Adicionar saldo (RN-LOJ-11) e Estornar (RF-LOJ-05). Expõe de volta: o Limite de gasto diário (editado só no CRM), exibido aqui somente leitura.

---

# 9. Itens abertos

1. *(Resolvido na v0.14.)* **Atualização de RN-USR-05** em Usuários: já incorpora a exclusão em cascata (soft-delete).
2. **`corban-anotacoes-operacoes-loja.md`: resolvido.** Atualizado para "Parcialmente superado": as seções 1 a 5 (Operação-instância por Loja) são revogadas por este módulo, a seção 7 foi superada por Cargos v0.4, e só a seção 6 segue aberta.

---

*Fim do documento. Lojas v0.14 — módulo da aplicação Admin, seção Vendas.*
