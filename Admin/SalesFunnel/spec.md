# Corban Admin · Funis de Vendas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Vendas
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 0.4    | Fecha o item aberto 1: **o Funil precisa ter ao menos um stage de cada Tipo** (Novo, Andamento, Pago, Cancelado) — RN-FNL-15. Bloqueia criação e remoção que violem a cobertura.                                                                                                                                                                                                                                                                                                                                                      |
| 0.5    | **O Funil deixa de ser criado manualmente — nasce automaticamente junto com o Segmento** (revoga RN-FNL-02), já populado com o conjunto de stages sugerido, em novo status **"Aguardando configuração"**. Removida a Aba/fluxo de criação de funil (RF-FNL-01 reescrito). RN-FNL-09 revisada: **Funil que já foi ativado nunca é excluído** — a exceção (Funil nunca ativado) é excluído apenas em cascata, disparada pelo módulo de Segmentos, nunca por ação direta aqui. Item aberto 1 fechado (Status de Proposta já atualizado). |
| 0.3    | A tela de configuração passa de 3 para **5 abas**: a aba Board se divide em **Stages, Fluxos e Tags**, cada uma própria. Total: Informações gerais, Stages, Fluxos, Tags, Preço das leads.                                                                                                                                                                                                                                                                                                                                            |
| 0.2    | Fecha os itens abertos. Funil ganha **nome próprio** (sugerido a partir do Segmento). **Stage ganha o campo Tipo**, reaproveitando o enum de Status de Proposta — que passa a ter quatro valores, com a inclusão de **Novo**. Funil **não pode ser excluído**, apenas inativado, com regra de drenagem das leads em curso. Segmento do funil é **imutável** após a criação. Fluxo sem stage permitido fica cadastrado, sem exibição.                                                                                                  |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-FNL) e regras de negócio (RN-FNL) próprios.

> **Este é o módulo mais amplo do Admin até aqui.** Reúne três domínios sob uma mesma tela, organizados em abas: identificação do funil, configuração do board (colunas, fluxos e tags) e precificação das leads entregues nele.

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

**RN-FNL-02 — O Funil nasce automaticamente, junto com o Segmento.** _(Revoga a versão anterior desta regra, que previa criação manual e separada.)_ Não existe fluxo de "criar funil" neste módulo — o Funil já existe desde o instante em que o Segmento é criado, disparado de lá (RN-SEG-05). Ele nasce **já populado** com o conjunto de stages sugerido (RN-FNL-03), cobrindo os quatro Tipos exigidos por RN-FNL-15, em status **"Aguardando configuração"**. O que este módulo oferece é a **configuração** de um Funil que já existe, nunca a criação de um do zero.

| Campo     | Obrigatório | Notas                                                                                                                                               |
| --------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome      | Sim         | Nome próprio do funil, **sugerido automaticamente a partir do nome do Segmento** no momento da criação automática — o gestor pode alterá-lo depois. |
| Segmento  | Sim         | Qual Segmento este funil atende. Único, e **imutável** (RN-FNL-12).                                                                                 |
| Descrição | Não         | Texto curto sobre o funil.                                                                                                                          |
| Status    | Sim         | **Aguardando configuração** / Ativo / Drenando / Inativo (ver §6).                                                                                  |

**RN-FNL-12 — O Segmento é imutável.** Não pode ser alterado depois de criado. Trocá-lo moveria todo o pipeline de um público para outro — leads de INSS passariam a ser de CLT sem que ninguém as tivesse movido.

## 3.2. Stage (coluna do board)

Etapa pela qual a lead avança. **Livremente definidas pelo gestor** — nome e ordem.

| Campo | Obrigatório | Notas                                                                                                  |
| ----- | ----------- | ------------------------------------------------------------------------------------------------------ |
| Nome  | Sim         | Como a coluna aparece no board.                                                                        |
| Tipo  | Sim         | **Novo**, **Andamento**, **Pago** ou **Cancelado** — mesmo enum de Status de Proposta (ver RN-FNL-13). |
| Ordem | Sim         | Posição da coluna no board.                                                                            |

**RN-FNL-03 — O sistema sugere uma estrutura padrão, não a impõe.** Ao criar um funil, o sistema oferece um conjunto inicial de stages — **Novo, Negociação, Formalização, Venda, Desqualificado** — que o gestor pode aceitar, editar por completo ou descartar. É sugestão, não estrutura obrigatória: **não existem stages de sistema**, e qualquer um pode ser removido ou renomeado.

_Isso revoga a Especificação v1.2, que previa Entrada, Ganho e Perdido como etapas fixas em existência._

**RN-FNL-15 — O funil precisa de ao menos um stage de cada Tipo.** Não basta ter um stage terminal (o mínimo que evitaria o funil nunca drenar, RN-FNL-14) — é exigida cobertura completa dos quatro tipos: **Novo, Andamento, Pago e Cancelado**. Dois pontos de aplicação:

- **Ao criar o funil**, não é possível salvar sem que a configuração de stages cubra os quatro tipos. O conjunto sugerido (RN-FNL-03) já satisfaz isso por construção.
- **Ao remover um stage**, se ele for o único do seu tipo, a remoção é bloqueada — mesmo que o stage esteja vazio. Essa checagem acontece **antes** da regra de destino para leads (RN-FNL-10): um stage que é o único do seu tipo não pode ser removido, então a pergunta "para onde vão as leads" nem chega a ser feita.

**RN-FNL-13 — O Tipo do stage é o que o sistema entende; o nome é livre.** Sem stages de sistema, o produto precisa de outra forma de saber se uma lead está entrando, em curso, convertida ou perdida — não dá para interpretar o nome que o tenant deu à coluna. Essa função passa a ser do **Tipo**, o mesmo enum usado em Status de Proposta:

| Tipo          | Significado no board                     |
| ------------- | ---------------------------------------- |
| **Novo**      | Lead recém-chegada, ainda não trabalhada |
| **Andamento** | Lead em curso                            |
| **Pago**      | Lead convertida — virou produção         |
| **Cancelado** | Lead perdida                             |

Um tenant pode ter três colunas de tipo Andamento com nomes próprios ("Negociação", "Formalização", "Aguardando banco") — para o sistema, as três significam "em curso".

**O enum é compartilhado com Status de Proposta**, que ganha o valor **Novo** por conta disso. As duas entidades continuam separadas; o que elas compartilham é o vocabulário de classificação.

## 3.3. Fluxo

**Automação de movimentação da lead no board.** Nesta fase, os fluxos **não são executados pelo Corban**: são automações já cadastradas na **Huggy** (ferramenta de conversação), e o que se faz aqui é registrá-las para que o board saiba quais existem e onde se aplicam.

| Campo             | Obrigatório | Notas                                                                                |
| ----------------- | ----------- | ------------------------------------------------------------------------------------ |
| FlowId            | Sim         | Código do fluxo registrado na Huggy. É a chave que liga o registro à automação real. |
| Nome              | Sim         | Como o fluxo aparece na configuração.                                                |
| Descrição         | Não         | O que o fluxo faz.                                                                   |
| Stages permitidos | Sim         | Em quais stages este fluxo pode ser aplicado.                                        |

**RN-FNL-04 — O Corban registra o fluxo, não o executa.** A automação roda na Huggy. O cadastro aqui é um espelho: se o fluxo for alterado ou removido lá, o Corban não fica sabendo — o registro continua apontando para um FlowId que pode não existir mais.

> **Nota de evolução — trazer a automação para o sistema.** A dependência da Huggy para executar automações é decisão desta fase, tomada para acelerar a entrega. A evolução prevista é o Corban executar seus próprios fluxos, com gatilho e ação configurados aqui — sem FlowId externo. Quando isso acontecer, os fluxos registrados hoje precisarão de migração.

## 3.4. Tag

**Classificador de card**, usado para marcar leads com informação de campanha. Também vem da Huggy.

| Campo     | Obrigatório | Notas                                                                 |
| --------- | ----------- | --------------------------------------------------------------------- |
| Id        | Sim         | Identificador da tag na Huggy.                                        |
| Nome      | Sim         | Como a tag aparece no card.                                           |
| Categoria | Sim         | **Audiência**, **Origem** ou **Departamento** — enum fixo do sistema. |

**RN-FNL-05 — Categoria é fixa; as tags dentro dela são livres.** As três categorias são do sistema e não podem ser criadas nem editadas pelo tenant. Dentro de cada uma, o gestor cadastra quantas tags quiser.

**RN-FNL-06 — "Origem" aqui é origem de campanha, não Origem de Venda.** São duas coisas distintas, com nomes parecidos:

| Conceito                    | Onde vive                | O que é                                                                                      |
| --------------------------- | ------------------------ | -------------------------------------------------------------------------------------------- |
| **Tag de categoria Origem** | Aqui, no Funil           | De qual campanha de marketing a lead veio (ex.: "Campanha Black Friday", "Remarketing base") |
| **Origem de Venda**         | Cadastro de Lead, no CRM | Por onde a venda chegou (ex.: "Indicação", "Porta a porta")                                  |

Vale registrar porque a confusão é provável e o efeito seria modelar uma como a outra.

## 3.5. Preço de lead

**Quanto a Loja paga por cada lead entregue neste funil**, conforme a procedência da lead.

**RN-FNL-07 — Duas linhas fixas, por tipo de marketing.** A tabela tem exatamente dois registros, não criáveis nem excluíveis — o gestor apenas preenche os valores de cada:

| Tipo        | O que é                                                                      |
| ----------- | ---------------------------------------------------------------------------- |
| **Interno** | Lead gerada pela própria base — análise de crédito de clientes já existentes |
| **Externo** | Lead gerada por campanha paga (Facebook Ads, TikTok, Google Ads, etc.)       |

| Campo | Notas                                                                                                                    |
| ----- | ------------------------------------------------------------------------------------------------------------------------ |
| Custo | O que o tenant gastou para adquirir a lead. Em marketing externo, é a verba de anúncio; em interno, o custo de operação. |
| Preço | O que a Loja paga ao tenant para receber a lead.                                                                         |

**RN-FNL-08 — A margem é a diferença, e pode ser negativa.** Preço menos custo é o que o tenant ganha por lead. Nada impede configurar preço abaixo do custo — é decisão comercial legítima (subsidiar a loja para ganhar volume), então o sistema não bloqueia, mas sinaliza (ver Microcopy).

> **Nota de evolução — integração com ferramentas de anúncio.** Nesta fase o custo é **informado manualmente** pelo gestor. Está prevista a integração com plataformas de anúncio (Facebook Ads, Google Ads, TikTok Ads) para que o custo real por lead seja importado automaticamente, tornando a precificação dinâmica em vez de estática. A ausência dessa integração é o que torna a versão atual viável a curto prazo.

---

# 4. Requisitos funcionais

## RF-FNL-01 — Listagem e configuração de funis

**Como** gestor comercial, **eu quero** ver os funis existentes e configurar cada um **para** que as leads de cada Segmento tenham um pipeline funcional.

**Comportamento esperado:** a listagem mostra **um funil por Segmento — sempre, sem exceção**, já que todo Segmento tem o seu (RN-FNL-01). Mostra o Segmento, o status do Funil e a quantidade de Lojas que o utilizam. **Não há ação de "criar funil"** — a lista é só de consulta e navegação para a configuração de cada um; a criação acontece automaticamente, disparada pelo cadastro de um Segmento novo (RN-FNL-02).

**Regras específicas:**

**RN-FNL-09 — Funil que já foi ativado nunca é excluído.** _(Revisão: a versão anterior dizia apenas "não há exclusão de funil" — agora existe uma exceção pontual.)_ Um Funil que já passou por Ativo alguma vez (está Ativo, Drenando, ou Inativo após ter sido usado) não tem exclusão, em nenhuma circunstância — a descontinuação é só por inativação, com drenagem (RN-FNL-14). Um Funil que **nunca saiu de "Aguardando configuração"** pode ser excluído, mas **não por ação direta neste módulo** — só em cascata, quando o Segmento pai é excluído (RN-SEG-04, módulo de Segmentos). Este módulo não oferece um botão de excluir Funil isoladamente, em hipótese alguma.

## RF-FNL-02 — Aba Informações gerais

Nome (sugerido a partir do Segmento na criação automática, editável depois), Segmento (imutável, RN-FNL-12), descrição e status. O controle de status aqui é o mesmo Switch usado na Lista — permite inativar/reativar o Funil **diretamente**, independente de qualquer ação sobre o Segmento. A cascata vinda do Segmento (RN-SEG-06, RN-SEG-07) é um segundo caminho para o mesmo campo, não um campo separado.

## RF-FNL-03 — Aba Stages

Criar, renomear, reordenar e remover colunas. Ao criar o funil, o conjunto sugerido já vem preenchido (RN-FNL-03). Salvar e remover são validados contra a cobertura completa de tipos (RN-FNL-15) — a interface precisa indicar, por tipo, se há ao menos um stage, e impedir a remoção do último de qualquer tipo antes mesmo de perguntar sobre destino de leads.

## RF-FNL-04 — Aba Fluxos

Cadastrar os fluxos da Huggy: FlowId, nome, descrição e os stages em que se aplicam — lista que consome os Stages da aba anterior.

## RF-FNL-05 — Aba Tags

Cadastrar as tags da Huggy: Id, nome e categoria (Audiência / Origem / Departamento).

**RN-FNL-10 — Remover um stage exige destino para as leads.** Se o stage contém leads, o gestor indica obrigatoriamente para qual stage elas vão, em lote. Mesmo padrão da RN022 da Especificação v1.2.

**RN-FNL-11 — Fluxo sem stage permitido fica cadastrado, sem exibição.** Ao remover um stage, os fluxos que o listavam perdem aquele destino. Um fluxo que fique sem nenhum stage permitido **não é removido** — continua cadastrado, mas deixa de ser exibido em qualquer coluna do board. Volta a aparecer se o gestor lhe atribuir um stage novamente.

## RF-FNL-06 — Aba Preço das leads

Tabela de duas linhas (Interno e Externo), com custo e preço editáveis em cada.

---

# 5. Regras de negócio (consolidado)

- **RN-FNL-01** — Um funil por Segmento, tenant-wide; Lojas associam-se ao Funil.
- **RN-FNL-02** — O Funil nasce automaticamente com o Segmento, já populado, em "Aguardando configuração".
- **RN-FNL-03** — O sistema sugere stages padrão, não os impõe; não há stage de sistema.
- **RN-FNL-04** — O Corban registra os fluxos da Huggy, não os executa.
- **RN-FNL-05** — Categoria de tag é fixa; as tags dentro dela são livres.
- **RN-FNL-06** — Tag de categoria Origem ≠ cadastro de Origem de Venda.
- **RN-FNL-07** — Preço de lead tem duas linhas fixas: Interno e Externo.
- **RN-FNL-08** — A margem pode ser negativa; o sistema sinaliza, não bloqueia.
- **RN-FNL-09** — Funil já ativado nunca é excluído; Funil nunca ativado só se exclui em cascata pelo Segmento pai (fora deste módulo).
- **RN-FNL-10** — Remover stage com leads exige destino.
- **RN-FNL-11** — Fluxo sem stage permitido fica cadastrado, sem exibição.
- **RN-FNL-12** — O Segmento do funil é imutável após a criação.
- **RN-FNL-13** — O Tipo do stage (Novo/Andamento/Pago/Cancelado) é o que o sistema entende; o nome é livre.
- **RN-FNL-14** — Inativar drena: leads em curso seguem, e o funil some das Lojas quando esvaziar.
- **RN-FNL-15** — O funil precisa de ao menos um stage de cada Tipo (Novo, Andamento, Pago, Cancelado); a remoção do último stage de um tipo é bloqueada.

---

# 6. Estados e transições

**RN-FNL-14 — Funil não é excluído; é inativado, com drenagem.** Não existe exclusão de funil — a operação de descontinuação é a inativação, e ela acontece em duas fases:

| Fase                   | O que acontece                                                                                                                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Ao inativar**        | O funil **para de receber leads novas**. As leads que já estão nele — de tipo **Novo** e **Andamento** — continuam sendo trabalhadas normalmente, sem interrupção. O funil segue visível para as Lojas. |
| **Depois da drenagem** | Quando não restar nenhuma lead em Novo ou Andamento, o funil **deixa de ser exibido às Lojas**.                                                                                                         |

A drenagem é o que evita o problema clássico de desligar um pipeline: leads em negociação não são abandonadas nem migradas à força — elas terminam o ciclo onde começaram.

**Funil:**

- **Aguardando configuração** — estado inicial, desde a criação automática. Nunca recebeu leads, nunca foi ativado. Já tem a estrutura sugerida de stages, mas não está disponível às Lojas.
- **Ativo** — recebe leads novas e é exibido às Lojas.
- **Inativo, drenando** — não recebe leads novas; as leads em curso seguem; ainda exibido às Lojas.
- **Inativo, drenado** — sem leads em curso; não exibido às Lojas.

Transições: aguardando configuração → ativo (livre — primeira ativação); ativo → inativo (livre, inicia a drenagem; pode ser direta, nesta tela, ou cascateada pela inativação do Segmento — RN-SEG-06); inativo → ativo (livre; pode ser direta, ou oferecida na reativação do Segmento — RN-SEG-07); inativo drenando → inativo drenado (automática, quando a última lead sai de Novo/Andamento).

**A diferença entre "Aguardando configuração" e "Inativo (drenado)" importa** — visualmente podem parecer o mesmo estado ("não ativo, sem leads"), mas a origem é diferente: um nunca foi usado; o outro já foi e voltou a ficar vazio. Manter os rótulos distintos evita que o gestor confunda um Funil recém-criado com um que já teve um ciclo de vida inteiro.

_Não há transição de "Aguardando configuração" (ou qualquer outro status) para "excluído" dentro deste módulo — a exclusão, quando possível, é sempre externa (RN-FNL-09)._

---

# 7. Limites do módulo

**Não** inclui: o board kanban operacional, onde as leads são trabalhadas (módulo de Leads, CRM); a associação de Lojas a Funis (módulo de Lojas); a execução das automações (Huggy); nem o cadastro de Segmentos.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**

- **Segmentos** — o evento de criação de um Segmento, que dispara a criação automática do Funil correspondente (RN-FNL-02); e o evento de inativação/reativação do Segmento, que pode cascatear para o Funil (RN-SEG-06, RN-SEG-07).
- **Huggy** — FlowId dos fluxos e Id das tags; a automação executa lá (RN-FNL-04).

**Expõe para:**

- **Lojas** — a lista de Funis, para associação.
- **Leads (CRM)** — stages, fluxos e tags do funil da operação.
- **Cash** — custo e preço por lead entregue. É a aplicação que consome esses valores para cobrança das Lojas; não modelada neste documento.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

_Fim do documento. Funis de Vendas v0.5 — módulo da aplicação Admin, seção Vendas._
