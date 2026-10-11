# Corban Marketing · Prospecção

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing — Documento de Produto
**Versão:** 0.8
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.8 | **Marcas de proposta confirmadas pelo usuário:** RN-PRS-15 (colunas de contrato viram Contratos importados), RN-PRS-09 (validade de 7 dias) e o tratamento de Descartadas e Bloqueadas na RN-PRS-16. |
| 0.7 | Acompanha o Documento de Produto v0.9: a Lista **notifica** quando entra em Aguardando aprovação e em Falha (RN-MKT-10). §8 passa a expor os eventos ao sino do Shell. |
| 0.6 | **Pendências fechadas pelo usuário.** Confirmados: Cliente criado para toda linha válida e não bloqueada, inclusive as Descartadas (RN-PRS-16), e criação no início do processamento. Itens 7 e 8 resolvidos. |
| 0.5 | **Decisões do usuário.** (1) A pré-análise **grava Simulação na injeção**, para o contato com Oferta aprovada (RN-PRS-04, RN-PRS-10; Produto, RN-MKT-02). (2) **Base legal, declarações e retenção saem do escopo inicial**: sai o campo Base legal, a declaração do assistente e a RN-PRS-14 (reservada); "Origem da base" passa a texto informativo opcional. (3) Saem os itens abertos que fogem da definição do produto (§9, item 5). |
| 0.4 | **Decisões do usuário.** (1) A **Carga de base foi removida** (§3.4, RF-PRS-06, RN-PRS-11 e RN-PRS-12 reservados). (2) O **Cliente nasce depois do mapeamento** (RN-PRS-16), mesmo sem CPF, e permanece mesmo que as Leads não sejam distribuídas. (3) O gestor escolhe entre **distribuir as Leads** e **concluir sem distribuir**; a Lista fica **Guardada** (novo estado) e pode ser distribuída depois (RN-PRS-06, RN-PRS-17, RF-PRS-04). (4) As colunas de contrato da linha viram **Contratos importados** no histórico do Cliente (RN-PRS-15) [proposta]. (5) A Lista de bloqueio fora do Marketing vira nota de evolução do Produto. Declarações de base legal e retenção ficam como estão, para o jurídico. |
| 0.3 | **Fecha quatro itens abertos (§9, itens 1 a 4), decisões do usuário.** E-mail passa a ser **opcional** na Carga de base (RN-PRS-11), com Clientes v0.8; a pré-análise considera Ofertas de qualquer Região; a Lista de bloqueio vale só para o Marketing; o valor potencial em lote usa parcela = margem e prazo máximo (Documento de Produto v0.5, RN-MKT-05 e RN-MKT-08). A pré-análise roda na **Lista**, não na Carga de base. |
| 0.2 | **Levantamento do Marketing, decisões do usuário.** (1) **Nova Carga de base** (§3.4): a importação de Clientes em lote sai do CRM e passa a ser exclusiva do Marketing, no nível do tenant, sem gerar Lead (o cliente é do tenant, não da Loja). (2) **Origem corrigida:** a Lead nasce com a **Origem da lead Prospecção** (Leads, RN-LED-12) e a **referência à Lista**, e não mais com a Tag de categoria "Origem". A Lista ganha **Origem de Venda** opcional (copiada às leads) e **Custo da base** opcional. (3) **A pré-análise não grava Simulação**: o resultado fica na própria Lista (RN-MKT-02). (4) **Campo sem valor não desclassifica** (RN-MKT-03). (5) **Injeção controlada:** novo estado Aguardando aprovação, aprovação obrigatória, limite diário opcional, cancelamento, revalidação a cada injeção (RN-PRS-06 a 09). (6) **Duplicidade completa:** Lead ativa, Proposta em andamento e carência de 90 dias (RN-MKT-04). (7) **Conformidade:** Lista de bloqueio "não perturbe", declaração de origem e base legal e retenção curta das linhas descartadas (§3.5, RN-MKT-05 e 06). (8) As regras compartilhadas com a Carteira Ativa passam a viver no Documento de Produto (RN-MKT). Texto reescrito; **as RN-PRS foram renumeradas**. |
| 0.1 | Versão inicial. Absorve a "Simulação em Lote" removida do módulo de Simulações (CRM) e a "Importação Tenant-wide com Pré-análise" citada em Clientes. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-PRS) e regras de negócio (RN-PRS) próprios. As regras que valem também para a Carteira Ativa (RN-MKT) estão no Documento de Produto do Marketing e são só referenciadas aqui.

> **O coração do Outbound.** A Prospecção atua no nível do tenant. Recebe bases externas (listas compradas, parcerias), passa cada contato pela "peneira" do motor de Ofertas e Regras do Admin e, depois da aprovação do gestor, entrega só o que qualificou (Leads) para a distribuição entre as Lojas. É também a **única porta de importação em lote de Clientes** do produto: os Clientes da base nascem aqui, e a distribuição das Leads é um passo opcional.

---

# 1. Objetivo do módulo

Permitir que o tenant importe bases de contatos, crie os Clientes, analise cada contato contra as regras comerciais do Admin e, **se quiser**, distribua os aprovados como Leads, de forma controlada. A Lista fica guardada para ser distribuída em outro momento.

---

# 2. Atores

- **Gestor de Marketing (tenant)** — importa, mapeia, configura a pré-análise, decide entre distribuir as Leads e concluir sem distribuir, e mantém a Lista de bloqueio.
- **Sistema** — processa a pré-análise em segundo plano e injeta as Leads no CRM, respeitando o ritmo aprovado.

---

# 3. Entidades e conceitos

## 3.1. Lista de Prospecção

O lote de contatos importado. Entidade gerencial do Marketing: é o recipiente dos contatos até que virem Leads no CRM.

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome da Lista | Sim | Identificador interno (ex.: "Base INSS Outubro 2026"). |
| Segmento alvo | Sim | Define o Funil de Vendas em que as Leads entram (Stage tipo Novo). |
| Origem da base | Não | Texto livre e informativo: de onde veio a base (fornecedor, parceria). |
| Origem de Venda | Não | Rótulo do tenant (módulo Origem de Venda, Admin), `Combobox`. **Copiado às Leads geradas** (RN-MKT-01). |
| Custo da base (R$) | Não | Quanto o tenant pagou pela base. Alimenta o custo no Dashboard. |
| Status | Sim | Ver §6. |
| Contadores | Calculados | Linhas, Clientes novos e já existentes, inválidas, bloqueadas, em carência/colisão, descartadas, qualificadas, injetadas (RF-PRS-03). |

**RN-PRS-01 — Importação centralizada no tenant.** A Lista de Prospecção pertence ao tenant. Todo Cliente criado por ela nasce do tenant e **sem Loja associada** (RN-MKT-09). Nenhum contato chega a uma Loja antes de virar Lead e passar pelo rodízio (módulo de Leads).

**RN-PRS-16 — Os Clientes nascem depois do mapeamento.** Ao confirmar a configuração da pré-análise, a Lista vai para Processando pré-análise, que **primeiro valida as linhas e cria os Clientes** e só depois avalia. Nasce um Cliente para toda linha **válida e não bloqueada**, inclusive as que depois ficarem Descartadas. O mínimo é **Nome e Telefone** válidos; o **CPF é opcional** e, se vier, precisa ser válido (preenchido depois, Clientes, RN-CLI-11). Linhas Inválidas e Bloqueadas não criam Cliente. Se o Cliente já existe (por CPF, ou por Nome + Telefone sem CPF, Clientes RN-CLI-02 e RN-CLI-12), é **reaproveitado, sem sobrescrever** os dados dele. O Cliente nasce sem Loja (RN-MKT-09) e permanece mesmo que a Lista nunca seja distribuída. *(Decisão do usuário.)*

## 3.2. Pré-análise

Rotina de sistema que substitui a simulação manual do operador, aplicada a cada linha válida da Lista.

**RN-PRS-02 — Cruzamento com o Admin.** Antes de rodar, o gestor escolhe as **Operações** a testar (ex.: Novo, Portabilidade). Para cada linha, o sistema avalia as Ofertas ativas e vigentes dessas Operações e as Regras de cada uma (bloqueantes e de aviso), usando os dados mapeados da linha. Os campos mapeáveis são os do catálogo de campos das Regras (Admin): Dados do cliente, Dados da empresa empregadora e Dados de contratos pré-existentes.

**RN-PRS-03 — Filtro de qualificação.** O contato só se qualifica se for elegível a **ao menos uma** Oferta testada. Os demais ficam "Descartados" no resultado da Lista, com o motivo (a Mensagem de erro da Regra bloqueante que falhou), e **não geram Lead** (o Cliente já existe, RN-PRS-16). Os **filtros macro** opcionais (ex.: "descartar se idade > 70") agem antes das Regras das Ofertas.

**RN-PRS-04 — Campo sem valor e Simulação.** Regra que depende de campo que a linha não trouxe **não desclassifica**: o requisito fica "não avaliado" (RN-MKT-03). Durante a análise, a pré-análise **não grava Simulação** (RN-MKT-02): o resultado por contato fica na Lista (RF-PRS-03). A Simulação é gravada na injeção (RN-PRS-10).

**RN-PRS-05 — Resultado por contato.** Cada linha termina em um destes resultados: **Inválida** (CPF ou telefone inválido), **Bloqueada** (Lista de bloqueio, RN-MKT-05), **Descartada** (nenhuma Oferta elegível), **Qualificada** (com as Ofertas elegíveis, o valor potencial e os requisitos não avaliados — RN-MKT-08). Em carência ou colisão, o contato fica Qualificado até a injeção, e é a injeção que o recusa (RN-PRS-08).

**RN-PRS-15 — Colunas de contrato viram Contratos importados.** Quando a linha traz dados de contrato ("Dados de contratos pré-existentes", RN-PRS-02), o sistema grava um **Contrato importado** no histórico do Cliente (Clientes, RN-CLI-13), com o Segmento alvo da Lista. É esse histórico que a Carteira Ativa passa a minerar. *(Decisão do usuário.)*

## 3.3. Aprovação e injeção

**RN-PRS-06 — Nada é injetado sem aprovação.** Terminada a pré-análise, a Lista fica **Aguardando aprovação**. O gestor revisa o resultado (RF-PRS-03) e escolhe: **distribuir** (aprova a injeção, RN-PRS-07), **concluir sem distribuir** (a Lista fica **Guardada**, RN-PRS-17) ou **cancelar**. Concluir sem distribuir e cancelar não geram Lead; os Clientes já criados permanecem (RN-PRS-16).

**RN-PRS-07 — Ritmo de injeção.** Ao aprovar, o gestor escolhe: **injetar tudo** ou **limite diário de N leads**. Com limite, o excedente espera na fila da Lista, e a Lista fica **Injetando** até esvaziá-la. O gestor pode pausar e retomar a injeção, e cancelar o que ainda não foi injetado (as Leads já injetadas continuam).

**RN-PRS-08 — Revalidação a cada injeção.** No momento de injetar cada contato, o sistema reavalia a duplicidade (RN-MKT-04) e a Lista de bloqueio (RN-MKT-05), porque a situação pode ter mudado desde a pré-análise. Contato recusado nessa hora é contado como "Em carência ou colisão" ou "Bloqueado" e **não é injetado**.

**RN-PRS-09 — O resultado tem validade.** A elegibilidade vale a da pré-análise. Um resultado distribuído depois de **7 dias** da pré-análise exige reprocessar a Lista, porque as Ofertas e Regras podem ter mudado.

**RN-PRS-10 — A injeção cria a Lead.** Para cada contato injetado, o Cliente já existe (RN-PRS-16) e a Lead associa-se a ele. A Lead nasce no Stage tipo Novo do Funil do Segmento alvo, com **Origem da lead Prospecção**, a referência à Lista e a Origem de Venda da Lista (RN-MKT-01), e entra no rodízio de Lojas, com cobrança (Leads, RN-LED-02, 03 e 13). Se o contato tem ao menos uma Oferta aprovada, a avaliação é **gravada como Simulação de origem Marketing**, ligada à Lead (Simulações, RN-SIM-01; Produto, RN-MKT-02).

**RN-PRS-17 — A Lista pode ser guardada e distribuída depois.** Em Aguardando aprovação, o gestor pode **concluir sem distribuir**: a Lista vai para **Guardada**, com o resultado preservado, os Clientes mantidos e nenhuma Lead gerada. Em Guardada, o gestor pode **distribuir** quando quiser, escolhendo o ritmo como na RN-PRS-07; a distribuição é uma aprovação e exige `prospecting_lists:approve`. Passada a validade do resultado (RN-PRS-09), é preciso **reprocessar** antes. A Lista Guardada continua na listagem, e não se exclui se tiver Leads. *(Decisão do usuário.)*

## 3.4. *(Removida na v0.4; número reservado)*

A **Carga de base** deixou de existir. Os Clientes nascem na importação da **Lista** (RN-PRS-16), mesmo que a Lista seja guardada sem distribuir as Leads (RN-PRS-17). Os números **RN-PRS-11** e **RN-PRS-12** ficam reservados.

## 3.5. Conformidade

**RN-PRS-13 — Lista de bloqueio.** O tenant mantém uma lista de **CPFs e telefones** que não podem ser abordados (cadastros "não perturbe"), importável por arquivo e editável (RF-PRS-07). Vale para toda injeção do Marketing (Prospecção e Carteira Ativa) como **bloqueio rígido** (RN-MKT-05).

**RN-PRS-14 — *(removida na v0.5; número reservado).*** A retenção das linhas descartadas saiu do escopo inicial do produto.

---

# 4. Requisitos funcionais

## RF-PRS-01 — Upload, mapeamento e dados da Lista

**Como** Gestor de Marketing, **eu quero** importar um arquivo CSV/XLSX e mapear as colunas **para** transformar a planilha em dados que o sistema entenda.

**Comportamento esperado:** Wizard: upload → mapeamento → dados da Lista → configuração da pré-análise (RF-PRS-02). O sistema tenta o *auto-match* dos cabeçalhos; **Nome e Telefone** são obrigatórios e o **CPF é opcional** (se vier, precisa ser válido); os demais campos vêm do catálogo de campos das Regras, inclusive os dados de contrato (RN-PRS-15). O gestor informa Nome da Lista, Segmento alvo e, opcionalmente, Origem da base, Origem de Venda e Custo da base. O arquivo é validado linha a linha (CPF, telefone, duplicidade no próprio arquivo). Upload tolerante a conexão instável.

## RF-PRS-02 — Configuração da pré-análise

**Como** Gestor de Marketing, **eu quero** definir o que testar **para** só aprovar contatos com potencial real de venda.

**Comportamento esperado:** O gestor escolhe as Operações a testar e, opcionalmente, filtros macro sobre os campos mapeados. Ao confirmar, a Lista vai para **Processando pré-análise**, trancada para edição: o sistema primeiro valida as linhas e **cria os Clientes** (RN-PRS-16) e depois avalia.

## RF-PRS-03 — Resultado da Lista

**Como** Gestor de Marketing, **eu quero** ver o resultado da pré-análise **para** saber o aproveitamento e os motivos de descarte antes de aprovar.

**Comportamento esperado:** Funil da Lista: **Total de linhas ➔ Inválidas ➔ Bloqueadas (não perturbe) ➔ Descartadas (com o motivo, via Mensagem de erro da Regra) ➔ Qualificadas ➔ Injetadas**, com as recusadas na injeção (carência ou colisão) à parte. Mostra também quantos Clientes foram criados e quantos já existiam. Para as Qualificadas, mostra o valor potencial total e o total de requisitos "não avaliados". Em Descartadas, filtra por motivo.

## RF-PRS-04 — Aprovar e controlar a injeção

**Como** Gestor de Marketing, **eu quero** aprovar a injeção e controlar o ritmo **para** não inundar as Lojas nem gastar o saldo delas de uma vez.

**Comportamento esperado:** Em **Aguardando aprovação**, o gestor vê o total de Leads que seriam injetadas e escolhe: **Distribuir** ("injetar tudo" ou limite diário), **Concluir sem distribuir** ou **Cancelar**. Em **Guardada**, pode **Distribuir** depois, com a mesma escolha de ritmo. Em **Injetando**, pode pausar, retomar e cancelar o restante. Resultado vencido (RN-PRS-09) oferece "Reprocessar".

## RF-PRS-05 — Gestão das Listas

**Como** Gestor de Marketing, **eu quero** listar e consultar as Listas **para** acompanhar o que foi importado e distribuir as guardadas.

**Comportamento esperado:** Listagem com nome, Segmento, status, data e contadores; filtros por status, Segmento e período. Lista em Pendente de mapeamento ou Falha pode ser descartada. **Lista com Leads injetadas não se exclui**, porque as Leads a referenciam.

## RF-PRS-06 — *(removido na v0.4; número reservado)*

A Carga de base foi removida. Ver RN-PRS-16 e RN-PRS-17.

## RF-PRS-07 — Lista de bloqueio

**Como** Gestor de Marketing, **eu quero** manter os contatos que não podem ser abordados **para** cumprir os cadastros "não perturbe".

**Comportamento esperado:** Importar arquivo de CPFs e telefones, adicionar e remover entradas manualmente, buscar. Cada entrada guarda a fonte e a data; mudanças são auditadas. Contatos já injetados não são retirados do CRM por entrar na lista depois.

## RF-PRS-08 — Parâmetro de carência

**Como** Gestor de Marketing, **eu quero** definir a carência entre injeções **para** não abordar a mesma pessoa repetidamente.

**Comportamento esperado:** Campo único do tenant: dias de carência, padrão **90** (RN-MKT-04).

---

# 5. Regras de negócio (consolidado)

- **RN-PRS-01** — A Lista pertence ao tenant; Cliente nasce sem Loja.
- **RN-PRS-02** — A pré-análise cruza os dados contra Ofertas e Regras das Operações escolhidas.
- **RN-PRS-03** — Só vira Lead quem é elegível a ao menos uma Oferta; os demais não geram Lead (o Cliente já existe).
- **RN-PRS-04** — Campo sem valor não desclassifica; a análise não grava Simulação (só a injeção grava).
- **RN-PRS-05** — Resultado por contato: Inválida, Bloqueada, Descartada ou Qualificada.
- **RN-PRS-06** — Nada é injetado sem aprovação; o gestor distribui, conclui sem distribuir ou cancela.
- **RN-PRS-07** — Injetar tudo ou limite diário; pausar, retomar, cancelar.
- **RN-PRS-08** — Duplicidade e bloqueio são revalidados a cada injeção.
- **RN-PRS-09** — O resultado vence em 7 dias e exige reprocessar.
- **RN-PRS-10** — A injeção cria a Lead (Origem Prospecção, referência à Lista) associada ao Cliente já criado, e grava a Simulação de origem Marketing.
- **RN-PRS-11** e **RN-PRS-12** — *(removidas; números reservados)*.
- **RN-PRS-13** — Lista de bloqueio rígida para toda injeção do Marketing.
- **RN-PRS-14** — *(removida; número reservado)*.
- **RN-PRS-15** — Colunas de contrato da linha viram Contratos importados no histórico do Cliente.
- **RN-PRS-16** — Os Clientes nascem ao iniciar a pré-análise, depois do mapeamento, sem Loja e às vezes sem CPF.
- **RN-PRS-17** — A Lista pode ser concluída sem distribuir, fica Guardada e pode ser distribuída depois.

---

# 6. Estados e transições

**Lista de Prospecção:**

| Estado | Significado | Sai para |
|---|---|---|
| **Pendente de mapeamento** | Arquivo carregado, mapeamento e pré-análise por configurar. | Processando pré-análise · descartada |
| **Processando pré-análise** | Rotina em segundo plano: cria os Clientes e depois avalia. Lista trancada. | Aguardando aprovação · Falha |
| **Aguardando aprovação** | Resultado pronto, nada foi injetado. | Injetando (distribuir) · Guardada (concluir sem distribuir) · Cancelada · Processando (reprocessar) |
| **Guardada** | Pré-análise pronta; o gestor concluiu sem distribuir. Clientes mantidos, nenhuma Lead gerada. Pode ser distribuída depois (RN-PRS-17). | Injetando (distribuir) · Processando pré-análise (reprocessar, se vencida) · Cancelada |
| **Injetando** | Distribuição aprovada; as Leads saem conforme o ritmo. | Concluída · Cancelada (o restante) · pausada e retomada |
| **Concluída** | Fila esvaziada. | — |
| **Falha** | Erro estrutural (arquivo corrompido). | descartada |
| **Cancelada** | Cancelada pelo gestor antes de injetar tudo. | — |

---

# 7. Limites do módulo

**Não** inclui: a distribuição em si (Leads, CRM); a edição das Ofertas e Regras (Admin); a precificação cobrada da Loja (Funis de Vendas); o envio de mensagens aos contatos; a importação de Clientes pela Loja (**removida do CRM**); notificações de lead injetada (as Leads aparecem no board e no pool); e a aplicação da Lista de bloqueio fora do Marketing (Leads manuais, Huggy e landing pages não passam por ela; é nota de evolução, Produto §6).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Ofertas e Regras (Admin)** — motor de elegibilidade e catálogo de campos.
- **Segmentos e Funis de Vendas (Admin)** — destino da Lead.
- **Origem de Venda (Admin)** — rótulo opcional.
- **Clientes (CRM)** — deduplicação (por CPF, ou por Nome + Telefone sem CPF) e criação do Cliente do tenant e do Contrato importado.

**Expõe para:**
- **Leads (CRM)** — o evento de injeção, que dispara o rodízio entre as Lojas.
- **Carteira Ativa** — a Lista de bloqueio e a carência (RN-MKT), e os Contratos importados que ela minera.
- **Dashboard (Marketing)** — aproveitamento, custo e volume gerado.
- **Notificações (Shell Privado)** — Lista em Aguardando aprovação e em Falha (RN-MKT-10).

---

# 9. Itens abertos

1. *(Resolvido na v0.3.)* **Região e pré-análise.** A pré-análise é do tenant, sem Loja, e considera as Ofertas ativas de **qualquer Região**; a Região só atua na Simulação da Loja que recebe a Lead (Ofertas, RN-REG-04). O resumo da Lead é referência.
2. *(Resolvido na v0.3.)* **E-mail na Carga de base:** opcional; o Cliente não exige e-mail (Clientes v0.8).
3. *(Movido na v0.4 para as notas de evolução do Produto, §6.)* **Lista de bloqueio** fora do Marketing.
4. *(Resolvido na v0.3.)* **Valor potencial em lote:** sem valor desejado, parcela = margem disponível e prazo = máximo da Oferta (Produto, RN-MKT-08).
5. *(Removido na v0.5: base legal e retenção estão fora do escopo do produto.)*
6. *(Resolvido na v0.4.)* A Carga foi removida. A Carteira Ativa lê o histórico de contratos, que a Lista também alimenta (RN-PRS-15).
7. *(Resolvido na v0.6.)* **Clientes de contatos descartados:** o Cliente é criado para toda linha válida e não bloqueada, inclusive as Descartadas (RN-PRS-16).
8. *(Resolvido na v0.6.)* **Momento de criar os Clientes:** no início do processamento, e não ao confirmar o mapeamento.

---

*Fim do documento. Prospecção v0.8 — módulo da aplicação Marketing.*
