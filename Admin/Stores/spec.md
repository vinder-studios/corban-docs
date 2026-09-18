# Corban Admin · Lojas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Vendas
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                                                        |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                                                                                                                 |
| 0.2    | Corrige os campos do Time: **Nome** e **Segmento** apenas — "Loja" não é um campo editável, é o vínculo de criação, e é **imutável** (RN-LOJ-10). Fecha os itens abertos 1 e 2 da v0.1: exclusão de Loja **exclui os Times em cascata**, confirmado; Time **não pode ser transferido de Loja**. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-LOJ) e regras de negócio (RN-LOJ) próprios. Cobre **Loja** e **Time**, que vivem na mesma tela de configuração.

> **Este documento substitui e revoga `corban-anotacoes-operacoes-loja.md`.** Aquele documento antecipava um modelo de "Operação-instância por Loja" que nunca chegou a existir — hoje sabemos que quem carrega o pipeline é o **Funil** (tenant-wide, 1:1 com Segmento, módulo Funis de Vendas), e é nele que Loja e Time se associam, não numa instância própria por Loja. O documento de anotações fica marcado como superado.

> **Dependência cruzada pendente:** este módulo introduz **exclusão definitiva de usuário (soft-delete)**, na cascata de exclusão de Loja (RN-LOJ-11). Isso contradiz RN-USR-05 do módulo de Usuários & Permissões, que hoje afirma "exclusão definitiva de usuário não existe na v1". Aquele módulo precisa ser atualizado — não feito neste documento.

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

| Campo    | Obrigatório     | Notas                                                                                                                                                                                                                                                                                                                                             |
| -------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome     | Sim             | —                                                                                                                                                                                                                                                                                                                                                 |
| Código   | Sim             | Identificador curto. Único no tenant, formato livre, editável após a criação — mesmo padrão dos demais cadastros.                                                                                                                                                                                                                                 |
| Endereço | Sim             | —                                                                                                                                                                                                                                                                                                                                                 |
| CNPJ     | Não             | Opcional nesta primeira versão.                                                                                                                                                                                                                                                                                                                   |
| Logo     | Não             | Imagem.                                                                                                                                                                                                                                                                                                                                           |
| Regiões  | Sim, com padrão | Zero ou mais Regiões (módulo de Regiões). **Nasce com o valor explícito "Todas"** — um chip visível no campo, não um campo vazio — que corresponde à ausência de restrição regional (RN-REG-04, módulo de Regiões: Loja sem Região vê todas as ofertas). Mostrar "Todas" de forma explícita, e não em branco, evita que o campo pareça esquecido. |
| Status   | Sim             | Ativa / Inativa (ver §6).                                                                                                                                                                                                                                                                                                                         |

## 3.2. Aba Segmentos (da Loja)

Não é uma entidade própria — é a associação entre a Loja e cada Segmento do tenant, com duas flags independentes, mas com dependência entre si:

| Flag             | O que controla                                           |
| ---------------- | -------------------------------------------------------- |
| **Ativo**        | O Segmento aparece como operado por esta Loja.           |
| **Recebe leads** | A Loja efetivamente recebe leads novas daquele Segmento. |

**RN-LOJ-01 — "Recebe leads" exige "Ativo".** Não é possível marcar "Recebe leads" para um Segmento que não está "Ativo" nesta Loja — não faria sentido a Loja receber leads de um Segmento que ela nem opera. A interface impede a combinação inválida (desabilita "Recebe leads" até "Ativo" estar marcado), em vez de validar depois.

## 3.3. Time

| Campo    | Obrigatório | Notas                                                                                                                                                                                          |
| -------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome     | Sim         | —                                                                                                                                                                                              |
| Segmento | Não         | No máximo um. Restrito aos Segmentos que a própria Loja tem **"Ativo" e "Recebe leads"** marcados (RN-LOJ-01, RN-LOJ-02) — um Time não pode escolher um Segmento que a Loja não opera de fato. |
| Status   | Sim         | Ativo / Inativando / Inativo (ver §6).                                                                                                                                                         |

**A Loja não é um campo do Time.** É o contexto em que o Time é criado (a Aba Times só existe dentro da configuração de uma Loja) — não um campo editável no formulário, e **imutável** (RN-LOJ-10): um Time nunca muda de Loja depois de criado.

**RN-LOJ-02 — O Segmento do Time é o que dá acesso ao Funil.** Um Time com Segmento selecionado acessa o Funil daquele Segmento (via a relação 1:1 Segmento↔Funil, já fechada no módulo de Funis de Vendas). Um Time **sem** Segmento existe normalmente, mas não acessa nenhum Funil — útil para times administrativos ou de suporte que não trabalham leads diretamente.

## 3.4. Escopos `store`, `team` e `self`

Os três escopos usados em todo o catálogo de permissões do produto (além de `all`) se ancoram nesta estrutura:

| Escopo  | Alcance                                               |
| ------- | ----------------------------------------------------- |
| `store` | Todos os dados da Loja inteira — todos os Times dela. |
| `team`  | Só os dados do Time do usuário.                       |
| `self`  | Só os dados do próprio usuário.                       |

Afetam **visibilidade de leads, pipeline e qualquer outro recurso do sistema** cujo catálogo de permissões ofereça esses escopos — a aplicação concreta é decidida em cada módulo consumidor (Leads, Simulações, etc.), não aqui. Este módulo só define a estrutura (Loja, Time) da qual esses escopos derivam.

---

# 4. Requisitos funcionais

## RF-LOJ-01 — Gestão de lojas (CRUD)

**Como** gestor de estrutura, **eu quero** criar, consultar, editar e inativar Lojas **para** refletir as unidades de venda do tenant.

Tela de configuração com três abas: **Informações gerais**, **Segmentos**, **Times**.

## RF-LOJ-02 — Ciclo de vida do Time

**RN-LOJ-03 — Inativar um Time tem dois modos.** Ao inativar, o Time para de receber itens novos (leads, atendimentos). O gestor escolhe entre:

- **Inativação imediata** — exige redistribuir, na hora, todos os itens ativos daquele Time para outro Time **da mesma Loja**.
- **Inativação após finalizar** — o Time entra em estado **Inativando**: para de receber itens novos, mas os que já estão em andamento seguem até o fim. Quando o último for concluído, o Time passa a **Inativo** automaticamente.

**RN-LOJ-04 — Reativar durante a drenagem cancela a inativação agendada.** Se o gestor reativa um Time que está em **Inativando**, ele volta a **Ativo** e recomeça a receber itens novos — a inativação automática que estava agendada é cancelada, não só adiada.

**RN-LOJ-05 — Excluir um Time.** O gestor escolhe entre migrar os membros do Time para outro Time (da mesma Loja), ou, se preferir não migrar, precisa redistribuir os itens ativos daquele Time para outro — mesmo mecanismo de RN-LOJ-03.

## RF-LOJ-03 — Ciclo de vida da Loja

**RN-LOJ-06 — Inativar uma Loja.** Ela para de receber itens novos (leads, atendimentos) em todos os seus Times. Diferente do Time, não há dois modos aqui — é sempre "para de receber novos, os em andamento seguem".

**RN-LOJ-07 — Excluir uma Loja é bloqueado enquanto houver itens ativos.** O gestor precisa inativar primeiro e aguardar os processos em andamento se encerrarem, em qualquer Time da Loja, antes de poder excluí-la.

**RN-LOJ-08 — Exclusão da Loja oferece excluir os usuários exclusivos dela.** Ao excluir, a interface pergunta se o gestor quer excluir também os usuários que estão associados **apenas** a esta Loja (via Cargo/Time, considerando que um usuário tem no máximo um Time — módulo de Usuários & Permissões). Se sim, exibe a lista completa desses usuários, pré-marcada, para o gestor confirmar ou desmarcar individualmente antes de aplicar. **Exclusão de usuário aqui é soft-delete**: some das listagens do sistema, mas o registro permanece no banco — contradiz RN-USR-05, que precisa ser atualizada (ver nota no topo deste documento).

**RN-LOJ-09 — Excluir a Loja exclui os Times dela em cascata.** Confirmado. Como a exclusão da Loja já exige que não haja itens ativos em nenhum Time (RN-LOJ-07), os Times dela estão necessariamente vazios de trabalho no momento da exclusão.

**RN-LOJ-10 — O Time não pode ser transferido de Loja.** A Loja é definida na criação do Time e nunca muda — não existe ação de "mover Time para outra Loja" em nenhuma tela do produto.

---

# 5. Regras de negócio (consolidado)

- **RN-LOJ-01** — "Recebe leads" exige "Ativo" no mesmo Segmento.
- **RN-LOJ-02** — Segmento do Time dá acesso ao Funil correspondente; sem Segmento, sem Funil.
- **RN-LOJ-03** — Inativar Time: imediata (com redistribuição) ou após finalizar (drenagem automática).
- **RN-LOJ-04** — Reativar Time em drenagem cancela a inativação agendada.
- **RN-LOJ-05** — Excluir Time: migra membros ou redistribui itens.
- **RN-LOJ-06** — Inativar Loja para recebimento de itens novos, sem modo imediato/drenagem — só um comportamento.
- **RN-LOJ-07** — Excluir Loja bloqueado com itens ativos em qualquer Time.
- **RN-LOJ-08** — Exclusão da Loja oferece soft-delete dos usuários exclusivos dela.
- **RN-LOJ-09** — Exclusão da Loja cascade-exclui os Times.
- **RN-LOJ-10** — Time não pode ser transferido de Loja; imutável após a criação.

---

# 6. Estados e transições

**Loja:**

- **Ativa** — opera Segmentos, recebe leads conforme a Aba Segmentos.
- **Inativa** — não recebe itens novos em nenhum Time.
- **Excluída** — bloqueada enquanto houver item ativo (RN-LOJ-07); hard delete (diferente do soft-delete dos usuários associados).

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

- **Usuários & Permissões** — a lista de Times, para o campo Time do usuário; os escopos `store`/`team` derivam desta estrutura.
- **Leads / pipeline (CRM)** — quais Funis cada Time efetivamente alcança.

---

# 9. Itens abertos

1. **Atualização de RN-USR-05** (módulo de Usuários & Permissões) para incorporar exclusão definitiva (soft-delete), introduzida aqui via RN-LOJ-08 — pendência cross-módulo, não resolvida neste documento.
2. **`corban-anotacoes-operacoes-loja.md` marcado como superado** — o modelo de Operação-instância por Loja que ele antecipava não se concretizou; este módulo é a fonte de verdade agora.

---

_Fim do documento. Lojas v0.2 — módulo da aplicação Admin, seção Vendas._
