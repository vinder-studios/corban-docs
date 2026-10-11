# Corban CRM · Metas

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.8
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.8 | **Levantamento do CRM, decisões do usuário.** (1) **A atribuição é a gravada no Pago** (Operador, Time e Cargo naquele momento): **sai o filtro dinâmico da v0.7**, e promoção, inativação ou troca de Time **não retiram** vendas já fechadas, nem do Time nem do Membro, para não penalizar o Time perto do fim do Prazo nem afetar comissões (RN-MET-03, RN-MET-11). (2) A Meta de Valor soma o **Valor de meta**; a de Quantidade, 1 por Proposta (a spec nunca dizia). (3) **A saída de um Membro não aumenta as Metas dos demais** (RN-MET-08b). (4) Inativar um Membro encerra as Metas individuais dele (RN-MET-10); **nova RN-MET-12:** excluir um Time encerra as Metas dele. (5) **Novo estado Futura** (Início no futuro) e o segundo caminho para Encerrada (§6). Remove os "a redigir" e reordena o consolidado e este histórico. |
| 0.7 | **"Cargos elegíveis" vira campo independente de "Distribuir entre Membros".** Antes só existia como parâmetro do rateio; agora toda Meta de Time pode ter um filtro de Cargo, que passa a restringir **quem conta** para a Produção/Quantidade da própria Meta de Time — não só quem recebe Meta individual. Motivação do usuário: evitar que alguém promovido (ex.: vendedor virando gerente) continue contando para uma meta de vendas da qual não participa mais. Quando Distribuir é ligado, reaproveita o mesmo filtro — não pede Cargo duas vezes. Filtro é dinâmico (Cargo atual, não o de quando a Proposta foi paga), mesmo princípio já usado em RN-MET-08 — decisão registrada, não pedida explicitamente. RN-MET-02, RN-MET-03 e RN-MET-06 revisadas; RN-MET-11 nova. |
| 0.6 | Confirma RN-MET-08(c) — quem entra depois da distribuição ganha Meta individual nova, mesma simetria de quem sai. Deixa de ser inferência, é regra fechada. Sem mais pendências. |
| 0.5 | Fecha os dois itens abertos da v0.4: **(1)** mudança na composição do Time (saída, entrada, troca de Cargo) também recalcula — o valor faltante de quem sai é redistribuído entre os demais, mesma fórmula de RN-MET-07 com a contagem atual; por simetria (inferência, não confirmada), quem passa a ser elegível depois ganha Meta individual nova nesse momento. **(2)** Meta individual de quem deixa de ser elegível é **encerrada**, nunca excluída — permanece vinculada à Meta de origem e ao Membro. RN-MET-08 ampliada; RN-MET-10 nova. |
| 0.4 | **Nova capacidade: distribuição automática da Meta de Time entre os Membros.** Opcional, escolhida na criação da Meta de Time — gera uma Meta individual por Membro elegível (filtrável por Cargo, ex.: excluir gerentes do rateio de meta de vendas), cada uma com `valor = arredondar_para_cima(valor_global / nº_membros_elegíveis)` — mesmo valor pra todos, soma pode superar o global, nunca fica abaixo. Editar o valor-alvo da Meta de Time **recalcula** as individuais geradas. Nelas, além do Tipo, também ficam travados **Data de Início** e **Valor-alvo** — este último porque é sempre derivado, nunca editável manualmente. RN-MET-06 a 09 novas. |
| 0.3 | Três confirmações do usuário: **(1) "Prazo" implica Início e Fim** — Início volta a ser campo explícito, não implícito (correção do que eu tinha assumido na v0.2); **(2)** uma Proposta Paga conta para todas as Metas Vigentes compatíveis simultaneamente — confirmado; **(3)** Tipo é travado após a criação — confirmado, inclusive porque define se o valor-alvo é numérico (Quantidade) ou monetário (Valor). Fecha os dois itens abertos da v0.2. |
| 0.2 | **Correção de fundo: Tipo deixa de ser slot fixo por destino.** Antes, cada Time/Membro tinha no máximo uma Meta de Valor e uma de Quantidade — vigentes ao mesmo tempo. Agora Meta é uma entidade de verdade, com Título e Descrição próprios; um destino pode ter **várias Metas simultâneas**, inclusive do mesmo Tipo (ex.: meta do mês e meta do trimestre, ambas Vigentes). Campo "Início da contabilização" sai como campo explícito — passa a ser implícito, a data de criação da Meta. RN-MET-01, 02, 03 e 04 revisadas. |
| 0.1 | Versão inicial. Substitui o Módulo 3 (parte de metas) da Especificação v1.2 — níveis mudam de Operação/Time/Operador para **Time/Membro** (Operação não existe mais como unidade organizacional); configuração passa da mão do tenant para a própria **Loja**. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-MET) e regras de negócio (RN-MET) próprios.

> **Sem destino de topo próprio.** Meta não tem tela de listagem centralizada — vive anexada à tela de **Time** e à tela de **Membro**, cada um vendo a própria meta e o próprio histórico. A visão comparativa entre vários Times/Membros de uma vez é responsabilidade do módulo **Rankings**, que consome os dados daqui.

---

# 1. Objetivo do módulo

Permitir que a Loja configure metas de Valor e de Quantidade para seus Times e Membros, com contabilização baseada em Propostas Pagas, preservando histórico por período.

---

# 2. Atores

- **Gestor de Loja** — configura metas para Times e Membros da própria Loja.
- **Membro** — consulta a própria meta e o próprio desempenho.

---

# 3. Entidades e conceitos

## 3.1. Meta

Uma Meta pertence a **um** destino — Time **ou** Membro, nunca os dois ao mesmo tempo — e a **um** Tipo.

| Campo | Obrigatório | Notas |
|---|---|---|
| Destino | Sim | Time ou Membro (RN-MET-01). Implícito pelo contexto — a tela de onde a Meta é criada. |
| Título | Sim | Identifica a Meta (ex.: "Meta de março", "Campanha de portabilidade"). |
| Descrição | Não | Texto livre, contexto adicional. |
| Tipo | Sim, travado após a criação | **Valor** (R$) ou **Quantidade** (nº de Propostas Pagas) — RN-MET-02. Classifica a Meta e define a natureza do Valor-alvo (monetário ou numérico) — RN-MET-05. Não limita quantas Metas de um Tipo um destino pode ter ao mesmo tempo. |
| Valor-alvo | Sim | O número a bater — em R$ para Tipo Valor, em contagem para Tipo Quantidade. **Travado quando a Meta é individual distribuída** (RN-MET-07) — sempre derivado, nunca editável manualmente. |
| **Prazo** (Início + Fim) | Sim | Duas datas/horas — início e fim do período em que Propostas contam para esta Meta (RN-MET-03, RN-MET-04). Não é contador aberto. **Início travado quando a Meta é individual distribuída** (RN-MET-07), além do Tipo — mantém sincronia temporal com a Meta de Time de origem. |
| Distribuir entre Membros | Sim, só em Metas de **Time** | `Boolean` — RN-MET-06. Decidido na criação, não pode ser ativado depois (RN-MET-06). |
| Cargos elegíveis | Não, só em Metas de **Time** | **Independente de "Distribuir"** (RN-MET-11) — filtra quais Cargos dos Membros do Time contam para o Valor de meta/Quantidade desta Meta de Time (pelo Cargo no momento do Pago, RN-MET-03). Sem preenchimento, todo o Time conta (padrão retrocompatível). Quando "Distribuir" está ligado, o rateio usa este mesmo filtro — não há um segundo campo de Cargo para o rateio. |
| Meta de origem | Calculado | Só em Metas **individuais geradas por distribuição** — referência à Meta de Time que a gerou (RN-MET-06). Ausente em Metas criadas normalmente. |

**RN-MET-01 — Meta é por Time ou por Membro, e os dois níveis são independentes.** Não há soma nem dependência entre o nível Time e o nível Membro — mesmo espírito de RN025 da v1.2 ("níveis independentes"), agora só com dois níveis em vez de três.

**RN-MET-11 — "Cargos elegíveis" é campo próprio da Meta de Time, independente de "Distribuir entre Membros".** *(Revisão: antes só existia como parâmetro do rateio — RN-MET-06.)* Qualquer Meta de Time pode ter um filtro de Cargos, usado para restringir **quem conta** (pelo Cargo que o Operador tinha no momento do Pago, RN-MET-03) para o Valor de meta/Quantidade da própria Meta — útil mesmo sem nunca gerar Metas individuais (ex.: medir só a Produção dos Vendedores do Time, sem distribuir nada). Quando "Distribuir" é ligado numa Meta que já tem Cargos elegíveis definidos, o rateio usa o mesmo filtro; se "Distribuir" é ligado sem filtro definido, todo o Time participa do rateio.

**RN-MET-02 — Um destino pode ter várias Metas simultâneas, inclusive do mesmo Tipo.** *(Revisão: a versão anterior tratava Valor e Quantidade como no máximo uma Meta vigente cada, por destino — dois "slots" fixos. Não é assim: Meta é uma entidade com identidade própria (Título, Descrição), e nada limita quantas existem ao mesmo tempo para o mesmo destino.)* Um Time (ou Membro) pode ter, por exemplo, uma Meta de Valor do mês e outra de Valor do trimestre, ambas Vigentes — Tipo classifica, não restringe quantidade.

**RN-MET-03 — Contabilização começa em Pago, e vale a atribuição gravada no Pago.** Uma Proposta só conta para uma Meta no momento em que chega a um Status de tipo Pago. Para a Meta de Tipo **Valor**, conta o **Valor de meta** (valor base × multiplicador, RN-PRP-05); para a de Tipo **Quantidade**, conta **1** por Proposta. **Conta para toda Meta Vigente do destino e Tipo compatíveis com a Proposta, simultaneamente** — se há duas Metas de Valor Vigentes para o mesmo Time, a mesma Proposta soma nas duas. O campo Início define **a partir de quando no tempo** a Meta passa a contar. **A atribuição é a gravada no Pago** (Propostas, RN-PRP-05): o Operador, o **Time** e o **Cargo** dele naquele momento (os Atores não contam, RN-PRP-16). Em Meta de Time com Cargos elegíveis (RN-MET-11), só conta a Proposta cujo Operador tinha, **no momento do Pago**, um Cargo elegível. **Promoção, inativação ou troca de Time depois não retiram vendas já fechadas**, nem do Time nem do Membro: a Meta continua registrada no cadastro do usuário. *(Decisão do usuário, levantamento do CRM: reverte o filtro dinâmico da v0.7, para não penalizar o Time perto do fim do Prazo nem afetar o pagamento de comissões.)*

**RN-MET-05 — Tipo define a natureza do Valor-alvo, e por isso trava após a criação.** Tipo Valor exige valor-alvo monetário (R$); Tipo Quantidade exige valor-alvo numérico (contagem de Propostas Pagas). Mudar o Tipo depois de criada reinterpretaria retroativamente o que o valor-alvo (e o que já foi contabilizado) significam — por isso o campo é travado assim que a Meta é criada.

> **Nota de evolução.** Será avaliada a possibilidade de configurar a meta para contabilizar a partir de um Status ou Stage diferente de Pago — por exemplo, contar atividade (entrada em Negociação) separadamente de conversão. Não implementado nesta versão; RN-PRP-05 (Proposta) e RN-MET-03 permanecem como a única fonte de contagem.

**RN-MET-06 — Distribuição automática entre Membros é opcional, decidida na criação da Meta de Time, e gera Metas individuais vinculadas.** Ao criar uma Meta de **Time** com "Distribuir entre Membros" ligado, o rateio usa os **Cargos elegíveis** já definidos para a Meta (RN-MET-11) — campo único, não pedido de novo aqui; sem Cargos elegíveis definidos, todo o Time participa. O sistema gera, na hora, uma Meta de Membro para cada Membro do Time elegível — mesmo Título da Meta de Time (não "Título — Nome", confirmado), mesmo Tipo, mesmo Início e Fim, e Valor-alvo calculado por RN-MET-07. Cada uma guarda referência à Meta de Time de origem. **A opção não pode ser ativada depois, numa Meta já criada sem ela** — é decisão do momento da criação; os Cargos elegíveis, porém, podem ser editados depois (RN-MET-11), recalculando tudo (RN-MET-08).

**RN-MET-07 — Valor individual é o valor global arredondado para cima, igual para todos, sem sobra distribuída.** `valor individual = arredondar_para_cima(valor-alvo da Meta de Time ÷ nº de Membros elegíveis)` — o mesmo valor para cada Membro elegível. Como consequência do arredondamento uniforme, **a soma dos valores individuais pode superar o valor-alvo da Meta de Time** — nunca fica abaixo. Diferente do padrão "resto vai pro último", aqui não há resto: todos recebem exatamente o mesmo número.

**RN-MET-08 — Editar o valor-alvo da Meta de Time, ou mudar quem está elegível, recalcula as Metas individuais vinculadas.** Dispara o recálculo (fórmula de RN-MET-07, com a contagem de elegíveis **no momento**) em duas situações — (a) e (c) —, e a (b) só encerra a Meta de quem saiu: (a) o gestor altera o valor-alvo da Meta de Time; (b) um Membro com Meta individual Vigente deixa de ser elegível (sai do Time, ou muda para um Cargo fora do filtro) — as Metas dos demais **não aumentam** (decisão do usuário: a saída de um Membro não penaliza quem fica; a soma das individuais pode ficar abaixo do global); (c) um Membro passa a ser elegível depois da distribuição já feita (entra no Time com um Cargo do filtro, ou muda para um) — ganha uma Meta individual nova nesse momento, e o recálculo reduz a parte dos demais — confirmado, mesma simetria do caso (b). Em nenhum caso o gestor precisa mexer manualmente em cada Meta individual.

**RN-MET-10 — Meta individual de Membro que deixa de ser elegível é encerrada, nunca excluída.** Quando um Membro deixa de ser elegível (RN-MET-08b) — por sair do Time, mudar para um Cargo fora do filtro **ou ser inativado** —, a Meta individual dele muda para **Encerrada** imediatamente — mesmo sem ter chegado ao Fim — e permanece **vinculada à Meta de Time de origem e ao Membro**, consultável no histórico dele. Segunda forma de chegar a Encerrada, além da passagem do Fim (RN-MET-04).

**RN-MET-09 — Meta individual distribuída trava Tipo, Início e Valor-alvo; o resto continua editável.** Além do travamento de Tipo já existente (RN-MET-05), uma Meta gerada por distribuição também trava **Início** (sincronia com a Meta de Time de origem) e **Valor-alvo** (sempre derivado, nunca editável manualmente — RN-MET-07/08). Título, Descrição e Fim continuam editáveis normalmente.

**RN-MET-04 — Meta tem Prazo definido (Início e Fim), e gera histórico.** Diferente de um contador contínuo, toda Meta conta só dentro da janela Início–Fim — ao encerrar (passado o Fim), o registro daquele período fica preservado (Título, Descrição, Tipo, valor-alvo, o que foi de fato atingido, Início, Fim), e uma nova Meta é criada quando fizer sentido (não é renovação automática — cada Meta é um registro independente). Consultar o desempenho de um Time/Membro é, portanto, consultar a lista de Metas daquele destino — vigentes e encerradas.

**RN-MET-12 — Excluir um Time encerra as Metas dele, que ficam no histórico.** O Time excluído é soft-delete (Lojas, RN-LOJ-05): suas Metas passam a **Encerradas** e continuam consultáveis, com a marca "(excluído)"; as vendas atribuídas ao Time no Pago continuam apontando para ele. *(Decisão do usuário, levantamento do CRM.)*

---

# 4. Requisitos funcionais

## RF-MET-01 — Configuração de meta

**Como** gestor de Loja, **eu quero** definir metas de Valor e/ou Quantidade para um Time ou Membro **para** acompanhar o desempenho contra um objetivo claro.

**Comportamento esperado:** a partir da tela do Time ou do Membro, o gestor cria uma nova Meta — Título, Descrição (opcional), Tipo, valor-alvo, Início e Fim. Numa Meta de Time, pode definir **Cargos elegíveis** (RN-MET-11, independente de distribuir) e, opcionalmente, ligar "Distribuir entre Membros" (RN-MET-06), que reaproveita o mesmo filtro — a criação já gera as Metas individuais vinculadas, se ligado. Nenhuma tela própria de listagem geral (módulo §1) — a lista vive anexada ao destino.

## RF-MET-02 — Acompanhamento

**Como** gestor de Loja ou Membro, **eu quero** ver o progresso da meta vigente e o histórico de períodos anteriores **para** entender a evolução do desempenho.

**Comportamento esperado:** na tela do Time/Membro, lista de Metas daquele destino — Vigentes com indicador de progresso (atingido vs. alvo), Encerradas como histórico (RN-MET-04), com filtro por Tipo e por status.

---

# 5. Regras de negócio (consolidado)

- **RN-MET-01** — Meta é por Time ou por Membro; níveis independentes.
- **RN-MET-02** — Um destino pode ter várias Metas simultâneas, inclusive do mesmo Tipo — Tipo classifica, não restringe.
- **RN-MET-03** — Contabilização só a partir do Pago, pela atribuição gravada no Pago (Operador, Time, Cargo); promoção, inativação ou troca de Time não retiram vendas fechadas; conta para toda Meta Vigente compatível.
- **RN-MET-04** — Meta tem Prazo definido (Início e Fim); gera histórico ao encerrar.
- **RN-MET-05** — Tipo define a natureza do Valor-alvo (monetário/numérico) e trava após a criação — confirmado.
- **RN-MET-06** — Distribuição entre Membros é opcional, só na criação de Meta de Time, reaproveita o filtro de Cargos elegíveis.
- **RN-MET-07** — Valor individual: valor global arredondado pra cima, igual pra todos — soma pode superar o global, nunca fica abaixo.
- **RN-MET-08** — Editar o valor-alvo da Meta de Time, ou a entrada de um Membro elegível, recalcula as individuais; a saída de um Membro não aumenta as dos demais.
- **RN-MET-09** — Meta individual distribuída trava Tipo, Início e Valor-alvo; Título/Descrição/Fim seguem editáveis.
- **RN-MET-10** — Meta individual de Membro que deixa de ser elegível ou é inativado é encerrada (nunca excluída), permanece vinculada.
- **RN-MET-11** — Cargos elegíveis é campo independente de Distribuir; filtra quem conta para a Produção/Quantidade da própria Meta de Time.
- **RN-MET-12** — Excluir um Time encerra as Metas dele, que ficam no histórico.

---

# 6. Estados e transições

**Meta:**
- **Futura** — o Início ainda não chegou; não conta Propostas ainda e é editável (menos o Tipo).
- **Vigente** — dentro do intervalo Início–Fim.
- **Encerrada** — passou do Fim; ou o Membro deixou de ser elegível ou foi inativado (RN-MET-10); ou o Time foi excluído (RN-MET-12). Vira histórico, não editável, e **continua no cadastro**.

Transições: futura → vigente (automática, pela data do Início); vigente → encerrada (pela data do Fim, ou por RN-MET-10 e RN-MET-12); não há reabertura.

---

# 7. Limites do módulo

**Não** inclui: a visão comparativa entre Times/Membros (módulo Rankings), o cálculo do valor de cada Proposta Paga (módulo de Propostas, RN-PRP-05), nem tela de listagem própria (módulo §1).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Propostas** (CRM) — o evento de chegar a Pago, o Valor de meta e a atribuição (Operador, Time, Cargo) gravados no Pago (RN-PRP-05).
- **Times, Membros** (CRM) — os destinos possíveis de uma Meta.

**Expõe para:**
- **Rankings** (CRM) — dados de progresso de Meta, para comparação entre destinos.
- **Dashboard, Relatórios** (CRM) — indicadores de atingimento.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Metas v0.8 — módulo da aplicação CRM.*
