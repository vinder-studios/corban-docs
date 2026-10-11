# Corban Marketing · Relacionamentos e Regras de Negócio

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing — Documento de Produto
**Versão:** 0.2
**Data:** 10/10/2026

## Histórico de revisões

| Versão | Mudanças |
| ------ | -------- |
| 0.2 | Pendências resolvidas (decisões do usuário): marcas de proposta da Prospecção confirmadas (Prospecção v0.8) e as seis relações inferidas confirmadas. |
| 0.1 | Versão inicial, montada a partir do Documento de Produto (RN-MKT) e dos módulos Prospecção, Carteira Ativa e Dashboard. Relações sem regra explícita na spec estão marcadas **(inferido)**. Corrige de passagem a descrição do estado Ativa na Carteira Ativa (§6), que contradizia a RN-CRA-08. |

## Como usar este documento

Visão consolidada das relações entre as entidades do Marketing e delas com o Admin e o CRM. Cada relação remete ao módulo dono da regra. Não substitui os documentos de módulo; existe para dar a leitura rápida do grafo e servir de base ao modelo de dados. **Em caso de divergência, vale o módulo.**

O Marketing atua no nível do tenant, **acima das Lojas**: nenhuma entidade dele pertence a uma Loja. A Loja só aparece quando a Lead injetada é distribuída (CRM · Leads).

---

# 📥 Lista de Prospecção

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Lista → Segmento alvo** (Admin) | `N : 1` | Obrigatório. Define o Funil em que as Leads entram (Stage tipo Novo). |
| **Lista → Origem de Venda** (Admin) | `N : 0..1` | Copiada às Leads geradas (RN-MKT-01). |
| **Lista → Operações testadas** | `N : N` (catálogo fixo) | Escolhidas na configuração da pré-análise (RN-PRS-02). |
| **Lista → Filtros macro** | `1 : N` | Opcionais; agem antes das Regras das Ofertas (RN-PRS-03). |
| **Lista → Mapeamento de colunas** | `1 : 1` | Coluna do arquivo → campo do catálogo de campos das Regras (Admin). Guardado com a Lista, para reprocessar. |
| **Lista → Linha (contato)** | `1 : N` | Ver Linha da Lista. |
| **Lista → Usuário criador** | `N : 1` | Recebe a notificação de Falha (RN-MKT-10). O criador é gravado. |
| **Lista → Lead** (CRM) | `1 : N` | Pelo lado da Lead, referência imutável (RN-MKT-01). A Lista não se exclui se tiver Leads (RN-PRS-17). |
| **Lista** | — | Pertence ao tenant (RN-PRS-01). Custo da base opcional, base do Dashboard financeiro. Estados em Prospecção §6. Resultado vale 7 dias e depois exige reprocessar (RN-PRS-09). |

---

# 📄 Linha da Lista (contato)

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Linha → Lista** | `N : 1` | Obrigatório. |
| **Linha → Cliente** (CRM) | `N : 0..1` | Toda linha válida e não bloqueada cria ou reaproveita um Cliente, **inclusive as Descartadas**; Inválidas e Bloqueadas não criam (RN-PRS-16). Reaproveitar não sobrescreve os dados do Cliente. O Cliente nasce sem Loja (RN-MKT-09). |
| **Linha → Resultado da pré-análise** | `1 : 1` | Inválida, Bloqueada, Descartada (com motivo) ou Qualificada (Ofertas elegíveis, valor potencial, requisitos não avaliados) (RN-PRS-05). |
| **Linha → Contrato importado** (CRM) | `1 : 0..1` | Quando a linha traz colunas de contrato, grava um Contrato no histórico do Cliente, com o Segmento alvo (RN-PRS-15). |
| **Linha → Lead** (CRM) | `1 : 0..1` | Só a Qualificada que passar na revalidação da injeção (RN-PRS-08, RN-PRS-10). |
| **Linha → Simulação** (CRM) | `1 : 0..1` | Gravada na injeção, origem Marketing, ligada à Lead, só com Oferta aprovada (RN-MKT-02). |
| **Linha** | — | Recusa na injeção (carência, colisão, bloqueio) é contada e não gera Lead (RN-PRS-08). |

---

# 🔁 Varredura

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Varredura → Segmento alvo** (Admin) | `N : 1` | Obrigatório. A base são os Clientes com Contrato nesse Segmento (RN-CRA-01). |
| **Varredura → Filtro de base** | `1 : 1` | Recortes sobre o **último Contrato** do Cliente no Segmento: Operação e tempo desde a Data de pagamento (Carteira Ativa §3.1). |
| **Varredura → Operações a simular** | `N : N` (catálogo fixo) | Obrigatório. |
| **Varredura → Origem de Venda** (Admin) | `N : 0..1` | Copiada às Leads geradas (RN-MKT-01). |
| **Varredura → Usuário criador** | `N : 1` | Recebe as notificações de Falha (RN-MKT-10). O criador é gravado. |
| **Varredura → Execução** | `1 : N` | Ver Execução. Única tem uma; Recorrente, uma a cada 7, 15 ou 30 dias (RN-CRA-08). |
| **Varredura → Lead** (CRM) | `1 : N` | Pelo lado da Lead, referência imutável (RN-MKT-01). |
| **Varredura** | — | Pertence ao tenant. Recorrente exige limite diário (RN-CRA-07) e só se edita Pausada; mudar Segmento, Filtro ou Operações volta a Aguardando aprovação (RN-CRA-09). Sem custo de base (RN-DMB-05). |

---

# ⏱️ Execução da Varredura

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Execução → Varredura** | `N : 1` | Obrigatório. Cada execução é um registro próprio, necessário para a notificação "Falha de execução recorrente" (RN-MKT-10) e para o resultado guardar "os dias já executados" (Carteira Ativa §6). |
| **Execução → Resultado por Cliente** | `1 : N` | Elegível, Sem dados para análise, Descartado ou Recusado (não perturbe, carência, Lead ativa, Proposta em andamento) (RN-CRA-02, RN-CRA-05). |
| **Resultado → Cliente** (CRM) | `N : 1` | O Cliente avaliado. A Varredura lê Contratos de **todas as Lojas**, sem expô-los a elas (RN-CRA-01). |
| **Resultado → Contrato** (CRM) | `N : 1` | O último Contrato do Cliente no Segmento, base da simulação silenciosa (RN-CRA-02). |
| **Resultado → Lead** (CRM) | `1 : 0..1` | Lead requalificada, Origem Carteira Ativa (RN-CRA-04). O que sobra na fila é reavaliado na execução seguinte (RN-CRA-07). |
| **Resultado → Simulação** (CRM) | `1 : 0..1` | Gravada na injeção, só com Oferta aprovada (RN-MKT-02). |

---

# 🚚 Fila de injeção

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Fila → Lista ou Varredura** | `1 : 1` | Existe a partir da aprovação. Ritmo: tudo agora ou limite diário (obrigatório na Recorrente) (RN-MKT-07). |
| **Fila → Aprovador** (Usuário) | `N : 1` | Quem aprovou; exige `approve` (Marketing §2). A aprovação é registrada e auditada (RNF005, grupo Dinheiro). |
| **Fila** | — | Pausar, retomar e cancelar o restante; o já injetado continua. Cada item revalida duplicidade e bloqueio na hora de injetar (RN-MKT-04, RN-MKT-05). A fila não é entidade separada: é o conjunto de Linhas ou Resultados Qualificados ainda não injetados. |

---

# 🚫 Lista de bloqueio

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Lista de bloqueio** | `1` por tenant | Itens de **CPF ou telefone**; importável por arquivo e editável (RF-PRS-07). |
| **Bloqueio → Linha da Lista e Resultado da Varredura** | — | Bloqueio rígido, conferido na pré-análise e de novo na injeção (RN-MKT-05, RN-PRS-08). Sem vínculo gravado: a checagem é por valor. |
| **Bloqueio → Lead fora do Marketing** | — | **Não se aplica** nesta versão (Leads manuais, Huggy); é evolução (Marketing §6). |

---

# ⚙️ Configurações do Marketing

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Tenant → Carência** | `1 : 1` | N dias, padrão 90 (RN-MKT-04, RF-PRS-08). Vale para Prospecção e Carteira Ativa. |

---

# 🔗 Relações com o CRM e o Admin

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Lead do Marketing → Lista ou Varredura** | `N : 1` | Origem da lead (Prospecção ou Carteira Ativa) e referência, ambas imutáveis (RN-MKT-01). É a amarração do Dashboard (RN-DMB-01). |
| **Lead do Marketing → Resumo das Ofertas elegíveis** | `1 : N` | Valor potencial e resumo gravados na injeção, só referência (RN-MKT-08). A pré-análise considera Ofertas de qualquer Região. |
| **Duplicidade → Lead, Proposta, injeção anterior** | — | Lida no CRM na hora da injeção: Lead ativa no Segmento, Proposta em andamento, injeção nos últimos N dias (RN-MKT-04). A "última injeção" do Cliente sai das Leads com Origem do Marketing, sem registro próprio. |
| **Lead → Débito** (Créditos) | `1 : 0..1` | A Lead cobra como qualquer Origem com preço (RN-CRA-06). Receita de leads do Dashboard = débitos dessas Leads (RN-DMB-05). |
| **Notificação → Lista ou Varredura** | `N : 1` | Três eventos de exceção, entregues pelo sino do Shell Privado (RN-MKT-10). |

---

# 📊 Dashboard

| Relacionamento | Cardinalidade | Regras |
|:---|:---:|:---|
| **Dashboard** | — | Sem entidade própria: lê Listas, Varreduras, Leads, Propostas e Créditos. Período = data da injeção (RN-DMB-02). Sem filtro por Loja (RN-DMB-03). |

---

# 📌 Pendências entre documentos

Nenhuma. As da v0.1 foram resolvidas na v0.2.

---

_Fim do documento. Relacionamentos e Regras de Negócio v0.2 — visão consolidada, Marketing._
