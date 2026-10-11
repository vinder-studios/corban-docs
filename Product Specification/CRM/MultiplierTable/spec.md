# Corban CRM · Tabelas Multiplicadoras

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.6
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Reformulação errada — inferi, a partir do nome plural do módulo, que existiam várias coleções nomeadas por Loja. Não existiam. |
| 0.6 | **RN-MUT-04 alinhada a Propostas (RN-PRP-05).** O multiplicador vigente é gravado na Proposta **quando a Oferta é associada** (e regravado a cada troca de Oferta), não no Pago; a partir do Pago ele fica travado com a Oferta. Decisão do usuário. |
| 0.5 | **Remove o Status (Ativa/Inativa)** adicionado na v0.4 — não fez sentido depois de ver o wireframe. RN-MUT-05 e o item aberto associado deixam de existir. O Sheet de edição permanece, agora só com o **Multiplicador** editável; dados da Oferta continuam somente leitura. |
| 0.4 | Cada "tabela" ganha **Status (Ativa/Inativa)** (RN-MUT-05) e a edição passa a ser feita por **Sheet** aberto pela ação "Editar" — não mais inline. Só Multiplicador e Status são editáveis; os dados da Oferta aparecem somente leitura. Semântica exata de "Inativa" assumida, não confirmada (item aberto §9.1). |
| 0.3 | Corrige RN-MUT-03: Operação é campo próprio da **Oferta** de novo (módulo de Ofertas v0.9), não do Produto. |
| 0.2 | **Correção, confirmada por wireframe.** Volta a ser estrutura única por Loja — uma linha por Oferta, sem agrupamento nomeado. "Tabelas" (plural, no nome do módulo) refere-se a **cada linha individual** sendo chamada de "tabela" na linguagem do produto — Oferta + Promotora + Produto + Multiplicador é, em si, uma "tabela" (um pequeno conjunto de dados), não um componente de UI nem uma coleção maior. Reverte os dois itens abertos da v0.1 (não se aplicam mais) e o campo "Tabela Multiplicadora usada" que eu tinha adicionado em Propostas. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-MUT) e regras de negócio (RN-MUT) próprios.

> **Contexto de acesso.** Vive no CRM, não no Admin — configurado pela própria Loja parceira. Mesmo padrão já registrado em Créditos e Membros.

> **Nota de vocabulário — por que "Tabelas" no plural.** Cada linha da tela (uma combinação Oferta + Multiplicador) é chamada de "tabela" na linguagem do produto — não porque seja um componente de interface com esse nome, mas porque representa, em si, um pequeno conjunto de dados (Oferta, Promotora, Produto, Multiplicador). O módulo tem esse nome porque lida com várias dessas — uma por Oferta —, não porque a Loja cria coleções nomeadas.

> **Nota de vocabulário — não confundir com Comissão.** O campo Comissão (módulo de Ofertas, Admin) é dinheiro efetivamente pago pelo tenant à Loja. O **Multiplicador** aqui é outra coisa: um fator que ajusta quanto uma Proposta conta para as metas de Time e usuário — pontuação, não pagamento. Os dois usam o mesmo **valor base** (RN-OFR-07), mas são cálculos independentes.

---

# 1. Objetivo do módulo

Permitir que cada Loja configure, para cada Oferta que ela enxerga, um multiplicador que ajusta o quanto uma Proposta fechada naquela Oferta conta para as metas de Time e de usuário.

---

# 2. Atores

- **Gestor de Loja** — usuário com permissão de gestão deste módulo, escopo `store`.
- **Operador** — não configura, mas o multiplicador vigente afeta o valor que suas Propostas contam para a meta dele.

---

# 3. Entidades e conceitos

## 3.1. Multiplicador de Meta ("tabela")

Uma linha por Oferta visível pela Loja — cada linha é, na linguagem do produto, "uma tabela".

| Campo | Obrigatório | Notas |
|---|---|---|
| Loja | Sim | Implícita pelo contexto — a tela é sempre a da própria Loja do usuário (escopo `store`). |
| Oferta | Sim | Uma das Ofertas que a Loja enxerga (RN-MUT-01). |
| Multiplicador | Sim, com padrão | Percentual. **Padrão 100%** — sem registro até a Loja alterar o valor; ausência de registro **é** 100%. |

**RN-MUT-01 — Escopo de Ofertas visíveis segue a regra de Região.** A lista de Ofertas configuráveis por uma Loja é exatamente a mesma que ela já enxerga por RN-REG-04 (módulo de Regiões) — mesmo recorte.

**RN-MUT-02 — Sem trava de valor.** O Multiplicador não tem mínimo nem máximo.

**RN-MUT-03 — Valor base é o mesmo de Comissão.** Usa o valor base por Operação (campo da Oferta, módulo de Ofertas v0.9) já definido em RN-OFR-07. O valor considerado para meta é `valor base × multiplicador`.

**RN-MUT-04 — Snapshot na associação da Oferta.** Quando a Oferta é associada à Proposta (e a cada troca de Oferta), o Multiplicador **vigente naquele momento** para a Oferta é gravado como snapshot na Proposta (campo "Multiplicador aplicado", módulo de Propostas) — RN-PRP-05. Em Pago e Cancelado a Oferta fica travada, e o multiplicador com ela (RN-PRP-15). Alterar o Multiplicador depois **não afeta** Propostas que já têm a Oferta associada — só as associações futuras usam o valor novo. *(Decisão do usuário, v0.6.)* Confirmado explicitamente: mesmo princípio já usado em Simulações e Ofertas.

---

# 4. Requisitos funcionais

## RF-MUT-01 — Configuração de multiplicadores por Loja

**Como** gestor de Loja, **eu quero** definir o multiplicador de cada Oferta que minha loja opera **para** calibrar o quanto cada venda conta nas metas do meu time.

**Comportamento esperado:** a tela lista as Ofertas que a Loja enxerga (RN-MUT-01), cada uma com Multiplicador (100% quando nunca alterado). Uma ação "Editar" por linha abre um Sheet onde **só o Multiplicador** é editável — os dados da Oferta (Nome, Promotora, Produto, Banco, Operação) aparecem para conferência, sem edição. Não há criação nem exclusão de linha — é uma tabela fixa, uma por Oferta visível.

---

# 5. Regras de negócio (consolidado)

- **RN-MUT-01** — Escopo de Ofertas visíveis segue a regra de Região.
- **RN-MUT-02** — Sem trava de valor.
- **RN-MUT-03** — Valor base compartilhado com Comissão.
- **RN-MUT-04** — Snapshot na associação da Oferta à Proposta; edições futuras não afetam o que já foi associado.

---

# 6. Estados e transições

Sem estados de publicação — a "tabela" existe implicitamente (100%) para toda Oferta visível e passa a ter registro próprio na primeira edição. Sem estado de exclusão — a linha nunca é removida.

---

# 7. Limites do módulo

**Não** inclui: o cadastro de Ofertas (Admin), o cálculo de metas em si (módulo de Metas), nem o fechamento de Proposta em si (módulo de Propostas — só consome o valor).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Ofertas** (Admin) — lista de Ofertas, Operação (campo próprio) e valor base por Operação (RN-OFR-07).
- **Regiões** (Admin) — recorte de visibilidade.

**Expõe para:**
- **Propostas** (CRM) — o multiplicador vigente por Oferta, para snapshot no campo "Multiplicador aplicado".

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Tabelas Multiplicadoras v0.5 — módulo da aplicação CRM.*
