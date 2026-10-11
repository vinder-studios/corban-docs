# Corban CRM · Times

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.3 | Esclarece que a lista de Membros disponíveis para associação é a de **Membros da Loja pelo vínculo direto** (RN-USR-15), com ou sem Time — sem esse vínculo, um Membro só apareceria na Loja depois de ter Time, e só ganharia Time aparecendo na lista. |
| 0.1 | Versão inicial. |
| 0.2 | Adiciona RN-TIM-03: a lista de Membros disponíveis para associação exclui quem tem Cargo com alguma linha de escopo `all` — mesmo filtro já usado em Membros (RN-MBR-02). |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-TIM) e regras de negócio (RN-TIM) próprios.

> **Mesma lógica do módulo Membros: recorte com escopo `store` de algo que hoje só existe no Admin.** A entidade Time e seu ciclo de vida (campos, inativação em dois modos, exclusão com migração) são exatamente os já definidos no módulo de Lojas (Admin), Aba Times — não redefinidos aqui. Este módulo acrescenta uma capacidade de interface que **não existe no Admin**: associar/desassociar um Membro a partir da tela do Time (lá, a associação só acontece no sentido inverso, pelo cadastro do usuário).

---

# 1. Objetivo do módulo

Permitir que uma Loja parceira gerencie os próprios Times — criar, editar, associar Segmento, compor com Membros — sem depender do tenant operador nem de acesso ao Admin.

---

# 2. Atores

- **Gestor de Loja** — usuário com permissão de gestão deste módulo, escopo `store`.

---

# 3. Entidades e conceitos

Reaproveita a entidade **Time** do módulo de Lojas (Admin) — Nome, Segmento (opcional, no máximo um), Status (Ativo/Inativando/Inativo) — sem campos novos.

**RN-TIM-01 — Associação de Membro é unitária, nunca em lote.** Diferente da Aba Segmentos (que lista várias linhas para marcar de uma vez), a associação de Membro ao Time é **um de cada vez** — não existe seleção múltipla nem ação em lote nesta tela.

**RN-TIM-02 — Associar um Membro que já tem Time move, com confirmação explícita.** Como todo Membro tem no máximo um Time (RN-USR-02, módulo de Usuários — sem alteração), associá-lo a um Time novo quando ele já pertence a outro **substitui** a associação anterior. A ação exige confirmação, avisando explicitamente que o Membro sai do Time atual antes de entrar no novo — nunca silenciosa.

**RN-TIM-03 — A lista de Membros disponíveis exclui quem tem Cargo de escopo `all`.** Mesmo filtro já aplicado em Membros (RN-MBR-02, módulo CRM · Membros): um usuário cujo Cargo tenha ao menos uma linha de permissão em escopo `all` não aparece como opção para associar a um Time, mesmo que esse usuário já pertença à Loja. Consistente com o princípio geral — nada de escopo `all` é alcançável a partir das telas de escopo `store`.

---

# 4. Requisitos funcionais

## RF-TIM-01 — Gestão de Times da Loja

**Como** gestor de Loja, **eu quero** listar, criar, editar e excluir os Times da minha Loja, e associar/desassociar Segmento **para** estruturar minha operação sem depender do Admin.

**Comportamento esperado:** idêntico ao já definido em RF-LOJ-02/03 (módulo de Lojas, Admin) — inativação em dois modos (RN-LOJ-03), cancelamento da inativação agendada ao reativar (RN-LOJ-04), exclusão com migração de membros ou redistribuição de itens (RN-LOJ-05). Escopo restrito à própria Loja do usuário.

## RF-TIM-02 — Associação de Membro ao Time

**Como** gestor de Loja, **eu quero** associar ou desassociar um Membro a um Time **para** compor minha equipe.

**Comportamento esperado:** a partir da tela do Time, o gestor associa um Membro por vez (RN-TIM-01). Se o Membro escolhido já pertence a outro Time da Loja, o sistema pede confirmação antes de mover (RN-TIM-02). Desassociar é direto — o Membro fica sem Time (estado válido, RN-USR-02).

---

# 5. Regras de negócio (consolidado)

- **RN-TIM-01** — Associação de Membro ao Time é unitária, nunca em lote.
- **RN-TIM-02** — Associar Membro que já tem Time move, com confirmação explícita.
- **RN-TIM-03** — Lista de Membros disponíveis exclui quem tem Cargo de escopo `all`.

---

# 6. Estados e transições

Idênticos ao módulo de Lojas (Admin), §6 — Ativo / Inativando / Inativo, sem alteração.

---

# 7. Limites do módulo

**Não** inclui: a entidade Time em si (definida no módulo de Lojas, Admin — este módulo só opera sobre ela com escopo `store`), a gestão de Membros (módulo próprio, CRM), nem qualquer ação sobre outra Loja.

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Lojas** (Admin) — entidade Time, campos, regras de ciclo de vida.
- **Segmentos** (Admin) — lista de Segmentos para associação.
- **Membros** (CRM) — lista de Membros da própria Loja (vínculo direto, RN-USR-15 — com ou sem Time), para associação ao Time, já filtrada por RN-TIM-03 (equivalente a RN-MBR-02, aplicado aqui do lado do Time).

**Expõe para:**
- **Leads** (CRM) — a composição de Times determina a elegibilidade de operador na distribuição interna (RN-LED-04).

---

# 9. Itens abertos

Nenhuma pendência de modelagem — módulo é, por construção, um recorte do que já existe em Lojas (Admin), mais a capacidade nova de associação unitária de Membro, filtrada por Cargo (RN-TIM-03).

---

*Fim do documento. Times v0.3 — módulo da aplicação CRM.*
