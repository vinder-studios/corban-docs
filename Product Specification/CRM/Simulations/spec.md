# Corban CRM · Simulações

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.14
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.14 | **Visibilidade da Simulação de origem Marketing corrigida:** não é visível a qualquer usuário com acesso ao Cliente; vale a regra de cada Loja ver o seu lado (Clientes, RN-CLI-04 e RN-CLI-09): a Loja vê as Simulações que fez e as de origem Marketing das Leads que recebeu. Decisão do usuário, no modelo de dados. |
| 0.13 | **Momento e visibilidade confirmados pelo usuário.** A Simulação de origem Marketing é gravada na injeção da Lead e fica no histórico do Cliente, visível a qualquer usuário com acesso a ele, como as demais. |
| 0.12 | **Terceira origem: Marketing.** Quando a avaliação em lote do Marketing encontra ao menos uma Oferta aprovada para um contato e a Lead dele é injetada, a avaliação é **gravada como Simulação** de origem **Marketing**, no histórico do Cliente (RN-SIM-01, RN-MKT-02). Decisão do usuário. Sem Oferta aprovada, ou sem distribuição, nada é gravado. |
| 0.11 | **Levantamento do CRM.** (1) **A RN-SIM-11, inserida por engano dentro da seção "Modalidade CLT — fora de escopo", vai para a §3.4** (junto da RN-SIM-10), e o consolidado volta à ordem. (2) A RN-SIM-01 diz **duas** origens (dizia três). (3) A Região é a da **Loja do operador** contra a da Promotora (RN-REG-04), e o §8 cita Lojas. (4) **Decisões do usuário:** na Página de Simulação, os Segmentos são os ativos que a Loja opera; a Promotora da Lead pré-preenche a da simulação; **o preenchimento por CPF só vale para Clientes já associados à Loja** (nenhum dado de outra Loja é revelado). (5) A RN-SIM-04 deixa de citar uma frase da RN-FNL-04 que não existe mais; "modalidade" vira Operação; o catálogo de Operação é de Ofertas. Reordena e fecha este histórico. |
| 0.10 | **O motivo passa a mostrar os requisitos que falharam, com o valor da Oferta** (nova RN-SIM-11), porque a mensagem de erro da Regra deixa de citar valores (Regras v0.8, RN-RGR-11): eles variam por Oferta. Vale para Oferta não elegível (Regra bloqueante) e para elegível com alerta (Regra de aviso). O snapshot passa a registrar os requisitos que falharam. |
| 0.9 | **Acompanha a decisão em Produtos v0.4: a Operação independe do Produto.** A RN-SIM-06a passa a dizer que a Operação é livre entre as 4 do catálogo; a RN-SIM-07 depende da Operação escolhida (e não "do Produto escolhido"); trocar a Operação, e não o Produto, descarta os campos condicionais. Alteração de consistência: a revisão completa do módulo segue na fila. |
| 0.8 | **Corrige resíduo da v0.4**, encontrado ao analisar o CRM para Relatórios: a v0.4 removeu a origem "Automática — via importação de Clientes" (escopo do Marketing), mas a tabela de campos ("Origem do disparo") continuou listando-a. Agora são só duas origens, coerente com RN-SIM-01. |
| 0.7 | **Adiciona o campo explícito Operação ao formulário** — Produto passa a suportar várias Operações (módulo de Produtos v0.3), então não dá mais para inferir qual delas o operador quer simular só pelo Produto. Novo campo, logo após Produto, restrito às Operações que aquele Produto suporta. RN-SIM-06 e RN-SIM-07 atualizadas. |
| 0.6 | Confirma o mapeamento Operação → campo de valor do card (RN-SIM-10) — a inferência por lógica de domínio estava correta. Sem mais pendências. |
| 0.5 | Adiciona RN-SIM-10: Ofertas aprovadas exibem-se como **card** (Banco, valor — campo condicional por Operação —, Prazo, Parcela, Taxa), reutilizado em três lugares (resultado da simulação, ficha do Cliente, Sheet de detalhes da oferta — este último, tela nova). |
| 0.4 | Remove a origem "Automática — via importação de Clientes" (RF-SIM-03, RN-SIM-02) — **não é escopo do CRM**, é capacidade da aplicação Marketing (tenant-wide, com pré-análise), não modelada aqui. Duas origens restantes: Manual e Automática via Lead. Adiciona o campo **Segmento** explícito (RN-SIM-06 revisada) — sempre usado como filtro, esquecido na v0.3. |
| 0.3 | **Reformulação de fundo.** Adiciona a **Página de Simulação** (destino de topo, formulário manual completo) — a origem "Manual" deixa de ser vaga e ganha especificação real: Dados do cliente capturados inline (CPF, Nome, Data de nascimento, Telefone), Dados da oferta (Produto + Promotora, não mais Oferta direta), Dados do contrato atual e Dados preferenciais — ambos **condicionais por Operação** (4 variantes, uma por Operação do Produto). **Revoga RN035 da v1.2** ("não existe simulação avulsa") — cliente não precisa estar cadastrado; os dados dele vão no snapshot de qualquer forma. **CLT/Jaéconsig sai de escopo nesta versão** — só o motor interno (Ofertas/Regras) está ativo; sem módulo de integrações externas ainda. |
| 0.2 | RN-SIM-04 ganha precisão: o gatilho de simulação automática via Lead é **sempre posterior** à criação do Cliente (RN-CLI-06, módulo de Clientes) — dois eventos sequenciais e distintos da Huggy, nunca o mesmo. |
| 0.1 | Versão inicial. Substitui e amplia o Módulo 5 da Especificação v1.2, incorporando o motor sobre Ofertas/Regras já fechado no Admin e **revogando explicitamente o limite de "sem simulação em lote"** daquele documento (§5.8) — a importação de Clientes agora dispara simulação automática em lote. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-SIM) e regras de negócio (RN-SIM) próprios.

> **Este módulo é orquestração, não cálculo.** A elegibilidade (Segmento, Região, Regras) e a parametrização financeira (taxa, prazo, comissão) já estão inteiramente definidas nos módulos de Ofertas e Regras (Admin). O que este módulo define é: quando uma simulação é disparada, o que ela avalia, e como o resultado é registrado.

---

# 1. Objetivo do módulo

Calcular e registrar, de forma comparável, as condições de crédito disponíveis para uma Lead ou Cliente — manualmente, pelo operador, ou automaticamente, ao vir de uma Lead.

---

# 2. Atores

- **Operador** — dispara simulações manuais, consulta resultados.
- **Sistema** — dispara a simulação automática via Lead e grava a avaliação em lote do Marketing.
- **Huggy** — origina o evento que dispara a simulação automática via Lead (fora do controle direto do Corban — RN-SIM-04).

---

# 3. Entidades e conceitos

## 3.1. Simulação

Registro **imutável** (snapshot) de uma rodada de cálculo comparativo. Mesmo princípio de RN033 da Especificação v1.2, sem mudança: alterações posteriores em Ofertas/Regras não afetam simulações já registradas — inclusive "refazer" (RF-SIM-04) sempre gera um **novo** registro, nunca sobrescreve o anterior.

| Campo | Notas |
|---|---|
| **Dados do cliente** | **Sempre capturados inline no snapshot** — CPF, Nome completo, Data de nascimento, Telefone. Ver RN-SIM-05: não exige Cliente cadastrado. |
| Lead (opcional) | Presente quando a simulação parte do contexto de uma Lead. |
| Cliente (opcional) | Presente quando o CPF informado já corresponde a um Cliente cadastrado (RN-CLI-02, associação automática) — ou quando a simulação vem de um Cliente já existente. |
| **Segmento** | Sempre presente — pré-preenchido a partir da Lead, quando houver; escolhido pelo operador na Página de Simulação quando não houver Lead de origem. Filtra as Ofertas elegíveis (RN-OFR-08, módulo de Ofertas), como em qualquer simulação. |
| **Dados da oferta** | Produto + **Operação** (uma das 4 do catálogo fixo — RN-SIM-06a) + Promotora. O motor avalia todas as Ofertas que combinam Produto, Operação, Promotora **e** o Segmento informado (RN-SIM-06). |
| **Dados do contrato atual** | Condicional pela Operação escolhida — ver RN-SIM-07. |
| **Dados preferenciais** | Condicional pela Operação escolhida — ver RN-SIM-07. |
| Origem do disparo | **Manual (Página de Simulação)**, **Automática — via Lead** ou **Marketing — avaliação em lote** (RN-SIM-01). |
| Resultados por Oferta | Lista comparável: Oferta, elegível ou não, motivo (mensagem de erro da Regra que desqualificou — RN-RGR-11, mais os requisitos que falharam, com o valor da Oferta — RN-SIM-11), taxa, prazo, parcela, valor liberado/total. |
| Data/hora, autor | Autor é o operador (manual) ou "Sistema" (automática e Marketing). |

**RN-SIM-05 — Cliente não precisa estar cadastrado; a Simulação não é mais estritamente vinculada.** *(Revoga RN035 da Especificação v1.2 — "não existe simulação avulsa em nenhuma versão do produto", citada como decisão permanente naquele documento.)* Como toda Simulação já grava um snapshot completo, os Dados do cliente bastam por si — cadastrado ou não. Se o CPF corresponder a um Cliente existente, a Simulação se associa a ele automaticamente (aparece na ficha dele); se não corresponder, a Simulação existe com os dados digitados, sem criar Cliente nem Lead como efeito colateral. **O preenchimento e a associação por CPF só valem para Clientes já associados à Loja do operador** (RN-CLI-04): para um CPF que só outras Lojas conhecem, usa-se o que o operador digitou, sem revelar nenhum dado e sem conceder acesso à Loja.

**RN-SIM-06 — Dados da oferta são Segmento + Produto + Operação + Promotora, não uma Oferta específica.** O operador escolhe (ou herda da Lead) o Segmento, escolhe Produto, escolhe a Operação (uma das 4 do catálogo — RN-SIM-06a) e Promotora; o motor avalia **todas** as Ofertas que combinam os quatro — mesmo comportamento comparativo já estabelecido (RN-OFR-02 Região, RN-OFR-08 Segmento, Regras). Confirmado: o filtro de Segmento **sempre** se aplica, com ou sem Lead de origem — nunca é pulado. Na Página de Simulação, sem Lead de origem, a lista de Segmentos traz os **Segmentos ativos que a Loja do operador opera** (Ativo na Aba Segmentos). A **Promotora** da Lead, quando há, pré-preenche a Promotora da simulação.

**RN-SIM-06a — Operação é campo próprio, livre entre as 4 do catálogo fixo.** A Operação não depende do Produto escolhido: o operador escolhe explicitamente qual está simulando, e qualquer Produto pode ser combinado com qualquer Operação (o catálogo é definido em Ofertas; o Produto não carrega Operação). Combinação que nenhuma Oferta ativa cobre não é erro: o resultado é "nenhuma oferta encontrada". Trocar a Operação (não o Produto) descarta os campos condicionais já preenchidos (RN-SIM-07).

**RN-SIM-07 — Dados do contrato atual e Dados preferenciais são condicionais pela Operação.** Os campos que aparecem no formulário dependem da Operação escolhida:

| Operação | Dados do contrato atual | Dados preferenciais |
|---|---|---|
| **Novo** | Margem, Prazo (opcional) | Nenhum |
| **Portabilidade** | Parcela Atual, Prazo Restante, Saldo devedor (opcional) | Prazo desejado, Margem disponível |
| **Refinanciamento** | Parcela Atual, Prazo Restante, Saldo devedor (opcional) | Prazo desejado, Troco Desejado, Margem disponível |
| **Portabilidade com Refinanciamento** | Fatura, Saldo devedor, Prazo | Nenhum |

Para Portabilidade, a mesma Operação absorve o que antes era "Compra de Dívida" (módulo de Produtos v0.3) — usa este mesmo conjunto de campos.

## 3.2. Origens do disparo

**RN-SIM-01 — Três origens possíveis, mesmo motor.** O cálculo em si — filtrar Ofertas por Segmento, depois por Região (a da Loja do operador contra a da Promotora, RN-REG-04), depois avaliar Regras bloqueantes e de aviso — é idêntico nas três origens. O que muda é só o que dispara a rodada:

| Origem | Gatilho |
|---|---|
| **Manual (Página de Simulação)** | Operador aciona explicitamente, pela tela dedicada — RF-SIM-01. Cliente cadastrado ou não (RN-SIM-05). |
| **Automática — via Lead** | Evento vindo da Huggy, quando o fluxo configurado pelo tenant considera que "os dados do usuário foram obtidos" (RN-SIM-04). |
| **Marketing — avaliação em lote** | A Lead de um contato com ao menos uma Oferta aprovada na pré-análise do Marketing é injetada. A avaliação é gravada como Simulação, ligada à Lead e ao Cliente, com os dados usados e as Ofertas aprovadas. Não consome Créditos (RN-SIM-03). Sem CPF no Cliente, o campo CPF do snapshot fica vazio. Fica no histórico do Cliente e é visível à Loja que recebeu a Lead (Clientes, RN-CLI-04 e RN-CLI-09): cada Loja vê só as Simulações do seu lado. |

**O cálculo em lote não é escopo deste módulo.** Simular uma base inteira pertence à aplicação **Marketing** (Prospecção e Carteira Ativa), que roda a avaliação em segundo plano **sem gravar Simulação**. Este módulo só recebe o registro das avaliações com Oferta aprovada, quando a Lead é injetada (origem Marketing). O que antes era RF-SIM-03/RN-SIM-02 foi removido daqui.

**RN-SIM-03 — Simulação não consome Créditos.** Diferente de receber uma lead (que debita saldo, módulo de Créditos), simular é ação livre e repetível — nenhuma das origens gera lançamento no Extrato.

**RN-SIM-04 — O gatilho automático via Lead é evento externo, fora do controle direto do Corban — e sempre posterior à criação do Cliente.** O momento exato de "dados obtidos" depende dos fluxos que o tenant configura na Huggy — mesmo princípio de RN-FNL-04 (módulo de Funis de Vendas): a Huggy é externa, e o Corban reage ao que recebe, sem depender dela. O Corban reage ao evento recebido; não modela nem valida a lógica interna daquele fluxo.

**Confirmado (RN-CLI-06, módulo de Clientes):** são dois eventos sequenciais e distintos, não um só. O fluxo da Huggy primeiro coleta os dados básicos (Nome, CPF, Telefone) — criando o Cliente — e só depois, noutro ponto do mesmo fluxo, coleta os dados adicionais necessários à simulação (valor/parcela, prazo). Consequência: a **simulação automática via Lead nunca dispara para uma Lead sem Cliente associado** — a ordem Cadastro → Simulação é garantida pela sequência do próprio fluxo configurado na Huggy, não verificada pelo Corban.

## 3.4. Exibição de Ofertas aprovadas — card padrão, reaproveitado em três lugares

**RN-SIM-10 — Toda Oferta aprovada exibe-se como card, com o mesmo conjunto de campos, em três superfícies do produto.** Banco, **valor** (campo condicional pela Operação — ver tabela), Prazo, Valor da parcela, Taxa.

| Operação | Campo de valor exibido |
|---|---|
| Novo | Valor liberado |
| Refinanciamento | Troco liberado |
| Portabilidade | Valor financiado |
| Portabilidade com Refinanciamento | Troco liberado |

Confirmado.

**As três superfícies que usam este card:**
1. Resultado da simulação (Página de Simulação e Sheet contextual, este módulo).
2. Ficha do Cliente (módulo de Clientes — histórico de Ofertas aprovadas em simulações passadas).
3. **Sheet de detalhes da oferta** (tela nova, RF-SIM-05) — abre a partir do card, em qualquer uma das duas superfícies acima, com o detalhe completo daquela Oferta dentro daquela Simulação específica.

**RN-SIM-11 — O motivo de uma Oferta que não passa mostra os requisitos que falharam, com o valor da Oferta.** Para cada Oferta **não elegível** (Regra bloqueante) e para cada Oferta **elegível com alerta** (Regra de aviso), o resultado mostra, junto da **mensagem de erro da Regra** (explicação geral, sem valores — RN-RGR-11), a lista dos **requisitos que falharam**: Campo, condição e o valor definido **pela Oferta** (ex.: "Idade: maior ou igual a 21"). É assim que o operador vê o número exigido sem que a mensagem precise citá-lo (o valor varia por Oferta, RN-RGR-12), e que se sabe qual requisito falhou numa Regra de vários. Se mais de um requisito falhou, todos são listados. O snapshot imutável registra, por Oferta avaliada, a Regra, a mensagem e os requisitos que falharam, com os valores da época (§3.1).

## 3.5. Modalidade CLT — fora de escopo nesta versão

**RN-SIM-08 — Só o motor interno está ativo; nenhuma API externa nesta versão.** Diferente do que se cogitou herdar da v1.2 (RN029/RF020 — consulta à API Jaéconsig), esta versão do produto **não inclui nenhuma integração externa de cálculo** — nem Jaéconsig (CLT), nem qualquer consulta automatizada de dados (ex.: Consignado Rápido para INSS). Simulações avaliam exclusivamente Ofertas do motor interno (Ofertas + Regras, Admin).

> **Nota de evolução.** CLT via Jaéconsig, e qualquer outra integração externa de simulação, ficam para quando o produto tiver um módulo de integrações — não modelado nesta rodada.

---

# 4. Requisitos funcionais

## RF-SIM-01 — Simulação manual (Página de Simulação)

**Como** operador, **eu quero** simular condições de crédito para qualquer cliente — cadastrado ou não — **para** apresentar as melhores opções disponíveis sem precisar que ele já exista na base.

**Comportamento esperado:** destino de topo próprio (**Página de Simulação**). O operador preenche, em sequência: Dados do cliente (CPF, Nome completo, Data de nascimento, Telefone — RN-SIM-05); Dados da oferta (Produto, Operação, Promotora — RN-SIM-06); Dados do contrato atual e Dados preferenciais, cujos campos mudam conforme a Operação escolhida (RN-SIM-07). O motor avalia todas as Ofertas compatíveis (Região, RN-REG-04; Regras bloqueantes e de aviso), resultado comparável — aprovadas e rejeitadas, com motivo — ordenado por melhor condição.

## RF-SIM-02 — Simulação automática via Lead

**Como** sistema, **eu quero** disparar uma simulação assim que os dados do cliente forem obtidos por um fluxo da Huggy **para** adiantar o trabalho do operador.

**Comportamento esperado:** ao receber o evento (RN-SIM-04), o sistema roda a mesma simulação de RF-SIM-01, com os dados disponíveis na Lead naquele momento (inclusive o Segmento dela — RN-SIM-06), e grava como snapshot de origem automática.

## RF-SIM-04 — Histórico de simulações

Herdado de RF021 da v1.2, sem mudança: consulta cronológica, filtros por Operação, banco e período, snapshot completo por item, opção de refazer com parâmetros pré-preenchidos.

**RN-SIM-09 — Refazer sempre gera um novo item no histórico, nunca sobrescreve.** Confirmado — mantém a imutabilidade de snapshot (§3.1). "Refazer" pré-preenche o formulário da Página de Simulação com os parâmetros da simulação anterior; o operador ajusta o que quiser e confirma, gerando um registro novo, independente do original.

## RF-SIM-05 — Sheet de detalhes da oferta

**Como** operador, **eu quero** ver o detalhe completo de uma Oferta aprovada **para** entender exatamente as condições antes de avançar com o cliente.

**Comportamento esperado:** a partir de qualquer card de Oferta aprovada (resultado da simulação, ou ficha do Cliente), abre um Sheet com o detalhe completo daquela linha do snapshot — todos os campos calculados, não só o resumo do card (RN-SIM-10).

---

# 5. Regras de negócio (consolidado)

- **RN-SIM-01** — Três origens de disparo (manual, automática via Lead e Marketing), mesmo motor de cálculo; o cálculo em lote é do Marketing.
- **RN-SIM-03** — Simulação não consome Créditos.
- **RN-SIM-04** — Gatilho automático via Lead é evento externo da Huggy, fora do controle do Corban, sempre posterior à criação do Cliente.
- **RN-SIM-05** — Cliente não precisa estar cadastrado; revoga RN035 da v1.2 ("sem simulação avulsa").
- **RN-SIM-06** — Dados da oferta são Segmento + Produto + Operação + Promotora, não uma Oferta específica; Segmento sempre filtra, com ou sem Lead.
- **RN-SIM-06a** — Operação é campo próprio, livre entre as 4 do catálogo; independe do Produto.
- **RN-SIM-07** — Dados do contrato atual e preferenciais são condicionais pela Operação (4 variantes).
- **RN-SIM-08** — Só o motor interno está ativo; nenhuma API externa (Jaéconsig, Consignado Rápido) nesta versão.
- **RN-SIM-09** — Refazer sempre gera novo item no histórico.
- **RN-SIM-10** — Ofertas aprovadas exibem-se como card padrão, reaproveitado em três superfícies (simulação, ficha do Cliente, Sheet de detalhes).
- **RN-SIM-11** — O motivo de uma Oferta que não passa mostra os requisitos que falharam, com o valor da Oferta; vale também para alerta de Regra de aviso.

---

# 6. Estados e transições

Sem estados de publicação — Simulação é snapshot imutável, existe ou não existe, como Regra e Origem de Venda.

---

# 7. Limites do módulo

**Não** inclui: a elegibilidade em si (Ofertas, Regras — Admin), a importação de Clientes (módulo próprio), a configuração dos fluxos da Huggy que disparam a simulação automática via Lead (RN-SIM-04 — vive na Huggy, fora do Corban), nem qualquer integração externa de cálculo (Jaéconsig, Consignado Rápido — RN-SIM-08, fora de escopo nesta versão).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Ofertas, Produtos, Regras, Regiões, Segmentos, Lojas** (Admin) — toda a lógica de elegibilidade e parametrização, e a Região e os Segmentos da Loja do operador.
- **Leads** (CRM) — a Lead de origem, quando aplicável.
- **Clientes** (CRM) — associação automática por CPF (RN-SIM-05), quando o cliente já está cadastrado.
- **Huggy** — o evento que dispara a simulação automática via Lead.

**Expõe para:**
- **Propostas** (CRM) — a simulação de referência, cujo valor pré-preenche o fechamento, e as Ofertas aprovadas, entre as quais o operador escolhe a da Proposta (RN-PRP-07).
- **Clientes** — o histórico de simulações, exibido na ficha.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Simulações v0.14 — módulo da aplicação CRM.*
