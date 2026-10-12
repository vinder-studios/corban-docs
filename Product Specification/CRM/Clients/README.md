# Corban CRM · Clientes

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.13
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.13 | **Perfil do Cliente e Notas** (§3.5, §3.6, RN-CLI-14 e RN-CLI-15): os dados que o Layout de Lead exibe (financeiros, do servidor, trabalhistas, da empresa) e as Notas passam a ter dono, o Cliente. Lacunas achadas no modelo de dados; decisão do usuário. |
| 0.12 | **Documentos do Cliente valem entre Lojas** (RF-CLI-04): qualquer pessoa com acesso ao Cliente, de qualquer Loja associada, vê todos os Documentos dele; os Documentos da Proposta seguem restritos à Proposta. Decisão do usuário, no modelo de dados. |
| 0.11 | **Pendências confirmadas pelo usuário:** duplicidade sem CPF por Nome + Telefone exatos, sem fusão (RN-CLI-12); campos do Contrato (RN-CLI-13), sem tela de consulta do histórico. Itens 2 e 3 resolvidos. |
| 0.10 | Sai o item aberto sobre o histórico anterior à v0.9 (§9, item 4), por ser detalhe de migração. A aba Simulações passa a incluir as de origem Marketing da Lead (Simulações v0.12). |
| 0.9 | **Decisões do usuário (Marketing).** (1) **CPF deixa de ser obrigatório** no Cliente (§3.1): o Marketing cria Clientes só com Nome e Telefone, e o CPF é preenchido depois, uma única vez (nova RN-CLI-11, RF-CLI-07). (2) **Duplicidade sem CPF** (nova RN-CLI-12). (3) **O Cliente passa a ter um histórico de contratos** (nova §3.3, RN-CLI-13), que alimenta a Carteira Ativa; é de uso interno, sem tela nesta versão. (4) A **Carga de base** foi removida do Marketing: as menções são trocadas pela importação da Lista de Prospecção. RN-CLI-03, RN-CLI-04, RN-CLI-05 e RF-CLI-02 acompanham. |
| 0.8 | **E-mail deixa de ser obrigatório** no Cliente (§3.1), porque o Cliente nasce de uma Lead só com Nome, CPF e Telefone e a Carga de base do Marketing pode não trazê-lo. Fecha o item aberto do §9. A Proposta continua pedindo e-mail no formulário dela. Decisão do usuário. |
| 0.7 | **Levantamento do Marketing, decisão do usuário: o Cliente é do tenant, não da Loja.** (1) **Sai a importação de Clientes pela Loja**: RF-CLI-02, RN-CLI-05 e a permissão `clients:import:store` deixam de existir; a importação em lote passa a ser exclusiva do Marketing (**Carga de base**), no nível do tenant, e o Cliente carregado **nasce sem Loja associada**. (2) A associação a uma Loja passa a nascer **só pela distribuição de uma Lead** (RN-CLI-04). (3) Os estados vazios e os limites deixam de oferecer "Importar". Os números RF-CLI-02 e RN-CLI-05 ficam reservados, para não quebrar referências. |
| 0.6 | **Levantamento do CRM, decisões do usuário.** (1) **Novo RF-CLI-07: editar dados do cliente**, por qualquer Loja associada, valendo para todas, com o **CPF não editável**. (2) **Nova RN-CLI-10: o Cliente é do tenant, a Loja só acessa**; excluir ou inativar a Loja não afeta o Cliente. (3) **A busca e o preenchimento por CPF só valem para Clientes já associados à Loja**, e criar Simulação ou Proposta não concede acesso (RN-CLI-04). (4) A RN-CLI-09, que só existia no consolidado, ganha o parágrafo no corpo. (5) Os avisos de abertura deixam de contar a história da rodada e passam a descrever o Marketing como é (Prospecção e Carteira Ativa). |
| 0.5 | **Ficha do Cliente vira Página com 7 abas** (Dados gerais · Benefícios · Documentos · Simulações · Leads associadas · Conversas · Propostas) — motivado pelo volume, mesmo critério já aplicado a Time e Membro. Três capacidades novas, nunca modeladas aqui: **Documentos do Cliente** (RF-CLI-04, mesmo padrão de Documentos da Proposta), **Conversas** (RF-CLI-05, lista de Chat por Cliente) e **Propostas** (RF-CLI-06, lista por Cliente). Conversas e Propostas escopadas à Loja de quem consulta, mesmo critério de RN-CLI-04. |
| 0.4 | Adiciona **Endereço** (com integração ViaCEP), **E-mail** (obrigatório) e **Benefícios** (múltiplas contas bancárias/matrículas por Cliente) — motivado pela especificação do Card de Proposta. |
| 0.3 | Remove a referência a RF-SIM-03 (simulação automática via importação) — **não existe mais no módulo de Simulações**, era escopo indevido do CRM; a ideia pertence à aplicação Marketing (item aberto §9.1, já registrado). Importação pela Loja (RF-CLI-02) não dispara mais nenhuma simulação automática. |
| 0.2 | Fecha o item aberto sobre a Simulação automática: são **dois eventos sequenciais e distintos** da Huggy, não um só — a criação do Cliente (dados básicos) sempre acontece primeiro; o gatilho de Simulação (dados adicionais) vem depois, num segundo momento do fluxo. |
| 0.1 | Versão inicial. Retomado após ter sido pulado — Simulações (RF-SIM-03) e Chat já dependiam deste módulo. Substitui e adapta o Módulo 7 da Especificação v1.2 ao modelo de Lojas parceiras. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-CLI) e regras de negócio (RN-CLI) próprios.

> **Lead e Cliente são entidades distintas:** a Lead não "vira" Cliente — ela **possui** um Cliente, associação que pode nascer em momentos diferentes conforme a origem (RN-CLI-01). Uma Lead pode existir por um tempo **sem** Cliente associado.

> **Marketing é a terceira aplicação do produto** (Shell Privado: "Admin, CRM e Marketing"). Ela injeta leads (Origens **Prospecção** e **Carteira Ativa**, Leads RN-LED-12), e a Carteira Ativa mina a base de Clientes do tenant. A **importação em lote de Clientes** (na Lista de Prospecção) e a injeção de Leads pertencem ao Marketing e não existem neste módulo.

---

# 1. Objetivo do módulo

Manter a ficha consolidada de Clientes — criados automaticamente a partir de Leads que reúnem dados suficientes, ou criados pelo Marketing na importação de uma Lista (sem Loja e, às vezes, sem CPF) — com controle de acesso por associação a Loja. A ficha reúne, numa única página, tudo que outros módulos já associam ao Cliente (Documentos, Simulações, Leads, Conversas, Propostas), sem redefinir o conteúdo de cada um.

---

# 2. Atores

- **Operador, Gestor de Loja** — consultam e editam Clientes dentro do escopo da própria Loja. A importação em lote não é feita aqui.

---

# 3. Entidades e conceitos

## 3.1. Cliente

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome completo | Sim | — |
| CPF | Não | **Único no tenant** quando preenchido (herdado de RN052 da v1.2). O Cliente do Marketing pode nascer sem ele; é preenchido depois, uma só vez (RN-CLI-11). |
| Telefone | Sim | — |
| E-mail | Não | Opcional no Cliente (v0.8). A Proposta pede o e-mail no próprio formulário (Propostas). |
| Endereço | Não | CEP, Logradouro, Número, Complemento, Bairro, Cidade, Estado — CEP integrado à API **ViaCEP** para autopreenchimento dos demais (RN-CLI-07). |
| Benefícios | Não | Zero ou mais — Matrícula, Banco, Tipo de Conta, Agência, Conta (RN-CLI-08). |
| Loja(s) associada(s) | — | Zero, uma ou mais — RN-CLI-04. Controla quem enxerga o Cliente. |

**RN-CLI-01 — Cliente nasce automaticamente quando a Lead reúne os três dados mínimos.** Assim que uma Lead tem Nome completo + CPF + Telefone — por qualquer canal (chatbot da Huggy pedindo os dados, landing page com esses campos, ou cadastro manual) —, o Cliente é criado automaticamente e associado à Lead. Até esse momento, a Lead existe **sem** Cliente associado — é estado transitório normal, não erro.

**RN-CLI-06 — Criação do Cliente sempre precede o gatilho de Simulação automática.** São dois eventos distintos e sequenciais da Huggy, não o mesmo: primeiro o fluxo coleta os dados básicos (Nome, CPF, Telefone) e o Cliente é criado (RN-CLI-01); só depois, em outro ponto do mesmo fluxo, os dados adicionais necessários à Simulação (valor/parcela, prazo — RF019 da v1.2) são coletados, dispatchando o gatilho de RF-SIM-02 (módulo de Simulações). A Simulação automática, portanto, **nunca dispara para uma Lead sem Cliente** — a ordem é sempre Cadastro → Simulação, garantida pela sequência do próprio fluxo, não verificada pelo Corban.

**RN-CLI-02 — CPF já existente associa, não duplica.** Se uma Lead reúne um CPF que já corresponde a um Cliente, ela se associa ao Cliente existente — nunca cria um segundo registro. Mesma pessoa pode ter várias Leads ao longo do tempo, todas ligadas à mesma ficha (herdado de RN052 da v1.2).

**RN-CLI-03 — Cliente pode existir sem nenhuma Lead.** Nasce da importação de uma Lista do Marketing, sem Loja associada, e só passa a ter Leads quando uma Lead dele for injetada e distribuída.

**RN-CLI-04 — Cliente é tenant-wide; acesso é por associação a Loja.** O registro do Cliente (CPF, nome, dados) é único no tenant, mas uma Loja só enxerga (na Lista, na busca, na ficha) os Clientes **associados a ela**. A associação nasce **num só momento**: quando uma Lead daquele Cliente é **distribuída** a uma Loja (módulo de Leads, RN-LED-02/03) — a Loja que recebeu a Lead passa a ter acesso ao Cliente correspondente. **A Loja não importa Clientes**: a importação em lote é do Marketing (Lista de Prospecção) e cria Clientes do tenant, sem Loja.

Um Cliente pode estar associado a **mais de uma Loja** ao longo do tempo — cada associação é independente, e cada Loja só vê o que aconteceu do seu lado (suas próprias Leads/Simulações/Propostas com aquele Cliente), não o histórico de outra Loja. **Não há uma terceira via de associação:** criar uma Simulação ou uma Proposta para um CPF que só outras Lojas conhecem **não** associa a Loja ao Cliente e **não** revela dado dele — o preenchimento e a busca por CPF só valem para Clientes já associados à Loja (RN-SIM-05, RF-PRP-03).

**RN-CLI-09 — Conversas e Propostas, na ficha, são escopadas à Loja de quem consulta.** Mesmo critério de RN-CLI-04: nunca cross-Loja (RF-CLI-05, RF-CLI-06).

**RN-CLI-10 — O Cliente é do tenant; a Loja só acessa.** O registro do Cliente pertence ao tenant, e as Lojas apenas o acessam por associação (RN-CLI-04). Excluir ou inativar uma Loja **não afeta o Cliente**, que continua no tenant e visível a quem tem visão `all`. *(Decisão do usuário, levantamento do CRM.)*

## 3.2. Endereço e Benefícios

**RN-CLI-07 — CEP autopreenche os demais campos de endereço, via ViaCEP.** O operador informa o CEP; o sistema busca e preenche Logradouro, Bairro, Cidade e Estado automaticamente — Número e Complemento continuam manuais. Falha na consulta (a integração externa fora do ar, RNF004 da v1.2) não bloqueia: o operador preenche manualmente.

**RN-CLI-08 — Benefícios são múltiplos, cada um com sua própria conta.** Um Cliente pode ter mais de um Benefício cadastrado (ex.: aposentadoria + pensão) — cada um com Matrícula, Banco, Tipo de Conta, Agência e Conta próprios.

## 3.3. CPF opcional e histórico de contratos

**RN-CLI-11 — O CPF é opcional e é preenchido depois.** Um Cliente criado pelo Marketing pode ter só **Nome e Telefone**. O CPF é preenchido por quem edita o Cliente (RF-CLI-07) ou ao informar o CPF numa Simulação ou Proposta, e **só uma vez**: depois de preenchido, não muda. Se o CPF informado já pertence a outro Cliente, o sistema **recusa** e avisa que o CPF já está cadastrado, **sem revelar dados** do outro Cliente (RN-CLI-04); não há fusão de Clientes nesta versão. Simulação e Proposta continuam exigindo o CPF (RN-SIM-05, RF-PRP-03). *(Decisão do usuário.)*

**RN-CLI-12 — Duplicidade sem CPF.** Com CPF, vale a RN-CLI-02. Sem CPF, o Cliente existente é reaproveitado quando **Nome completo e Telefone** coincidem exatamente; do contrário, nasce um novo.

**RN-CLI-13 — Histórico de contratos.** O Cliente guarda um **histórico de Contratos**, usado pela Carteira Ativa para saber quando uma nova operação pode se abrir. É de **uso interno do tenant**: as Lojas não o veem, e esta versão não tem tela para consultá-lo. Cada Contrato é um registro com:

| Campo | Notas |
|---|---|
| Cliente | O dono do Contrato. |
| Segmento | O da Proposta (via Lead) ou o Segmento alvo da Lista que o importou. |
| Operação | Uma das 4 do catálogo. |
| Banco | Quando conhecido. |
| Valor, Parcela, Prazo (meses) | Parcela e Prazo são os dados que o motor precisa; sem eles, o Contrato fica "sem dados para análise" na Carteira Ativa. |
| Data de pagamento ao cliente | Base do tempo decorrido; o prazo restante é calculado a partir dela. |
| Origem do registro | **Proposta Paga** (criado quando a Proposta chega a Pago, Propostas RN-PRP-18, e acompanha as edições dela) ou **Importado** (gravado a partir das colunas de contrato da Lista, Prospecção RN-PRS-15). |

(Campos confirmados pelo usuário.)

## 3.4. Importação em lote

**RN-CLI-05 — *(removida na v0.7; número reservado).*** A importação pela Loja deixou de existir. Clientes em lote chegam pela **Lista de Prospecção** do Marketing (Prospecção, RN-PRS-16), nascem **sem Loja** e só passam a ser vistos por uma Loja quando uma Lead deles é distribuída a ela (RN-CLI-04).


## 3.5. Perfil do Cliente

**RN-CLI-14 — Os dados de perfil são do Cliente.** Rendimentos, margem, data de nascimento, sexo, Órgão (com a esfera e o Estado ou Prefeitura), data de admissão e os dados da empresa empregadora (nome, CNPJ, data de fundação, nº de funcionários, CNAE) descrevem a pessoa, não a negociação: ficam no Cliente e aparecem em toda Lead dele, nos blocos do Layout de Lead (Dados financeiros, Dados do servidor, Dados trabalhistas, Dados da empresa, Slot 3 do Card). A Matrícula vem dos Benefícios (RN-CLI-08). São preenchidos pela Huggy, pelo operador (edição da ficha), pela Simulação (dados informados nela) e pelas colunas mapeadas da Lista de Prospecção, e vale o último valor informado. São também os campos que as Regras avaliam (Regras, catálogo de campos). *(Decisão do usuário.)*

## 3.6. Notas

**RN-CLI-15 — Notas são do Cliente.** O operador registra anotações em texto no Sheet da Lead (bloco Notas, RN-LAY-08); cada nota guarda autor e data, aparece em toda Lead do Cliente e é visível a qualquer pessoa com acesso a ele, de qualquer Loja associada, como os Documentos do Cliente (RF-CLI-04). A nota não é editada; quem a criou pode excluí-la. *(Decisão do usuário.)*

---

# 4. Requisitos funcionais

## RF-CLI-01 — Ficha do cliente

Herdado de RF031 da v1.2, reorganizado em 7 abas — escopado às Lojas que o usuário alcança:

| Aba | Conteúdo | Fonte |
|---|---|---|
| Dados gerais | Nome, CPF, Telefone, E-mail, Endereço | Este módulo |
| Benefícios | Matrícula, Banco, Tipo de Conta, Agência, Conta (RN-CLI-08) | Este módulo |
| Documentos | Upload/download/exclusão (RF-CLI-04) | Este módulo |
| Simulações | Histórico, com atalho para Nova simulação pré-preenchida com este Cliente; inclui as de origem Marketing das Leads da Loja | Módulo de Simulações — não redefinido aqui |
| Leads associadas | Ativas e concluídas | Módulo de Leads — não redefinido aqui |
| Conversas | Histórico de Chat (RF-CLI-05) | Módulo de Chat — não redefinido aqui |
| Propostas | Lista por Cliente (RF-CLI-06) | Módulo de Propostas — não redefinido aqui |

## RF-CLI-04 — Documentos do Cliente

**Como** operador, **eu quero** anexar documentos à ficha do Cliente **para** ter esse material disponível em qualquer negociação com ele.

**Comportamento esperado:** mesmo padrão já usado em Documentos da Proposta (RF-PRP-07, módulo de Propostas) — filepicker com drag-and-drop, tabela com Descrição, Data de envio, Operador, ações Baixar e Excluir. **Diferente de Documentos da Proposta** (exclusivos de uma negociação — RN-PRP-11): um documento aqui aparece em **todas** as negociações do Cliente, distinção já registrada em Layout de Lead (RN-LAY-06) e Propostas (RN-PRP-11), agora com RF próprio neste módulo. **Quem vê:** qualquer pessoa com acesso ao Cliente, de qualquer Loja associada a ele (RN-CLI-04), vê todos os Documentos do Cliente, inclusive os enviados por outra Loja. É a exceção à regra de cada Loja só ver o seu lado (RN-CLI-09), porque o documento é da pessoa, não da negociação. *(Decisão do usuário.)*

## RF-CLI-05 — Aba Conversas

**Como** operador ou gestor, **eu quero** ver o histórico de conversas do Cliente **para** ter contexto de atendimento sem precisar abrir cada Lead separadamente.

**Comportamento esperado:** lista das Conversas (módulo de Chat) de todas as Leads deste Cliente **dentro da Loja de quem consulta** — mesmo critério de escopo de RN-CLI-04, nunca cross-Loja. Cada item abre o Painel de Conversa correspondente (RF-CHT-03).

## RF-CLI-06 — Aba Propostas

**Como** operador ou gestor, **eu quero** ver todas as Propostas do Cliente **para** ter visão completa do relacionamento comercial com ele.

**Comportamento esperado:** lista de Propostas (módulo de Propostas) vinculadas a este Cliente — via Lead ou diretamente (RN-PRP-01, Lead é opcional em Proposta) — **dentro da Loja de quem consulta**, mesmo critério de RN-CLI-04. Cada item abre o Card de Proposta correspondente.

## RF-CLI-07 — Editar dados do cliente

**Como** operador ou gestor de uma Loja associada, **eu quero** editar os dados do Cliente **para** mantê-los corretos.

**Comportamento esperado:** Nome completo, Telefone, E-mail, Endereço (com ViaCEP, RN-CLI-07) e Benefícios (RN-CLI-08) são editáveis a partir da ficha, por quem tem a permissão de edição no escopo certo. Como o Cliente é do tenant (RN-CLI-10), **a edição vale para todas as Lojas associadas**. O **CPF** só pode ser preenchido quando está vazio, **uma única vez**; depois disso **não é editável**, porque é a identidade do Cliente e é único (RN-CLI-02, RN-CLI-11). *(Decisão do usuário, levantamento do CRM.)*

## RF-CLI-02 — *(removido na v0.7; número reservado)*

A importação de clientes pela Loja deixou de existir: o Cliente é do tenant, e a importação em lote é a **Lista de Prospecção** do Marketing (Prospecção, RF-PRS-01).

## RF-CLI-03 — Exportação de clientes

Herdado de RF033 da v1.2, sem mudança de princípio — escopado aos Clientes que a Loja enxerga.

---

# 5. Regras de negócio (consolidado)

- **RN-CLI-01** — Cliente nasce automaticamente ao reunir Nome + CPF + Telefone na Lead.
- **RN-CLI-02** — CPF já existente associa, não duplica.
- **RN-CLI-03** — Cliente pode existir sem nenhuma Lead (criado pela Lista do Marketing).
- **RN-CLI-04** — Cliente é tenant-wide; acesso por associação a Loja (nascida só da distribuição de Lead), podendo ter mais de uma.
- **RN-CLI-05** — *(removida; número reservado)*.
- **RN-CLI-06** — Criação do Cliente sempre precede o gatilho de Simulação automática — dois eventos sequenciais da Huggy, nunca o mesmo.
- **RN-CLI-07** — CEP autopreenche endereço via ViaCEP.
- **RN-CLI-08** — Benefícios são múltiplos, cada um com sua própria conta.
- **RN-CLI-09** — Conversas e Propostas, na ficha, são escopadas à Loja de quem consulta — mesmo critério de RN-CLI-04, nunca cross-Loja.
- **RN-CLI-10** — O Cliente é do tenant; a Loja só acessa. Excluir ou inativar a Loja não afeta o Cliente.
- **RN-CLI-11** — O CPF é opcional e preenchido depois, uma só vez; é único no tenant.
- **RN-CLI-12** — Sem CPF, a duplicidade é por Nome + Telefone exatos.
- **RN-CLI-13** — O Cliente tem um histórico de contratos, de uso interno, que alimenta a Carteira Ativa.

---

# 6. Estados e transições

Cliente não tem estados de publicação — existe ou não existe, uma vez criado permanece (sem inativação/exclusão modelada nesta versão — herdado do silêncio da v1.2 a respeito).

---

# 7. Limites do módulo

**Não** inclui: a **importação em lote** de Clientes (Marketing, Lista de Prospecção), a injeção de Leads por pré-análise (Marketing), a distribuição de Leads em si (módulo de Leads), nem simulações/propostas (módulos próprios — este só referencia).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Leads** (CRM) — o evento de reunir dados mínimos (RN-CLI-01), o de distribuição a uma Loja (RN-CLI-04), e a lista de Leads associadas (aba Leads associadas).
- **Simulações** (CRM) — histórico exibido na aba Simulações; o atalho de Nova simulação (já definido no módulo de Simulações, "acesso contextual" via ficha do Cliente).
- **Chat** (CRM) — as Conversas exibidas na aba Conversas (RF-CLI-05).
- **Propostas** (CRM) — as Propostas exibidas na aba Propostas (RF-CLI-06) e a geração do Contrato quando a Proposta chega a Pago (RN-CLI-13).

**Expõe para:**
- **Simulações** (CRM) — associação automática por CPF quando uma Simulação avulsa (Página de Simulação) referencia um Cliente já cadastrado.
- **Propostas** (CRM) — o Cliente de origem, via Lead ou direto.
- **Layout de Lead** (Admin) — Documentos do Cliente (RF-CLI-04), distintos de Documentos da Proposta (RN-LAY-06).
- **Marketing** — a Prospecção cria Clientes (e Contratos importados); a Carteira Ativa minera o **histórico de contratos** do tenant para gerar leads com Origem Carteira Ativa (Leads, RN-LED-12).

---

# 9. Itens abertos

1. *(Resolvido na v0.8.)* **E-mail:** opcional no Cliente; a Proposta o exige no formulário dela.
2. *(Resolvido na v0.11.)* **Duplicidade sem CPF:** Nome + Telefone exatos, sem fusão de Clientes.
3. *(Resolvido na v0.11.)* **Campos do Contrato** confirmados (RN-CLI-13). O histórico segue de uso interno, sem tela.
4. *(Removido na v0.10: migração do histórico é detalhe de implementação.)*

---

*Fim do documento. Clientes v0.13 — módulo da aplicação CRM.*
