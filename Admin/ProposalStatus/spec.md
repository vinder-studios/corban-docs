# Corban Admin · Status de Proposta

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                           |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                    |
| 0.2    | Fecha os itens abertos: migração restrita ao mesmo tipo (RN-STP-05); cor do texto do badge é derivada automaticamente da cor de fundo escolhida, garantindo contraste (RN-STP-06). |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-STP) e regras de negócio (RN-STP) próprios.

> **Este módulo não segue o padrão de bloqueio dos demais Cadastros.** Em Segmentos, Produtos, Regiões e Promotoras, um vínculo existente **impede** inativar/excluir. Aqui não: propostas vinculadas disparam um **fluxo de migração** — o gestor escolhe para onde elas vão e a ação prossegue. É a mesma família do dialog de Redistribuição de Itens, no módulo de Usuários & Permissões.

---

# 1. Objetivo do módulo

Manter os status pelos quais uma proposta passa, do envio ao desfecho. Cada tenant define os seus, classificando-os em três tipos que o sistema reconhece: em andamento, pago e cancelado.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`.
- **Demais usuários** — consomem Status de Proposta ao trabalhar propostas, sem gerenciá-los aqui.

---

# 3. Entidades e conceitos

**Status de Proposta.** Etapa ou desfecho de uma proposta. Cadastro **livre do tenant**. Campos:

| Campo  | Obrigatório | Notas                                                                                                                                       |
| ------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome   | Sim         | Como aparece em toda a interface (ex.: "Em análise", "Averbado", "Recusado pelo banco").                                                    |
| Tipo   | Sim         | Enum fixo do sistema: **Andamento**, **Pago**, **Cancelado**. Ver abaixo.                                                                   |
| Cor    | Sim         | Escolha livre (color picker), não uma paleta fechada. É a cor de **fundo** do badge; a cor do texto é derivada automaticamente (RN-STP-06). |
| Status | Sim         | Ativo / Inativo (ver §6).                                                                                                                   |

**Sem código e sem descrição** — o nome é suficiente para identificar e o status não é referenciado por integração.

**Tipo — enum fixo, com significado de negócio.** O tenant cria quantos status quiser, mas cada um precisa se declarar como um dos três tipos que o sistema entende:

| Tipo          | Significado                                                           | Natureza           |
| ------------- | --------------------------------------------------------------------- | ------------------ |
| **Andamento** | A proposta está em curso — enviada, em análise, pendente de documento | Não terminal       |
| **Pago**      | A proposta se converteu — crédito liberado                            | Terminal, positivo |
| **Cancelado** | A proposta não se converteu — recusada, desistência, erro             | Terminal, negativo |

O tipo não é rótulo decorativo: é o que permite ao sistema saber se uma proposta ainda está viva, se virou produção ou se morreu — sem precisar interpretar o nome que o tenant deu. Um tenant pode ter "Averbado" e "Liberado" ambos como tipo Pago, e o sistema trata os dois como conversão.

**Sem restrição de quantidade por tipo.** Não é exigido ter ao menos um status de cada tipo, nem há limite máximo. O tenant pode ter cinco status de Andamento e nenhum de Cancelado, se isso fizer sentido para ele.

---

# 4. Requisitos funcionais

## RF-STP-01 — Gestão de status de proposta (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Status de Proposta **para** refletir as etapas pelas quais as propostas da empresa passam.

**Comportamento esperado:** o gestor cadastra nome, tipo e cor. A listagem permite busca por nome e filtros por tipo e por status. A edição cobre todos os campos, inclusive o tipo. A inativação e a exclusão disparam o fluxo de migração quando há propostas vinculadas (RN-STP-02).

**Regras específicas:**

**RN-STP-01 — Cadastro livre do tenant, tipo fixo do sistema.** Os status são criados pelo tenant; os três tipos (Andamento, Pago, Cancelado) são do sistema e não podem ser criados, editados nem excluídos.

**RN-STP-02 — Propostas vinculadas disparam migração, não bloqueio.** Ao inativar ou excluir um Status que tem propostas vinculadas, o sistema **não impede** a ação: abre um fluxo em que o gestor escolhe o destino dessas propostas. As opções são:

- **Outro Status** — a lista de status disponíveis para a troca;
- **Sem status** — as propostas ficam sem status atribuído.

Concluída a escolha, as propostas são migradas e a inativação/exclusão se completa.

**RN-STP-03 — Exclusão definitiva existe.** Diferente da inativação, é irreversível. Sujeita ao mesmo fluxo de migração.

**RN-STP-05 — A migração só oferece status do mesmo tipo.** No fluxo de migração (RN-STP-02), a lista de destino apresenta **apenas os status ativos do mesmo tipo** do status sendo inativado/excluído — mais a opção "Sem status". Um status do tipo Pago só migra para outro Pago; um de Andamento, para outro de Andamento.

**Por quê:** mover propostas entre tipos não é realocação, é reclassificação — propostas contadas como conversão deixariam de sê-lo, ou o inverso, sem que ninguém tivesse decidido isso conscientemente. A restrição garante que a migração preserve o significado de negócio das propostas envolvidas.

**Consequência a considerar:** se não houver outro status ativo do mesmo tipo, a única opção oferecida será "Sem status". A interface precisa deixar isso claro em vez de mostrar uma lista vazia (ver Microcopy).

**RN-STP-06 — Contraste do badge é garantido pelo sistema.** O gestor escolhe apenas a cor de fundo do status. A cor do texto sobre esse fundo é **calculada automaticamente** pelo sistema, garantindo contraste legível em qualquer escolha. Não há validação nem bloqueio de cores no picker — nenhuma escolha do gestor pode produzir um badge ilegível.

**RN-STP-04 — Alterar o tipo de um status em uso muda a leitura do histórico.** O tipo governa como o sistema interpreta a proposta (viva, convertida, morta). Alterar o tipo de um status que já tem propostas vinculadas **reclassifica retroativamente** todas elas — propostas antes contadas como conversão podem deixar de ser, e vice-versa. A ação é permitida, mas exige aviso explícito (ver Microcopy).

---

# 5. Regras de negócio (consolidado)

- **RN-STP-01** — Cadastro livre do tenant; tipo é enum fixo do sistema.
- **RN-STP-02** — Propostas vinculadas disparam migração, não bloqueio.
- **RN-STP-03** — Exclusão definitiva existe, sujeita ao mesmo fluxo.
- **RN-STP-04** — Alterar o tipo reclassifica retroativamente as propostas vinculadas.
- **RN-STP-05** — A migração só oferece status do mesmo tipo, mais "Sem status".
- **RN-STP-06** — A cor do texto do badge é derivada da cor de fundo; contraste garantido pelo sistema.

---

# 6. Estados e transições

**Status de Proposta:**

- **Ativo** — disponível para ser atribuído a propostas.
- **Inativo** — não pode ser atribuído a novas propostas.

Transições: ativo → inativo (com migração se houver propostas vinculadas, RN-STP-02); inativo → ativo (livre); ativo/inativo → excluído (mesmo fluxo, RN-STP-03).

**Nota:** diferente dos outros cadastros, nenhuma transição aqui é _bloqueada_ — todas são possíveis, algumas mediante migração.

---

# 7. Limites do módulo

Trata apenas do cadastro do Status. **Não** inclui: o fluxo da proposta em si, as regras de quais transições de status são permitidas, nem o que cada tipo dispara no negócio (módulo de Propostas).

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base.

**Expõe para:**

- **Propostas** — a lista de Status ativos, com nome, cor e tipo.
- **Relatórios** — o tipo de cada status, para separar produção de perda sem depender do nome dado pelo tenant.

---

# 9. Itens abertos

Nenhuma pendência de modelagem. Um ponto de atenção, decorrente de RN-STP-04 e RN-STP-05 juntas:

- **Alterar o tipo é a saída para o que a migração não permite.** RN-STP-05 impede migrar propostas entre tipos, mas RN-STP-04 permite alterar o tipo de um status em uso — reclassificando tudo de uma vez. Ou seja: a reclassificação que a migração barra continua alcançável por outro caminho. Não é contradição (são ações diferentes, com avisos diferentes), mas vale saber que a proteção de RN-STP-05 não é absoluta.

---

_Fim do documento. Status de Proposta v0.2 — módulo da aplicação Admin, seção Cadastros._
