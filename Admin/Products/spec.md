# Corban Admin · Produtos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.1
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças        |
| ------ | --------------- |
| 0.1    | Versão inicial. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRD) e regras de negócio (RN-PRD) próprios.

**Nota de vocabulário — Produto × Operação.** São conceitos distintos, e confundi-los é o erro mais provável neste domínio:

| Conceito     | O que é                             | Exemplos                                                                |
| ------------ | ----------------------------------- | ----------------------------------------------------------------------- |
| **Produto**  | O instrumento de crédito em si      | Empréstimo, Cartão Benefício, Cartão Consignado, Cartão de Crédito      |
| **Operação** | O que se faz com aquele instrumento | Novo, Refinanciamento, Portabilidade, Portabilidade com refinanciamento |

Os dois compõem a Oferta de forma independente. Uma oferta de **Empréstimo + Novo** e outra de **Empréstimo + Refinanciamento** são ofertas diferentes, com o mesmo Produto e Operações distintas.

> **Correção em relação à Especificação v1.2.** Aquele documento usava o termo "Modalidade" para o que hoje chamamos de **Operação**, e a tratava como _atributo_ do Produto. No modelo atual são entidades separadas, ambas componentes da Oferta — o Produto não carrega a operação.

---

# 1. Objetivo do módulo

Manter o cadastro dos instrumentos de crédito que o tenant comercializa. O Produto é um dos componentes da Oferta, ao lado de Promotora, Banco e Operação.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Produto é cadastro tenant-wide.
- **Demais usuários** — consomem Produtos indiretamente, através das Ofertas.

---

# 3. Entidades e conceitos

**Produto.** Instrumento de crédito comercializado pelo tenant. Cadastro **livre do tenant**. Campos:

| Campo     | Obrigatório | Notas                                                                                                        |
| --------- | ----------- | ------------------------------------------------------------------------------------------------------------ |
| Nome      | Sim         | Como aparece em toda a interface (ex.: "Cartão Benefício").                                                  |
| Código    | Sim         | Identificador curto (ex.: `EMP`, `CARTAO-BEN`). **Único no tenant**, formato livre, editável após a criação. |
| Descrição | Não         | Texto curto explicando o produto.                                                                            |
| Status    | Sim         | Ativo / Inativo (ver §6).                                                                                    |

**Operação.** **Catálogo fixo do sistema** — não é cadastro do tenant, que não exerce nenhuma ação sobre ele (não cria, não edita, não inativa). O conjunto é definido pelo produto e disponibilizado para composição da Oferta. Referenciada aqui apenas para deixar claro que **não** pertence a este módulo nem a nenhum CRUD da seção Cadastros.

**O que Produto NÃO governa.** Registrado explicitamente porque a intuição sugere o contrário:

- **Produto não é vinculado a Segmento** — existe independentemente do público pagador.
- **Produto não carrega a Operação** — as duas compõem a Oferta separadamente (ver nota de vocabulário).
- **Produto não carrega parametrização financeira** — taxas, prazos, coeficientes e comissão pertencem à Oferta.

**O que Produto governa.**

- **Compõe a Oferta**, junto com Promotora, Banco e Operação. É o único vínculo que parte do Produto.

---

# 4. Requisitos funcionais

## RF-PRD-01 — Gestão de produtos (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Produtos **para** refletir os instrumentos de crédito que a empresa comercializa.

**Comportamento esperado:** o gestor cadastra nome, código e descrição. A listagem permite busca por nome/código e filtro por status. A edição cobre todos os campos, incluindo o código. A inativação é o caminho padrão de descontinuação; a exclusão definitiva existe para Produtos que nunca chegaram a ser usados.

**Regras específicas:**

**RN-PRD-01 — Cadastro livre do tenant.** Produtos não são catálogo fixo do sistema — cada tenant cria e mantém os seus. _(Diferente de Operação, que é fixa.)_

**RN-PRD-02 — Inativação de Produto com Oferta vinculada.** Um Produto que componha ao menos uma Oferta **pode ser inativado**, mas o gestor precisa escolher como tratar as Ofertas que o utilizam: inativar todas elas ou substituir o Produto por outro Produto ativo, no módulo de Ofertas. A escolha deve ser aplicada a todas as Ofertas afetadas antes da conclusão da inativação.

**RN-PRD-03 — Oferta bloqueia exclusão definitiva.** Produto pode ser excluído em definitivo somente se não compuser nenhuma Oferta. Serve ao caso de cadastro feito por engano; a existência de Oferta vinculada continua bloqueando apenas a exclusão.

---

# 5. Regras de negócio (consolidado)

- **RN-PRD-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-PRD-02** — Produto com Oferta vinculada pode ser inativado após o gestor escolher entre inativar todas as Ofertas afetadas ou substituir o Produto por outro Produto ativo.
- **RN-PRD-03** — Oferta vinculada bloqueia a exclusão definitiva.

---

# 6. Estados e transições

**Produto:**

- **Ativo** — disponível para compor Ofertas.
- **Inativo** — não pode compor novas Ofertas. Ao inativar um Produto que compõe Ofertas, o gestor precisa definir o tratamento das Ofertas afetadas (RN-PRD-02).

Transições: ativo → inativo (com decisão obrigatória para Ofertas vinculadas, RN-PRD-02); inativo → ativo (livre); ativo/inativo → excluído (bloqueada enquanto houver Oferta vinculada, RN-PRD-03).

---

# 7. Limites do módulo

Trata apenas do cadastro do Produto. **Não** inclui: o catálogo de Operações (fixo do sistema, sem CRUD), a composição da Oferta (módulo próprio), nem qualquer parametrização financeira (módulo de Ofertas).

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base, não referencia outras entidades.

**Expõe para:**

- **Ofertas** — a lista de Produtos ativos, para composição. É o único consumidor direto.
- **Simulações, Propostas** — indiretamente, sempre através da Oferta.

---

# 9. Itens abertos

1. **Código editável e integrações.** Mesmo item registrado em Segmentos: o código é editável após a criação; se alguma integração externa vier a referenciar Produto por código, editá-lo passa a ser ação de risco. Reavaliar quando as integrações forem definidas.
2. **Efeito sobre Propostas e simulações.** Falta definir, no módulo de Ofertas, o que acontece com as Propostas e simulações já realizadas quando uma Oferta é inativada ou quando seu Produto é substituído — é lá que essa consequência precisa ser tratada.

---

_Fim do documento. Produtos v0.1 — módulo da aplicação Admin, seção Cadastros._
