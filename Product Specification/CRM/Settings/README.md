# Corban CRM · Configurações

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Documento de referência:** Corban Admin · Lojas (v0.11) — fonte das entidades Loja e Segmento-por-Loja
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.5 | **Acompanha Lojas v0.13: a Loja escolhe quais tipos de lead recebe em cada Segmento.** O interruptor "Recebe leads" é substituído por um interruptor por Origem da lead (Campanha, Orgânica, Prospecção, Carteira Ativa). RN-CFG-03 a 05 reescritas; **nova RN-CFG-07:** a Loja com "Pode receber lead sem saldo" vê os tipos ligados e travados (RN-LOJ-18) |
| 0.4 | **Levantamento do CRM, decisões do usuário.** (1) **Nova RN-CFG-05:** desmarcar "Ativo" é bloqueado enquanto houver leads em andamento do Segmento (hoje o Time perderia o acesso às leads que ainda trabalha; vale também em Admin · Lojas, RN-LOJ-16). (2) **Nova RN-CFG-06:** só Segmentos ativos na lista. (3) A tabela de self-service passa a dizer **Código, CNPJ e Regiões ficam no Admin**, e que a flag "sem saldo" só se edita no Admin com `manage_credits` em `all`. Corrige o título da tabela ("Aba Créditos de Lojas") e a versão de Admin · Lojas citada. Reordena este histórico. |
| 0.3 | Fecha o item aberto §11 — não havia problema real: um Time cujo Segmento foi inativado se comporta como "Time sem Segmento" (já previsto em RN-LOJ-02), sem dado órfão nem decisão de sistema pendente. |
| 0.2 | Estreita o item aberto §11 — a cascata Segmento→Funil (RN-SEG-06, RN-FNL-14) já cobria o efeito principal; falta só o que acontece com o campo Segmento do Time depois que o Funil correspondente drena. |
| 0.1 | Versão inicial. |

## Como usar este documento

Doc de módulo: requisitos funcionais (RF-CFG) e regras de negócio (RN-CFG) próprios.

> **Este módulo é um recorte de escrita sobre a Loja, não uma redefinição.** A entidade Loja e a associação Loja↔Segmento (com a flag Ativo e os tipos de lead) são definidas em **Admin · Lojas** — aqui só se habilita que a própria Loja edite um subconjunto desses campos, sem depender do tenant. Mesmo padrão de recorte já usado em Membros (CRM) sobre Usuários (Admin), e em Créditos/Tabelas Multiplicadoras (CRM), que já davam à Loja autogestão de outras partes de si mesma.

---

# 1. Objetivo do módulo

Permitir que a própria Loja edite os dados cadastrais básicos e controle quais Segmentos está ativamente operando e recebendo leads — sem que o tenant precise ser acionado para isso, e sem dar à Loja acesso à aplicação Admin.

---

# 2. Atores

- **Membro da Loja** com permissão de Configurações — tipicamente o gestor de Loja.

---

# 3. O que fica de self-service, e o que não fica

| Campo/flag (Admin · Lojas) | Vira self-service aqui? |
|---|---|
| Nome, Endereço, Logo | Sim — Aba Informações básicas |
| Código, CNPJ | **Não** — identidade da Loja, fica no Admin |
| Regiões | **Não** — definem quais Ofertas a Loja enxerga (RN-REG-04); decisão comercial do tenant, no Admin |
| Ativo (por Segmento) | Sim — Aba Segmentos |
| Tipos de lead que recebe (por Segmento, um interruptor por Origem) | Sim — Aba Segmentos |
| Status da Loja (Ativa/Inativando/Inativa) | **Não** — decisão de lifecycle, fica no Admin |
| "Pode receber lead sem saldo" | **Não** — é por Loja (não por Segmento), exceção financeira que o tenant precisa controlar (RN-LOJ-12) — no Admin, só com `stores:manage_credits` em `all` |
| Saldo, Extrato, Limite de gasto diário, Adicionar crédito | **Não aqui** — já são self-service, mas em módulo próprio (CRM · Créditos) |
| Times | **Não aqui** — já é self-service, em módulo próprio (CRM · Times) |
| Exclusão da Loja | **Não** — Admin |

**RN-CFG-01 — Critério de inclusão: risco operacional baixo, sem efeito financeiro direto nem em outra Loja.** Dados cadastrais e o Ativo e os tipos de lead afetam só a própria Loja e são reversíveis a qualquer momento pela própria Loja. Status, a flag de saldo negativo e exclusão ficam de fora porque são decisões de risco, financeiras ou de lifecycle que o tenant precisa manter sob controle.

---

# 4. Abas

## 4.1. Informações básicas

Nome, Endereço, Logo — mesmos campos de Admin · Lojas, Aba Informações gerais, editáveis aqui diretamente pela Loja.

**RN-CFG-02 — Edição aqui e no Admin escrevem o mesmo registro.** Não é uma cópia — é a mesma entidade Loja (Admin · Lojas). Uma alteração feita aqui aparece imediatamente para quem olha pelo Admin, e vice-versa.

## 4.2. Segmentos

Lista dos Segmentos **ativos** do tenant (RN-CFG-06), cada um com a flag **Ativo** e um interruptor por tipo de lead (**Campanha, Orgânica, Prospecção e Carteira Ativa**), definidos em Admin · Lojas (RN-LOJ-17), com a mesma dependência entre eles.

**RN-CFG-03 — Mesma regra de dependência de Admin · Lojas.** Os tipos de lead exigem "Ativo" marcado (RN-LOJ-01) — a interface impede a combinação inválida. Ao marcar "Ativo", as quatro Origens nascem ligadas.

**RN-CFG-04 — Desligar um tipo de lead pausa a entrada daquele tipo, de efeito imediato e reversível.** A Loja volta a receber a qualquer momento, religando o interruptor. Não afeta leads já em andamento. Desligar os quatro equivale a pausar o Segmento.

**RN-CFG-05 — Desmarcar "Ativo" é bloqueado enquanto houver leads em andamento daquele Segmento na Loja.** Mesma regra de Admin · Lojas (RN-LOJ-16): o `Switch` não muda e a interface orienta a desligar os tipos de lead do Segmento e a concluir ou redistribuir as leads antes. O Time desse Segmento não perde o acesso ao Funil enquanto houver leads em andamento. *(Decisão do usuário, levantamento do CRM.)*

**RN-CFG-06 — A lista mostra só os Segmentos ativos do tenant.** *(Decisão do usuário.)*

**RN-CFG-07 — A Loja com "Pode receber lead sem saldo" não desliga tipos de lead.** Nessa Loja, o destino de última instância da distribuição (Lojas, RN-LOJ-18), os interruptores aparecem **ligados e travados**, com `Tooltip` explicando. *(Decisão do usuário.)*

---

# 5. Acesso

Dois sub-recursos de permissão, mesmo critério de separação já usado em outros módulos recém-revisados (ex.: `crm_reports_*`): `store_profile` (Aba Informações básicas) e `store_segments` (Aba Segmentos), ambos com ação `edit`, escopo único `store`. Um Cargo pode ter um sem o outro — por exemplo, um gestor de marketing edita Logo e Endereço sem poder mexer em quais Segmentos a Loja atende.

---

# 6. Requisitos funcionais

## RF-CFG-01 — Editar Informações básicas

**Como** membro da Loja, **eu quero** editar Nome, Endereço e Logo da minha Loja **para** manter esses dados atualizados sem acionar o tenant.

**Comportamento esperado:** campos editáveis inline, efeito imediato por campo.

## RF-CFG-02 — Ativar/desativar recebimento de leads por Segmento

**Como** membro da Loja, **eu quero** ligar e desligar, por Segmento, se minha Loja está ativa e recebendo leads **para** controlar minha capacidade operacional sem depender do tenant.

**Comportamento esperado:** lista de Segmentos do tenant, com a flag Ativo e os tipos de lead (RN-CFG-03), efeito imediato.

---

# 7. Regras de negócio (consolidado)

- **RN-CFG-01** — Critério de inclusão: baixo risco, sem efeito financeiro ou em outra Loja.
- **RN-CFG-02** — Edição aqui e no Admin escrevem o mesmo registro.
- **RN-CFG-03** — Os tipos de lead exigem "Ativo", mesma regra de Admin · Lojas; ao ativar, nascem ligados.
- **RN-CFG-04** — Desligar um tipo de lead pausa a entrada daquele tipo, imediato e reversível.
- **RN-CFG-05** — Desmarcar "Ativo" é bloqueado enquanto houver leads em andamento do Segmento na Loja.
- **RN-CFG-06** — A lista mostra só os Segmentos ativos do tenant.
- **RN-CFG-07** — A Loja com "Pode receber lead sem saldo" vê os tipos de lead ligados e travados.

---

# 8. Estados e transições

Sem ciclo de vida próprio — os campos aqui são os mesmos da entidade Loja e da associação Loja↔Segmento, cujos estados são definidos em Admin · Lojas.

---

# 9. Limites do módulo

**Não** inclui: Status da Loja, a flag "Pode receber lead sem saldo", exclusão da Loja (todos ficam no Admin — RN-CFG-01); Saldo/Extrato/Limite de gasto diário (módulo CRM · Créditos); Times (módulo CRM · Times).

---

# 10. Dependências e o que o módulo expõe

**Depende de:** Admin · Lojas (entidade Loja, associação Loja↔Segmento); Segmentos (Admin, lista de Segmentos do tenant).

**Expõe para:** nenhum outro módulo diretamente — escreve na mesma entidade que Admin · Lojas já expõe para Times, Funis de Vendas e outros.

---

# 11. Itens abertos

Nenhuma pendência de modelagem. O efeito em Time, quando a Loja desativa um Segmento, já está coberto pela cascata existente (RN-SEG-06, Segmentos; RN-FNL-14, Funis de Vendas) — o Time passa a se comportar como "sem Segmento" (RN-LOJ-02, Admin · Lojas), sem exigir regra nova.

---

*Fim do documento. Configurações v0.5 — módulo da aplicação CRM.*
