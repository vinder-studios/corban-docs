# Corban Admin · Status de Proposta

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.7
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Fecha os itens abertos: migração restrita ao mesmo tipo (RN-STP-05); cor do texto do badge é derivada automaticamente da cor de fundo escolhida, garantindo contraste (RN-STP-06). |
| 0.3 | O enum de tipo ganha o valor **Novo**, passando a quatro valores. A inclusão vem do módulo de Funis de Vendas, onde o mesmo enum classifica os stages do board (RN-FNL-13) — as duas entidades continuam separadas, compartilhando só o vocabulário de classificação. |
| 0.4 e 0.5 | *(Registro reconstruído na revisão da v0.6: o histórico original não registrou estas duas versões, e a divisão entre elas não é conhecida.)* O StatusType deixa de ser compartilhado com o Tipo de Stage do Funil (StageType, 5 valores): são dois enums independentes, ligados por eventos (RN-FNL-17 e RN-FNL-18), e não por enum — o que revoga o compartilhamento descrito na v0.3. A Proposta passa a nascer automaticamente, criada pela Ação Formalizar do Funil (RN-FNL-17), e não só por ação manual. |
| 0.6 | **Remove a opção "Sem status"** da migração: Propostas exige Status em toda proposta, e uma proposta Paga que ficasse sem status deixaria de contar como produção. Em troca, **o último status ativo de um tipo não pode ser inativado, excluído nem mudar de tipo** (nova RN-STP-07), de modo que a migração sempre tem destino. Pode haver vários status Novo; a Proposta nasce no ativo mais antigo (RN-STP-08). Nome único no tenant (RN-STP-09). Corrige o cabeçalho, que dizia que Segmentos e Regiões bloqueiam por vínculo (hoje cascateiam), e a lacuna do tenant sem status de algum tipo vira item aberto. Reordena esta tabela e as regras no corpo. |
| 0.7 | **O tenant nasce com um status padrão de cada tipo** (nova RN-STP-10): Novo, Em negociação, Pago e Cancelado, comuns e editáveis, protegidos apenas pela RN-STP-07. Fecha o item aberto "tenant sem status de algum tipo" (aberto em Funis v0.13) e torna a RN-STP-07 incondicional. A Lista deixa de ter o estado vazio "nenhum status" (resta o vazio de filtro). O Console da Plataforma entra em "Depende de". Decisão do usuário, na revisão de Funis. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-STP) e regras de negócio (RN-STP) próprios.

> **Este módulo não segue o padrão de bloqueio de Produtos e Promotoras**, onde uma Oferta que usa o cadastro **impede** inativar/excluir. Aqui, propostas vinculadas disparam um **fluxo de migração** — o gestor escolhe para onde elas vão e a ação prossegue. A única exceção é o último status ativo de um tipo (RN-STP-07), que não tem para onde migrar e por isso é protegido. (Regiões e Segmentos têm um terceiro caminho: a cascata, com confirmação.) É a mesma família do dialog de Redistribuição de Itens, no módulo de Usuários & Permissões.

---

# 1. Objetivo do módulo

Manter os status pelos quais uma proposta passa, do envio ao desfecho. Cada tenant define os seus, classificando-os em quatro tipos que o sistema reconhece: novo, em negociação, pago e cancelado.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`.
- **Demais usuários** — consomem Status de Proposta ao trabalhar propostas, sem gerenciá-los aqui.

---

# 3. Entidades e conceitos

**Status de Proposta.** Etapa ou desfecho de uma proposta. Cadastro **livre do tenant**. Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece em toda a interface (ex.: "Em análise", "Averbado", "Recusado pelo banco"). Único no tenant, ignorando maiúsculas e espaços nas pontas (RN-STP-09). |
| Tipo | Sim | Enum fixo do sistema, **StatusType** — próprio deste módulo: **Novo**, **Negociação**, **Pago**, **Cancelado**. Ver abaixo. |
| Cor | Sim | Escolha livre (color picker), não uma paleta fechada. É a cor de **fundo** do badge; a cor do texto é derivada automaticamente (RN-STP-06). |
| Status | Sim | Ativo / Inativo (ver §6). |

**Sem código e sem descrição** — o nome é suficiente para identificar e o status não é referenciado por integração.

**Tipo — enum fixo, com significado de negócio.** O tenant cria quantos status quiser, mas cada um precisa se declarar como um dos quatro tipos que o sistema entende (**StatusType**):

| Tipo | Significado | Natureza |
|---|---|---|
| **Novo** | A proposta acaba de entrar, ainda não foi trabalhada | Não terminal |
| **Negociação** | A proposta está em curso — enviada, em análise, pendente de documento | Não terminal |
| **Pago** | A proposta se converteu — crédito liberado | Terminal, positivo |
| **Cancelado** | A proposta não se converteu — recusada, desistência, erro | Terminal, negativo |

**StatusType não é compartilhado com o Tipo de Stage do Funil** (StageType, módulo de Funis de Vendas — 5 valores, inclui "Formalização"). *(Revoga a versão anterior desta regra.)* São dois enums independentes, parecidos em espírito: cada um diz ao respectivo sistema o que uma etapa significa, sem depender do nome que o tenant deu. A ligação entre os dois módulos acontece por eventos — Lead entrando em Formalização cria a Proposta (RN-FNL-17); Proposta atingindo Pago/Cancelado move a Lead (RN-FNL-18) — não por compartilhar enum.

**A Proposta agora nasce automaticamente**, criada pelo Funil ao entrar em Stage tipo Formalização (RN-FNL-17) — não mais só por ação manual. Detalhe completo no módulo de Propostas.

O tipo não é rótulo decorativo: é o que permite ao sistema saber se uma proposta ainda está viva, se virou produção ou se morreu — sem precisar interpretar o nome que o tenant deu. Um tenant pode ter "Averbado" e "Liberado" ambos como tipo Pago, e o sistema trata os dois como conversão.

**Quantidade por tipo.** Não há limite máximo, e o tenant pode ter vários status do mesmo tipo, inclusive vários Novo (a Proposta nasce no ativo mais antigo, RN-STP-08). O tenant **já nasce com um status ativo de cada tipo** (RN-STP-10), e o **último** de cada tipo não pode ser removido (RN-STP-07): nunca falta um tipo.

---

# 4. Requisitos funcionais

## RF-STP-01 — Gestão de status de proposta (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Status de Proposta **para** refletir as etapas pelas quais as propostas da empresa passam.

**Comportamento esperado:** o gestor cadastra nome, tipo e cor. A listagem permite busca por nome e filtros por tipo e por status. A edição cobre todos os campos, inclusive o tipo. A inativação e a exclusão disparam o fluxo de migração quando há propostas vinculadas (RN-STP-02), exceto no último status ativo do tipo, que é bloqueado (RN-STP-07).

**Regras específicas:**

**RN-STP-01 — Cadastro livre do tenant, tipo fixo do sistema.** Os status são criados pelo tenant; os quatro tipos (Novo, Negociação, Pago, Cancelado — StatusType) são do sistema e não podem ser criados, editados nem excluídos.

**RN-STP-02 — Propostas vinculadas disparam migração, não bloqueio.** Ao inativar ou excluir um Status que tem propostas vinculadas, o sistema **não impede** a ação (salvo no último status ativo do tipo, RN-STP-07): abre um fluxo em que o gestor escolhe, entre os outros status disponíveis (RN-STP-05), o destino dessas propostas. Toda proposta continua tendo um status — não existe "sem status", porque o módulo de Propostas exige o Status. Concluída a escolha, as propostas são migradas e a inativação/exclusão se completa.

**RN-STP-03 — Exclusão definitiva existe.** Diferente da inativação, é irreversível. Sujeita ao mesmo fluxo de migração.

**RN-STP-04 — Alterar o tipo de um status em uso muda a leitura do histórico.** O tipo governa como o sistema interpreta a proposta (viva, convertida, morta). Alterar o tipo de um status que já tem propostas vinculadas **reclassifica retroativamente** todas elas — propostas antes contadas como conversão podem deixar de ser, e vice-versa. A ação é permitida, mas exige aviso explícito (ver Microcopy) — exceto no último status ativo do tipo, onde é bloqueada (RN-STP-07).

**RN-STP-05 — A migração só oferece status do mesmo tipo.** No fluxo de migração (RN-STP-02), a lista de destino apresenta **apenas os status ativos do mesmo tipo** do status sendo inativado/excluído. Um status do tipo Pago só migra para outro Pago; um de Negociação, para outro de Negociação.

**Por quê:** mover propostas entre tipos não é realocação, é reclassificação — propostas contadas como conversão deixariam de sê-lo, ou o inverso, sem que ninguém tivesse decidido isso conscientemente. A restrição garante que a migração preserve o significado de negócio das propostas envolvidas.

**Consequência:** a lista de destino nunca fica vazia, porque o último status ativo de um tipo não pode ser inativado nem excluído (RN-STP-07).

**RN-STP-06 — Contraste do badge é garantido pelo sistema.** O gestor escolhe apenas a cor de fundo do status. A cor do texto sobre esse fundo é **calculada automaticamente** pelo sistema, garantindo contraste legível em qualquer escolha. Não há validação nem bloqueio de cores no picker — nenhuma escolha do gestor pode produzir um badge ilegível.

**RN-STP-07 — O último status ativo de um tipo não pode ser inativado, excluído nem mudar de tipo.** Se o status é o único ativo do seu tipo, essas três ações são **bloqueadas**, com a mensagem de que é o único do tipo e de que é preciso criar ou reativar outro antes. É a única situação em que este módulo bloqueia: sem outro status do tipo, a migração (RN-STP-02) não teria destino, e uma proposta Paga que perdesse o status deixaria de contar como produção. Vale sempre, porque o tenant já nasce com um status ativo de cada tipo (RN-STP-10).

**RN-STP-08 — A Proposta nasce no status ativo mais antigo do tipo Novo.** Pode haver vários status do tipo Novo; a Proposta criada pela Ação Formalizar (RN-FNL-17) nasce no **ativo cadastrado há mais tempo**. O tenant não escolhe qual; para mudar o status de entrada, inativa o mais antigo (permitido se houver outro Novo ativo, RN-STP-07). No tenant recém-criado, é o status padrão Novo (RN-STP-10). *(Decisão desta revisão: o desempate não estava definido; "o mais antigo" é o mais simples e não exige campo novo.)*

**RN-STP-09 — Nome único no tenant.** Não existem dois status com o mesmo nome, ignorando maiúsculas/minúsculas e espaços nas pontas. *(Decisão desta revisão.)*

**RN-STP-10 — O tenant nasce com um status padrão de cada tipo.** Ao ser implantado, o tenant recebe quatro status prontos, um por tipo — **Novo** (tipo Novo), **Em negociação** (tipo Negociação), **Pago** (tipo Pago) e **Cancelado** (tipo Cancelado) —, cada um com a cor correspondente à semântica do tipo [paleta a definir no Design System]. Assim a Ação Formalizar sempre tem um status Novo onde criar a Proposta (RN-FNL-17, RN-STP-08), e Pago e Cancelado existem desde o primeiro dia. São status **comuns** do tenant, sem marca de sistema: podem ser renomeados, recoloridos, ter o tipo alterado, inativados ou excluídos como quaisquer outros, sempre respeitando a RN-STP-07 (o último ativo de cada tipo permanece). Tenants já existentes que não tenham algum dos tipos recebem o status padrão daquele tipo. *(Decisão do usuário, na revisão de Funis, no mesmo espírito do Funil que nasce com stages sugeridos; os nomes e a regra para tenants existentes são escolha desta revisão.)*

---

# 5. Regras de negócio (consolidado)

- **RN-STP-01** — Cadastro livre do tenant; tipo é enum fixo do sistema.
- **RN-STP-02** — Propostas vinculadas disparam migração, não bloqueio (salvo o último ativo do tipo).
- **RN-STP-03** — Exclusão definitiva existe, sujeita ao mesmo fluxo.
- **RN-STP-04** — Alterar o tipo reclassifica retroativamente as propostas vinculadas.
- **RN-STP-05** — A migração só oferece status ativos do mesmo tipo; não existe "Sem status".
- **RN-STP-06** — A cor do texto do badge é derivada da cor de fundo; contraste garantido pelo sistema.
- **RN-STP-07** — O último status ativo de um tipo não pode ser inativado, excluído nem mudar de tipo.
- **RN-STP-08** — A Proposta nasce no status ativo mais antigo do tipo Novo (pode haver vários).
- **RN-STP-09** — Nome único no tenant, ignorando maiúsculas e espaços nas pontas.
- **RN-STP-10** — O tenant nasce com um status padrão de cada tipo (Novo, Em negociação, Pago, Cancelado), comuns e editáveis.

---

# 6. Estados e transições

**Status de Proposta:**
- **Ativo** — disponível para ser atribuído a propostas.
- **Inativo** — não pode ser atribuído a novas propostas.

Transições: ativo → inativo (com migração se houver propostas vinculadas, RN-STP-02); inativo → ativo (livre); ativo/inativo → excluído (mesmo fluxo, RN-STP-03).

**Nota:** diferente de Produtos e Promotoras, as transições aqui não são bloqueadas por propostas vinculadas — são possíveis, algumas mediante migração. A única exceção é o último status ativo de um tipo (RN-STP-07), que não pode ser inativado, excluído nem mudar de tipo.

---

# 7. Limites do módulo

Trata apenas do cadastro do Status. **Não** inclui: o fluxo da proposta em si, as regras de quais transições de status são permitidas, nem o que cada tipo dispara no negócio (módulo de Propostas).

---

# 8. Dependências e o que o módulo expõe

**Depende de:** o fluxo de **Implantação**, que dispara a criação dos status padrão (RN-STP-10). Fora isso, é cadastro-base.

**Expõe para:**
- **Propostas** — a lista de Status ativos, com nome, cor e tipo, e o status em que a Proposta nasce (RN-STP-08).
- **Relatórios** — o tipo de cada status, para separar produção de perda sem depender do nome dado pelo tenant.

---

# 9. Itens abertos

Nenhum item aberto. Um ponto de atenção, decorrente de RN-STP-04 e RN-STP-05 juntas:

- **Alterar o tipo é a saída para o que a migração não permite.** RN-STP-05 impede migrar propostas entre tipos, mas RN-STP-04 permite alterar o tipo de um status em uso — reclassificando tudo de uma vez (salvo o último ativo do tipo, RN-STP-07). Ou seja: a reclassificação que a migração barra continua alcançável por outro caminho. Não é contradição (são ações diferentes, com avisos diferentes), mas vale saber que a proteção de RN-STP-05 não é absoluta.

---

*Fim do documento. Status de Proposta v0.7 — módulo da aplicação Admin, seção Cadastros.*
