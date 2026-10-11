# Corban CRM · Dashboard

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.4
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.4 | **Renomeia o prefixo das regras de `RN-DSH` para `RN-DSC`** (Dashboard do CRM): o Dashboard do Admin usa `RN-DSH`, e os dois módulos tinham regras com o mesmo identificador (RN-DSH-01 a 04). Mesmo padrão de `RN-RLC` (Relatórios do CRM). Sem mudança de conteúdo; renomeado também nas referências do módulo e de dois artefatos. |
| 0.1 | Versão inicial. |
| 0.3 | Fecha o item aberto: escolha de período no gráfico **não persiste** — sempre volta ao padrão (6) a cada carregamento do Dashboard, confirmado. |
| 0.2 | Fecha os 2 itens abertos: gráfico de evolução vira **configurável** (3/6/12 períodos, padrão 6); ordem/presença dos blocos confirmada como **fixa** nesta versão — personalização vira nota de evolução. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-DSH) e regras de negócio (RN-DSH) próprios.

---

# 1. Objetivo do módulo

Tela inicial do CRM — conjunto de blocos, cada um condicionado por permissão (mesma filosofia já usada em todo o produto, sem telas fixas por "papel"), reunindo carteira de Leads, resultados próprios do operador, progresso de Meta e alertas financeiros. Sem entidade própria — consome dados já existentes em outros módulos.

---

# 2. Atores

- **Qualquer usuário autenticado do CRM** — vê os blocos que suas permissões habilitam. Sem permissão nenhuma dos módulos de origem, o bloco correspondente simplesmente não aparece.

---

# 3. Blocos

## 3.1. Minha carteira

Contagem de Leads atribuídas ao usuário, agrupadas por Stage do Funil — com destaque às que estão "paradas" (critério já definido em Leads/Funis de Vendas, não redefinido aqui). Atalho para o Board, já filtrado por "minhas leads".

**RN-DSC-01 — "Minha carteira" exige `leads:view` em algum escopo.** Sem nenhuma permissão de ver Leads (nem `self`), o bloco não aparece.

## 3.2. Meus Resultados

Produção (R$) e Quantidade (nº) de Propostas Pagas do próprio usuário, no período atual — com indicador de comparação (▲/▼/—) contra o período anterior, mesma lógica já usada em Rankings (RN-RNK-04, módulo de Rankings, não redefinida aqui). Inclui um **gráfico de evolução** (linha), últimos 6 períodos, para Produção e Quantidade.

**RN-DSC-02 — "Meus Resultados" exige `proposals:view` em algum escopo.** Mostra sempre os dados do próprio usuário (`self`), independentemente do escopo mais amplo que ele tenha.

**RN-DSC-03 — Gráfico de evolução é configurável entre 3, 6 ou 12 períodos, com 6 como padrão.** Mesma granularidade de período já usada em Metas/Rankings (RN-MET-04) — tipicamente mensal. O usuário escolhe quantos períodos ver; a escolha **não persiste** — confirmado: sempre volta ao padrão (6) a cada carregamento do Dashboard.

## 3.3. Minha Meta

Lista de Metas Vigentes do usuário (como Membro) — Título, Tipo, indicador de progresso (atingido vs. valor-alvo), Prazo. Mesmo formato de item já usado na lista de Metas (módulo de Metas, não redefinido aqui). Bloco inteiro some quando o usuário não tem nenhuma Meta Vigente.

**RN-DSC-04 — "Minha Meta" exige `goals:view` em algum escopo, e só aparece com ao menos uma Meta Vigente.** Mostra **todas** as Metas Vigentes do usuário simultaneamente (não só a mais próxima do Prazo) — mesmo critério de "lista, não slot único" já fixado no módulo de Metas (RN-MET-02).

## 3.4. Alerta de Créditos

Saldo atual da Loja, com destaque quando baixo ou negativo, e indicador de proximidade do Limite de gasto diário (módulo de Créditos, não redefinido aqui) — cumprindo o compromisso já registrado naquele módulo (item aberto §9.3 de Créditos v0.1, resolvido aqui).

**RN-DSC-05 — "Alerta de Créditos" exige `credits:view:store`.** Só aparece para quem tem visão financeira da Loja — mesma permissão que já controla a tela de Créditos.

## 3.5. Atalhos rápidos

Botões de ação direta: "Nova lead" (RF-LED-02, módulo de Leads) e "Nova simulação" (Página de Simulação, módulo de Simulações).

**RN-DSC-06 — Cada atalho exige a permissão de criação do módulo de origem.** "Nova lead": `leads:create:store`. "Nova simulação": `simulations:create` em algum escopo. Atalho sem a permissão correspondente simplesmente não aparece.

---

# 4. Requisitos funcionais

## RF-DSH-01 — Exibir Dashboard

**Como** qualquer usuário do CRM, **eu quero** ver, ao entrar no sistema, um resumo do que importa para mim **para** começar o dia sem precisar navegar por vários módulos.

**Comportamento esperado:** tela inicial do CRM (pós-login). Renderiza os blocos de §3 conforme as permissões efetivas do usuário — nenhum bloco fixo, nenhuma ordem obrigatória além da já descrita.

---

# 5. Regras de negócio (consolidado)

- **RN-DSC-01** — Minha carteira exige `leads:view` em algum escopo.
- **RN-DSC-02** — Meus Resultados exige `proposals:view` em algum escopo; sempre `self`.
- **RN-DSC-03** — Gráfico de evolução cobre 6 períodos, fixo.
- **RN-DSC-04** — Minha Meta exige `goals:view`; só aparece com Meta Vigente; mostra todas, não só a mais próxima.
- **RN-DSC-05** — Alerta de Créditos exige `credits:view:store`.
- **RN-DSC-06** — Atalhos exigem a permissão de criação do módulo de origem.

---

# 6. Estados e transições

Sem ciclo de vida de entidade — todos os blocos são leitura computada de dados que já existem em outros módulos.

---

# 7. Limites do módulo

**Não** inclui: o cálculo de Leads/Propostas/Metas/Créditos em si (módulos próprios — este só consome), nem a prévia de um Ranking no Dashboard (deixado de fora desta rodada por decisão do usuário).

> **Nota de evolução.** Ordem e presença dos blocos são fixas nesta versão (confirmado pelo usuário) — permitir que cada um reordene ou oculte blocos individualmente fica registrado como evolução futura, fora de escopo agora.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Leads** (CRM) — contagem por Stage, critério de "parada" (Minha carteira).
- **Propostas** (CRM) — Propostas Pagas, Produção/Quantidade (Meus Resultados).
- **Metas** (CRM) — Metas Vigentes do usuário (Minha Meta).
- **Créditos** (CRM) — Saldo, Limite de gasto diário (Alerta de Créditos).
- **Simulações** (CRM) — atalho de criação.

**Expõe para:**
- Nenhum outro módulo consome Dashboard — é uma visão terminal, igual Rankings.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Dashboard v0.4 — módulo da aplicação CRM.*
