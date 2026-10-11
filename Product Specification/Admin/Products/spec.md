# Corban Admin · Produtos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.5
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | **Reverte a decisão anterior: Operação passa a ser campo do Produto.** Motivada pelo módulo de Simulações — o formulário de simulação precisa saber, a partir do Produto escolhido, quais campos condicionais exibir (contrato atual, preferências), e isso só funciona se Operação estiver ligada ao Produto, não à Oferta. De brinde, corrige as referências a "Tabela de Convênio" como entidade — ela não existe (fundida em Oferta, módulo de Ofertas v0.7). Enum de Operação cai de 5 para 4 valores: Compra de Dívida passa a ser tratada como **a mesma coisa que Portabilidade**, não uma Operação separada. |
| 0.3 | **Terceira correção: Produto suporta várias Operações — relação muitos-para-muitos, não campo único.** Um Produto como "Empréstimo" pode ser usado em Novo, Portabilidade, Refinanciamento e Portabilidade com Refinanciamento ao mesmo tempo — não é um Produto por Operação. O campo Operação vira **seleção múltipla** ("Operações suportadas"). Oferta volta a ter seu próprio campo Operação (módulo de Ofertas), agora restrito às Operações que o Produto escolhido suporta. Simulações ganha um campo explícito de Operação no formulário. |
| 0.4 | **Desfaz a restrição Produto → Operação (quarta revisão desta relação).** O Produto deixa de ter o campo "Operações suportadas": **a Operação é escolhida livremente na Oferta e na Simulação**, entre as 4 do catálogo fixo, sem depender do Produto. Motivo (decisão do usuário, na revisão do módulo): na operação real nenhum Produto é restrito a um subconjunto de Operações — e a restrição gerava uma lacuna de regra (o que acontece ao retirar uma Operação já usada por uma Oferta). Combinação que nenhuma Oferta cobre resulta em "nenhuma oferta encontrada" na simulação. Corrige ainda: o §8 listava Ofertas como "único consumidor direto" (a Simulação escolhe o Produto direto no formulário; Relatórios filtram por ele); o §6 falava em "novas Tabelas" (conceito que virou Oferta); o RF-PRD-01 prometia exclusão só para Produtos "nunca usados", e a regra (RN-PRD-03) exige só que nenhuma Oferta o referencie. Reordena esta tabela. |
| 0.5 | **O catálogo de Operação sai daqui**: passa a ser definido em Ofertas (dono do campo), e este módulo só aponta; o Produto não o carrega. **O bloqueio por Oferta ganha o atalho "Ver ofertas"** (RN-PRD-02), que abre a Lista de Ofertas filtrada por este Produto. Corrige a linha da v0.2 deste histórico, que não fechava a tabela. (Levantamento do Admin, segunda passada.) |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRD) e regras de negócio (RN-PRD) próprios.

**Nota de vocabulário — Produto × Operação.** São conceitos distintos e **independentes** — o Produto não carrega Operação alguma:

| Conceito | O que é | Exemplos |
|---|---|---|
| **Produto** | O instrumento de crédito em si | "Empréstimo", "Cartão Benefício" |
| **Operação** | O que se faz com o instrumento | Ex.: Novo, Portabilidade (catálogo completo em Ofertas) |

Quem escolhe **qual** Operação vale para um caso concreto é a Oferta (módulo de Ofertas) ou a Simulação (módulo de Simulações), livremente entre as 4 do catálogo — qualquer Produto pode ser combinado com qualquer Operação. Uma combinação que nenhuma Oferta ativa cobre não é erro de cadastro: simplesmente não gera resultado na simulação.

> **Histórico desta relação.** A v1.2 tratava "Modalidade" (hoje Operação) como atributo do Produto; as v0.2 e v0.3 deste módulo alternaram entre Operação como campo único do Produto e como conjunto escolhido por ele. A v0.4 encerra a discussão: na operação real nenhum Produto é restrito a um subconjunto de Operações, então a Operação não pertence ao Produto.

**Catálogo de Operação.** O catálogo fixo de 4 valores é definido no módulo de Ofertas, dono do campo; o Produto não o carrega. **Compra de Dívida deixou de ser uma Operação separada — é tratada como a mesma coisa que Portabilidade**, inclusive para efeito de valor base de comissão (RN-OFR-07, módulo de Ofertas: usa a regra que antes era exclusiva de Portabilidade — "valor da dívida/saldo transferido" — não mais a antiga regra de Compra de Dívida).

---

# 1. Objetivo do módulo

Manter o cadastro dos instrumentos de crédito que o tenant comercializa. É um dos componentes que a Oferta combina, ao lado de Promotora e Banco.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Produto é cadastro tenant-wide.
- **Demais usuários** — consomem Produtos através das Ofertas e do módulo de Simulações, sem gerenciá-los aqui.

---

# 3. Entidades e conceitos

**Produto.** Instrumento de crédito comercializado pelo tenant. Cadastro **livre do tenant**. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece em toda a interface (ex.: "Cartão Benefício"). |
| Código | Sim | Identificador curto (ex.: `EMP`, `CARTAO-BEN`). **Único no tenant**, formato livre, editável após a criação. |
| Descrição | Não | Texto curto explicando o produto. |
| Status | Sim | Ativo / Inativo (ver §6). |

**O que Produto NÃO governa.** Registrado explicitamente porque a intuição sugere o contrário:
- **Produto não é vinculado a Segmento** — existe independentemente do público pagador.
- **Produto não carrega parametrização financeira** — taxas, prazos, coeficientes e comissão pertencem à Oferta.
- **Produto não carrega Operação** — qual Operação vale é escolha da Oferta e da Simulação, entre as 4 do catálogo fixo; não há restrição por Produto.

**O que Produto governa.**
- Só o cadastro do instrumento (nome, código, descrição, status).
- É referenciado pela Oferta (módulo de Ofertas), que combina Produto + Operação + Promotora + Banco + parametrização financeira, e é escolhido diretamente na Simulação (módulo de Simulações).

---

# 4. Requisitos funcionais

## RF-PRD-01 — Gestão de produtos (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Produtos **para** refletir os instrumentos de crédito que a empresa comercializa.

**Comportamento esperado:** o gestor cadastra nome, código e descrição. A listagem permite busca por nome/código e filtro por status. A edição cobre todos os campos, incluindo o código. A inativação é o caminho padrão de descontinuação; a exclusão definitiva existe para Produtos que não estão em nenhuma Oferta (RN-PRD-03).

**Regras específicas:**

**RN-PRD-01 — Cadastro livre do tenant.** Produtos não são catálogo fixo do sistema — cada tenant cria e mantém os seus. *(Diferente de Operação, que é fixa.)*

**RN-PRD-02 — Oferta bloqueia inativação e exclusão.** Um Produto referenciado por ao menos uma Oferta **não pode ser inativado nem excluído**. O gestor precisa primeiro remover ou reatribuir as Ofertas que o usam, no módulo de Ofertas. A mensagem de bloqueio traz o atalho **"Ver ofertas"**, que abre a Lista de Ofertas já filtrada por este Produto (RF-OFR-01).

**RN-PRD-03 — Exclusão definitiva existe, sujeita ao mesmo bloqueio.** Produto pode ser excluído em definitivo, desde que não esteja referenciado por nenhuma Oferta (RN-PRD-02). Serve ao caso de cadastro feito por engano.

---

# 5. Regras de negócio (consolidado)

- **RN-PRD-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-PRD-02** — Oferta bloqueia inativação e exclusão.
- **RN-PRD-03** — Exclusão definitiva existe, sujeita ao mesmo bloqueio da inativação.

---

# 6. Estados e transições

**Produto:**
- **Ativo** — disponível para compor Ofertas.
- **Inativo** — não pode compor novas Ofertas. Só alcançável a partir de Ativo quando não compõe nenhuma (RN-PRD-02).

Transições: ativo → inativo (bloqueada por vínculo, RN-PRD-02); inativo → ativo (livre); ativo/inativo → excluído (mesmo bloqueio, RN-PRD-03).

---

# 7. Limites do módulo

Trata apenas do cadastro do Produto. **Não** inclui: a Operação (catálogo fixo do sistema, sem CRUD; a escolha de **qual** vale para uma Oferta ou Simulação é desses módulos — o Produto não a conhece), nem qualquer parametrização financeira (Ofertas).

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base, não referencia outras entidades.

**Expõe para:**
- **Ofertas** — a lista de Produtos ativos, para composição.
- **Simulações** (CRM) — a lista de Produtos ativos, escolhido direto no formulário (RN-SIM-06).
- **Relatórios** (Admin e CRM) — o cadastro, como dimensão de filtro e de agrupamento.
- **Propostas** — indiretamente, através da Oferta que referencia este Produto.

---

# 9. Itens abertos

1. **Código editável e integrações.** Mesmo item registrado em Segmentos: o código é editável após a criação; se alguma integração externa vier a referenciar Produto por código, editá-lo passa a ser ação de risco. Reavaliar quando as integrações forem definidas.

---

*Fim do documento. Produtos v0.5 — módulo da aplicação Admin, seção Cadastros.*
