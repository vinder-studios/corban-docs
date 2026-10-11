# Corban Admin · Origem de Venda

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros *(sem destino de navegação: o módulo vive no cadastro de Lead)*
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.8
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha o comportamento da exclusão: a lead mantém o rótulo como texto, desvinculado do cadastro (RN-ORI-04). |
| 0.3 | Define os escopos iniciais. |
| 0.4 | Ajusta os escopos: **`view` sem permissão** (acesso universal), `create` em `all`/`store`, **`delete` apenas em `all`** — excluir afeta todas as Lojas e não deve caber a escopo de loja. |
| 0.5 | **Reconcilia o módulo com o resto do produto.** Origem de Venda deixa de ser "o único consumidor é Leads": Relatórios também a consome, e o campo passa a ser **opcional** na lead (RN-ORI-06; leads automáticas nascem sem ela). Distingue a Origem de Venda da **Origem da lead** (campo do sistema, módulo de Leads) e da Tag de campanha (Funis). Fecha o item aberto do rótulo órfão (Relatórios agrupam pelo rótulo, RN-REL-17), define o caso da Origem excluída enquanto selecionada, e a RN-ORI-03 passa a ignorar maiúsculas e espaços nas pontas. Reordena esta tabela. |
| 0.6 | **A Origem de Venda passa a ter lugar no Sheet da Lead**: campo editável do bloco Dados de Marketing (Layout de Lead v0.6, RN-LAY-07), que antes não existia em nenhum bloco. Corrige a RN-ORI-01 ("diferente dos demais cadastros da seção": Regiões e Status de Proposta também não bloqueiam; Produtos, Promotoras e Segmentos bloqueiam), anota que o módulo não tem destino de navegação e cita o Layout de Lead entre os consumidores. (Levantamento do Admin, segunda passada.) |
| 0.7 | **A Proposta passa a ser consumidora**: tem o seu próprio campo Origem de Venda (a Tag "Origem"), copiado da Lead na criação e independente depois; capturado no cadastro manual e na importação de Propostas (decisão do usuário, levantamento do CRM). §3 e §7 acompanham. |
| 0.8 | **Acompanha o Marketing (decisão do usuário).** (1) **Corrige a RN-ORI-06:** as Leads de Prospecção e Carteira Ativa deixam de nascer sem Origem de Venda e passam a herdar a da Lista ou da Varredura, copiada na injeção; as da Huggy e de landing page continuam sem. (2) **Nova RN-ORI-07:** a Lista de Prospecção e a Varredura guardam uma Origem de Venda opcional, no mesmo Combobox criável e com as mesmas permissões, e a exclusão de uma Origem preserva o rótulo como texto também nelas. (3) O Marketing (Prospecção, Carteira Ativa e o filtro do Dashboard) entra como consumidor em §3 e §7 |

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

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Único campo da entidade. |

**Sem código, sem descrição, sem status.** Deliberado:
- **Sem código** — não há integração nem referência externa que justifique um identificador curto.
- **Sem descrição** — o nome é autoexplicativo por natureza ("Indicação", "Facebook", "Porta a porta").
- **Sem status (ativo/inativo)** — não faz sentido inativar algo que se exclui diretamente do combobox. O ciclo de vida é criar/excluir, sem estado intermediário.

**Onde vive.** Combobox **opcional** no cadastro manual de Lead (módulo de Leads, CRM) e no bloco **Dados de Marketing** do Sheet da Lead (módulo de Layout de Lead, RN-LAY-07), onde é o **único campo editável** do bloco. Também na **Lista de Prospecção** e na **Varredura** (módulo de Marketing), de onde é copiada às Leads injetadas (RN-ORI-06, RN-ORI-07). Também no cadastro manual de **Proposta** e, como coluna opcional, na importação de Propostas (módulo de Propostas): a Origem de Venda da Proposta nasce copiada da Lead, quando há, e depois é **independente** dela. É lida também por Relatórios, que agrupam e filtram por ela (RN-REL-17). Apesar de documentado no Admin, o recurso `sale_origins` é exercido no CRM e no Marketing — mais um caso para a pendência de arquitetura das permissões (o catálogo precisa declarar a aplicação de cada recurso, RN-USR-16).

> **Não confundir com a Origem da lead.** São campos diferentes da Lead: a **Origem da lead** é do sistema (Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa ou Manual — módulo de Leads, RN-LED-12) e define se a lead cobra; a **Origem de Venda** é um rótulo livre do tenant, escolhido pelo operador ("Indicação", "Porta a porta"). Há ainda a Tag de categoria **Origem** de campanha de marketing (RN-FNL-06, módulo de Funis de Vendas).

---

# 4. Requisitos funcionais

## RF-ORI-01 — Consultar, criar e excluir Origem de Venda no ponto de uso

**Como** usuário cadastrando uma lead, **eu quero** escolher a origem da venda numa lista com busca — e criar ou excluir uma origem ali mesmo — **para** registrar a procedência correta sem interromper o cadastro.

**Comportamento esperado:** o campo é um **Combobox** (componente do Design System) na variante **criável**, com seleção única, **opcional** — pode ficar em branco, e quem selecionou uma Origem pode limpá-la (RN-ORI-06):

- **Consultar** — a lista mostra as Origens existentes, filtráveis por texto conforme a digitação.
- **Criar** — quando a busca não retorna resultado, a lista exibe a ação "Criar nova: '[termo digitado]'". Ao acionar, a Origem é criada e já fica selecionada na lead em edição.
- **Excluir** — cada item da lista oferece uma ação de excluir, disparando confirmação (RN-ORI-02).

**Regras específicas:**

**RN-ORI-01 — Sem bloqueio de exclusão.** Diferente de Produtos, Promotoras e Segmentos (cuja exclusão é bloqueada por vínculo), Origem de Venda **não tem vínculo bloqueante** — como Regiões (cascata) e Status de Proposta (migração): pode ser excluída a qualquer momento, mesmo estando em uso por leads existentes.

**RN-ORI-04 — Exclusão preserva o histórico como texto.** Ao excluir uma Origem, as leads que a referenciavam **mantêm o rótulo como texto**, desvinculado do cadastro. O histórico daquelas leads continua legível — a lead que veio de "Indicação" continua dizendo "Indicação" —, mas o valor deixa de existir como opção selecionável e vira dado solto.

Consequências a considerar na implementação:
- O rótulo órfão **não volta a se vincular** se alguém criar depois uma Origem com o mesmo nome. Serão duas coisas distintas: o texto histórico e o novo cadastro.
- **Relatórios e filtros agrupam pelo rótulo (o texto):** o texto órfão e um cadastro de mesmo nome caem na mesma linha (RN-REL-17, módulo de Relatórios).
- **Lead com rótulo órfão:** ao abrir a lead, o campo mostra o texto como valor atual, marcado "(origem excluída)". O usuário pode mantê-lo, trocá-lo ou limpá-lo.
- **Origem excluída enquanto selecionada numa lead em edição** (por quem edita, ou por outra pessoa enquanto a lead estava aberta): o campo volta ao vazio com um aviso, e a lead só é salva depois de escolher outra Origem ou deixar o campo em branco.

**RN-ORI-02 — Exclusão exige confirmação reforçada por digitação.** Apesar de não haver bloqueio, a exclusão é definitiva e feita de dentro de um fluxo de trabalho — o risco de acionamento acidental é alto. Por isso usa o nível mais forte de confirmação do produto: dialog exigindo que o usuário **digite o nome exato** da Origem para liberar o botão.

**RN-ORI-05 — Origem é tenant-wide; o acesso é assimétrico por ação.** A Origem de Venda **não pertence a Loja nenhuma** — é cadastro único do tenant. As três ações têm níveis de acesso deliberadamente diferentes:

| Ação | Acesso | Por quê |
|---|---|---|
| **Consultar** | Universal, sem permissão | O campo vive no cadastro de lead; esconder a lista impediria quem cadastra de usar o campo |
| **Criar** | Escopo `all` ou `store` | Criação acontece no meio do atendimento; exigir `all` quebraria o fluxo |
| **Excluir** | Apenas escopo `all` | Excluir afeta todas as Lojas, inclusive as que usam a Origem — não cabe a escopo de loja |

Consequência a registrar: um usuário com `create:store` cria Origens que **todas as Lojas passam a ver**. O escopo qualifica quem age, não restringe o dado. Já em `delete`, o `store` não é oferecido justamente para evitar o inverso — alguém de uma loja apagando algo que outra usa.

**RN-ORI-03 — Nome único no tenant.** Não existem duas Origens de Venda com o mesmo nome, **ignorando maiúsculas/minúsculas e espaços nas pontas** ("indicação" e "Indicação " são a mesma) *(decisão desta revisão)*. A tentativa de criar uma duplicata é impedida — na prática, o Combobox já teria encontrado a existente na busca, então este caso é raro.

**RN-ORI-06 — Origem de Venda é opcional na lead.** O campo pode ficar em branco, e quem o preencheu pode limpá-lo. Leads criadas automaticamente pela **Huggy** e por **landing page** nascem sem Origem de Venda; o operador pode preenchê-la depois, na ficha. As Leads do **Marketing** (Prospecção e Carteira Ativa) nascem com a Origem de Venda **da Lista ou da Varredura** que as injetou, copiada no momento da injeção, quando ela tiver (Marketing, RN-MKT-01); depois disso o rótulo é da Lead, e o operador pode alterá-lo ou limpá-lo. Relatórios mostram as leads sem ela em "Sem origem de venda" (RN-REL-17).

**RN-ORI-07 — A Lista de Prospecção e a Varredura também têm Origem de Venda.** É um campo opcional, no mesmo Combobox criável e com as mesmas permissões (`sale_origins:create`, `sale_origins:delete`). A Lead recebe o rótulo vigente da Lista ou da Varredura **no momento da injeção**; mudá-lo depois não altera as Leads já injetadas. Ao excluir uma Origem (RN-ORI-04), a Lista ou a Varredura que a referenciava **mantém o rótulo como texto**, como as leads. *(Decisão do usuário, levantamento do Marketing.)*

---

# 5. Regras de negócio (consolidado)

- **RN-ORI-01** — Sem bloqueio de exclusão; não há vínculo que impeça.
- **RN-ORI-02** — Exclusão exige confirmação reforçada por digitação.
- **RN-ORI-03** — Nome único no tenant, ignorando maiúsculas e espaços nas pontas.
- **RN-ORI-04** — Exclusão preserva o rótulo nas leads como texto desvinculado.
- **RN-ORI-05** — Origem é tenant-wide; escopo `store` qualifica quem age, não restringe o dado.
- **RN-ORI-06** — Origem de Venda é opcional na lead; as da Huggy e de landing page nascem sem ela, e as do Marketing herdam a da Lista ou da Varredura.
- **RN-ORI-07** — Lista de Prospecção e Varredura também guardam uma Origem de Venda opcional; a exclusão preserva o rótulo como texto.

---

# 6. Estados e transições

Origem de Venda **não tem estados** — existe ou não existe. Sem ativo/inativo, sem rascunho.

Transições: (inexistente) → criada, pelo Combobox; criada → excluída, com confirmação por digitação.

---

# 7. Dependências e o que o módulo expõe

**Depende de:** nada.

**Expõe para:**
- **Leads (CRM)** — a lista de Origens, para o Combobox opcional do cadastro e da ficha da lead.
- **Layout de Lead (Admin)** — hospeda o campo no bloco Dados de Marketing do Sheet da Lead, como o único campo editável do bloco (RN-LAY-07).
- **Marketing** — a lista de Origens, para o campo opcional da Lista de Prospecção e da Varredura (RN-ORI-07), e o filtro por Origem de Venda do Dashboard.
- **Propostas (CRM)** — o campo Origem de Venda da Proposta (a Tag "Origem", RN-PRP-13), com o mesmo Combobox criável e a mesma lista; lido também pelos relatórios de Propostas (Admin · Relatórios, RN-REL-16).
- **Relatórios** (Admin e CRM) — o rótulo, como dimensão de filtro e de agrupamento.

---

# 8. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Origem de Venda v0.8 — módulo da aplicação Admin, seção Cadastros. Sem página própria; vive no cadastro de Lead.*
