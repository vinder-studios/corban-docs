# Corban Admin · Ofertas

**Status:** Draft
**Aplicação:** Admin
**Seção:** Oferta comercial
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                                |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                                                                                         |
| 0.5    | Fecha o item aberto: editar o template de um requisito de Regra usada por Oferta **Ativa** passa a ser **bloqueado** no módulo de Regras (RN-RGR-13, v0.7) — não mais uma decisão deste módulo, mas a garantia elimina o cenário de Oferta Ativa com condição inválida. |
| 0.4    | Reabre §9 com um item encontrado ao detalhar a Matriz de Estados: o que acontece quando uma Regra em uso por Oferta **já Ativa** é invalidada por edição de template (RN-OFR-04) — a Oferta continua Ativa mesmo com uma associação incompleta?                         |
| 0.3    | Confirma RN-OFR-01: sem unicidade, sem aviso — duplicatas são permitidas livremente.                                                                                                                                                                                    |
| 0.2    | Fecha o item aberto 1: define o **valor base de comissão por Operação** (RN-OFR-07).                                                                                                                                                                                    |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-OFR) e regras de negócio (RN-OFR) próprios.

> **Este módulo é o ponto de convergência de toda a Oferta comercial.** A "Tabela de Convênio" que aparecia solta em conversas anteriores **não existe como entidade própria** — foi fundida diretamente na Oferta: Promotora, Produto, Banco e Operação são campos dela, não de um cadastro intermediário. É também aqui que Regras deixam de ser templates abstratos e viram condições avaliáveis de verdade, com valores concretos.

---

# 1. Objetivo do módulo

Compor e manter as Ofertas — a unidade comercial que o motor de simulações avalia. Uma Oferta combina Promotora, Produto, Banco e Operação com a parametrização financeira (taxa, prazo, comissão) e as Regras de elegibilidade que se aplicam a ela.

---

# 2. Atores

- **Gestor comercial** — usuário cujas permissões efetivas incluem a gestão deste módulo.
- **Operador** — consome Ofertas indiretamente, através do motor de simulações.

---

# 3. Entidades e conceitos

## 3.1. Oferta

Cadastro **livre do tenant**. Campos:

| Campo             | Obrigatório | Notas                                                                                                                                                                                     |
| ----------------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome              | Sim         | Identificação da Oferta na listagem e nas simulações.                                                                                                                                     |
| Promotora         | Sim         | Referência ao cadastro de Promotoras.                                                                                                                                                     |
| Produto           | Sim         | Referência ao cadastro de Produtos.                                                                                                                                                       |
| Banco             | Sim         | **Buscado via API do Banco Central** — sem cadastro interno, mesmo padrão já usado em Regras (RN-RGR-03).                                                                                 |
| Operação          | Sim         | **Catálogo fixo do sistema** — Novo, Refinanciamento, Portabilidade, Portabilidade com Refinanciamento, Compra de Dívida. Sem CRUD, mesmo padrão definido no módulo de Produtos.          |
| Taxa              | Sim         | Uma taxa por Oferta — não há faixas por prazo nesta versão.                                                                                                                               |
| Prazo             | Sim         | Intervalo — mínimo e máximo.                                                                                                                                                              |
| Comissão          | Sim         | Percentual pago à Loja, calculado sobre um "valor base" que depende da Operação escolhida — ver RN-OFR-07.                                                                                |
| Regras associadas | Não         | Zero ou mais. Cada associação define, para aquela Oferta: os valores dos requisitos da Regra (RN-RGR-12) e, opcionalmente, uma sobrescrita do comportamento padrão (RN-RGR-05). Ver §3.3. |
| Status            | Sim         | Ativo / Inativo (ver §6).                                                                                                                                                                 |

**RN-OFR-01 — Promotora + Produto + Banco + Operação não têm restrição de unicidade, e não há aviso.** Nada impede duas Ofertas com a mesma combinação (ex.: duas Ofertas de Empréstimo Novo, mesma Promotora e Banco, taxas diferentes) — confirmado como comportamento pretendido, não lacuna de modelagem. Cada Oferta é independente, sem checagem de duplicidade nem mensagem de alerta.

**RN-OFR-07 — O valor base da comissão depende da Operação.**

| Operação                          | Valor base                          |
| --------------------------------- | ----------------------------------- |
| Novo                              | Valor liberado ao cliente           |
| Refinanciamento                   | Valor financiado                    |
| Portabilidade                     | Valor da dívida (saldo transferido) |
| Portabilidade com Refinanciamento | Valor financiado                    |
| Compra de Dívida                  | Valor financiado                    |

A comissão paga à Loja é `Taxa de comissão × valor base`, com o valor base determinado automaticamente pela Operação escolhida na Oferta — o gestor não o informa manualmente, só o percentual de comissão.

**RN-OFR-02 — Região não é campo da Oferta.** A Oferta herda o recorte geográfico da sua Promotora (módulo de Promotoras, RN-PRM-02): se a Promotora não tem Região, a Oferta é global; se tem, a visibilidade segue RN-REG-04 (módulo de Regiões) — a Loja só a enxerga se tiver ao menos uma Região em comum, ou se a Loja não tiver Região nenhuma (que também vê tudo).

## 3.2. Sem código

Diferente de Segmentos, Produtos e Regiões, Oferta não tem campo de código — é identificada só pelo Nome, já que não há integração externa que a referencie por identificador curto.

## 3.3. Associação com Regras — a Regra vira condição de verdade

Uma Regra, isolada, é só um template (Campo + Operador, módulo de Regras v0.6, RN-RGR-12). Ao associá-la a uma Oferta, o gestor:

1. **Preenche o(s) valor(es) de cada requisito** da Regra, na forma que o operador determina (um campo, dois para Intervalo, seleção múltipla para lista) — RN-RGR-04, RN-RGR-12.
2. **Opcionalmente sobrescreve o comportamento** (Bloqueia/Avisa) — se não sobrescrever, vale o padrão da Regra (RN-RGR-05).

**RN-OFR-03 — A associação só fica completa com todos os valores preenchidos.** Uma Regra associada sem os valores de todos os seus requisitos não é avaliável — a Oferta não pode ser ativada nessa condição (ver §6).

**RN-OFR-04 — Editar o template de uma Regra em uso invalida os valores desta Oferta, quando permitido.** Consequência de RN-RGR-13 (módulo de Regras, v0.7): se o gestor de Regras altera o Campo ou o Operador de um requisito, os valores que esta Oferta já tinha preenchido para ele deixam de existir — a associação volta ao estado "incompleta" (RN-OFR-03), e a Oferta é sinalizada até que o gestor comercial os preencha de novo.

**Isso só pode acontecer com esta Oferta Inativa.** RN-RGR-13 bloqueia a edição do template no módulo de Regras enquanto houver alguma Oferta **Ativa** associada — então uma Oferta Ativa nunca é pega de surpresa com uma condição invalidada. Para editar o template de uma Regra usada por esta Oferta, o gestor de Regras primeiro precisaria vê-la Inativa.

---

# 4. Requisitos funcionais

## RF-OFR-01 — Gestão de ofertas (CRUD)

**Como** gestor comercial, **eu quero** criar, consultar, editar, inativar e excluir Ofertas **para** controlar o que o tenant comercializa.

**Comportamento esperado:** o gestor cadastra Nome, Promotora, Produto, Banco (busca via API do BC), Operação, Taxa, Prazo e Comissão. Associa Regras conforme necessário, preenchendo valores e ajustando comportamento. A listagem permite busca por nome e filtros por Promotora, Produto, Operação e status.

**Regras específicas:**

**RN-OFR-05 — Exclusão é permitida, sem bloqueio — mas é ação crítica.** Diferente de Segmentos, Produtos e Promotoras, **nenhum vínculo bloqueia a exclusão de uma Oferta** — pode ser excluída mesmo com simulações ou propostas que já a referenciaram (que preservam snapshot imutável, então não perdem histórico). Por ser irreversível e afetar o que o tenant comercializa, usa confirmação reforçada por digitação, mesmo sem bloqueio.

**RN-OFR-06 — Inativar não exige nada além do padrão.** Sem regra especial — segue o padrão simples de switch com confirmação descritiva já usado nos demais cadastros.

## RF-OFR-02 — Associação de Regras

**Como** gestor comercial, **eu quero** associar Regras a uma Oferta, definindo valores e comportamento **para** compor as condições de elegibilidade daquela Oferta.

Coberto em detalhe em §3.3.

---

# 5. Regras de negócio (consolidado)

- **RN-OFR-01** — Sem restrição de unicidade entre Promotora+Produto+Banco+Operação.
- **RN-OFR-02** — Região não é campo da Oferta; herda da Promotora.
- **RN-OFR-03** — Associação de Regra só fica completa com todos os valores preenchidos.
- **RN-OFR-04** — Editar template de Regra em uso invalida valores desta Oferta.
- **RN-OFR-05** — Exclusão sem bloqueio, mas com confirmação reforçada (ação crítica).
- **RN-OFR-06** — Inativação segue o padrão simples.
- **RN-OFR-07** — Valor base da comissão determinado pela Operação (tabela fixa).

---

# 6. Estados e transições

**Oferta:**

- **Ativa** — disponível para simulação. Só alcançável se todas as Regras associadas estiverem com valores completos (RN-OFR-03).
- **Inativa** — não disponível para simulação; pode ter Regras com associação incompleta (é o estado natural de uma Oferta em construção).
- **Excluída** — hard delete, sem bloqueio (RN-OFR-05).

Transições: inativa → ativa (bloqueada se houver Regra associada com valores incompletos — RN-OFR-03); ativa → inativa (livre); qualquer estado → excluída (livre, com confirmação reforçada).

---

# 7. Limites do módulo

**Não** inclui: o cadastro de Promotoras, Produtos, Regras e Regiões (módulos próprios — este só consome), o motor de simulações em si (módulo de Simulações), nem a fórmula de cálculo de comissão por tipo de Operação (item aberto §9.1).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**

- **Promotoras, Produtos, Regras** — cadastros consumidos na composição da Oferta.
- **API do Banco Central** — busca de Banco.
- **Operação** — catálogo fixo do sistema (sem módulo próprio).
- **Regiões** (indiretamente, via Promotora) — para a visibilidade por Loja.

**Expõe para:**

- **Simulações** — a Oferta completa: parametrização financeira + Regras com valores, prontas para avaliação.
- **Relatórios** — dados de comissão e produção por Oferta.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

_Fim do documento. Ofertas v0.5 — módulo da aplicação Admin, seção Oferta comercial._
