# Corban CRM · Chat

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.8
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.8 | **Exceção à não persistência:** o SmartCorbans guarda, por conversa, só o **trecho da última mensagem** e os horários (última mensagem, última do cliente, aguardando resposta), para a lista do Chat, o Card e a janela de 24h. O conteúdo completo continua só na Huggy. Decisão do usuário, por usabilidade. |
| 0.7 | Corrige RN-CHT-03: acesso do operador responsável deixa de ser tratado como bypass do catálogo de permissões — passa a ser escopo `self` normal ("Conversas de Leads atribuídas a mim"), mesma definição usada em todo o produto. Simplifica a autorização: uma query de escopo padrão resolve tudo, sem caminho especial. |
| 0.6 | Restringe Dados do contato (RN-CHT-07) à tela dedicada de Chat — o painel aberto pelo card da Lead **não** oferece essa opção. |
| 0.5 | Confirma os três itens abertos: fixar conversa é individual; janela de 24h só reabre com mensagem do cliente; templates não vivem no Corban. Nenhuma suposição — comportamento confirmado. |
| 0.4 | Adiciona: painel de conversa e tela dedicada de Chat são **componentes distintos**, não o mesmo reaproveitado (tela dedicada é layout estilo WhatsApp — lista + painel); sheet de **Dados do contato**; recursos de mensagem (anexos, áudio, emoji, fixar conversa); **janela de atendimento de 24h** (RN-CHT-07), com template de mensagem como saída após expirar. |
| 0.3 | Fecha os dois itens abertos: (1) **mensagens são espelhadas, não persistidas** — janela ao vivo sincronizada via API, não iframe; sem entidade Mensagem no banco do Corban. (2) Conversa concluída **continua consultável, somente leitura** (RN-CHT-05). Adiciona os **dois pontos de acesso** (botão no card da Lead; tela própria de Chat com filtros) e o RF correspondente. |
| 0.2 | Corrige uma suposição errada em RN-CHT-02 e no item aberto §9.2: **Lead não "vira" Cliente ao concluir** — ela **possui** um Cliente, do mesmo jeito que possui Propostas, ao longo de toda a sua vida, não só no desfecho. O ciclo de vida da Lead termina ao chegar num Stage de conclusão, ponto — sem relação com o Cliente virar ou deixar de existir. |
| 0.1 | Versão inicial. **Sai do modo demonstração** previsto no Módulo 8 da Especificação v1.2 (RN055) — Chat entra como funcionalidade plena, mesma mudança já aplicada a Propostas. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-CHT) e regras de negócio (RN-CHT) próprios.

> **Confirmado: janela ao vivo, sem persistência local.** As mensagens são espelhadas via API, sincronizadas em tempo real com a Huggy — não há entidade Mensagem gravada no banco do Corban, e não é um iframe embutido (a interface é construída pelo Corban, só o conteúdo vem ao vivo da API). Consequência direta: não existe busca por conteúdo de mensagem antiga dentro do Corban — o que é pesquisável é a **Conversa como um todo** (metadados que o Corban já possui: Lead, Cliente, Time, Operador, Status da Proposta, Tags), não o texto trocado.

---

# 1. Objetivo do módulo

Apresentar, no contexto da Lead, a conversa do canal digital de origem (Huggy) — permitindo atender o cliente sem trocar de ferramenta, com notificação em tempo real de mensagens novas.

---

# 2. Atores

- **Operador responsável** — vê e responde a conversa da própria Lead.
- **Gestor** (Time/Loja/tenant) — vê conversas dentro do seu escopo de permissão, mesmo sem ser o responsável direto.
- **Huggy** — origem das mensagens e do evento de criação automática de Lead.

---

# 3. Entidades e conceitos

## 3.1. Conversa

Uma **Conversa** vive associada a uma Lead, 1:1 — mas **não é uma entidade com histórico próprio no Corban**. É uma janela ao vivo: o conteúdo (mensagens, direção, autor, data/hora de cada uma) é buscado da API da Huggy no momento em que a tela é aberta, e atualizado em tempo real enquanto permanece aberta. O que o Corban de fato possui e persiste é só a **associação** Conversa↔Lead — não o conteúdo trocado. **Exceção:** para a lista do Chat, o Card e a janela de 24h (RN-CHT-10), guarda-se também um resumo da conversa, com o **trecho da última mensagem** e os horários da última mensagem e da última do cliente; o conteúdo completo nunca. *(Decisão do usuário, v0.8.)*

**Degradação graciosa.** Se a API da Huggy estiver indisponível, o painel de conversa mostra estado de erro — sem histórico local para exibir como alternativa (RNF004 da v1.2, sem caminho manual possível aqui, diferente de outras integrações do produto).

**RN-CHT-01 — Mensagem recebida sem Lead existente cria a Lead automaticamente.** Mesmo mecanismo já definido no módulo de Leads (RF-LED-03) para entrada tenant-wide via Huggy — não é regra nova, é a Chat confirmando que também dispara esse caminho. A Lead criada entra imediatamente na distribuição entre Lojas e depois operadores (RN-LED-02 a RN-LED-05).

**RN-CHT-02 — A Conversa acompanha a Lead do Stage Novo até um Stage de tipo Pago ou Cancelado.** Existe durante toda a vida ativa da Lead, sem interrupção entre Stages — o ciclo de vida da Lead termina ao chegar num Stage de conclusão, independentemente de ela possuir ou não um Cliente associado (Lead **possui** um Cliente, como possui Propostas — não "vira" um).

**RN-CHT-05 — Conversa concluída permanece consultável, somente leitura.** Depois que a Lead chega num Stage de conclusão (Pago ou Cancelado), a Conversa continua acessível — o histórico ao vivo da Huggy ainda pode ser consultado —, mas passa a **somente leitura**: não é mais possível enviar mensagem nova a partir dali. Consultável também pela ficha do Cliente que a Lead já possuía, quando o módulo de Clientes definir esse acesso.

## 3.2. Acesso

**RN-CHT-03 — Acesso é inteiramente por escopo de permissão — `self`, `team`, `store` ou `all`.** *(Revisão: a versão anterior tratava o operador responsável como caso especial fora do catálogo; agora é escopo `self` normal, mesma definição usada em todo o produto — "Conversas de Leads atribuídas a mim".)*

| Escopo | Alcance |
|---|---|
| `self` | Só as Conversas das Leads atribuídas ao próprio usuário. |
| `team` | + Conversas de todo o Time. |
| `store` | + Conversas de todos os Times da Loja. |
| `all` | + Conversas de todas as Lojas do tenant. |

Isso não torna a Conversa "de todo mundo do Time" por padrão — só quem tem a permissão no escopo certo. Um Cargo mal configurado, sem nenhum escopo de `chats:view`, deixa até o próprio operador sem acesso às conversas das próprias Leads — mesmo risco de má configuração já aceito no resto do catálogo do produto.

## 3.3. Notificação

**RN-CHT-04 — Mensagem nova gera notificação em tempo real — exceção deliberada ao princípio geral do produto.** O UX v0.1 estabelece "notificar exceção, não rotina" como regra geral do sino de notificações (evitar fadiga de alerta em fluxo de alto volume). Mensagem de chat é a exceção assumida: é comunicação direta do cliente, não evento de rotina do pipeline, e precisa de resposta em tempo hábil.

---

# 4. Requisitos funcionais

## RF-CHT-01 — Conversa no contexto da Lead

**Como** operador, **eu quero** ver e responder a conversa do cliente sem sair da Lead **para** negociar com todo o contexto à mão.

**Comportamento esperado:** a partir do card da Lead, no board (RF-CHT-03), o operador abre o painel de conversa — histórico ao vivo + campo de resposta (quando a Conversa não está em modo somente leitura, RN-CHT-05). Substitui integralmente a versão demonstrativa do Módulo 8 da v1.2 (RN055, RN056) — sem selo de demonstração, com efeito real (envio de mensagem chega ao cliente via Huggy).

## RF-CHT-03 — Acesso pelo card da Lead

**Como** operador, **eu quero** abrir a conversa direto do card da Lead no board **para** não precisar navegar até uma tela separada.

**Comportamento esperado:** botão no próprio card, no board kanban (módulo de Leads) — abre o painel de conversa daquela Lead especificamente.

## RF-CHT-04 — Tela própria de Chat, com filtros

**Como** operador ou gestor, **eu quero** uma tela dedicada listando as Conversas ao meu alcance, filtráveis **para** localizar uma conversa específica sem precisar navegar pelo board.

**Comportamento esperado:** destino de topo próprio, com **layout estilo WhatsApp** — lista de Conversas (filtrável) à esquerda, painel principal da conversa selecionada ocupando o restante da tela. **Componente distinto** do painel aberto via card da Lead (RF-CHT-01/RF-CHT-03) — não é o mesmo reaproveitado, ainda que ambos exibam a mesma conversa ao vivo. Filtros disponíveis na lista à esquerda:

| Filtro | Notas |
|---|---|
| Nome do cliente | Busca textual. |
| Time | **Só aparece** para quem tem acesso a conversas em escopo `store` ou superior — filtrar por Time não faz sentido para quem só enxerga o próprio Time. |
| Operador | **Só aparece** para quem tem acesso em escopo `team` ou superior. |
| Tipo de status da proposta | Filtra pelo StatusType (Novo/Negociação/Pago/Cancelado — módulo de Status de Proposta), não pelo status específico. |
| Tags | Um filtro **por categoria** de Tag (Audiência, Origem, Departamento — módulo de Funis de Vendas) — três filtros independentes, não um só. |
| Período | Recorte de tempo — sobre a Lead/Conversa (criação ou última atividade), não sobre mensagens individuais, já que estas não são persistidas (RN-CHT-01, §3.1). |

**RN-CHT-06 — Filtros de Time e Operador são condicionados ao escopo do usuário, não apenas ocultos por permissão binária.** Diferente do padrão comum de "sem permissão, oculto", aqui a presença do filtro depende de **qual escopo** o usuário tem na permissão de leitura de conversas — um usuário com escopo `team` nunca vê o filtro de Time (não teria uso), mesmo tendo a permissão de ver conversas.

## 3.3. Dados do contato

**RN-CHT-07 — Dados do contato existe só na tela dedicada de Chat, não no painel via card da Lead.** Exibe um recorte do Cliente associado à Lead (módulo de Clientes) — Nome, CPF, Telefone. Somente leitura; não redefine nem edita o Cliente, só exibe. O painel de conversa aberto a partir do card da Lead (RF-CHT-03) **não oferece essa opção** — quem precisa ver os dados do contato usa a tela dedicada (RF-CHT-04).

## 3.4. Recursos de mensagem

**RN-CHT-08 — Anexos, áudio e emoji seguem o mesmo princípio de espelhamento (§3.1).** Documentos (enviar/baixar), áudios (enviar/baixar/ouvir) e emojis (enviar/visualizar) fazem parte do conteúdo ao vivo da Conversa — nenhum é armazenado pelo Corban, mesmo tratamento das mensagens de texto.

**RN-CHT-09 — Fixar conversa é individual.** O usuário pode fixar uma Conversa, destacando-a no topo da lista (tela dedicada de Chat) — a fixação é própria de cada operador/gestor, não compartilhada com o resto da equipe. Confirmado.

## 3.5. Janela de atendimento de 24 horas

**RN-CHT-10 — Sem mensagem de texto livre após 24h da última mensagem do cliente.** Contadas 24 horas desde a **última mensagem recebida do cliente**, o operador deixa de poder enviar mensagem de texto livre — só um **Template de mensagem** (pré-aprovado) reabre a possibilidade de contato. Só uma nova mensagem **do cliente** reabre a janela — mensagem do operador (incluindo um template enviado) não reabre. Confirmado: segue o comportamento padrão do WhatsApp Business, sem exceção.

**Templates de mensagem não vivem no Corban — confirmado.** Mesmo princípio já usado para Fluxos e Tags (módulo de Funis de Vendas, RN-FNL-04): os templates são criados e aprovados na Huggy (ou diretamente na plataforma do WhatsApp Business, dependendo de como o tenant configura); o Corban só oferece a ação de "enviar template", consumindo o que já existe lá — sem tela própria de gestão de templates neste módulo.

## RF-CHT-02 — Notificação em tempo real

**Como** operador ou gestor com acesso à Conversa, **eu quero** ser avisado assim que uma mensagem nova chegar **para** responder rapidamente.

**Comportamento esperado:** notificação em tempo real (RN-CHT-04), respeitando o mesmo recorte de acesso de RN-CHT-03 — só quem tem acesso à Conversa é notificado dela.

---

# 5. Regras de negócio (consolidado)

- **RN-CHT-01** — Mensagem sem Lead cria Lead automaticamente (mesmo caminho de RF-LED-03).
- **RN-CHT-02** — Conversa acompanha a Lead do Stage Novo até um Stage terminal.
- **RN-CHT-03** — Acesso por responsabilidade direta ou por escopo de permissão (`team`/`store`/`all`); nunca automaticamente coletivo.
- **RN-CHT-04** — Mensagem nova notifica em tempo real — exceção deliberada ao princípio geral de notificação do produto.
- **RN-CHT-05** — Conversa concluída permanece consultável, somente leitura.
- **RN-CHT-06** — Filtros de Time e Operador, na tela de Chat, condicionados ao escopo do usuário.
- **RN-CHT-07** — Sheet de Dados do contato, somente leitura, dentro do painel de conversa.
- **RN-CHT-08** — Anexos, áudio e emoji seguem o mesmo princípio de espelhamento da Conversa.
- **RN-CHT-09** — Fixar conversa, destaque no topo da lista.
- **RN-CHT-10** — Janela de 24h: sem texto livre após a última mensagem do cliente; só Template reabre.

---

# 6. Estados e transições

**Conversa:**
- **Ativa** — Lead ainda não concluída; permite enviar mensagem.
- **Somente leitura** — Lead concluída (Pago/Cancelado); histórico ao vivo continua acessível, sem campo de resposta (RN-CHT-05).

Sem entidade Mensagem — nada além da Conversa (e sua associação com a Lead) é persistido pelo Corban (§3.1).

---

# 7. Limites do módulo

**Não** inclui: a distribuição de Lead em si (módulo de Leads — este só confirma que dispara o mesmo caminho), a infraestrutura de tempo real (WebSocket/push — decisão técnica, não de produto), nem a lógica de acesso à conversa depois que a Lead conclui (item aberto §9.2).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Huggy** — origem de toda mensagem, e do evento de criação automática de Lead.
- **Leads** (CRM) — a Lead à qual toda Conversa se associa; reaproveita a distribuição já definida lá.
- **Usuários** (Admin) — o operador responsável e o cálculo de escopo (`team`/`store`/`all`) para acesso de gestores.

**Expõe para:**
- Nada — é módulo terminal, consumido diretamente pelo operador/gestor.

---

# 9. Itens abertos

Nenhuma pendência de modelagem.

---

*Fim do documento. Chat v0.8 — módulo da aplicação CRM.*
