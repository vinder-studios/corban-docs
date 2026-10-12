# Corban Admin · Segmentos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.8
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.8 | Acompanha Lojas v0.13: o interruptor "Recebe leads" por Segmento virou **tipos de lead** (um por Origem da lead). Só o vocabulário muda; a RN-SEG-09 passa a dizer que a exclusão remove também os tipos de lead da Loja |
| 0.1 | Versão inicial. |
| 0.2 | Segmento deixa de ter vínculo direto com Loja e Time — Lojas e Times passam a associar-se ao **Funil de Vendas**. Código único, formato livre, editável. Exclusão definitiva passa a existir. |
| 0.3 | **Funil passa a nascer automaticamente com o Segmento** (revoga RN-SEG-03), já populado com o conjunto de stages sugerido, em status "Aguardando configuração". Isso elimina o bloqueio antigo de inativação/exclusão por "funil configurado" e o substitui por um modelo de **cascata parcial e informada**: inativar o Segmento inativa o Funil automaticamente (só se Ativo, e com aviso explícito); reativar o Segmento **oferece** reativar o Funil junto (nunca automático); excluir o Segmento só é bloqueado se o Funil **já foi ativado** alguma vez — senão, exclui os dois em cascata. |
| 0.4 | **Reconcilia o vocabulário Segmento × Funil com Lojas (v0.6) e Configurações (CRM).** A v0.3 afirmava que Loja e Time não se ligam a Segmento "em nenhum lugar do produto", mas Lojas liga por Segmento (flags Ativo e Recebe leads, Segmento do Time) e Configurações também. Agora: a ligação acontece pelo Segmento na interface e equivale à ligação ao Funil, por ser 1:1. Corrige RN-SEG-05 (cinco Tipos, não quatro) e o exemplo de Operação na tabela de vocabulário (Cartão é Produto). |
| 0.5 | **Oferta passa a travar a exclusão do Segmento** (nova RN-SEG-08). A Oferta tem um campo direto "Segmento" (RN-OFR-08), e a RN-SEG-04 permitia excluir um Segmento cujo Funil nunca foi ativado mesmo com Ofertas o referenciando, o que deixaria a Oferta com referência pendurada. Agora a exclusão é bloqueada também enquanto houver Oferta, como em Produtos e Promotoras, e o Segmento Inativo não pode ser escolhido em novas Ofertas. Ofertas entra no §8 como consumidora. Achado na revisão de Ofertas, que a revisão de Segmentos não tinha visto. |
| 0.6 | **Excluir o Segmento remove as associações com Lojas e Times** (nova RN-SEG-09): as flags "Ativo" e "Recebe leads" das Lojas e o Segmento do Time deixam de apontar para um Segmento que não existe mais. Cascata com aviso do impacto, como em Regiões; segura porque um Funil nunca ativado não recebeu lead nenhuma. Achado na revisão de Lojas (a revisão de Ofertas já tinha achado o buraco análogo para Ofertas). |
| 0.7 | A tabela de vocabulário deixa de listar os 4 valores de Operação: o catálogo é definido em Ofertas (dono do campo) e aqui só há o ponteiro. (Levantamento do Admin, segunda passada.) |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-SEG) e regras de negócio (RN-SEG) próprios. Conceitos transversais (Loja, Time, Funil) são definidos nos módulos deles e apenas referenciados aqui.

**Nota de vocabulário.** Três conceitos vizinhos, que o mercado de corban trata com nomes distintos e que o produto passa a usar de forma consistente:

| Conceito | Termo do produto | Exemplos |
|---|---|---|
| Público pagador | **Segmento** | INSS, Servidor Público, CLT |
| Entidade pagadora específica | **Promotora** | SIAPE, GOV-SP, GOV-RJ |
| Natureza da operação de crédito | **Operação** | Catálogo fixo de 4 valores, definido em Ofertas (ex.: Novo, Portabilidade) |

Este módulo trata apenas do primeiro.

---

# 1. Objetivo do módulo

Manter o cadastro dos públicos pagadores com que o tenant trabalha. Todo Segmento nasce com seu Funil de Vendas — a peça que de fato organiza a operação comercial: é o Funil, não o Segmento, quem carrega o pipeline. Lojas e Times se ligam ao Segmento (a Loja marca, por Segmento, se está Ativa e quais tipos de lead recebe; o Time escolhe um Segmento), e como Segmento e Funil são 1:1, isso equivale a se ligar ao Funil daquele Segmento.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Segmento é cadastro tenant-wide.
- **Demais usuários** — consomem Segmentos indiretamente (nas abas de configuração da Loja, no Admin e em Configurações, no CRM; no Segmento do Time; na configuração de funis), sem gerenciá-los aqui.

---

# 3. Entidades e conceitos

**Segmento.** Público pagador com que o tenant trabalha. Cadastro **livre do tenant** — não é um catálogo fixo do sistema; cada tenant cria os seus. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece em toda a interface (ex.: "Servidor Público Estadual"). |
| Código | Sim | Identificador curto (ex.: `INSS`, `SP-EST`, `CLT`). **Único no tenant**, formato livre, editável após a criação. |
| Descrição | Não | Texto curto explicando o que o Segmento cobre. |
| Status | Sim | Ativo / Inativo (ver §6). |

**O que Segmento NÃO governa.** Registrado explicitamente porque a intuição sugere o contrário:
- **Produtos não são vinculados a Segmento** — um Produto existe independentemente do público.
- **Promotoras não são vinculadas a Segmento** — apesar de, na prática, uma promotora atender tipicamente um público, o produto não modela esse vínculo.
- **A ligação de Lojas e Times ao Segmento não é governada aqui.** Na interface, a Loja marca por Segmento se está Ativa e quais tipos de lead recebe (módulo Lojas, RN-LOJ-01) e o Time escolhe um Segmento (RN-LOJ-02). Como todo Segmento tem exatamente um Funil, ligar-se ao Segmento equivale a ligar-se ao Funil dele — e é o Funil que carrega o pipeline (módulo Funis de Vendas). Essas ligações são mantidas nos módulos de Lojas e de Times, fora deste documento.

**O que Segmento governa.**
- **Todo Segmento tem exatamente um Funil, sempre.** Relação 1:1 obrigatória, criada de uma vez (RN-SEG-05) — não existe Segmento sem Funil, nem Funil sem Segmento.

---

# 4. Requisitos funcionais

## RF-SEG-01 — Gestão de segmentos (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Segmentos **para** refletir os públicos pagadores com que a empresa trabalha.

**Comportamento esperado:** o gestor cadastra nome, código e descrição. Ao salvar a criação, o **Funil correspondente já nasce junto** (RN-SEG-05) — não há uma segunda etapa separada. A listagem permite busca e filtro por status, e mostra, por Segmento, o **status do Funil** associado, com um atalho para a página de configuração dele. A edição cobre todos os campos, incluindo o código.

**Regras específicas:**

**RN-SEG-01 — Cadastro livre do tenant.** Segmentos não são um catálogo fixo do sistema. Cada tenant cria e mantém os seus, com os nomes e códigos que fizerem sentido para a sua operação.

**RN-SEG-05 — O Funil nasce automaticamente com o Segmento.** *(Revoga a antiga RN-SEG-03, que previa o oposto.)* Ao criar um Segmento, o sistema cria, na mesma operação, o Funil de Vendas correspondente — já populado com o conjunto de stages sugerido (Novo, Negociação, Formalização, Venda, Desqualificado, cobrindo os cinco Tipos exigidos por RN-FNL-15 — Novo, Negociação, Formalização, Pago e Cancelado; Venda corresponde ao tipo Pago e Desqualificado ao tipo Cancelado, RN-FNL-03), em status **"Aguardando configuração"**. O gestor pode usar a estrutura sugerida como está, ajustá-la, ou reconfigurá-la por completo — mas o Funil já existe e é válido desde o primeiro instante, nunca fica num estado intermediário inválido.

**RN-SEG-06 — Inativar o Segmento cascateia para o Funil, apenas se ele estiver Ativo — e o usuário é informado antes.** Inativar um Segmento **nunca é bloqueado**. Se o Funil associado está com status **Ativo**, ele **também é inativado** no mesmo momento — iniciando a drenagem das leads em curso, conforme já definido em RN-FNL-14. Os demais status do Funil (Aguardando configuração, Drenando, Inativo) **não são alterados** pela inativação do Segmento — a cascata só existe nesse único sentido, de Ativo para Inativo.

A ação **não é silenciosa**: a confirmação de inativar o Segmento informa explicitamente, antes de aplicar, que o Funil também será inativado e o que isso significa (ver Microcopy).

**RN-SEG-07 — Reativar o Segmento nunca reativa o Funil automaticamente; oferece a opção.** Ao reativar um Segmento cujo Funil não está em "Aguardando configuração" (ou seja, já passou por algum ciclo de uso), a interface **oferece** — não aplica sozinha — a opção de reativar o Funil junto. O gestor pode preferir revisar a configuração do Funil antes de reabri-lo, então a decisão é sempre explícita. Se o Funil está em "Aguardando configuração", não há o que oferecer: ele nunca foi ativado, e reativar o Segmento não muda nada nele.

**RN-SEG-04 — Exclusão bloqueada se o Funil já foi ativado ou se houver Oferta; senão, exclui os dois em cascata.** Diferente da versão anterior desta regra, o bloqueio não depende de "ter funil configurado" (todo Segmento tem). Há **duas causas**: o Funil **já ter sido ativado alguma vez** e a existência de **Oferta** que referencie o Segmento (RN-SEG-08). Pelo Funil:

| Status do Funil | Efeito ao excluir o Segmento |
|---|---|
| **Aguardando configuração** (nunca foi ativado) | Exclusão **permitida, se nenhuma Oferta referencia o Segmento** (RN-SEG-08) — o Funil é excluído junto, em cascata, e as associações com Lojas e Times são removidas (RN-SEG-09). Exceção pontual à regra geral de que Funil ativado nunca se exclui (RN-FNL-09) — aqui ele nunca chegou a ser usado. |
| **Ativo, Drenando, ou Inativo** (já foi ativado) | Exclusão **bloqueada**. O gestor precisa lidar com o Funil primeiro — mas como Funil não tem exclusão própria (RN-FNL-09), na prática isso significa que um Segmento cujo Funil já circulou **nunca mais pode ser excluído**, só inativado. |

**RN-SEG-08 — Oferta bloqueia a exclusão do Segmento e impede a escolha de Segmento inativo.** Um Segmento referenciado por ao menos uma Oferta (campo Segmento, RN-OFR-08) **não pode ser excluído**, mesmo que o Funil nunca tenha sido ativado: a Oferta ficaria com a referência pendurada. O gestor precisa primeiro remover ou reatribuir as Ofertas, no módulo de Ofertas. Um Segmento **Inativo** não pode ser escolhido em **novas** Ofertas (nem ao reatribuir uma existente); as Ofertas que já o tinham mantêm o Segmento. Inativar o Segmento não é bloqueado por Ofertas (RN-SEG-06). *(Decisão do usuário, na revisão de Ofertas.)*

**RN-SEG-09 — Excluir o Segmento remove as associações com Lojas e Times.** Um Segmento pode constar na flag "Ativo" e nos tipos de lead de Lojas (aba Segmentos da Loja) e no campo Segmento de Times. Como a exclusão só é possível quando o Funil nunca foi ativado (RN-SEG-04) — logo nenhuma lead passou por ele —, essas associações são apenas configuração: a exclusão as **remove** (a Loja deixa de listar o Segmento; os Times que o tinham ficam **sem Segmento**, estado que já existe, RN-LOJ-02), com aviso prévio do impacto (quantas Lojas e quantos Times). Não há bloqueio por Loja nem por Time. *(Decisão do usuário, na revisão de Lojas.)*

---

# 5. Regras de negócio (consolidado)

- **RN-SEG-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-SEG-04** — Exclusão bloqueada se o Funil já foi ativado ou se houver Oferta; senão, cascata de exclusão.
- **RN-SEG-05** — O Funil nasce automaticamente com o Segmento, populado e em "Aguardando configuração".
- **RN-SEG-06** — Inativar o Segmento cascateia para o Funil só se Ativo, com aviso explícito.
- **RN-SEG-07** — Reativar o Segmento oferece (não aplica) reativar o Funil, quando aplicável.
- **RN-SEG-08** — Oferta bloqueia a exclusão do Segmento; Segmento inativo não pode ser escolhido em novas Ofertas.
- **RN-SEG-09** — Excluir o Segmento remove as associações com Lojas e Times (os Times ficam sem Segmento).

**Revogada na v0.3:** RN-SEG-02 (bloqueio de inativação/exclusão por "funil configurado") e RN-SEG-03 (Funil não nasce automaticamente) — ambas substituídas pelo modelo de cascata acima.

---

# 6. Estados e transições

**Segmento:**
- **Ativo** — Funil correspondente pode estar em qualquer status.
- **Inativo** — mesma observação; o status do Segmento não implica nada sobre o status do Funil, exceto no instante da própria transição (RN-SEG-06, RN-SEG-07). Não pode ser escolhido em novas Ofertas (RN-SEG-08).

Transições: ativo → inativo (sempre livre; cascateia ao Funil se Ativo, RN-SEG-06); inativo → ativo (sempre livre; oferece reativar o Funil, RN-SEG-07); ativo/inativo → excluído (bloqueada se o Funil já foi ativado ou se houver Oferta; senão, cascata — RN-SEG-04, RN-SEG-08 e RN-SEG-09).

---

# 7. Limites do módulo

Este módulo trata apenas do cadastro do Segmento em si. **Não** inclui: a configuração do Funil (módulo Funis de Vendas — este módulo só dispara a criação automática e oferece o atalho), nem a escolha de quais Segmentos uma Loja opera e quais tipos de lead recebe deles e de qual Segmento um Time tem acesso (domínio de Lojas e de Times; por serem 1:1, equivale a escolher o Funil do Segmento). Este módulo só cadastra o Segmento.

---

# 8. Dependências e o que o módulo expõe

**Depende de:** o módulo de Funis de Vendas, para criar o Funil correspondente no momento do cadastro (RN-SEG-05) e para o atalho de configuração na listagem.

**Expõe para:**
- **Funis de Vendas** — o evento de criação de Segmento, que dispara a criação do Funil.
- **Lojas e Times (Admin e CRM)** — a lista de Segmentos, para a flag Ativo e os tipos de lead por Segmento (Lojas; aba Segmentos de Configurações, no CRM) e para o Segmento do Time. Na exclusão do Segmento, essas associações são removidas (RN-SEG-09).
- **Ofertas** — a lista de Segmentos ativos, para o campo Segmento (RN-OFR-08).
- **Leads / pipeline (CRM)** — indiretamente, através do Funil.

---

# 9. Itens abertos

1. **Código editável e integrações.** O código é editável após a criação. Se alguma integração externa vier a referenciar Segmento por código, editá-lo passa a ser uma ação de risco — vale reavaliar quando as integrações do produto forem definidas.

---

*Fim do documento. Segmentos v0.8 — módulo da aplicação Admin, seção Cadastros. Matriz de Permissões em documento companheiro.*
