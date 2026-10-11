# Corban Admin · Regras

**Status:** Draft
**Aplicação:** Admin
**Seção:** Oferta comercial
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.9
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Quatro mudanças: (1) operadores **Igual a** e **Diferente de** passam a servir campos numéricos **e** de texto; (2) Regra **sem requisito é permitida**; (3) **Regra não tem inativação nem status** — o ciclo de vida é criar/editar/excluir, e **não há bloqueio de exclusão**; (4) excluir uma Regra a **desvincula em lote** de todas as Ofertas, mediante alerta. |
| 0.3 | Correção: **Regra sem requisito não existe** — mínimo de um requisito é obrigatório para salvar. A v0.2 registrou o oposto, por interpretação errada da decisão. |
| 0.4 | O alerta de exclusão passa a comunicar a **consequência**, não só a contagem de Ofertas — com texto distinto para Regra bloqueante e de aviso (RN-RGR-10). Sem itens abertos. |
| 0.5 | Regra ganha o campo **Mensagem de erro** — o texto que o operador vê na simulação quando o cliente não atende à condição. Substitui o nome da Regra como motivo exibido (RN-RGR-11). |
| 0.6 | **Reformulação de fundo.** Requisito deixa de carregar Valor — passa a ser só **Campo + Operador** (um template). O(s) valor(es) de cada requisito passam a ser definidos **por Oferta**, no momento da associação (RN-RGR-12). **Comportamento vira padrão**, sobrescrevível por Oferta (RN-RGR-05 revogada e substituída). Nova regra sobre alterar o template de um requisito já usado por Ofertas (RN-RGR-13). Mensagem de erro passa a ser um item aberto — texto estático pode não refletir o valor real de cada Oferta. |
| 0.7 | RN-RGR-13 ganha bloqueio parcial: editar Campo/Operador de um requisito é **bloqueado** se alguma Oferta que o usa estiver **Ativa** — permanece só aviso (não bloqueio) quando as Ofertas associadas estão Inativas. Fecha o item aberto correspondente no módulo de Ofertas. |
| 0.8 | **A mensagem de erro passa a ser a explicação geral da Regra, sem valores** (RN-RGR-11): os valores vivem na Oferta (RN-RGR-12) e variam entre elas, então a mensagem não pode citá-los. Quem mostra o valor é a Simulação, listando os requisitos que falharam com o valor da Oferta (Simulações v0.10, RN-SIM-11), o que também resolve a dúvida de "qual requisito falhou" numa Regra de vários requisitos. Fecha o §9. Corrige resíduos: RF-RGR-01 (dizia "inativar" e "filtro por status", que a Regra não tem); §3.1 e RN-RGR-07 ("diferente de todos os demais cadastros": Origem de Venda também não tem status, e Regiões, Segmentos e Status de Proposta também não bloqueiam a exclusão); RN-RGR-02 (são três tipos de campo, não dois). Reordena esta tabela. |
| 0.9 | **A mensagem de erro passa a ter dois públicos.** Além do operador, na Simulação (CRM), quem lê o motivo é o Gestor de Marketing, na pré-análise em lote (Marketing, Prospecção RF-PRS-03 e Carteira Ativa), onde a mensagem explica por que um contato foi **Descartado**. A RN-RGR-11 e o texto do campo acompanham; o conteúdo da mensagem não muda. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-RGR) e regras de negócio (RN-RGR) próprios.

> **Este módulo é o mais denso da série.** Diferente dos demais cadastros — que são nome, código e status —, uma Regra carrega uma estrutura interna: uma lista de requisitos, cada um com campo, operador e valores. É a razão de ele ter sido separado dos Cadastros simples.

---

# 1. Objetivo do módulo

Manter as condições de elegibilidade que determinam se um cliente pode ou não receber uma oferta. As Regras são cadastradas uma vez e reutilizadas por quantas Ofertas fizer sentido — o que evita recriar a mesma condição a cada oferta nova.

---

# 2. Atores

- **Gestor comercial** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`.
- **Operador** — consome as Regras indiretamente: ao simular, vê o resultado da avaliação (elegível, elegível com alerta, ou não elegível com o motivo).
- **Gestor de Marketing** — lê a mesma mensagem de erro na pré-análise em lote, como motivo de descarte de um contato ou Cliente (Marketing).

---

# 3. Entidades e conceitos

## 3.1. Regra

Condição de elegibilidade reutilizável. Cadastro **livre do tenant**.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece na associação com Ofertas e nos motivos de desqualificação (ex.: "Idade INSS", "Sem atraso relevante"). |
| Descrição | Não | Texto curto explicando o que a regra verifica. |
| Comportamento padrão | Sim | **Bloqueia** ou **Avisa** — ver §3.5. É o valor inicial; cada Oferta pode sobrescrevê-lo na associação. |
| Mensagem de erro | Sim | Explicação **geral**, sem valores, exibida quando o cliente não atende à Regra: ao operador, na Simulação, e ao Gestor de Marketing, na pré-análise em lote. Os valores variam por Oferta, e quem os mostra é a Simulação — ver RN-RGR-11. |
| Requisitos | Sim | **Ao menos um.** Não existe Regra sem requisito — ver RN-RGR-06. Cada requisito aqui é só **Campo + Operador** — sem valor (RN-RGR-12). |

**Sem código** — a Regra é referenciada pelo nome, não por integração.

**Sem status (ativo/inativo).** Diferente da maioria dos cadastros do produto (só Origem de Venda é igual), Regra **não tem inativação**. O ciclo de vida é criar → editar → excluir, sem estado intermediário (§6).

## 3.2. Requisito

Cada Regra é composta por um ou mais requisitos. O requisito, aqui, é **o formato da condição** — não a condição completa:

| Elemento | O que é |
|---|---|
| **Campo** | O que será avaliado (ver §3.3) |
| **Operador** | Como comparar — catálogo fixo do sistema |

**RN-RGR-12 — O valor do requisito é definido pela Oferta, não pela Regra.** A Regra define **Campo + Operador** — o formato, ou template, do requisito. O(s) **valor(es)** — o parâmetro concreto da comparação (ex.: o "18" de "idade ≥ 18") — são preenchidos **em cada Oferta**, no momento em que ela associa aquela Regra (módulo de Ofertas). A mesma Regra "Idade mínima" (Campo: Idade, Operador: Maior ou igual) serve a uma Oferta que exige 18 anos e a outra que exige 21, sem precisar duplicar a Regra.

*Consequência: uma Regra, sozinha, não é uma condição avaliável — só vira uma ao ser associada a uma Oferta, que fornece os valores que faltam. É por isso que o módulo de Ofertas, não este, é quem efetivamente "fecha" a condição.*

**RN-RGR-01 — Todos os requisitos precisam passar.** A lista de requisitos é **plana** e sempre conjuntiva: o cliente só atende à Regra se atender a **todos** os seus requisitos. Não há operador lógico configurável, não há agrupamento e **não há aninhamento** de condições.

*Consequência prática: para expressar uma alternativa ("idade acima de 60 **ou** margem acima de 500"), o gestor cria duas Regras separadas e as associa às Ofertas conforme o caso — não há como fazê-lo dentro de uma Regra.*

## 3.3. Catálogo de campos

Os campos são **fixos do sistema**, organizados em quatro origens. O tenant não cria campos novos.

**Dados do cliente**
Margens · Sexo · Idade · Tempo de emprego

**Dados da empresa empregadora**
Idade da empresa · Número de funcionários

**Dados de contratos pré-existentes**
Nº de contratos · Parcelas a vencer · Parcelas atrasadas · Valor em atraso · Valor a vencer · Quantidade de parcelas pagas · Valor total a vencer · Valor da parcela

**Dados de aceitação do banco**
Taxa de juros · Parcela mínima · Bancos não aceitos (portabilidade) · Troco mínimo · Margem mínima · Taxa mínima

**RN-RGR-02 — Cada campo tem um tipo, e o tipo determina os operadores disponíveis.** Os campos são numéricos, de texto ou de lista. Ao escolher o campo, o seletor de operador oferece apenas os compatíveis:

| Tipo do campo | Operadores oferecidos |
|---|---|
| Numérico | Intervalo · Maior que · Maior ou igual · Menor que · Menor ou igual · **Igual a** · **Diferente de** |
| Texto | **Igual a** · **Diferente de** |
| Lista | Deve conter · Não deve conter |

**Igual a** e **Diferente de** servem aos dois primeiros tipos — são os únicos operadores compartilhados. O que muda é a forma do campo de valor: numérico aceita número, texto aceita a opção correspondente (ex.: **Sexo**, campo de texto, comparado por igualdade).

*O campo "Bancos não aceitos (portabilidade)" é o caso de lista.*

**RN-RGR-03 — Bancos vêm da API do Banco Central.** O campo "Bancos não aceitos" tem seus valores populados pela API do BC, não por cadastro interno — não há cadastro de Bancos no produto. O seletor de valor desse campo é, portanto, diferente dos demais.

## 3.4. Operadores

Catálogo **fixo do sistema**. O tenant não cria nem edita operadores.

| Operador | Tipo | Valores esperados |
|---|---|---|
| Intervalo | Numérico | Dois — mínimo e máximo |
| Maior que | Numérico | Um |
| Maior ou igual que | Numérico | Um |
| Menor que | Numérico | Um |
| Menor ou igual que | Numérico | Um |
| Igual a | Numérico ou texto | Um |
| Diferente de | Numérico ou texto | Um |
| Deve conter | Lista | Um ou mais |
| Não deve conter | Lista | Um ou mais |

**RN-RGR-04 — O operador determina a forma do campo de valor.** Um operador de valor único exige um campo; Intervalo exige dois (mínimo e máximo); operadores de lista exigem seleção múltipla. Essa forma é a que aparece **na tela de Ofertas**, quando o gestor preenche o valor — não existe mais campo de valor na tela de Regras (RN-RGR-12).

## 3.5. Comportamento — Bloqueia ou Avisa

**RN-RGR-05 — O comportamento da Regra é um padrão, sobrescrevível por Oferta.** *(Revoga a versão anterior desta regra, que o tornava fixo e imutável por associação.)* Toda Regra define um comportamento padrão (Bloqueia ou Avisa), usado quando a Oferta não o altera. Cada Oferta, ao associar a Regra, pode **sobrescrever** esse padrão para o seu próprio caso — a mesma Regra pode bloquear numa Oferta e apenas avisar em outra.

| Comportamento | Efeito na simulação |
|---|---|
| **Bloqueia** | Cliente que não atende fica **não elegível** para a oferta. A oferta aparece no resultado sinalizada, exibindo a **mensagem de erro** da Regra como motivo da desqualificação. |
| **Avisa** | Cliente que não atende **permanece elegível**. A oferta aparece com alerta exibindo a **mensagem de erro** da Regra. |

*Isso elimina a necessidade de duplicar Regras só para variar o comportamento entre Ofertas — o motivo que justificava a versão anterior (rastreabilidade — "olhar a Regra e saber o que ela faz") passa a valer só para o padrão; o efetivo, por Oferta, é visível na tela de associação daquela Oferta.*

---

# 4. Requisitos funcionais

## RF-RGR-01 — Gestão de regras (CRUD)

**Como** gestor comercial, **eu quero** criar, consultar, editar e excluir Regras **para** controlar quais clientes se qualificam para cada oferta.

**Comportamento esperado:** o gestor cadastra nome, descrição, comportamento padrão e a lista de requisitos (cada um só Campo + Operador, sem valor — RN-RGR-12). A listagem permite busca por nome; mostra, por Regra, a quantidade de requisitos e de Ofertas que a utilizam. A edição cobre todos os campos, inclusive a composição de requisitos.

**Regras específicas:**

**RN-RGR-06 — Regra exige ao menos um requisito.** Não existe Regra sem requisito. O formulário não permite salvar enquanto a lista estiver vazia, nem remover o último requisito de uma Regra existente.

*Isso fecha, por construção, o caso da Regra vacuamente verdadeira: se "atender à Regra" é "atender a todos os requisitos" (RN-RGR-01), uma Regra vazia aprovaria qualquer cliente — associá-la a uma Oferta pareceria restringir sem restringir nada. Exigir o mínimo de um requisito elimina esse estado silencioso.*

**RN-RGR-07 — Exclusão não é bloqueada; desvincula em lote.** Diferente de Produtos e Promotoras, onde uma Oferta que usa o cadastro impede excluir, **nenhum vínculo impede excluir uma Regra** — como em Regiões, a exclusão remove os vínculos. Ao excluir, ela é automaticamente **desvinculada de todas as Ofertas** que a utilizavam, em lote — o gestor não precisa desassociar uma a uma. A ação exibe alerta explícito informando quantas Ofertas serão afetadas antes de confirmar.

**Consequência:** as Ofertas afetadas passam a avaliar uma condição a menos. A gravidade depende do comportamento **efetivo em cada Oferta** — o padrão da Regra, ou o que aquela Oferta sobrescreveu (RN-RGR-05) — não de um único comportamento fixo da Regra. Ver RN-RGR-10.

**RN-RGR-10 — O alerta de exclusão comunica a consequência, não só a contagem.** Informar "esta regra está em N ofertas" descreve o alcance, mas não o efeito. O alerta precisa dizer **o que muda**, agrupando as Ofertas afetadas pelo comportamento **efetivo** de cada uma (padrão da Regra ou sobrescrito, RN-RGR-05):

| Comportamento efetivo na Oferta | O que muda | Gravidade |
|---|---|---|
| **Bloqueia** | Clientes que hoje são desqualificados por esta condição, naquela Oferta, **passam a se qualificar**. | Alta — a exclusão **amplia** o alcance comercial sem que ninguém tenha decidido isso |
| **Avisa** | Um alerta deixa de ser exibido na simulação, naquela Oferta. A elegibilidade não muda. | Baixa — nenhum cliente muda de situação |

Se as Ofertas afetadas tiverem comportamentos efetivos diferentes entre si (algumas bloqueando, outras avisando — possível agora que o comportamento é sobrescrevível), o alerta apresenta os dois grupos separadamente, com a contagem de cada um. O texto é redigido na Microcopy Library deste módulo.

**RN-RGR-11 — A mensagem de erro é a explicação geral que quem avalia o cliente lê, sem valores.** Nome e descrição são para o gestor, dentro do Admin; a **mensagem de erro** é para quem lê o resultado da avaliação: o **operador**, na Simulação, e o **Gestor de Marketing**, na pré-análise em lote, onde ela é o motivo de um contato ser **Descartado** (Marketing, RF-PRS-03). É ela que aparece como motivo quando o cliente não atende à Regra — não o nome da Regra.

A distinção importa porque os dois públicos precisam de coisas diferentes: "Idade INSS" identifica a regra para quem a configura; "Cliente não atende à idade mínima exigida pela oferta" explica a recusa para quem atende o cliente.

**A mensagem não cita valores.** Os valores dos requisitos são definidos por Oferta (RN-RGR-12) e variam entre elas: uma Regra "Idade mínima" pode exigir 18 anos numa Oferta e 21 noutra, e um texto como "pelo menos 18 anos" estaria errado na segunda. Quem mostra o valor é a **Simulação**: junto da mensagem, ela lista os **requisitos que falharam, com o valor da Oferta** (módulo de Simulações, RN-SIM-11). *(Decisão desta revisão.)*

**Uma mensagem por Regra, não por requisito.** Mesmo com vários requisitos, a Regra tem uma única mensagem, que cobre a condição como um todo. Qual requisito falhou **não** é papel da mensagem: a Simulação o indica na lista de requisitos que falharam.

**RN-RGR-08 — Editar uma Regra em uso afeta as Ofertas imediatamente.** Alterar o comportamento padrão de uma Regra vale para **todas** as Ofertas que a utilizam e não o sobrescreveram, a partir daquele momento. Não há efeito retroativo sobre simulações já realizadas — cada simulação guarda snapshot imutável das condições do momento.

**RN-RGR-13 — Alterar o template de um requisito é bloqueado se houver Oferta Ativa; permitido, com aviso, se só houver Inativas.** Mudar o **Campo** ou o **Operador** de um requisito invalida os valores que as Ofertas associadas já tinham preenchido para ele (RN-RGR-12) — um valor único para "Maior que" não serve se o operador vira "Intervalo", por exemplo.

| Situação das Ofertas associadas | Comportamento |
|---|---|
| Nenhuma Oferta associada | Edição livre. |
| Só Ofertas **Inativas** associadas | Edição permitida, com confirmação explícita informando quantas Ofertas serão afetadas e que os valores delas precisarão ser preenchidos de novo. |
| **Ao menos uma Oferta Ativa** associada | **Edição bloqueada.** O gestor precisa inativar aquela(s) Oferta(s) primeiro, no módulo de Ofertas, antes de poder alterar o formato do requisito aqui. |

**Por que o critério é "Ativa", não "qualquer associação".** Bloquear sempre que há qualquer Oferta associada tornaria uma Regra popular quase impossível de manter. O risco real que motivou o bloqueio é mais específico: uma Oferta **em produção**, rodando simulações, ficar com uma condição sem valor sem que ninguém perceba. Ofertas Inativas não têm esse risco — o aviso já basta.

Adicionar um requisito novo, ou remover um existente (respeitando RN-RGR-06), segue a mesma tabela.

**RN-RGR-09 — Exclusão é o único caminho de descontinuação.** Sem inativação, quem quer tirar uma Regra de circulação a exclui. Não há estado intermediário nem forma de "guardar" uma Regra fora de uso.

## RF-RGR-02 — Composição de requisitos

**Como** gestor comercial, **eu quero** montar a lista de requisitos de uma Regra **para** definir o formato da condição de elegibilidade que ela verifica.

**Comportamento esperado:** dentro da Regra, o gestor adiciona requisitos em sequência. Para cada um: escolhe o **campo** (de uma lista agrupada pelas quatro origens), depois o **operador** (filtrado pelo tipo do campo, RN-RGR-02). **Não há campo de valor aqui** — a forma que o valor vai ter (um campo, dois, ou seleção múltipla) já fica definida pelo operador escolhido (RN-RGR-04), mas o preenchimento em si acontece em cada Oferta (RN-RGR-12). Requisitos podem ser removidos individualmente; a ordem entre eles não tem significado, já que todos precisam passar.

---

# 5. Regras de negócio (consolidado)

- **RN-RGR-01** — Lista de requisitos plana e conjuntiva; todos precisam passar.
- **RN-RGR-02** — Cada campo tem um tipo; o tipo determina os operadores oferecidos.
- **RN-RGR-03** — Valores do campo "Bancos não aceitos" vêm da API do BC.
- **RN-RGR-04** — O operador determina a forma do campo de valor (usada na tela de Ofertas).
- **RN-RGR-05** — Comportamento é um padrão da Regra, sobrescrevível por Oferta.
- **RN-RGR-06** — Regra exige ao menos um requisito; não existe Regra vazia.
- **RN-RGR-07** — Exclusão não é bloqueada; desvincula de todas as Ofertas em lote, com alerta.
- **RN-RGR-08** — Editar comportamento padrão em uso afeta Ofertas que não sobrescreveram, sem efeito retroativo.
- **RN-RGR-09** — Exclusão é o único caminho de descontinuação; não há inativação.
- **RN-RGR-10** — O alerta de exclusão comunica a consequência por grupo de comportamento efetivo.
- **RN-RGR-11** — A mensagem de erro é a explicação geral exibida ao operador (Simulação) e ao Gestor de Marketing (pré-análise), sem valores; uma por Regra. Qual requisito falhou, e com que valor, quem mostra é a Simulação (RN-SIM-11).
- **RN-RGR-12** — O valor do requisito é definido pela Oferta, não pela Regra; a Regra define só Campo + Operador.
- **RN-RGR-13** — Alterar template de requisito: bloqueado se houver Oferta Ativa associada; aviso, sem bloqueio, se só Inativas.

---

# 6. Estados e transições

**Regra não tem estados.** Existe ou não existe — sem ativo/inativo, sem rascunho. É o segundo módulo do produto assim, ao lado de Origem de Venda.

Transições: (inexistente) → criada; criada → excluída, com desvínculo em lote das Ofertas (RN-RGR-07).

**Por que sem inativação.** Nos demais cadastros, inativar serve para tirar algo de circulação preservando o histórico. Aqui isso não é necessário: as simulações já realizadas guardam snapshot imutável das condições do momento (RN-RGR-08), então excluir uma Regra não altera nenhum histórico — só afeta as avaliações futuras.

---

# 7. Limites do módulo

Trata do cadastro da Regra e da composição do **formato** de seus requisitos (Campo + Operador). **Não** inclui: o preenchimento dos valores dos requisitos, nem a sobrescrita de comportamento (ambos no módulo de Ofertas, no momento da associação), a avaliação em si durante a simulação (módulo de Simulações), nem o catálogo de campos, que é fixo do sistema.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **API do Banco Central** — valores do campo "Bancos não aceitos" (RN-RGR-03).

**Expõe para:**
- **Ofertas** — a lista de Regras (com seus requisitos em formato Campo+Operador, sem valor) para associação; é lá que o valor de cada requisito e a eventual sobrescrita de comportamento são definidos (RN-RGR-12, RN-RGR-05). Excluir uma Regra desvincula-a de todas elas (RN-RGR-07).
- **Marketing** (Prospecção e Carteira Ativa) — a definição completa da condição, para a pré-análise em lote, e a **mensagem de erro**, exibida como motivo de descarte (RN-RGR-11).
- **Simulações** — a definição completa da condição (formato da Regra + valores da Oferta), para avaliar contra os dados do cliente; e a **mensagem de erro**, exibida como motivo junto dos requisitos que falharam (RN-RGR-11, RN-SIM-11).

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Regras v0.9 — módulo da aplicação Admin.*
