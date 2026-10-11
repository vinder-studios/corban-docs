# Corban Admin · Relacionamentos e Regras de Negócio

**Status:** Draft
**Aplicação:** Admin
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.4
**Data:** 10/10/2026

## Histórico de revisões

| Versão | Mudanças |
| ------ | -------- |
| 0.1 | Versão inicial. |
| 0.2 | Revisão completa com base nos specs de módulo. Corrige Segmento↔Funil (1:1 obrigatório), Loja↔Time (1:N) e as regras de bloqueio de Região, Promotora e Status de Proposta. |
| 0.3 | Corrige Região → Promotora/Loja e Regra (inativação). |
| 0.4 | **Reescrita sobre os módulos atuais**, que evoluíram depois da v0.3 (levantamento do Admin, segunda passada). Preço de Lead passa a **quatro linhas por Origem da lead**, com histórico append-only; **Fluxo sai e entram as Ações** (Funis v0.17); Stage com os tipos Novo, Negociação, Formalização, Pago e Cancelado e cardinalidade por tipo; **Operação com 4 valores** (Compra de Dívida absorvida por Portabilidade); Loja→Segmento com **tipos de lead por Origem**; Oferta→Segmento; Usuário, Cargo, Layout de Lead e Origem de Venda incluídos. As divergências registradas na v0.2 e v0.3 (Promotora, Região, Regra) foram superadas pelas revisões dos próprios módulos e saem daqui: **vale o módulo**. Caminhos passam a `Product Specification/Admin/<Módulo>/spec.md`. Cargo conforme Cargos v0.4. As entidades do CRM e do Marketing têm documentos próprios. |

## Como usar este documento

Visão consolidada das relações entre as entidades do Admin. Cada relação remete ao módulo dono da regra (`Product Specification/Admin/<Módulo>/spec.md`). Não substitui os documentos de módulo; existe para dar a leitura rápida do grafo e servir de base ao modelo de dados. **Em caso de divergência, vale o módulo.**

Lead, Cliente, Simulação, Proposta e Créditos estão em `CRM/Relacionamentos e Regras de Negócio.md`; Lista, Varredura e Lista de bloqueio, em `Marketing/Relacionamentos e Regras de Negócio.md`.

---

# 🔐 Cargo

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Cargo → Linha de permissão** | `1 : N` | Zero ou mais, no formato `recurso:ação:escopo` (spec §5). Mudança vale imediatamente (RN-CRG-02). |
| **Cargo → Usuário** | `1 : N` | 🔒 Cargo em uso não se exclui sem migrar os usuários para outro Cargo (RN-CRG-01); nenhum usuário fica sem Cargo (RN-USR-01). Inativo não recebe novas atribuições e mantém as existentes. "Em uso" é derivado da contagem, não é estado. |
| **Cargo** | — | Status Ativo/Inativo; nome único (RN-CRG-05). Toda mudança, inclusive nas linhas, é auditada (RN-CRG-03). `roles:manage_permissions:all` concede qualquer acesso, inclusive a si mesmo: decisão consciente (RN-CRG-04). Admin é um Cargo como outro, não papel de sistema. |

---

# 👤 Usuário

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Usuário → Cargo** | `N : 1` | Obrigatório e único (RN-USR-01). Owner é protegido: único, bypass total, criado na implantação (RN-USR-03). |
| **Usuário → Permissão adicional** | `1 : N` | Somam ao Cargo, nunca subtraem (RN-USR-13). |
| **Usuário → Loja** | `N : 0..1` | Vínculo direto, independente do Time; se há Time, a Loja é a dele (RN-USR-15). |
| **Usuário → Time** | `N : 0..1` | Opcional e único; sem Time, o escopo `team` não alcança nada (RN-USR-02). "Pode receber lead" só vale com Time (RN-USR-14). |
| **Usuário → Identidade no Keycloak** | `1 : 1` | Realm único; a Loja do usuário é uma Organização (ADR-0001). |
| **Usuário** | — | E-mail único (RN-USR-04). Inativar preserva o histórico, derruba sessões e exige redistribuir as Leads em andamento; exclusão só em cascata da exclusão da Loja, soft-delete (RN-USR-05). Gestão de acesso auditada (RN-USR-17). |

---

# 🏪 Loja

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Loja → Time** | `1 : N` | Time imutável de Loja (RN-LOJ-10). 🗑️ Excluir a Loja exclui os Times em cascata, soft-delete (RN-LOJ-09); só é possível sem itens ativos em nenhum Time (RN-LOJ-07). |
| **Loja → Segmento** | `N : N` (associação com dados) | Não é pertencimento: flag **Ativo** e um interruptor por **Origem da lead** (Campanha, Orgânica, Prospecção, Carteira Ativa). Origens exigem Ativo; ao ativar, as quatro nascem ligadas (RN-LOJ-01, RN-LOJ-17). ⚠️ Desmarcar Ativo é bloqueado com Leads em andamento no Segmento (RN-LOJ-16). Loja com "Pode receber lead sem saldo" tem as Origens ligadas e travadas (RN-LOJ-18). |
| **Loja → Região** | `N : N` | Nasce com "Todas" (sem restrição); remover a última repõe "Todas" (RN-LOJ-14, RN-REG-04). |
| **Loja → Usuário** | `1 : N` | Ver Usuário. 🗑️ Excluir a Loja oferece soft-delete dos usuários exclusivos dela (RN-LOJ-08). |
| **Loja → Organização do Keycloak** | `1 : 1` | ADR-0001. |
| **Loja → Saldo e Extrato** | `1 : 1` / `1 : N` | Ver CRM · Créditos. Saldo manual e estorno são exclusivos do Admin (RN-LOJ-11, RN-LOJ-13). |
| **Loja** | — | Código único entre Lojas não excluídas. Exclusão é soft-delete e libera o Código; saldo diferente de zero só avisa (RN-LOJ-15). Inativar só bloqueia itens novos (RN-LOJ-06). |

---

# 👥 Time

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Time → Loja** | `N : 1` | Obrigatório e imutável (RN-LOJ-10). |
| **Time → Segmento** | `N : 0..1` | Só Segmentos que a Loja tem Ativo com ao menos uma Origem ligada. O Segmento dá acesso ao Funil; sem Segmento, sem Funil (RN-LOJ-02). Excluir o Segmento deixa o Time sem Segmento (RN-SEG-09). |
| **Time → Usuário** | `1 : N` | Ver Usuário. |
| **Time → Meta** | `1 : N` | Ver CRM · Metas. Excluir o Time encerra as Metas (RN-MET-12). |
| **Time** | — | Inativar: imediata (com redistribuição) ou após finalizar (drenagem) (RN-LOJ-03, RN-LOJ-04). Excluir: migra membros ou redistribui, soft-delete (RN-LOJ-05). |

---

# 🌎 Região

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Região → Promotora** | `N : N` | ⚠️ Inativar ou excluir a Região **remove os vínculos**, com confirmação em dois passos (impacto + digitar o nome); reativar não os devolve (RN-REG-02, RN-REG-03). |
| **Região → Loja** | `N : N` | Mesma regra. |
| **Região → Oferta** | *(sem vínculo direto)* | A Oferta herda a Região da Promotora (RN-OFR-02). Loja sem Região vê tudo; com Região, vê as Ofertas com interseção e as globais (RN-REG-04). Vale em Ofertas, Simulações e Tabelas Multiplicadoras; a distribuição de Leads não usa Região. |

---

# 📦 Produto

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Produto → Oferta** | `1 : N` | 🔒 Oferta vinculada **bloqueia** inativação e exclusão, com atalho "Ver ofertas" (RN-PRD-02, RN-PRD-03). |
| **Produto → Operação** | *(sem vínculo)* | A Operação é escolhida livremente na Oferta e na Simulação (Produtos v0.4). |

---

# 🏢 Promotora

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Promotora → Oferta** | `1 : N` | 🔒 Oferta vinculada **bloqueia** inativação e exclusão, com atalho "Ver ofertas" (RN-PRM-03, RN-PRM-04). |
| **Promotora → Região** | `N : N` | Sem Região, as Ofertas são globais (RN-PRM-02). Associar a primeira Região pede confirmação leve (RN-PRM-06); remover a última pede confirmação explícita (RN-PRM-05). |

---

# 🎁 Oferta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Oferta → Segmento** | `N : 1` | Obrigatório. Primeiro filtro de elegibilidade, antes de Região e Regras; só Segmento ativo em Oferta nova (RN-OFR-08, RN-SEG-08). |
| **Oferta → Promotora** | `N : 1` | Obrigatório. |
| **Oferta → Produto** | `N : 1` | Obrigatório. |
| **Oferta → Operação** | `N : 1` (catálogo fixo) | **Novo, Refinanciamento, Portabilidade, Portabilidade com Refinanciamento.** Compra de Dívida é tratada como Portabilidade. Define o valor base da comissão (RN-OFR-07). |
| **Oferta → Banco** | `N : 1` | Da API do Banco Central; sem cadastro interno. |
| **Oferta → Regra** | `N : N` (associação com dados) | A associação guarda os **valores de cada requisito** e a sobrescrita opcional de Bloqueia/Avisa (RN-RGR-12, RN-RGR-05). Só fica completa com todos os valores; sem isso a Oferta não ativa (RN-OFR-03). |
| **Oferta → Multiplicador** | `1 : N` (um por Loja) | Ver CRM · Tabelas Multiplicadoras. |
| **Oferta** | — | Sem unicidade de Promotora+Produto+Operação+Banco (RN-OFR-01). Exclusão sem bloqueio, com confirmação reforçada; Simulações e Propostas guardam snapshot (RN-OFR-05). |

---

# 📋 Regra

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Regra → Requisito** | `1 : N` (mínimo 1) | Requisito = **Campo + Operador**; o valor vive na Oferta (RN-RGR-06, RN-RGR-12). |
| **Regra → Oferta** | `N : N` | ⚠️ Exclusão não bloqueia: desvincula de todas as Ofertas, com alerta por comportamento efetivo (RN-RGR-07, RN-RGR-10). Editar Campo/Operador bloqueado com Oferta Ativa associada; com só Inativas, aviso, e os valores da Oferta se perdem (RN-RGR-13, RN-OFR-04). |
| **Regra** | — | **Sem inativação**: exclusão é o único caminho (RN-RGR-09). Uma Mensagem de erro por Regra, sem valores (RN-RGR-11). |

---

# 🏷️ Segmento

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Segmento → Funil** | `1 : 1` (obrigatória) | O Funil nasce com o Segmento, populado, em "Aguardando configuração" (RN-SEG-05). Inativar cascateia para o Funil só se Ativo, com aviso; reativar oferece reativar o Funil (RN-SEG-06, RN-SEG-07). |
| **Segmento → Oferta** | `1 : N` | 🔒 Oferta bloqueia a exclusão do Segmento (RN-SEG-08). |
| **Segmento → Loja / Time** | `N : N` / `1 : N` | Excluir o Segmento remove as associações com Lojas e deixa os Times sem Segmento (RN-SEG-09). |
| **Segmento** | — | 🔒 Exclusão bloqueada se o Funil já foi ativado ou se há Oferta; senão, exclui em cascata com o Funil (RN-SEG-04). |

---

# 🔄 Funil

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Funil → Segmento** | `1 : 1` | Imutável (RN-FNL-12). Configuração única para todas as Lojas (RN-FNL-01). |
| **Funil → Stage** | `1 : N` | Cobre os cinco Tipos (StageType): **um só** Novo, Formalização e Pago; um ou mais Negociação e Cancelado. Remover o único de um Tipo é bloqueado; remover Stage com Leads exige destino; Stage destino de Ação exige reatribuir a Ação (RN-FNL-15, RN-FNL-10, RN-FNL-21). |
| **Funil → Ação** | `1 : N` | Instâncias configuráveis de Desqualificar, Finalizar, Reabrir e Formalizar (Mover lead é dinâmica, sem cadastro). Huggy-id opcional e não bloqueante (RN-FNL-04). |
| **Funil → Tag** | `1 : N` | Espelho da Huggy; Categoria fixa (Audiência, Origem, Departamento) (RN-FNL-05). |
| **Funil → Preço de lead** | `1 : 4` (fixo) | Uma linha por Origem da lead com preço: **Campanha, Orgânica, Prospecção, Carteira Ativa**. Nascem com custo e preço 0; margem negativa só sinaliza (RN-FNL-07, RN-FNL-08). |
| **Preço de lead → Histórico** | `1 : N` | Append-only; cada mudança de custo ou preço gera registro (RN-FNL-16). |
| **Funil → Layout de Lead** | `1 : 1` | Ver Layout de Lead. |
| **Funil → Lead** | `1 : N` | Ver CRM · Leads. Inativar drena: Leads em curso seguem (RN-FNL-14). |
| **Funil** | — | 🔒 Nunca é excluído diretamente; já ativado, nunca se exclui; nunca ativado, só em cascata pelo Segmento (RN-FNL-09). |

---

# ➡️ Ação

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Ação → Stage de destino** | `N : 1` | Por tipo: Desqualificar e Finalizar → Cancelado; Reabrir → Negociação (só Lead sem Proposta); Formalizar → o Stage Formalização e cria a Proposta (RN-FNL-19, RN-FNL-17). Nenhuma Ação leva a Pago (RN-FNL-20). |
| **Ação → Fluxo da Huggy** | `N : 0..1` | Referência por Huggy-id; o fluxo vive na Huggy. |

---

# 🧩 Layout de Lead

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Layout → Funil** | `1 : 1` | Escopo por Funil, não global (RN-LAY-02). Grade fixa; só o conteúdo dos slots é configurável (RN-LAY-01). |
| **Layout → Bloco do Sheet** | `1 : N` | Oito obrigatórios, não removíveis (RN-LAY-05). Slot 3 do Card: uma de três variantes por Segmento (RN-LAY-12). |

---

# 📊 Status de Proposta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Status → Proposta** | `1 : N` | ⚠️ Não bloqueia inativação nem exclusão: dispara **migração** para outro Status ativo do mesmo Tipo; não existe "Sem status" (RN-STP-02, RN-STP-05). 🔒 O último ativo de um Tipo não se inativa, exclui nem muda de Tipo (RN-STP-07). Mudar o Tipo reclassifica as Propostas vinculadas (RN-STP-04). |
| **Status** | — | Tipo (StatusType): Novo, Negociação, Pago, Cancelado; não compartilha enum com Stage. Nome único (RN-STP-09). O tenant nasce com um de cada Tipo (RN-STP-10). A Proposta nasce no ativo mais antigo do tipo Novo (RN-STP-08). |

---

# 🔖 Origem de Venda

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Origem de Venda → Lead, Proposta, Lista, Varredura** | `1 : N` | Opcional em todas (RN-ORI-06, RN-ORI-07). Exclusão sem bloqueio, com confirmação por digitação; o rótulo **fica como texto** nos registros (RN-ORI-01, RN-ORI-02, RN-ORI-04). |
| **Origem de Venda** | — | Tenant-wide; nome único (RN-ORI-03, RN-ORI-05). Diferente da Tag de categoria Origem e da Origem da lead (RN-FNL-06). |

---

# 📈 Relatórios e Dashboard

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Visão salva → Usuário** | `N : 1` | Pessoal (Relatórios v0.2). |
| **Dashboard, Relatórios** | — | Leituras sobre as demais entidades; Comissões a pagar e Produção leem o snapshot gravado na Proposta (RN-REL-14, RN-REL-20). |

---

# ⚠️ Catálogos e referências externas

- **Banco** — sem cadastro; vem da API do Banco Central (RN-RGR-03).
- **Operação** — catálogo fixo de 4 valores, definido em Ofertas.
- **StageType** (5 valores) e **StatusType** (4 valores) — enums do sistema.
- **Origem da lead** — Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa, Manual; definida pelo sistema (CRM · Leads RN-LED-12).
- **Categoria de Tag** — Audiência, Origem, Departamento.

---

# 📌 Pendências entre documentos

Nenhuma. Cargos v0.4 e Ofertas v0.15 foram repostos pelo usuário.

---

_Fim do documento. Relacionamentos e Regras de Negócio v0.4 — visão consolidada, Admin._
