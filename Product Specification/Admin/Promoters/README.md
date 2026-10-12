# Corban Admin · Promotoras

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.4
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha os dois itens abertos: bloqueio por Tabela de Convênio confirmado; remoção da última Região passa a exigir confirmação explícita, avisando que as ofertas da Promotora se tornarão globais (RN-PRM-05). |
| 0.3 | **Troca "Tabela de Convênio" por Oferta** em todo o módulo (a Tabela foi fundida em Ofertas): a RN-PRM-03 passa a ser "Oferta bloqueia inativação e exclusão", como em Produtos. **RN-PRM-05:** o caminho "inativar uma Região desassocia a Promotora" volta a valer com a cascata de Regiões v0.5, e agora inclui excluir, com a confirmação de dois passos daquele módulo. **Nova RN-PRM-06:** associar a primeira Região a uma Promotora existente exige confirmação leve (a RN-PRM-02 prometia um aviso que nenhum artefato tinha; Lojas já tinha o equivalente). **§8:** Simulações e Relatórios entram como consumidores diretos. **RF-PRM-01:** a exclusão passa a ser descrita como a regra (nenhuma Oferta a usa), e não "nunca usadas". Reordena as regras no corpo. |
| 0.4 | **O bloqueio por Oferta ganha o atalho "Ver ofertas"** (RN-PRM-03). **§7 corrigido**: a regra de visibilidade por Região é aplicada também em Simulações e Tabelas Multiplicadoras, e não só em Ofertas. **§9 ganha o item "código editável e integrações"**, que valia para os quatro cadastros mas constava só em Segmentos e Produtos (a Promotora, com códigos como SIAPE, é a mais exposta). (Levantamento do Admin, segunda passada.) |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRM) e regras de negócio (RN-PRM) próprios.

**Nota de vocabulário.** Promotora é a **entidade pagadora específica** — SIAPE, GOV-SP, GOV-RJ. É o que a Especificação v1.2 chamava de *Convênio*. Não confundir com **Segmento** (o público pagador agregado: INSS, Servidor Público, CLT), que é cadastro separado e **não** se vincula a Promotora.

---

# 1. Objetivo do módulo

Manter o cadastro das entidades pagadoras com que o tenant opera. A Promotora é um dos componentes da Oferta, ao lado de Produto, Operação e Banco, e é ela que carrega o recorte geográfico que determina quais Lojas enxergam quais ofertas.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`.
- **Demais usuários** — consomem Promotoras através das Ofertas e do módulo de Simulações, sem gerenciá-las aqui.

---

# 3. Entidades e conceitos

**Promotora.** Entidade pagadora à qual o crédito consignado se vincula. Cadastro **livre do tenant**. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece em toda a interface (ex.: "GOV-SP", "SIAPE"). |
| Código | Sim | Identificador curto. **Único no tenant**, formato livre, editável após a criação. |
| Regiões | Não | Zero ou mais Regiões (cadastro próprio). Determina o alcance geográfico — ver RN-PRM-02. |
| Status | Sim | Ativo / Inativo (ver §6). |

**Sem campo de descrição** — nome e código bastam, como em Regiões.

**O que Promotora NÃO governa.**
- **Não é vinculada a Segmento.** Apesar de, na prática, uma promotora atender tipicamente um público (SIAPE → Servidor Público Federal), o produto não modela esse vínculo.
- **Não carrega parametrização financeira** — taxas, prazos, coeficientes e comissão pertencem à Oferta.

**O que Promotora governa.**
- **Compõe a Oferta**, junto com Produto, Operação e Banco.
- **Carrega o recorte geográfico das Ofertas.** A Oferta não se associa a Região diretamente: a região dela é derivada da Promotora que a compõe (RN-PRM-02).

---

# 4. Requisitos funcionais

## RF-PRM-01 — Gestão de promotoras (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Promotoras **para** refletir as entidades pagadoras com que a empresa opera.

**Comportamento esperado:** o gestor cadastra nome, código e, opcionalmente, uma ou mais Regiões. A listagem permite busca por nome/código e filtro por status e por Região. A edição cobre todos os campos, incluindo o código e as Regiões. A inativação é o caminho padrão de descontinuação; a exclusão definitiva existe para Promotoras que não estão em nenhuma Oferta (RN-PRM-04).

**Regras específicas:**

**RN-PRM-01 — Cadastro livre do tenant.** Promotoras não são catálogo fixo do sistema.

**RN-PRM-02 — Região da Promotora define a visibilidade das Ofertas.** As Regiões associadas a uma Promotora determinam quais Lojas enxergam as Ofertas construídas sobre ela (RN-REG-04, módulo de Regiões):
- **Promotora sem Região** → suas ofertas são **globais**: visíveis a qualquer Loja.
- **Promotora com Região(ões)** → suas ofertas são visíveis apenas às Lojas que tenham ao menos uma Região em comum, além das Lojas sem Região nenhuma (que veem tudo).

**Consequência a observar:** associar a **primeira** Região a uma Promotora **reduz** o alcance das ofertas dela — antes eram globais. É contraintuitivo, já que "adicionar" normalmente amplia; a interface pede confirmação (RN-PRM-06).

**RN-PRM-03 — Oferta bloqueia inativação e exclusão.** Uma Promotora referenciada por ao menos uma Oferta **não pode ser inativada nem excluída**. O gestor precisa primeiro remover ou reatribuir as Ofertas que a utilizam, no módulo de Ofertas. A mensagem de bloqueio traz o atalho **"Ver ofertas"**, que abre a Lista de Ofertas já filtrada por esta Promotora (RF-OFR-01).

**RN-PRM-04 — Exclusão definitiva existe, sujeita ao mesmo bloqueio.**

**RN-PRM-05 — Remover a última Região exige confirmação explícita.** Remover Regiões de uma Promotora é ação livre, **exceto** quando se trata da última: nesse caso a Promotora volta a não ter Região nenhuma, e todas as suas Ofertas passam de restritas a **globais** — disponíveis a todas as Lojas. A interface exige confirmação, deixando essa consequência explícita antes de aplicar (ver Microcopy).

Vale para as duas formas de chegar lá: removendo a Região no cadastro da Promotora, ou inativando ou excluindo a Região no módulo de Regiões, que a remove da Promotora. Neste segundo caso a confirmação é o diálogo de dois passos de Regiões (RN-REG-02), cujo passo de impacto já explicita essa consequência.

**RN-PRM-06 — Associar a primeira Região exige confirmação leve.** Quando uma Promotora **existente** não tem Região e o gestor associa a primeira, as ofertas dela **deixam de ser globais**: passam a aparecer só para as Lojas com ao menos uma Região em comum e para as Lojas sem Região (RN-PRM-02). Como "adicionar" costuma ampliar e aqui restringe, a interface pede uma confirmação leve antes de aplicar, explicitando isso (ver Microcopy). Mesmo padrão da Loja, que confirma ao escolher a primeira Região específica (módulo de Lojas). As Regiões seguintes não pedem confirmação. No **cadastro** de uma Promotora nova a confirmação não aparece: ela ainda não tem ofertas, e o texto auxiliar do campo já explica a regra *(decisão desta revisão)*.

---

# 5. Regras de negócio (consolidado)

- **RN-PRM-01** — Cadastro livre do tenant.
- **RN-PRM-02** — Região da Promotora define a visibilidade das Ofertas; sem Região, ofertas são globais.
- **RN-PRM-03** — Oferta bloqueia inativação e exclusão.
- **RN-PRM-04** — Exclusão definitiva existe, sujeita ao mesmo bloqueio.
- **RN-PRM-05** — Remover a última Região exige confirmação: as ofertas da Promotora se tornam globais.
- **RN-PRM-06** — Associar a primeira Região a uma Promotora existente exige confirmação leve: as ofertas deixam de ser globais.

---

# 6. Estados e transições

**Promotora:**
- **Ativa** — disponível para compor Ofertas.
- **Inativa** — não pode compor novas Ofertas. Só alcançável a partir de Ativa quando não está em nenhuma (RN-PRM-03).

Transições: ativa → inativa (bloqueada por vínculo); inativa → ativa (livre); ativa/inativa → excluída (mesmo bloqueio).

---

# 7. Limites do módulo

Trata apenas do cadastro da Promotora. **Não** inclui: o cadastro de Regiões (módulo próprio), a composição da Oferta (módulo de Ofertas), nem a aplicação da regra de visibilidade — que é enunciada aqui e em Regiões, mas aplicada nos domínios de Ofertas e de Simulações (o motor filtra as Ofertas por Região, RN-SIM-01) e nas Tabelas Multiplicadoras (RN-MUT-01).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Regiões** — a lista de Regiões ativas, para associação; e a Região é removida da Promotora quando inativada ou excluída lá (RN-REG-02).

**Expõe para:**
- **Ofertas** — a lista de Promotoras ativas, para composição; é da Promotora que a Oferta herda o recorte geográfico (RN-OFR-02).
- **Simulações** (CRM) — a lista de Promotoras ativas, escolhida direto no formulário (RN-SIM-06).
- **Relatórios** (Admin e CRM) — o cadastro, como dimensão de filtro e de agrupamento.

---

# 9. Itens abertos

1. **Código editável e integrações.** Mesmo item registrado em Segmentos e Produtos: o código é editável após a criação; se alguma integração externa vier a referenciar a Promotora por código (por exemplo, SIAPE), editá-lo passa a ser ação de risco. Reavaliar quando as integrações forem definidas.

---

*Fim do documento. Promotoras v0.4 — módulo da aplicação Admin, seção Cadastros.*
