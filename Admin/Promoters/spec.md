# Corban Admin · Promotoras

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                      |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                               |
| 0.2    | Fecha os dois itens abertos: bloqueio por Tabela de Convênio confirmado; remoção da última Região passa a exigir confirmação explícita, avisando que as ofertas da Promotora se tornarão globais (RN-PRM-05). |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRM) e regras de negócio (RN-PRM) próprios.

**Nota de vocabulário.** Promotora é a **entidade pagadora específica** — SIAPE, GOV-SP, GOV-RJ. É o que a Especificação v1.2 chamava de _Convênio_. Não confundir com **Segmento** (o público pagador agregado: INSS, Servidor Público, CLT), que é cadastro separado e **não** se vincula a Promotora.

---

# 1. Objetivo do módulo

Manter o cadastro das entidades pagadoras com que o tenant opera. A Promotora é um dos componentes da Tabela de Convênio, ao lado de Produto, Banco e Operação, e é ela que carrega o recorte geográfico que determina quais Lojas enxergam quais ofertas.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`.
- **Demais usuários** — consomem Promotoras indiretamente, através das Tabelas de Convênio e das Ofertas.

---

# 3. Entidades e conceitos

**Promotora.** Entidade pagadora à qual o crédito consignado se vincula. Cadastro **livre do tenant**. Campos:

| Campo   | Obrigatório | Notas                                                                                    |
| ------- | ----------- | ---------------------------------------------------------------------------------------- |
| Nome    | Sim         | Como aparece em toda a interface (ex.: "GOV-SP", "SIAPE").                               |
| Código  | Sim         | Identificador curto. **Único no tenant**, formato livre, editável após a criação.        |
| Regiões | Não         | Zero ou mais Regiões (cadastro próprio). Determina o alcance geográfico — ver RN-PRM-02. |
| Status  | Sim         | Ativo / Inativo (ver §6).                                                                |

**Sem campo de descrição** — nome e código bastam, como em Regiões.

**O que Promotora NÃO governa.**

- **Não é vinculada a Segmento.** Apesar de, na prática, uma promotora atender tipicamente um público (SIAPE → Servidor Público Federal), o produto não modela esse vínculo.
- **Não carrega parametrização financeira** — taxas, prazos, coeficientes e comissão pertencem à Oferta.

**O que Promotora governa.**

- **Compõe a Tabela de Convênio**, junto com Produto, Banco e Operação.
- **Carrega o recorte geográfico das Ofertas.** A Oferta não se associa a Região diretamente: a região dela é derivada da Promotora que compõe sua Tabela de Convênio (RN-PRM-02).

---

# 4. Requisitos funcionais

## RF-PRM-01 — Gestão de promotoras (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Promotoras **para** refletir as entidades pagadoras com que a empresa opera.

**Comportamento esperado:** o gestor cadastra nome, código e, opcionalmente, uma ou mais Regiões. A listagem permite busca por nome/código e filtro por status e por Região. A edição cobre todos os campos, incluindo o código e as Regiões. A inativação é o caminho padrão de descontinuação; a exclusão definitiva existe para Promotoras que nunca chegaram a ser usadas.

**Regras específicas:**

**RN-PRM-01 — Cadastro livre do tenant.** Promotoras não são catálogo fixo do sistema.

**RN-PRM-02 — Região da Promotora define a visibilidade das Ofertas.** As Regiões associadas a uma Promotora determinam quais Lojas enxergam as Ofertas construídas sobre ela (RN-REG-04, módulo de Regiões):

- **Promotora sem Região** → suas ofertas são **globais**: visíveis a qualquer Loja.
- **Promotora com Região(ões)** → suas ofertas são visíveis apenas às Lojas que tenham ao menos uma Região em comum, além das Lojas sem Região nenhuma (que veem tudo).

**Consequência a observar:** associar a **primeira** Região a uma Promotora **reduz** o alcance das ofertas dela — antes eram globais. É contraintuitivo, já que "adicionar" normalmente amplia; a interface precisa avisar (ver Microcopy).

**RN-PRM-05 — Remover a última Região exige confirmação explícita.** Remover Regiões de uma Promotora é ação livre, **exceto** quando se trata da última: nesse caso a Promotora volta a não ter Região nenhuma, e todas as suas Ofertas passam de restritas a **globais** — disponíveis a todas as Lojas. A interface exige confirmação, deixando essa consequência explícita antes de aplicar (ver Microcopy).

Vale para as duas formas de chegar lá: removendo a Região no cadastro da Promotora, ou desassociando a Promotora ao inativar uma Região no módulo de Regiões.

**RN-PRM-03 — Tabela de Convênio bloqueia inativação e exclusão.** Uma Promotora que componha ao menos uma Tabela de Convênio **não pode ser inativada nem excluída**. O gestor precisa primeiro remover as Tabelas que a utilizam.

**RN-PRM-04 — Exclusão definitiva existe, sujeita ao mesmo bloqueio.**

---

# 5. Regras de negócio (consolidado)

- **RN-PRM-01** — Cadastro livre do tenant.
- **RN-PRM-02** — Região da Promotora define a visibilidade das Ofertas; sem Região, ofertas são globais.
- **RN-PRM-03** — Tabela de Convênio bloqueia inativação e exclusão.
- **RN-PRM-04** — Exclusão definitiva existe, sujeita ao mesmo bloqueio.
- **RN-PRM-05** — Remover a última Região exige confirmação: as ofertas da Promotora se tornam globais.

---

# 6. Estados e transições

**Promotora:**

- **Ativa** — disponível para compor Tabelas de Convênio.
- **Inativa** — não pode compor novas Tabelas. Só alcançável a partir de Ativa quando não compõe nenhuma (RN-PRM-03).

Transições: ativa → inativa (bloqueada por vínculo); inativa → ativa (livre); ativa/inativa → excluída (mesmo bloqueio).

---

# 7. Limites do módulo

Trata apenas do cadastro da Promotora. **Não** inclui: o cadastro de Regiões (módulo próprio), a composição da Tabela de Convênio (módulo próprio), nem a aplicação da regra de visibilidade — que é enunciada aqui e em Regiões, mas implementada no domínio de Ofertas.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**

- **Regiões** — a lista de Regiões ativas, para associação.

**Expõe para:**

- **Tabelas de Convênio** — a lista de Promotoras ativas, para composição.
- **Ofertas** — indiretamente, via Tabela de Convênio: é da Promotora que a Oferta herda seu recorte geográfico.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

_Fim do documento. Promotoras v0.2 — módulo da aplicação Admin, seção Cadastros._
