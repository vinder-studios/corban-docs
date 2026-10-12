# Corban Admin · Layout de Lead (Card e Sheet)

**Status:** Draft
**Aplicação:** Admin
**Seção:** Vendas — 6ª aba do módulo Funis de Vendas
**Documento pai:** Corban Admin · Funis de Vendas (módulo v0.15)
**Versão:** 0.8
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha 3 dos 4 itens abertos: botão "Ações" **executa** Fluxo (Sheet de execução, RN-LAY-10) — revoga parte de RN-FNL-04 (Funis de Vendas v0.10); combobox de operador confirmado respeitando RN-LED-04; bloco Histórico confirmado obrigatório. Refina o bloco "Dados do servidor" com campo condicional (Estado ou Prefeitura, conforme o Órgão). Item 4 (campos de "Informações do contrato", Slot 3 do Card) segue aberto — a resposta recebida era sobre outro bloco. Introduz Server-Driven UI para o Card da Lead (board) e o Sheet de Detalhe — configurados pelo tenant, por Funil, a partir de um catálogo fechado de blocos. |
| 0.3 | Fecha o item 4 (último item aberto): Slot 3 do Card ("Informações do contrato") não é um bloco único — são **três variantes**, uma por Segmento (Servidor Público, INSS, CLT), das quais o tenant escolhe uma por Funil. A variante de Servidor Público reaproveita o mesmo bloco "Dados do servidor" já definido para o Sheet (RN-LAY-11). Módulo sem pendências. |
| 0.4 | *(Registro reconstruído na revisão da v0.5: o histórico original não o registrou.)* Substitui "Fluxo" por "Ação" na RN-LAY-10 e no botão "Ações" (Funis de Vendas v0.11: cinco tipos fixos de Ação). |
| 0.5 | **Padrões definidos**: o Slot 3 nasce como "Informações do beneficiário" (a mais genérica das três variantes) e o Sheet nasce com os oito blocos obrigatórios na ordem da tabela, sem opcionais (RN-LAY-03). **A Origem da lead passa a aparecer**: badge no Slot 4, que vira "Origem e tags", e primeiro campo de "Dados de Marketing" (RN-LAY-07). **RN-LAY-10 reduzida a um ponteiro para Leads** (RN-LED-11): a tabela de Ações por Stage que havia aqui divergia da de Leads e incluía Formalização, onde o operador não executa Ações (RN-LED-10). Corrige "sete" para **oito** blocos obrigatórios (consolidado e RF-LAY-02), o título da §3.6 ("fluxos" → botão "Ações"), o pai (Funis v0.11 → v0.15) e a menção a "duas dúvidas em aberto", quando o §9 está vazio. Reordena esta tabela. |
| 0.6 | **A Origem de Venda ganha lugar no Sheet**: campo editável do bloco Dados de Marketing, que antes não existia em nenhum bloco (o Combobox "na ficha da Lead" de Leads não tinha onde viver). A RN-LAY-07 deixa de manter a Origem de Venda "separada deste bloco". Decisão do usuário, seguindo a recomendação do levantamento do Admin. **O wireframe externo precisa acompanhar.** |
| 0.8 | **Origem dos dados dos blocos de perfil e das Notas:** vêm do Cliente (Clientes v0.13, RN-CLI-14 e RN-CLI-15). |
| 0.7 | **Acompanha o Marketing (Leads v0.11, RN-LED-16).** (1) O **Valor** do Card (Slot 2) e do Header do Sheet é o **valor potencial** da Lead, que nas Leads do Marketing é calculado pelo sistema e somente leitura. (2) O bloco **Ofertas** passa a mostrar também o **resumo das Ofertas elegíveis** da pré-análise, com os **requisitos não avaliados** (nova RN-LAY-13). (3) O bloco **Dados de Marketing** ganha a **Lista ou Varredura de origem** (só leitura, só o nome), e a Origem de Venda já pode vir preenchida (copiada da Lista ou Varredura). Decisão do usuário |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-LAY) e regras de negócio (RN-LAY) próprios. Vive como **6ª aba** da tela de Configuração do Funil (módulo de Funis de Vendas) — um layout por Funil, não um layout global do tenant.

> **Por que módulo próprio, e não só "mais uma aba descrita inline" em Funis de Vendas.** O catálogo de blocos e a mecânica de slots fixos (Card) e lista ordenável (Sheet) têm densidade própria — mesmo critério já usado para separar Regras de Ofertas.

---

# 1. Objetivo do módulo

Permitir que o tenant configure, por Funil, a composição do Card da Lead (board kanban) e do Sheet de Detalhe — escolhendo entre um catálogo fechado de blocos pré-construídos, sem bind livre a campos arbitrários.

---

# 2. Atores

- **Gestor comercial** — configura o layout, por Funil.
- **Operador, Gestor de Loja** — consomem o resultado (Card e Sheet) no CRM, sem editar o layout.

---

# 3. Entidades e conceitos

## 3.1. Princípio geral

**RN-LAY-01 — Grade fixa, conteúdo configurável.** Tanto o Card quanto o Sheet têm uma estrutura estrutural fixa (posições, seções, header) que o tenant **não** altera. O que é configurável é **qual bloco do catálogo ocupa cada posição configurável** — nunca a grade em si, nunca bind livre a um campo qualquer.

**RN-LAY-02 — Escopo é por Funil.** Cada Funil (Segmento) tem seu próprio layout de Card e de Sheet — dois Segmentos podem exibir composições completamente diferentes, já que os dados de contrato variam por público.

## 3.2. Layout do Card

Grade fixa, com seis **slots configuráveis** (um bloco cada) e um Footer **totalmente fixo**, sem slot:

| Região | Slot | Bloco padrão (RN-LAY-03) |
|---|---|---|
| Header, coluna direita | 1 | Identificação — Nome, CPF, Data da última movimentação |
| Header, coluna esquerda | 2 | Valor e Status — o Valor é o **valor potencial** da Lead (RN-LAY-13) |
| Body, Seção 1, coluna 1 | 3 | Informações do contrato — **três variantes por Segmento** (RN-LAY-12); padrão: **Informações do beneficiário** |
| Body, Seção 1, coluna 2 | 4 | Origem e tags — badge da **Origem da lead** (RN-FNL-06) seguido das Tags (badges) |
| Body, Seção 2, linha 1 | 5 | Seleção de operador (combobox) — **restrito aos elegíveis por RN-LED-04**, confirmado |
| Body, Seção 2, linha 2 | 6 | Última mensagem recebida sem resposta |
| Footer | — (fixo, sem slot) | Botão "Ações" (executa Ação, RN-LAY-10) + Botão "Chat" |

**RN-LAY-03 — Estrutura descrita é o default de fábrica.** Todo Funil novo nasce com os seis slots preenchidos exatamente como na tabela acima — o tenant pode trocar o bloco de cada slot pelo catálogo (§3.4), mas nunca remover um slot nem adicionar um novo. No Slot 3, que tem três variantes (RN-LAY-12), o padrão é **Informações do beneficiário**, a mais genérica (Órgão e Matrícula): o sistema não tem como saber o público do Segmento, cujo nome é livre, e o gestor troca a variante quando precisar. O **Sheet** nasce com os **oito blocos obrigatórios, na ordem da tabela da RN-LAY-05**, sem blocos opcionais. *(Decisão do usuário; a variante padrão é escolha desta revisão.)*

**RN-LAY-04 — Footer não é configurável.** Os dois botões (Ações, Chat) existem em todo Card, sempre, sem exceção — não fazem parte do catálogo nem de nenhum slot.

## 3.3. Layout do Sheet

Header **fixo, sem slot** (Nome + Sobrenome + ID à esquerda, Valor potencial à direita) + Body como **lista ordenável de blocos** (accordions) — adicionar, remover e reordenar livremente, respeitando os obrigatórios.

**RN-LAY-05 — Blocos obrigatórios não podem ser removidos.** Oito blocos são obrigatórios em todo Sheet, não removíveis pelo tenant:

| Bloco obrigatório | Conteúdo |
|---|---|
| Dados pessoais | Nome completo, CPF, Telefone |
| Dados financeiros | Rendimentos, margem |
| Ofertas | Ofertas em consideração na negociação, com indicação de qual foi selecionada (RN-PRP-07, módulo de Propostas). Nas Leads do Marketing, mostra também o **resumo das Ofertas elegíveis** da pré-análise (RN-LAY-13) |
| Documentos do Cliente | Documentos associados ao Cliente na base — aparecem em toda negociação dele (RN-LAY-06) |
| Documentos da Proposta | Documentos específicos desta negociação — não aparecem em outras (RN-LAY-06) |
| Dados de Marketing | **Origem da lead** (só leitura), **Lista ou Varredura de origem** (só leitura, só o nome; nas Leads de Prospecção e Carteira Ativa), **Origem de Venda** (editável, Combobox criável; nas Leads do Marketing já vem preenchida, se a Lista ou Varredura tinha), fonte, campanha, conteúdo — da Lead que abriu o Sheet, não do Cliente (RN-LAY-07) |
| Dados adicionais | Id do contato, Id do chat |
| Histórico | Ação executada, data, quem executou — obrigatório, confirmado |

**RN-LAY-06 — Documentos dividem-se por dono: Cliente ou Proposta.** Um documento anexado ao **Cliente** aparece em todas as negociações dele, presentes e futuras. Um documento anexado à **Proposta** é exclusivo daquela negociação. Dois blocos separados, nunca um só — mistura os dois seria perder essa distinção.

**RN-LAY-07 — "Dados de Marketing" é da Lead, não do Cliente.** Diferente da maioria dos blocos (que mostram o histórico agregado do Cliente através de todas as suas Leads), este mostra fonte/campanha/conteúdo **especificamente da Lead que abriu este Sheet** — uma campanha não é propriedade permanente da pessoa. *(Não confundir com três coisas de nome parecido — RN-FNL-06: a Tag de categoria Origem, mantida separada deste bloco por ora; a **Origem da lead**, que **entra** neste bloco como primeiro campo, só de leitura, por ser dado da própria Lead definido pelo sistema (RN-LED-12); e a **Origem de Venda**, que também **entra** neste bloco, como o **único campo editável** dele (Combobox criável, módulo de Origem de Venda); "Dados de Marketing" pode vir a substituir as Tags de Origem no futuro, quando a captura automática desses dados estiver confiável — nota de evolução, não implementado agora.)*

**RN-LAY-13 — As Leads do Marketing mostram o valor potencial e o resumo das Ofertas, em modo de referência.** Os dados vêm da pré-análise do Marketing e são gravados na injeção, somente leitura (Leads, RN-LED-16):
- **Valor potencial.** É o **Valor** do Slot 2 do Card e do Header do Sheet. Na Lead manual é o valor digitado pelo operador; na Lead do Marketing é o maior valor pré-aprovado entre as Ofertas elegíveis, rotulado como **estimativa**, e não como valor de Proposta.
- **Resumo das Ofertas elegíveis.** Fica no bloco **Ofertas** do Sheet, numa área **"Pré-análise do Marketing"**, separada das Ofertas da Simulação. Lista as Ofertas elegíveis e, destacados, os **requisitos não avaliados** (campos que a base não trouxe), com a orientação de confirmá-los na primeira Simulação. Traz o aviso de que **não garante aprovação nem condição** (Marketing, RN-MKT-08). Não é Simulação e permanece visível depois que o operador faz a primeira.
- **Lista ou Varredura de origem.** Mostra só o **nome**, sem link: a Loja não acessa o Marketing, que é da gestão do tenant.
- Esses itens aparecem só nas Leads de Origem Prospecção ou Carteira Ativa; nas demais, a área "Pré-análise do Marketing" não é exibida.

*(Decisão do usuário, levantamento do Marketing.)*

**Blocos opcionais** (removíveis, fora dos oito acima): Dados do servidor, Dados Trabalhistas (admissão, tempo empregado, matrícula, elegibilidade), Dados da Empresa (nome, CNPJ, idade, nº funcionários, CNAE + descrição), Notas. Os dados de Dados financeiros, Dados do servidor, Dados Trabalhistas e Dados da Empresa vêm do **perfil do Cliente** (Clientes, RN-CLI-14).

**RN-LAY-11 — Dados do servidor tem campo condicional.** O bloco exibe **Órgão** sempre, e um segundo campo que depende do tipo de Órgão: **Estado** para Órgãos federais ou estaduais; **Prefeitura** para Órgãos municipais. Nunca os dois ao mesmo tempo — é um ou outro, conforme o tipo.

**RN-LAY-08 — Notas é uma lista de links, cada um abrindo um Dialog.** O accordion Notas lista as anotações do Cliente (Clientes, RN-CLI-15), com a opção de registrar uma nova; clicar num link abre Dialog com o texto completo e a data da anotação — não expande inline.

**RN-LAY-09 — Todo trigger de accordion segue o mesmo padrão visual.** Ícone + rótulo à esquerda, seta indicadora à direita — consistente em todos os blocos, obrigatórios ou não.

## 3.4. Catálogo de blocos

Fechado — o tenant escolhe entre blocos pré-construídos, nunca define um novo campo nem faz bind livre. Cada bloco já sabe de onde tirar seu próprio dado (Lead, Cliente ou Proposta, conforme o caso).

## 3.5. Slot 3 do Card — três variantes de "Informações do contrato"

**RN-LAY-12 — O bloco do Slot 3 varia por Segmento; o tenant escolhe uma das três variantes por Funil.** Diferente dos demais slots (um bloco fixo cada), o Slot 3 oferece três opções, cada uma pensada para um dos públicos que o Corban atende:

| Variante | Campos | Segmento típico |
|---|---|---|
| **Informações do servidor** | Órgão + Estado ou Prefeitura (condicional, conforme o tipo de Órgão) | Servidor Público |
| **Informações do beneficiário** | Órgão, Matrícula | INSS |
| **Informações do Trabalhador** | Empresa, Tempo de contrato | CLT |

**"Informações do servidor" é o mesmo bloco de "Dados do servidor" (Sheet, RN-LAY-11), reaproveitado aqui** — mesma definição, dois lugares de uso (Card e Sheet), sem duplicar a regra do campo condicional.

## 3.6. Botão "Ações"

**RN-LAY-10 — O botão "Ações", no Footer do Card, abre o Sheet de execução de Ações, definido em Leads.** O Sheet de execução é **tela própria do módulo de Leads** (RN-LED-11), que também define, num só lugar, quais Ações aparecem para cada Stage da Lead. Este módulo só garante que o botão existe em todo Card (RN-LAY-04). As instâncias das Ações são configuradas em Funis de Vendas (RN-FNL-19). *(Reduzida na v0.5: a versão anterior repetia aqui a tabela de Ações por Stage, que divergia da de Leads — incluía Formalização, onde o operador já não executa Ações, RN-LED-10.)*

---

# 4. Requisitos funcionais

## RF-LAY-01 — Configuração do Card (6ª aba do Funil)

**Como** gestor comercial, **eu quero** escolher qual bloco ocupa cada slot do Card **para** que o board mostre a informação mais relevante para o meu Segmento.

**Comportamento esperado:** seis seletores, um por slot (RN-LAY-03), cada um oferecendo os blocos do catálogo compatíveis com aquela posição. Footer não aparece como editável (RN-LAY-04).

## RF-LAY-02 — Configuração do Sheet

**Como** gestor comercial, **eu quero** montar a composição do Sheet de Detalhe, adicionando/removendo/reordenando blocos **para** priorizar a informação que meu time mais usa.

**Comportamento esperado:** lista ordenável de blocos, com os oito obrigatórios (RN-LAY-05) sempre presentes e não removíveis, e os demais do catálogo disponíveis para adicionar.

---

# 5. Regras de negócio (consolidado)

- **RN-LAY-01** — Grade fixa; só o conteúdo dos slots é configurável.
- **RN-LAY-02** — Escopo por Funil, não global.
- **RN-LAY-03** — Estrutura descrita é o default de fábrica do Card; Slot 3 nasce como "Informações do beneficiário", e o Sheet nasce com os oito obrigatórios.
- **RN-LAY-04** — Footer do Card não é configurável.
- **RN-LAY-05** — Oito blocos obrigatórios no Sheet, não removíveis.
- **RN-LAY-06** — Documentos dividem-se por dono: Cliente ou Proposta.
- **RN-LAY-07** — Dados de Marketing é da Lead, não do Cliente; inclui a Origem da lead e a Lista ou Varredura de origem (só leitura) e a Origem de Venda (único campo editável do bloco).
- **RN-LAY-08** — Notas é lista de links, cada um abrindo Dialog.
- **RN-LAY-09** — Trigger de accordion segue padrão visual único.
- **RN-LAY-10** — Botão "Ações" abre o Sheet de execução, definido em Leads (RN-LED-11).
- **RN-LAY-11** — Dados do servidor tem campo condicional (Estado ou Prefeitura, conforme o Órgão).
- **RN-LAY-12** — Slot 3 do Card tem três variantes por Segmento; tenant escolhe uma por Funil.
- **RN-LAY-13** — As Leads do Marketing mostram o valor potencial (Slot 2 e Header) e o resumo das Ofertas (bloco Ofertas), em modo de referência, somente leitura.

---

# 6. Estados e transições

Sem estados de publicação — a configuração de layout existe desde a criação do Funil (com o default, RN-LAY-03) e é sempre editável.

---

# 7. Limites do módulo

**Não** inclui: o conteúdo interno de cada bloco em detalhe fino (fica para a Descrição de Telas, quando este módulo chegar à Fase 2), bind livre a campos arbitrários (deliberadamente fora de escopo — catálogo fechado), nem a execução do que os blocos exibem (cada um consome dado já modelado em outro módulo — Leads, Clientes, Propostas, Chat).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Funis de Vendas** — é a 6ª aba daquele módulo; um layout por Funil.
- **Leads, Clientes, Propostas, Chat** — cada bloco do catálogo consome dado de um desses módulos.

**Expõe para:**
- **Leads (CRM)** — a composição do Card, renderizada no board.
- **Clientes / Propostas (CRM)** — a composição do Sheet, renderizada a partir do card da Lead.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Layout de Lead (Card e Sheet) v0.8 — módulo da aplicação Admin, 6ª aba de Funis de Vendas.*
