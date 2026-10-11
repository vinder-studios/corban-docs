# Corban CRM · Relacionamentos e Regras de Negócio

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.2
**Data:** 10/10/2026

## Histórico de revisões

| Versão | Mudanças |
| ------ | -------- |
| 0.2 | Pendências resolvidas (decisões do usuário): multiplicador gravado na associação da Oferta (Tabelas Multiplicadoras v0.6); ViaCEP incluído na spec §4.4 e no C4; as quatro relações inferidas confirmadas. |
| 0.1 | Versão inicial, montada a partir das seções "Entidades e conceitos" e "Depende de" dos 14 módulos do CRM. Relações sem regra explícita na spec estavam marcadas como inferidas. |

## Como usar este documento

Visão consolidada das relações entre as entidades do CRM e delas com o Admin e o Marketing. Cada relação remete ao módulo dono da regra. Não substitui os documentos de módulo; existe para dar a leitura rápida do grafo e servir de base ao modelo de dados. **Em caso de divergência, vale o módulo.**

Entidades do Admin que o CRM só consome (Funil, Stage, Oferta, Status de Proposta, Loja, Time, Usuário, Cargo, Segmento) estão no documento de relacionamentos do Admin; aqui aparecem só pelo lado do CRM.

---

# 🎯 Lead

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Lead → Funil** (Admin) | `N : 1` | Obrigatório. Vem do Segmento; a Lead nasce no Stage tipo Novo do Funil (RN-FNL-13, RN-FNL-15). |
| **Lead → Stage** (Admin) | `N : 1` | Obrigatório; sempre um Stage do próprio Funil. **Só muda por Ação** (RN-LED-11), nunca por arrastar. |
| **Lead → Loja** (Admin) | `N : 1` | Vazio só em trânsito, enquanto a distribuição entre Lojas resolve (RN-LED-01). Isolamento absoluto entre Lojas, mesmo no mesmo Funil (RN-LED-06). |
| **Lead → Operador** (Usuário) | `N : 1` | Vazio enquanto a Lead está no pool da Loja (RN-LED-05). |
| **Lead → Cliente** | `N : 1` (opcional) | A Lead nasce sem Cliente; o Cliente é criado ou associado quando ela reúne Nome + CPF + Telefone (RN-CLI-01). Mesmo CPF associa ao Cliente existente, nunca duplica (RN-CLI-02). Um Cliente pode ter várias Leads ao longo do tempo. |
| **Lead → Promotora** (Admin) | `N : 1` (opcional) | Pré-preenche a Promotora da Simulação (RN-SIM-06). |
| **Lead → Origem de Venda** (Admin) | `N : 1` (opcional) | Rótulo editável. Na Lead do Marketing nasce copiada da Lista ou Varredura (RN-LED-16). |
| **Lead → Lista ou Varredura** (Marketing) | `N : 1` (só Prospecção e Carteira Ativa) | Referência **imutável**, somente leitura (RN-LED-16). Vazia nas demais Origens. |
| **Lead → Resumo das Ofertas elegíveis** | `1 : N` (só Leads do Marketing) | Gravado na injeção, somente leitura, com os requisitos não avaliados (RN-LED-16). |
| **Lead → Conversa** | `1 : 1` | Ver Conversa. |
| **Lead → Simulação** | `1 : N` | Ver Simulação. |
| **Lead → Proposta** | `1 : 0..1` | No máximo uma Proposta por Lead (RN-PRP-01). |
| **Lead → Débito** (Créditos) | `1 : 0..1` | Só as Origens com linha de preço debitam, uma vez, ao atribuir à Loja (RN-LED-13, RN-CRD-01). Recontato e Manual não debitam. Um único débito por Lead. |
| **Lead** | — | **Origem da lead** definida pelo sistema, uma vez, imutável (RN-LED-12). Lead que reabre? Só por Ação Reabrir; Lead cancelada por Proposta não reabre (RN-FNL-19). Não é excluída fisicamente (exclusão suave, RN-SC-08). |

---

# 👤 Cliente

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Cliente → Loja** (Admin) | `N : N` (associação) | É o que controla quem vê o Cliente (RN-CLI-04). A associação nasce **só** quando uma Lead do Cliente é distribuída à Loja. Simulação ou Proposta com o CPF não associam (RN-CLI-04). Excluir ou inativar a Loja não afeta o Cliente (RN-CLI-10). |
| **Cliente → Lead** | `1 : N` | Ver Lead. Cliente pode existir sem Lead (nasce de Lista do Marketing, RN-CLI-03). |
| **Cliente → Benefício** | `1 : N` | Zero ou mais, cada um com Matrícula, Banco, Tipo de Conta, Agência e Conta (RN-CLI-08). |
| **Cliente → Documento do Cliente** | `1 : N` | Aparecem em todas as negociações do Cliente; distintos dos Documentos da Proposta (RN-PRP-11, RF-CLI-04). |
| **Cliente → Contrato** | `1 : N` | Histórico interno do tenant; as Lojas não veem (RN-CLI-13). |
| **Cliente → Simulação** | `1 : N` (opcional do lado da Simulação) | Associação automática por CPF, só para Cliente já associado à Loja (RN-SIM-05). |
| **Cliente → Proposta** | `1 : N` | Toda Proposta tem Cliente, por referência ou dados inline (Propostas, §3.1). |
| **Cliente** | — | **CPF único no tenant** quando preenchido; opcional, preenchido uma vez e depois imutável (RN-CLI-11). Sem CPF, reaproveita por Nome completo + Telefone exatos (RN-CLI-12). Sem fusão de Clientes nesta versão. |

---

# 📑 Contrato (histórico do Cliente)

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Contrato → Cliente** | `N : 1` | Obrigatório. |
| **Contrato → Proposta** | `1 : 1` (Origem "Proposta Paga") | Criado quando a Proposta chega a Pago e **acompanha** as edições posteriores dela (RN-PRP-18). |
| **Contrato → Lista** (Marketing) | `N : 1` (Origem "Importado") | Gravado a partir das colunas de contrato da Lista (RN-PRS-15). |
| **Contrato → Segmento, Banco** | `N : 1` | Segmento da Proposta (via Lead) ou o alvo da Lista; Banco quando conhecido (API do Banco Central). |
| **Contrato** | — | Operação do catálogo fixo. Sem Parcela e Prazo, fica "sem dados para análise" na Carteira Ativa (RN-CLI-13). |

---

# 🧮 Simulação

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Simulação → Lead** | `N : 1` (opcional) | Presente quando parte do contexto de uma Lead. |
| **Simulação → Cliente** | `N : 1` (opcional) | Ver Cliente. Os dados do cliente são sempre gravados **inline** no snapshot (RN-SIM-05). |
| **Simulação → Segmento, Produto, Promotora** (Admin) | `N : 1` | Obrigatórios; com a Operação (catálogo fixo), definem as Ofertas avaliadas (RN-SIM-06, RN-SIM-06a). |
| **Simulação → Resultado por Oferta** | `1 : N` | Uma linha por Oferta avaliada: elegível ou não, motivo, requisitos que falharam com o valor da Oferta, taxa, prazo, parcela, valores (RN-SIM-11). |
| **Resultado → Oferta** (Admin) | `N : 1` | Referência **e** snapshot dos valores da época; mudar a Oferta depois não altera a Simulação. |
| **Simulação** | — | **Imutável**; refazer gera registro novo (§3.1). Três origens de disparo: Manual, Automática via Lead, Marketing (RN-SIM-01). Não consome Créditos (RN-SIM-03). |

---

# 📝 Proposta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Proposta → Lead** | `0..1 : 1` | Opcional; quando presente, 1:1 (RN-PRP-01). Só o nascimento automático (Formalização) sempre tem Lead (RN-PRP-02). |
| **Proposta → Cliente** | `N : 1` | Obrigatório: referência ao Cliente **ou** dados inline (CPF, Nome, Telefone). |
| **Proposta → Operador** (Usuário) | `N : 1` | Obrigatório. Recebe o crédito da venda (RN-PRP-16). |
| **Proposta → Atores** (Usuários) | `N : 1` cada (Consultor, Averbador, Emissor de Boleto, Digitador) | Opcionais; cada um trava ao ser atribuído (RN-PRP-09). Entram no escopo `self` (RN-PRP-17), mas não recebem crédito. |
| **Proposta → Status de Proposta** (Admin) | `N : 1` | Obrigatório; nasce no ativo mais antigo do tipo Novo (RN-STP-08). O Tipo comanda o comportamento e o que é editável (RN-PRP-15). |
| **Proposta → Oferta** (Admin) | `N : 1` + snapshot | Uma só; pode faltar em Novo, obrigatória para sair dele. Snapshot na associação e a cada troca: Oferta, Produto, Promotora, Banco, Operação, taxa de comissão e multiplicador (RN-PRP-05, RN-PRP-07). Travada em Pago e Cancelado. |
| **Proposta → Simulação de referência** | `N : 0..1` | Pré-preenche o valor. |
| **Proposta → Banco de origem** | `N : 0..1` | Obrigatório em Portabilidade e Portabilidade com Refinanciamento (RN-PRP-14); API do Banco Central. |
| **Proposta → Origem de Venda** (Admin) | `N : 0..1` | Copiada da Lead na criação, depois independente (RN-PRP-13). |
| **Proposta → Documento da Proposta** | `1 : N` | Exclusivos da negociação (RN-PRP-11). |
| **Proposta → Linha do tempo** | `1 : N` | Gerada pelo sistema, não editável (RN-PRP-12). |
| **Proposta → Atribuição no Pago** | `1 : 0..1` | Gravada no Pago: Operador, **Time** e **Cargo** naquele instante, Valor de meta e valor da comissão (RN-PRP-05). Metas, Rankings, Dashboard e Relatórios leem esta atribuição. |
| **Proposta → Contrato** | `1 : 0..1` | Ver Contrato. |
| **Proposta** | — | **Nunca é excluída** (RN-PRP-08); engano se resolve cancelando. Pago e Cancelado movem a Lead para o Stage do mesmo Tipo (RN-PRP-03). Não debita saldo (RN-PRP-06). |

---

# 💬 Conversa

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Conversa → Lead** | `1 : 1` | O SmartCorbans persiste **só a associação** com a conversa da Huggy; as mensagens não são gravadas (Chat, §3.1). |
| **Conversa** | — | Ativa do Stage Novo até Pago ou Cancelado; depois, somente leitura (RN-CHT-02, RN-CHT-05). Mensagem sem Lead cria a Lead (RN-CHT-01). |

---

# 💰 Créditos

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Loja → Saldo** | `1 : 1` | Saldo em R$ e Limite de gasto diário opcional (Créditos, §3.1). Nunca negativo, exceto com a flag "Pode receber lead sem saldo" (RN-CRD-03). |
| **Loja → Lançamento (Extrato)** | `1 : N` | **Append-only**: nada é editado ou excluído (Créditos, §3.2). Cada lançamento guarda o saldo resultante. |
| **Lançamento de Débito → Lead** | `1 : 1` | Ver Lead. Guarda o Funil, a Origem e o Produto que definiram o preço. |
| **Lançamento de Estorno → Débito** | `0..1 : 1` | O estorno é um Crédito novo que referencia o Débito original (RN-CRD-06). Um débito só pode ser estornado uma vez. |
| **Lançamento de Crédito → Cobrança Pix** | `0..1 : 1` | A cobrança tem estado próprio (Aguardando, Confirmado, Expirado/Cancelado); só a confirmada gera lançamento (Créditos, §6). |
| **Lançamento de Crédito manual → Usuário do Admin** | `N : 1` | Quem adicionou, com nota opcional. |

---

# ✖️ Multiplicador de Meta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Multiplicador → Loja × Oferta** | único por par | Ausência de registro **é** 100% (RN-MUT-01 a 03). Só para Ofertas que a Loja enxerga pela Região. |
| **Multiplicador → Proposta** | — | Copiado como snapshot na Proposta ao associar a Oferta e a cada troca; travado a partir do Pago (RN-MUT-04, RN-PRP-05). |

---

# 🏁 Meta

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Meta → Time ou Membro** | `N : 1`, exclusivo | Um destino só, nunca os dois (RN-MET-01). Vários por destino, inclusive do mesmo Tipo (RN-MET-02). |
| **Meta de Time → Cargos elegíveis** | `N : N` | Opcional; filtra quem conta, pelo Cargo gravado no Pago (RN-MET-11). |
| **Meta individual → Meta de Time de origem** | `N : 1` | Só nas geradas por distribuição (RN-MET-06). Encerrada, nunca excluída, quando o Membro deixa de ser elegível (RN-MET-10). |
| **Meta → Proposta** | — | Sem vínculo gravado: o progresso é calculado das Propostas Pagas pela atribuição no Pago, dentro do Prazo (RN-MET-03). Não há tabela de ligação Meta↔Proposta. |
| **Meta** | — | Excluir o Time encerra as Metas dele (RN-MET-12). Tipo travado após a criação (RN-MET-05). |

---

# 🏆 Ranking

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Ranking → Loja** | `N : 1` | Sempre de uma Loja, nunca cross-Loja (RN-RNK-01). |
| **Ranking → Cargos elegíveis** | `N : N` | Só em Tipo Membros (RN-RNK-11). |
| **Loja → Link do Modo TV** | `1 : 0..1` | Token gerado no primeiro acesso a Rankings; regenerar invalida o anterior (RN-RNK-12). |
| **Ranking → Proposta** | — | Calculado das Propostas Pagas do mês, pela atribuição no Pago (RN-RNK-02). Sem vínculo gravado. |

---

# 👥 Membro, Time e Configurações

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Membro = Usuário** (Admin) | — | Mesma entidade, com escopo `store` (CRM · Membros). Nasce na Loja de quem cadastra (RN-MBR-01). |
| **Membro → Time** | `N : 0..1` | No máximo um Time, da mesma Loja; trocar move, com confirmação (RN-TIM-02, RN-USR-02). |
| **Time** (Admin) | — | Mesma entidade do Admin · Lojas; o CRM não acrescenta campos (CRM · Times). |
| **Loja → Segmento × Origem da lead** (Admin) | — | O CRM edita o recorte "Ativo" e os tipos de lead que recebe por Segmento, no mesmo registro do Admin (RN-CFG-01). |

---

# 📊 Relatórios e Dashboard

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Visão salva → Usuário** | `N : 1` | Pessoal (Relatórios, §3.2). |
| **Dashboard, Relatórios** | — | Leituras sobre Leads, Propostas, Metas e Créditos; sem entidade própria além da Visão salva. |

---

# 📌 Pendências entre documentos

Nenhuma. As três da v0.1 foram resolvidas na v0.2.

---

_Fim do documento. Relacionamentos e Regras de Negócio v0.2 — visão consolidada, CRM._
