# Corban Admin · Segmentos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.3
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 0.2    | Segmento deixa de ter vínculo direto com Loja e Time — Lojas e Times passam a associar-se ao **Funil de Vendas**. Código único, formato livre, editável. Exclusão definitiva passa a existir.                                                                                                                                                                                                                                                                                                                                                                                                     |
| 0.3    | **Funil passa a nascer automaticamente com o Segmento** (revoga RN-SEG-03), já populado com o conjunto de stages sugerido, em status "Aguardando configuração". Isso elimina o bloqueio antigo de inativação/exclusão por "funil configurado" e o substitui por um modelo de **cascata parcial e informada**: inativar o Segmento inativa o Funil automaticamente (só se Ativo, e com aviso explícito); reativar o Segmento **oferece** reativar o Funil junto (nunca automático); excluir o Segmento só é bloqueado se o Funil **já foi ativado** alguma vez — senão, exclui os dois em cascata. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-SEG) e regras de negócio (RN-SEG) próprios. Conceitos transversais (Loja, Time, Funil) são definidos nos módulos deles e apenas referenciados aqui.

**Nota de vocabulário.** Três conceitos vizinhos, que o mercado de corban trata com nomes distintos e que o produto passa a usar de forma consistente:

| Conceito                        | Termo do produto | Exemplos                                            |
| ------------------------------- | ---------------- | --------------------------------------------------- |
| Público pagador                 | **Segmento**     | INSS, Servidor Público, CLT                         |
| Entidade pagadora específica    | **Promotora**    | SIAPE, GOV-SP, GOV-RJ                               |
| Natureza da operação de crédito | **Operação**     | Empréstimo Novo, Refinanciamento, Cartão Consignado |

Este módulo trata apenas do primeiro.

---

# 1. Objetivo do módulo

Manter o cadastro dos públicos pagadores com que o tenant trabalha. Todo Segmento nasce com seu Funil de Vendas — a peça que de fato organiza a operação comercial: é nele que Lojas e Times se associam, e é o Funil, não o Segmento, quem carrega o pipeline.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Segmento é cadastro tenant-wide.
- **Demais usuários** — consomem Segmentos indiretamente (nas abas de configuração da Loja, na associação de Times, na configuração de funis), sem gerenciá-los aqui.

---

# 3. Entidades e conceitos

**Segmento.** Público pagador com que o tenant trabalha. Cadastro **livre do tenant** — não é um catálogo fixo do sistema; cada tenant cria os seus. Campos:

| Campo     | Obrigatório | Notas                                                                                                            |
| --------- | ----------- | ---------------------------------------------------------------------------------------------------------------- |
| Nome      | Sim         | Como aparece em toda a interface (ex.: "Servidor Público Estadual").                                             |
| Código    | Sim         | Identificador curto (ex.: `INSS`, `SP-EST`, `CLT`). **Único no tenant**, formato livre, editável após a criação. |
| Descrição | Não         | Texto curto explicando o que o Segmento cobre.                                                                   |
| Status    | Sim         | Ativo / Inativo (ver §6).                                                                                        |

**O que Segmento NÃO governa.** Registrado explicitamente porque a intuição sugere o contrário:

- **Produtos não são vinculados a Segmento** — um Produto existe independentemente do público.
- **Promotoras não são vinculadas a Segmento** — apesar de, na prática, uma promotora atender tipicamente um público, o produto não modela esse vínculo.
- **Lojas e Times não são vinculados a Segmento.** Não há associação direta. O que Lojas e Times associam é o **Funil de Vendas** — e é o Funil que conhece o seu Segmento, não o contrário. Modelado no domínio de Funis de Vendas, fora deste documento.

**O que Segmento governa.**

- **Todo Segmento tem exatamente um Funil, sempre.** Relação 1:1 obrigatória, criada de uma vez (RN-SEG-05) — não existe Segmento sem Funil, nem Funil sem Segmento.

---

# 4. Requisitos funcionais

## RF-SEG-01 — Gestão de segmentos (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Segmentos **para** refletir os públicos pagadores com que a empresa trabalha.

**Comportamento esperado:** o gestor cadastra nome, código e descrição. Ao salvar a criação, o **Funil correspondente já nasce junto** (RN-SEG-05) — não há uma segunda etapa separada. A listagem permite busca e filtro por status, e mostra, por Segmento, o **status do Funil** associado, com um atalho para a página de configuração dele. A edição cobre todos os campos, incluindo o código.

**Regras específicas:**

**RN-SEG-01 — Cadastro livre do tenant.** Segmentos não são um catálogo fixo do sistema. Cada tenant cria e mantém os seus, com os nomes e códigos que fizerem sentido para a sua operação.

**RN-SEG-04 — Exclusão bloqueada apenas se o Funil já foi ativado; senão, exclui os dois em cascata.** Diferente da versão anterior desta regra, o bloqueio não depende de "ter funil configurado" (todo Segmento tem). Depende de o Funil **já ter sido ativado alguma vez**:

| Status do Funil                                  | Efeito ao excluir o Segmento                                                                                                                                                                                                                |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Aguardando configuração** (nunca foi ativado)  | Exclusão **permitida** — o Funil é excluído junto, em cascata. Exceção pontual à regra geral de que Funil ativado nunca se exclui (RN-FNL-09) — aqui ele nunca chegou a ser usado.                                                          |
| **Ativo, Drenando, ou Inativo** (já foi ativado) | Exclusão **bloqueada**. O gestor precisa lidar com o Funil primeiro — mas como Funil não tem exclusão própria (RN-FNL-09), na prática isso significa que um Segmento cujo Funil já circulou **nunca mais pode ser excluído**, só inativado. |

**RN-SEG-05 — O Funil nasce automaticamente com o Segmento.** _(Revoga a antiga RN-SEG-03, que previa o oposto.)_ Ao criar um Segmento, o sistema cria, na mesma operação, o Funil de Vendas correspondente — já populado com o conjunto de stages sugerido (Novo, Negociação, Formalização, Venda, Desqualificado, cobrindo os quatro Tipos exigidos por RN-FNL-15), em status **"Aguardando configuração"**. O gestor pode usar a estrutura sugerida como está, ajustá-la, ou reconfigurá-la por completo — mas o Funil já existe e é válido desde o primeiro instante, nunca fica num estado intermediário inválido.

**RN-SEG-06 — Inativar o Segmento cascateia para o Funil, apenas se ele estiver Ativo — e o usuário é informado antes.** Inativar um Segmento **nunca é bloqueado**. Se o Funil associado está com status **Ativo**, ele **também é inativado** no mesmo momento — iniciando a drenagem das leads em curso, conforme já definido em RN-FNL-14. Os demais status do Funil (Aguardando configuração, Drenando, Inativo) **não são alterados** pela inativação do Segmento — a cascata só existe nesse único sentido, de Ativo para Inativo.

A ação **não é silenciosa**: a confirmação de inativar o Segmento informa explicitamente, antes de aplicar, que o Funil também será inativado e o que isso significa (ver Microcopy).

**RN-SEG-07 — Reativar o Segmento nunca reativa o Funil automaticamente; oferece a opção.** Ao reativar um Segmento cujo Funil não está em "Aguardando configuração" (ou seja, já passou por algum ciclo de uso), a interface **oferece** — não aplica sozinha — a opção de reativar o Funil junto. O gestor pode preferir revisar a configuração do Funil antes de reabri-lo, então a decisão é sempre explícita. Se o Funil está em "Aguardando configuração", não há o que oferecer: ele nunca foi ativado, e reativar o Segmento não muda nada nele.

---

# 5. Regras de negócio (consolidado)

- **RN-SEG-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-SEG-04** — Exclusão bloqueada apenas se o Funil já foi ativado; senão, cascata de exclusão.
- **RN-SEG-05** — O Funil nasce automaticamente com o Segmento, populado e em "Aguardando configuração".
- **RN-SEG-06** — Inativar o Segmento cascateia para o Funil só se Ativo, com aviso explícito.
- **RN-SEG-07** — Reativar o Segmento oferece (não aplica) reativar o Funil, quando aplicável.

**Revogada na v0.3:** RN-SEG-02 (bloqueio de inativação/exclusão por "funil configurado") e RN-SEG-03 (Funil não nasce automaticamente) — ambas substituídas pelo modelo de cascata acima.

---

# 6. Estados e transições

**Segmento:**

- **Ativo** — Funil correspondente pode estar em qualquer status.
- **Inativo** — mesma observação; o status do Segmento não implica nada sobre o status do Funil, exceto no instante da própria transição (RN-SEG-06, RN-SEG-07).

Transições: ativo → inativo (sempre livre; cascateia ao Funil se Ativo, RN-SEG-06); inativo → ativo (sempre livre; oferece reativar o Funil, RN-SEG-07); ativo/inativo → excluído (bloqueada se o Funil já foi ativado; senão, cascata, RN-SEG-04).

---

# 7. Limites do módulo

Este módulo trata apenas do cadastro do Segmento em si. **Não** inclui: a configuração do Funil (módulo Funis de Vendas — este módulo só dispara a criação automática e oferece o atalho), nem a escolha de quais Funis uma Loja opera e a quais Funis um Time tem acesso (domínio de Funis de Vendas / Lojas). Não há, em nenhum lugar do produto, associação direta de Loja ou Time a Segmento.

---

# 8. Dependências e o que o módulo expõe

**Depende de:** o módulo de Funis de Vendas, para criar o Funil correspondente no momento do cadastro (RN-SEG-05) e para o atalho de configuração na listagem.

**Expõe para:**

- **Funis de Vendas** — o evento de criação de Segmento, que dispara a criação do Funil.
- **Lojas, Times, Leads / pipeline (CRM)** — indiretamente, sempre através do Funil. Nenhum deles referencia Segmento diretamente.

---

# 9. Itens abertos

1. **Código editável e integrações.** O código é editável após a criação. Se alguma integração externa vier a referenciar Segmento por código, editá-lo passa a ser uma ação de risco — vale reavaliar quando as integrações do produto forem definidas.

---

_Fim do documento. Segmentos v0.3 — módulo da aplicação Admin, seção Cadastros. Matriz de Permissões em documento companheiro._
