# Corban Admin · Ofertas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Oferta comercial
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.16
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha o item aberto 1: define o **valor base de comissão por Operação** (RN-OFR-07). |
| 0.3 | Confirma RN-OFR-01: sem unicidade, sem aviso — duplicatas são permitidas livremente. |
| 0.4 | Reabre §9 com um item encontrado ao detalhar a Matriz de Estados: o que acontece quando uma Regra em uso por Oferta **já Ativa** é invalidada por edição de template (RN-OFR-04) — a Oferta continua Ativa mesmo com uma associação incompleta? |
| 0.5 | Fecha o item aberto: editar o template de um requisito de Regra usada por Oferta **Ativa** passa a ser **bloqueado** no módulo de Regras (RN-RGR-13, v0.7) — não mais uma decisão deste módulo, mas a garantia elimina o cenário de Oferta Ativa com condição inválida. |
| 0.6 a 0.8 | *(Sem registro: o histórico original não documentou estas três versões.)* |
| 0.9 | **Oferta reganha campo próprio de Operação** (módulo de Produtos v0.3: Produto suporta várias Operações, não uma só). A Oferta escolhe qual das Operações suportadas pelo Produto selecionado está usando — `Select` restrito, não livre. RN-OFR-01 volta a incluir Operação na combinação (ainda sem restrição de unicidade, RN-OFR-01 confirmada). |
| 0.10 | RN-OFR-07 ganha a ressalva de não retroatividade: editar a comissão de uma Oferta só vale para Propostas que chegarem a Pago depois, porque a comissão passou a ser gravada no Pago (RN-PRP-05, Propostas v0.10). |
| 0.11 | **Acompanha a decisão em Produtos v0.4: a Operação deixa de ser restrita pelo Produto.** O campo Operação da Oferta é um `Select` entre as 4 do catálogo fixo, sem depender do Produto escolhido. Ajusta as menções à restrição e o título da RN-OFR-07 ("da Operação do Produto" → "da Operação da Oferta"). Os artefatos de tela já descreviam a Operação como catálogo fixo e não mudam. Alteração de consistência: a revisão completa do módulo segue na fila. |
| 0.12 | **Revisão completa do módulo.** O **Segmento** passa a constar onde faltava: RF-OFR-01 (cadastro e filtros) e §8 (depende de Segmentos). Segmento Inativo não pode ser escolhido em Ofertas novas, e a exclusão de um Segmento é bloqueada enquanto houver Oferta (Segmentos v0.5, RN-SEG-08). **Vigência removida de propósito** (decisão do usuário: o tenant inativa a Oferta quando precisar), o que a v1.2 previa e nenhum histórico registrava. O §8 passa a listar Propostas e Tabelas Multiplicadoras como consumidoras, e o módulo as citava como "Tabela de Multiplicadores". O §3.2 inclui Promotoras entre os cadastros com código. O §3.3 registra que os valores preenchidos são os mostrados ao operador como "Exigido pela oferta" (RN-SIM-11). Reordena esta tabela e marca as v0.6 a 0.8 como sem registro. |
| 0.13 | **A Lista ganha o filtro "Regras incompletas"** (RF-OFR-01), alvo do alerta "Ofertas com regras incompletas" do Dashboard do Admin: o alerta prometia abrir a Lista já filtrada, e o filtro não existia (achado na revisão do Dashboard). |
| 0.14 | **O módulo passa a ser o dono do catálogo de Operação** (os 4 valores), que estava definido em Produtos e copiado em Segmentos (levantamento do Admin, segunda passada). A Lista passa a poder abrir já filtrada por Produto ou Promotora, o destino do atalho "Ver ofertas" dos bloqueios de Produtos e Promotoras. A premissa da RN-OFR-05 (simulações e propostas preservam snapshot) **é decisão do usuário**; quem a contradiz hoje é o módulo de Propostas (RN-PRP-05: snapshot só no Pago), a corrigir na revisão do CRM. |
| 0.16 | **Portabilidade passa a usar o Valor financiado** como valor base (RN-OFR-07), e o catálogo registra que **Saque complementar é Novo** (não é Operação própria). Decisão do usuário, no modelo de dados de Propostas. |
| 0.15 | A RN-OFR-05 passa a apontar para a regra de Propostas que cumpre a sua premissa (a Proposta grava o snapshot da Oferta ao associá-la, RN-PRP-05). Sem mudança de comportamento. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-OFR) e regras de negócio (RN-OFR) próprios.

> **Este módulo é o ponto de convergência de toda a Oferta comercial.** A "Tabela de Convênio" que aparecia solta em conversas anteriores **não existe como entidade própria** — foi fundida diretamente na Oferta: Promotora, Produto, Banco e **Operação** são campos dela, não de um cadastro intermediário. Operação é escolhida livremente na Oferta, entre as 4 do catálogo fixo — o Produto não restringe Operações (módulo de Produtos v0.4). É também aqui que Regras deixam de ser templates abstratos e viram condições avaliáveis de verdade, com valores concretos.

---

# 1. Objetivo do módulo

Compor e manter as Ofertas — a unidade comercial que o motor de simulações avalia. Uma Oferta combina Promotora, Produto, a Operação escolhida e Banco com a parametrização financeira (taxa, prazo, comissão) e as Regras de elegibilidade que se aplicam a ela.

---

# 2. Atores

- **Gestor comercial** — usuário cujas permissões efetivas incluem a gestão deste módulo.
- **Operador** — consome Ofertas indiretamente, através do motor de simulações.

---

# 3. Entidades e conceitos

## 3.1. Oferta

Cadastro **livre do tenant**. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Identificação da Oferta na listagem e nas simulações. |
| **Segmento** | Sim | Referência ao cadastro de Segmentos — determina para qual público esta Oferta é elegível na simulação (RN-OFR-08). Só Segmentos ativos podem ser escolhidos em Ofertas novas (RN-SEG-08). |
| Promotora | Sim | Referência ao cadastro de Promotoras. |
| Produto | Sim | Referência ao cadastro de Produtos. |
| **Operação** | Sim | `Select` entre as 4 Operações do catálogo fixo do sistema — **Novo, Refinanciamento, Portabilidade e Portabilidade com Refinanciamento**; esta é a **definição do catálogo** (os outros módulos apontam para cá). Compra de Dívida não é uma Operação à parte: é tratada como Portabilidade; Saque complementar também não: é tratado como Novo. Independe do Produto escolhido — qualquer Produto pode ter Ofertas em qualquer Operação. Determina o valor base da comissão (RN-OFR-07). |
| Banco | Sim | **Buscado via API do Banco Central** — sem cadastro interno, mesmo padrão já usado em Regras (RN-RGR-03). |
| Taxa | Sim | Uma taxa por Oferta — não há faixas por prazo nesta versão. |
| Prazo | Sim | Intervalo — mínimo e máximo. |
| Comissão | Sim | Percentual pago à Loja, calculado sobre um "valor base" que depende da Operação escolhida — ver RN-OFR-07. |
| Regras associadas | Não | Zero ou mais. Cada associação define, para aquela Oferta: os valores dos requisitos da Regra (RN-RGR-12) e, opcionalmente, uma sobrescrita do comportamento padrão (RN-RGR-05). Ver §3.3. |
| Status | Sim | Ativo / Inativo (ver §6). |

**Sem vigência.** A Especificação v1.2 previa vigência (início e fim) na Oferta, com a regra "só oferta ativa e vigente simula" (RN027). Foi **removida de propósito**: o tenant não a usa, e quando precisa tirar uma Oferta de circulação basta inativá-la. A Oferta simula quando está **Ativa** (§6). *(Decisão do usuário, na revisão.)*

**RN-OFR-01 — Promotora + Produto + Operação + Banco não têm restrição de unicidade, e não há aviso.** *(Operação faz parte da combinação, já que o mesmo Produto pode ter Ofertas em Operações diferentes.)* Nada impede duas Ofertas com a mesma combinação (ex.: duas Ofertas do mesmo Produto+Operação, mesma Promotora e Banco, taxas diferentes) — confirmado como comportamento pretendido, não lacuna de modelagem. Cada Oferta é independente, sem checagem de duplicidade nem mensagem de alerta.

**RN-OFR-07 — O valor base da comissão depende da Operação da Oferta.** *(Tabela reduzida de 5 para 4 linhas — Compra de Dívida deixou de ser Operação própria, módulo de Produtos v0.2.)*

| Operação | Valor base |
|---|---|
| Novo | Valor liberado ao cliente |
| Refinanciamento | Valor financiado |
| Portabilidade | Valor financiado — inclui o antigo tratamento de Compra de Dívida |
| Portabilidade com Refinanciamento | Valor financiado |

A comissão paga à Loja é `Taxa de comissão × valor base`, com o valor base determinado automaticamente pela Operação escolhida na Oferta — o gestor não o informa manualmente, só o percentual de comissão. Editar a comissão de uma Oferta vale só para Propostas que chegarem a Pago depois: as já Pagas guardam a comissão do momento do Pago (RN-PRP-05, módulo de Propostas).

**Este valor base é compartilhado com outro cálculo, fora deste módulo.** O CRM (módulo Tabelas Multiplicadoras) usa o mesmo valor base para calcular o valor que conta para metas de Time/usuário: `valor base × multiplicador configurado pela Loja`, com o multiplicador — diferente da comissão — definido por Loja, não pela Oferta. Os dois cálculos partem do mesmo valor base, mas são independentes: mudar a Taxa de comissão não afeta o Multiplicador de Meta, e vice-versa.

**RN-OFR-08 — Segmento determina elegibilidade na simulação.** O motor de simulações (módulo CRM · Simulações) só oferece, para uma Lead ou Cliente de um Segmento, as Ofertas daquele mesmo Segmento — é o primeiro filtro aplicado, antes de Região (RN-OFR-02) e de Regras. Diferente de Região, Segmento **não** é herdado de outra entidade (Promotora não carrega Segmento, módulo de Promotoras) — é campo direto da Oferta, porque o gestor pode querer a mesma Promotora/Produto/Banco atendendo Segmentos diferentes em Ofertas distintas.

**RN-OFR-02 — Região não é campo da Oferta.** A Oferta herda o recorte geográfico da sua Promotora (módulo de Promotoras, RN-PRM-02): se a Promotora não tem Região, a Oferta é global; se tem, a visibilidade segue RN-REG-04 (módulo de Regiões) — a Loja só a enxerga se tiver ao menos uma Região em comum, ou se a Loja não tiver Região nenhuma (que também vê tudo).

## 3.2. Sem código

Diferente de Segmentos, Produtos, Promotoras e Regiões, Oferta não tem campo de código — é identificada só pelo Nome, já que não há integração externa que a referencie por identificador curto.

## 3.3. Associação com Regras — a Regra vira condição de verdade

Uma Regra, isolada, é só um template (Campo + Operador, módulo de Regras v0.6, RN-RGR-12). Ao associá-la a uma Oferta, o gestor:

1. **Preenche o(s) valor(es) de cada requisito** da Regra, na forma que o operador determina (um campo, dois para Intervalo, seleção múltipla para lista) — RN-RGR-04, RN-RGR-12.
2. **Opcionalmente sobrescreve o comportamento** (Bloqueia/Avisa) — se não sobrescrever, vale o padrão da Regra (RN-RGR-05).

Os valores preenchidos aqui são os que o operador vê na simulação, junto da mensagem da Regra, no bloco "Exigido pela oferta" (RN-SIM-11, módulo de Simulações).

**RN-OFR-03 — A associação só fica completa com todos os valores preenchidos.** Uma Regra associada sem os valores de todos os seus requisitos não é avaliável — a Oferta não pode ser ativada nessa condição (ver §6).

**RN-OFR-04 — Editar o template de uma Regra em uso invalida os valores desta Oferta, quando permitido.** Consequência de RN-RGR-13 (módulo de Regras, v0.7): se o gestor de Regras altera o Campo ou o Operador de um requisito, os valores que esta Oferta já tinha preenchido para ele deixam de existir — a associação volta ao estado "incompleta" (RN-OFR-03), e a Oferta é sinalizada até que o gestor comercial os preencha de novo.

**Isso só pode acontecer com esta Oferta Inativa.** RN-RGR-13 bloqueia a edição do template no módulo de Regras enquanto houver alguma Oferta **Ativa** associada — então uma Oferta Ativa nunca é pega de surpresa com uma condição invalidada. Para editar o template de uma Regra usada por esta Oferta, o gestor de Regras primeiro precisaria vê-la Inativa.

---

# 4. Requisitos funcionais

## RF-OFR-01 — Gestão de ofertas (CRUD)

**Como** gestor comercial, **eu quero** criar, consultar, editar, inativar e excluir Ofertas **para** controlar o que o tenant comercializa.

**Comportamento esperado:** o gestor cadastra Nome, Segmento, Promotora, Produto, Operação (catálogo fixo), Banco (busca via API do BC), Taxa, Prazo e Comissão. Associa Regras conforme necessário, preenchendo valores e ajustando comportamento. A listagem permite busca por nome e filtros por Segmento, Promotora, Produto, Operação, status e situação das Regras ("Regras incompletas": Ofertas com ao menos uma associação de Regra incompleta, RN-OFR-03 — alvo do alerta do Dashboard do Admin). A Lista também abre já filtrada por Produto ou por Promotora, a partir do atalho "Ver ofertas" dos bloqueios de Produtos e Promotoras.

**Regras específicas:**

**RN-OFR-05 — Exclusão é permitida, sem bloqueio — mas é ação crítica.** Diferente de Segmentos, Produtos e Promotoras, **nenhum vínculo bloqueia a exclusão de uma Oferta** — pode ser excluída mesmo com simulações ou propostas que já a referenciaram (que preservam snapshot imutável — a Simulação grava o seu, e a Proposta grava o da Oferta ao associá-la (Propostas, RN-PRP-05) —, então não perdem histórico). Por ser irreversível e afetar o que o tenant comercializa, usa confirmação reforçada por digitação, mesmo sem bloqueio.

**RN-OFR-06 — Inativar não exige nada além do padrão.** Sem regra especial — segue o padrão simples de switch com confirmação descritiva já usado nos demais cadastros.

## RF-OFR-02 — Associação de Regras

**Como** gestor comercial, **eu quero** associar Regras a uma Oferta, definindo valores e comportamento **para** compor as condições de elegibilidade daquela Oferta.

Coberto em detalhe em §3.3.

---

# 5. Regras de negócio (consolidado)

- **RN-OFR-01** — Sem restrição de unicidade entre Promotora+Produto+Operação+Banco.
- **RN-OFR-02** — Região não é campo da Oferta; herda da Promotora.
- **RN-OFR-08** — Segmento é campo direto da Oferta; determina elegibilidade na simulação, filtro anterior a Região e Regras; só Segmento ativo em Ofertas novas (RN-SEG-08).
- **RN-OFR-03** — Associação de Regra só fica completa com todos os valores preenchidos.
- **RN-OFR-04** — Editar template de Regra em uso invalida valores desta Oferta.
- **RN-OFR-05** — Exclusão sem bloqueio, mas com confirmação reforçada (ação crítica).
- **RN-OFR-06** — Inativação segue o padrão simples.
- **RN-OFR-07** — Valor base da comissão determinado pela Operação da Oferta (tabela fixa, 4 valores).

---

# 6. Estados e transições

**Oferta:**
- **Ativa** — disponível para simulação. Só alcançável se todas as Regras associadas estiverem com valores completos (RN-OFR-03).
- **Inativa** — não disponível para simulação; pode ter Regras com associação incompleta (é o estado natural de uma Oferta em construção).
- **Excluída** — hard delete, sem bloqueio (RN-OFR-05).

Transições: inativa → ativa (bloqueada se houver Regra associada com valores incompletos — RN-OFR-03); ativa → inativa (livre); qualquer estado → excluída (livre, com confirmação reforçada).

---

# 7. Limites do módulo

**Não** inclui: o cadastro de Promotoras, Produtos, Regras e Regiões (módulos próprios — este só consome), nem o motor de simulações em si (módulo de Simulações).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Segmentos, Promotoras, Produtos, Regras** — cadastros consumidos na composição da Oferta.
- **API do Banco Central** — busca de Banco.
- **Operação** — campo próprio da Oferta, catálogo fixo do sistema (Novo, Refinanciamento, Portabilidade, Portabilidade com Refinanciamento); não é restringida pelo Produto.
- **Regiões** (indiretamente, via Promotora) — para a visibilidade por Loja.

**Expõe para:**
- **Simulações** — a Oferta completa: parametrização financeira + Regras com valores, prontas para avaliação.
- **Propostas** (CRM) — a Operação, o valor base e a comissão (RN-OFR-07), lidos quando a Proposta chega a Pago.
- **Tabelas Multiplicadoras** (CRM) — a lista de Ofertas e o valor base (RN-OFR-07), sobre os quais cada Loja define o multiplicador.
- **Relatórios** — dados de comissão e produção por Oferta.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Ofertas v0.16 — módulo da aplicação Admin, seção Oferta comercial.*
