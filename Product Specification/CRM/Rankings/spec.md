# Corban CRM · Rankings

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.5 | **Levantamento do CRM, decisões do usuário.** (1) O critério "Produção" passa a se chamar **Valor de meta** (era a mesma coisa que o Valor de meta, mas com o nome do valor base dos Relatórios). (2) Cada venda conta para o **Time e o Operador gravados no Pago** (RN-RNK-02); o Ranking de Times soma também as vendas de quem saiu. (3) **Nova RN-RNK-13:** só Membros e Times Ativos entram. (4) **Nova RN-RNK-14:** o Modo TV fica **inativo** sem Ranking marcado (fecha o item aberto). (5) A RN-RNK-03 deixa de ser inferência. (6) A RN-RNK-10, que estava dentro do §7, volta ao §3; corrige erros de texto e reordena o consolidado e este histórico. |
| 0.4 | **Remove o pareamento de TV por código — substitui por link único por Loja.** Decisão do usuário: pareamento atrapalhava mais a experiência do que entregava valor. Modo TV passa a ser acessado por uma URL com token longo, copiável no CRM; sem expiração, sem tela de "TVs pareadas". Gestor pode gerar um novo link (invalidando o antigo) se precisar revogar acesso. Remove RF-RNK-04/05 e RN-RNK-05/06; adiciona RF-RNK-04 (novo) e RN-RNK-12. |
| 0.3 | **Reformulação de fundo: Ranking vira entidade configurável, não mais fixo em "Top Membros"/"Top Times".** Gestor cria quantos Rankings quiser — Título, Tipo (Membros/Times), Critério (Produção/Quantidade/%Meta) e Cargos elegíveis (opcional, mesmo conceito de RN-MET-11). Modo TV passa a rotacionar entre os Rankings marcados "Exibir no Modo TV", em vez de fixo entre as duas visões anteriores. Período permanece fora do escopo configurável — registrado como possível evolução. |
| 0.2 | Fecha os 3 itens abertos: rotação do Modo TV fixada em **20 segundos** por visão (RN-RNK-07); ranking **permite empate** — Membros/Times com valor igual dividem a mesma posição, sem critério de desempate artificial (RN-RNK-09 nova); Modo TV e ranking normal mostram **estado vazio** quando não há Membros/Times suficientes, em vez de um ranking degenerado (RN-RNK-10 nova). |
| 0.1 | Versão inicial — módulo nunca tocado antes, pulado desde o início da especificação do CRM. Inclui o Modo TV, sugestão do usuário incorporada integralmente: rotação automática, pareamento por código, celebração ao bater meta. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-RNK) e regras de negócio (RN-RNK) próprios.

---

# 1. Objetivo do módulo

Permitir que o gestor **crie e mantenha seus próprios Rankings** — combinando Tipo (Membros ou Times), Critério (Valor de meta, Quantidade, % de atingimento de Meta) e, opcionalmente, um filtro de Cargo — e exiba qualquer um deles numa tela compartilhada da operação (**Modo TV**), sem necessidade de login.

---

# 2. Atores

- **Operador ou gestor** — consulta o ranking normalmente, dentro do CRM; o **gestor** também cria e mantém os Rankings e gera o link do Modo TV.
- **TV** — dispositivo sem login, exibindo o Modo TV ao abrir o link com token da Loja (RF-RNK-04).

---

# 3. Entidades e conceitos

## 3.1. Ranking (entidade persistida, configurável pelo gestor)

| Campo | Obrigatório | Notas |
|---|---|---|
| Título | Sim | Identifica o Ranking (ex.: "Top Vendedores do mês", "Melhores Times"). |
| Tipo | Sim, travado após a criação | Membros ou Times — RN-RNK-01. Mesmo critério de Tipo travado já usado em Meta (RN-MET-05), pela mesma razão: muda a natureza do que é listado. |
| Critério | Sim | **Valor de meta** (R$), Quantidade (nº de Propostas Pagas) ou % de atingimento de Meta — RN-RNK-02, RN-RNK-03. |
| Cargos elegíveis | Não, só em Tipo Membros | Filtra quais Cargos entram no Ranking — mesmo conceito de RN-MET-11 (módulo de Metas). Sem preenchimento, todos os Membros da Loja entram. |
| Exibir no Modo TV | Sim, com padrão | `Boolean`, padrão desligado. Controla se este Ranking participa da rotação do Modo TV (RN-RNK-07). |
| Período | — | **Fora do escopo desta versão** — sempre "mês atual", fixo, não configurável por Ranking. Registrado como evolução possível. |
| Escopo | — | Sempre uma Loja — RN-RNK-01. Implícito, não é campo. |

**RN-RNK-01 — Ranking é sempre por Loja, nunca cross-Loja.** Mesmo critério de isolamento já usado em todo o CRM — um gestor de Loja nunca vê o ranking de outra. Rankings criados por uma Loja não aparecem para outra, mesmo que o Título coincida.

**RN-RNK-02 — Valor de meta e Quantidade somam as Propostas Pagas no mês atual, pela atribuição gravada no Pago.** **Valor de meta:** soma do Valor de meta (valor base × multiplicador, RN-PRP-05) de cada Proposta que chegou a Pago no mês atual. **Quantidade:** contagem das mesmas Propostas. Cada venda conta para o **Time e o Operador gravados no Pago**; promoção, inativação ou troca de Time depois não a movem (RN-PRP-05). Por isso o Ranking de **Times** soma também as vendas de quem já saiu do Time; o de **Membros** lista só os **Ativos** (RN-RNK-13). O crédito é só do Operador (RN-PRP-16).

**RN-RNK-03 — % de atingimento usa a Meta Vigente mais recente do Membro/Time, do Tipo compatível.** *(Confirmado pelo usuário.)* Como um Membro ou Time pode ter várias Metas simultâneas, inclusive do mesmo Tipo (RN-MET-02, módulo de Metas), o ranking por este critério usa, para cada um, a Meta **Vigente mais recentemente criada** daquele Tipo (Valor ou Quantidade, conforme o Ranking foi configurado) — não soma nem faz média entre várias. Quem não tem nenhuma Meta Vigente daquele Tipo fica fora do ranking por este critério (não aparece com 0%).

**RN-RNK-11 — Cargos elegíveis filtra quem entra no Ranking, só em Tipo Membros.** Mesmo mecanismo de RN-MET-11 (módulo de Metas) — sem preenchimento, todos os Membros da Loja entram; com preenchimento, só quem tem um dos Cargos escolhidos aparece. Não se aplica a Tipo Times (Times não têm Cargo).

**RN-RNK-13 — Só Membros e Times Ativos entram.** No Ranking de Membros, um Membro **inativo** sai da lista (a venda dele continua no total do Time, RN-RNK-02); no de Times, só Times **Ativos** aparecem. *(Decisão do usuário, levantamento do CRM.)*

**RN-RNK-04 — Indicador de movimento compara com o fechamento do período anterior equivalente.** Setas ▲/▼/— ao lado da posição, comparando com a posição que aquele Membro/Time tinha no mesmo ranking (mesmo Tipo, mesmo Critério) no período imediatamente anterior (ex.: ranking do mês atual compara com o ranking do mês passado, fechado).

**RN-RNK-09 — Empate é permitido; sem critério de desempate.** Dois ou mais Membros/Times com exatamente o mesmo valor no Critério selecionado dividem a mesma posição (ex.: dois em 2º lugar, próximo já em 4º) — não há regra de desempate artificial por nenhum critério secundário.

**RN-RNK-10 — Estado vazio quando não há Membros/Times suficientes para um ranking de verdade.** Tanto o ranking normal (dentro do CRM) quanto o Modo TV mostram um estado vazio dedicado — não tentam montar um "ranking" de uma pessoa só, nem exibem a lista degenerada.

## 3.2. Link do Modo TV

| Campo | Notas |
|---|---|
| Token | Longo, não adivinhável, gerado automaticamente na primeira vez que a Loja acessa a tela de Rankings |
| URL | `.../tv/[token]` — é o próprio link que, aberto no navegador da TV, exibe o Modo TV daquela Loja |
| Vigência | Sem expiração — vale até ser regenerado |

**RN-RNK-12 — Link não expira; regenerar invalida o anterior.** O token não tem validade — o mesmo link funciona indefinidamente, sem precisar reabrir nada na TV. Gerar um novo link (ação manual do gestor) torna o anterior inválido imediatamente — útil se o link vazou ou a TV antiga não deve mais exibir os dados da Loja.

**RN-RNK-14 — Modo TV inativo sem Ranking marcado.** Quando nenhum Ranking tem "Exibir no Modo TV" ligado, o Modo TV fica **inativo**: o botão e o link ficam desabilitados no CRM, com a orientação de marcar "Exibir no Modo TV" em um Ranking; uma TV que já esteja com o link aberto mostra "Modo TV inativo" e volta sozinha assim que houver um Ranking marcado. *(Decisão do usuário, levantamento do CRM; fecha o item aberto §9.1.)*

---

# 4. Requisitos funcionais

## RF-RNK-01 — Criar e gerenciar Rankings

**Como** gestor, **eu quero** criar quantos Rankings eu precisar, combinando Tipo, Critério e Cargos elegíveis **para** comparar exatamente o recorte que fizer sentido para a minha operação (ex.: "só os Vendedores", "só os Times de INSS").

**Comportamento esperado:** o gestor cria um Ranking informando Título, Tipo (Membros ou Times), Critério e, se Tipo for Membros, Cargos elegíveis (opcional). Pode editar tudo depois, exceto o Tipo (travado — RN-RNK no §3.1). Pode excluir um Ranking a qualquer momento.

## RF-RNK-02 — Consultar um Ranking

**Como** operador ou gestor, **eu quero** abrir um Ranking já criado e ver a lista ordenada **para** acompanhar o desempenho do recorte que ele representa.

**Comportamento esperado:** lista ordenada pelo Critério do Ranking, com indicador de movimento (RN-RNK-04). Pódio visual para os 3 primeiros.

## RF-RNK-03 — Modo TV

**Como** gestor, **eu quero** uma tela própria para exibir o ranking numa TV da sala de operação **para** criar engajamento e visibilidade coletiva do desempenho do time.

**Comportamento esperado:** rota própria, sem sidebar nem header do shell — tela cheia, alto contraste, tipografia grande, pensada para leitura à distância. Alterna automaticamente entre os Rankings marcados "Exibir no Modo TV" (RN-RNK-07), com auto-atualização periódica dos dados. Quando algum Membro ou Time cruzar 100% de atingimento da própria Meta (critério % de Meta), uma celebração visual é exibida (RN-RNK-08).

**Regras específicas:**

**RN-RNK-07 — Rotação automática entre os Rankings marcados para TV, 20 segundos cada.** O Modo TV percorre, em sequência, todos os Rankings com "Exibir no Modo TV" ligado, 20 segundos cada, e repete o ciclo — sem interação do usuário. A ordem segue a ordem de criação dos Rankings (mais antigo primeiro). *(Sem Ranking nenhum marcado para TV, o Modo TV fica inativo — RN-RNK-14.)*

**RN-RNK-08 — Celebração ao bater 100% da Meta.** Quando o cálculo de % de atingimento (RN-RNK-03) de um Membro ou Time cruza 100% pela primeira vez dentro do período vigente da Meta, o Modo TV exibe um destaque visual (ex.: confete, highlight) — mostrado uma vez por Meta batida, não repetido a cada atualização subsequente.

## RF-RNK-04 — Link do Modo TV

**Como** gestor, **eu quero** copiar um link único da minha Loja e abri-lo na TV **para** não depender de login nem de digitar código com controle remoto.

**Comportamento esperado:** o CRM exibe o link do Modo TV da Loja (gerado automaticamente na primeira vez), com ação de copiar. Abrir esse link em qualquer navegador — inclusive o de uma Smart TV — exibe o Modo TV daquela Loja diretamente, sem login. O gestor também pode gerar um novo link (RN-RNK-12), invalidando o anterior.

---

# 5. Regras de negócio (consolidado)

- **RN-RNK-01** — Ranking é sempre por Loja, nunca cross-Loja.
- **RN-RNK-02** — Valor de meta e Quantidade somam Propostas Pagas no mês atual, pela atribuição gravada no Pago.
- **RN-RNK-03** — % de atingimento usa a Meta Vigente mais recente, do Tipo compatível (confirmado).
- **RN-RNK-04** — Indicador de movimento compara com o fechamento do período anterior equivalente.
- **RN-RNK-07** — Rotação automática entre os Rankings marcados para TV, 20 segundos cada.
- **RN-RNK-08** — Celebração visual ao cruzar 100% de atingimento, uma vez por Meta.
- **RN-RNK-09** — Empate é permitido; sem critério de desempate.
- **RN-RNK-10** — Estado vazio quando não há Membros/Times suficientes.
- **RN-RNK-11** — Cargos elegíveis filtra quem entra no Ranking, só em Tipo Membros.
- **RN-RNK-12** — Link do Modo TV não expira; gerar um novo invalida o anterior.
- **RN-RNK-13** — Só Membros e Times Ativos entram no Ranking.
- **RN-RNK-14** — Modo TV inativo quando nenhum Ranking está marcado.

---

# 6. Estados e transições

**Link do Modo TV:** gerado automaticamente na primeira vez — sem mais estados além de "ativo". Gerar um novo (RN-RNK-12) é a única transição: o link anterior some, o novo passa a valer, imediatamente.

**Meta, para fins de celebração (RN-RNK-08):** a celebração é um evento, não um estado persistido da Meta em si — o Modo TV precisa de alguma forma de saber "já mostrei a celebração desta Meta" para não repetir a cada atualização (implementação não detalhada aqui).

---

# 7. Limites do módulo

**Não** inclui: o cálculo de Metas e Propostas em si (módulos próprios — este só consome), **Período configurável por Ranking** (fixo em "mês atual" — registrado como evolução possível), nem personalização do tempo de rotação do Modo TV (fixo em 20 segundos, RN-RNK-07, não configurável).


---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Metas** (CRM) — Metas Vigentes e seu atingimento, para o critério % de Meta.
- **Propostas** (CRM) — Propostas Pagas, para Produção e Quantidade.
- **Membros, Times** (CRM) — a quem o ranking se refere.
- **Tabelas Multiplicadoras** (CRM) — o multiplicador que forma o Valor de meta (RN-PRP-05).

**Expõe para:**
- Nenhum outro módulo consome Rankings — é uma visão de consulta, terminal.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Rankings v0.5 — módulo da aplicação CRM.*
