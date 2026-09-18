# Corban Admin · Origem de Venda

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.4
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                  |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                           |
| 0.3    | Define os escopos iniciais.                                                                                                                                                               |
| 0.4    | Ajusta os escopos: **`view` sem permissão** (acesso universal), `create` em `all`/`store`, **`delete` apenas em `all`** — excluir afeta todas as Lojas e não deve caber a escopo de loja. |
| 0.2    | Fecha o comportamento da exclusão: a lead mantém o rótulo como texto, desvinculado do cadastro (RN-ORI-04).                                                                               |

## Como usar este documento

Doc de módulo: entidade, requisitos funcionais (RF-ORI) e regras de negócio (RN-ORI) próprios.

> **Este módulo é diferente dos demais Cadastros.** Origem de Venda **não tem página própria** nem destino de navegação. Ela existe exclusivamente como um combobox dentro do cadastro de Lead, onde é consultada, criada e excluída sem sair daquele fluxo. Por isso este módulo tem um conjunto reduzido de artefatos — não há Lista, não há Detalhe, não há Mapa de Telas no sentido usual.

---

# 1. Objetivo do módulo

Manter o rótulo que identifica por onde uma venda chegou. É um cadastro auxiliar, gerido no ponto de uso: o operador que cadastra uma lead e não encontra a origem certa na lista cria ali mesmo, sem interromper o atendimento.

---

# 2. Atores

- **Quem cadastra leads** — usuário com acesso ao cadastro de Lead. É quem consome, cria e exclui Origens de Venda, no combobox.

Não há um "gestor de cadastros" separado: o módulo não tem tela administrativa própria.

---

# 3. Entidades e conceitos

**Origem de Venda.** Rótulo que identifica a procedência de uma lead. Cadastro **livre do tenant**, criado no ponto de uso.

| Campo | Obrigatório | Notas                    |
| ----- | ----------- | ------------------------ |
| Nome  | Sim         | Único campo da entidade. |

**Sem código, sem descrição, sem status.** Deliberado:

- **Sem código** — não há integração nem referência externa que justifique um identificador curto.
- **Sem descrição** — o nome é autoexplicativo por natureza ("Indicação", "Facebook", "Porta a porta").
- **Sem status (ativo/inativo)** — não faz sentido inativar algo que se exclui diretamente do combobox. O ciclo de vida é criar/excluir, sem estado intermediário.

**Onde vive.** Combobox no cadastro de Lead (módulo de Leads, CRM). Nenhuma outra tela do produto consome Origem de Venda hoje.

---

# 4. Requisitos funcionais

## RF-ORI-01 — Consultar, criar e excluir Origem de Venda no ponto de uso

**Como** usuário cadastrando uma lead, **eu quero** escolher a origem da venda numa lista com busca — e criar ou excluir uma origem ali mesmo — **para** registrar a procedência correta sem interromper o cadastro.

**Comportamento esperado:** o campo é um **Combobox** (componente do Design System) na variante **criável**, com seleção única:

- **Consultar** — a lista mostra as Origens existentes, filtráveis por texto conforme a digitação.
- **Criar** — quando a busca não retorna resultado, a lista exibe a ação "Criar nova: '[termo digitado]'". Ao acionar, a Origem é criada e já fica selecionada na lead em edição.
- **Excluir** — cada item da lista oferece uma ação de excluir, disparando confirmação (RN-ORI-02).

**Regras específicas:**

**RN-ORI-01 — Sem bloqueio de exclusão.** Diferente dos demais cadastros da seção, Origem de Venda **não tem vínculo bloqueante**: pode ser excluída a qualquer momento, mesmo estando em uso por leads existentes.

**RN-ORI-04 — Exclusão preserva o histórico como texto.** Ao excluir uma Origem, as leads que a referenciavam **mantêm o rótulo como texto**, desvinculado do cadastro. O histórico daquelas leads continua legível — a lead que veio de "Indicação" continua dizendo "Indicação" —, mas o valor deixa de existir como opção selecionável e vira dado solto.

Consequências a considerar na implementação:

- O rótulo órfão **não volta a se vincular** se alguém criar depois uma Origem com o mesmo nome. Serão duas coisas distintas: o texto histórico e o novo cadastro.
- Relatórios e filtros que agrupam por Origem precisam decidir se tratam o texto órfão como categoria própria ou o agregam ao cadastro homônimo, caso exista. **Decisão do domínio de Relatórios**, registrada aqui como dependência.

**RN-ORI-02 — Exclusão exige confirmação reforçada por digitação.** Apesar de não haver bloqueio, a exclusão é definitiva e feita de dentro de um fluxo de trabalho — o risco de acionamento acidental é alto. Por isso usa o nível mais forte de confirmação do produto: dialog exigindo que o usuário **digite o nome exato** da Origem para liberar o botão.

**RN-ORI-05 — Origem é tenant-wide; o acesso é assimétrico por ação.** A Origem de Venda **não pertence a Loja nenhuma** — é cadastro único do tenant. As três ações têm níveis de acesso deliberadamente diferentes:

| Ação          | Acesso                   | Por quê                                                                                  |
| ------------- | ------------------------ | ---------------------------------------------------------------------------------------- |
| **Consultar** | Universal, sem permissão | O campo vive no cadastro de lead; esconder a lista produziria leads sem origem           |
| **Criar**     | Escopo `all` ou `store`  | Criação acontece no meio do atendimento; exigir `all` quebraria o fluxo                  |
| **Excluir**   | Apenas escopo `all`      | Excluir afeta todas as Lojas, inclusive as que usam a Origem — não cabe a escopo de loja |

Consequência a registrar: um usuário com `create:store` cria Origens que **todas as Lojas passam a ver**. O escopo qualifica quem age, não restringe o dado. Já em `delete`, o `store` não é oferecido justamente para evitar o inverso — alguém de uma loja apagando algo que outra usa.

**RN-ORI-03 — Nome único no tenant.** Não existem duas Origens de Venda com o mesmo nome. A tentativa de criar uma duplicata é impedida — na prática, o Combobox já teria encontrado a existente na busca, então este caso é raro.

---

# 5. Regras de negócio (consolidado)

- **RN-ORI-01** — Sem bloqueio de exclusão; não há vínculo que impeça.
- **RN-ORI-02** — Exclusão exige confirmação reforçada por digitação.
- **RN-ORI-03** — Nome único no tenant.
- **RN-ORI-04** — Exclusão preserva o rótulo nas leads como texto desvinculado.
- **RN-ORI-05** — Origem é tenant-wide; escopo `store` qualifica quem age, não restringe o dado.

---

# 6. Estados e transições

Origem de Venda **não tem estados** — existe ou não existe. Sem ativo/inativo, sem rascunho.

Transições: (inexistente) → criada, pelo Combobox; criada → excluída, com confirmação por digitação.

---

# 7. Dependências e o que o módulo expõe

**Depende de:** nada.

**Expõe para:**

- **Leads (CRM)** — a lista de Origens, para o combobox do cadastro de lead. É o único consumidor.

---

# 8. Itens abertos

1. **Tratamento do rótulo órfão em Relatórios.** RN-ORI-04 deixa textos soltos no histórico das leads. Falta definir, no domínio de Relatórios, se um agrupamento por Origem trata esses textos como categoria própria ou os agrega a um cadastro de mesmo nome, quando existir.

---

_Fim do documento. Origem de Venda v0.4 — módulo da aplicação Admin, seção Cadastros. Sem página própria; vive no cadastro de Lead._
