# ADR-0015 — Tempo real também no Marketing, para notificações e andamento

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-11 (`C4/03-componentes-marketing.md`), **rejeitada**; decisão de produto de incluir notificações no Marketing na v1 (Marketing, Documento de Produto v0.9, RN-MKT-10)
**Afeta:** ADR-0010 (escopo estendido); C4 · Componentes do Marketing e da API (Tempo real); Marketing · Prospecção e Carteira Ativa

## 1. Contexto

A pré-análise em lote, a execução de Varreduras e a injeção rodam no Worker e levam minutos (ADR-0007). A P-11 propunha acompanhar o andamento por consulta periódica, porque notificações estavam fora da v1 do Marketing.

Na revisão, notificações **entraram na v1** (RN-MKT-10): uma Lista ou Varredura parada em Aguardando aprovação, sem ninguém saber, trava a injeção; e uma falha precisa chegar a quem pode agir. O sino já existe no Shell Privado, o pacote `notifications` já existe no monorepo e o hub de tempo real já existe na API (ADR-0010).

## 2. Decisão

**O Marketing também se conecta ao hub de tempo real da API**, que passa a atender o CRM e o Marketing.

- **Notificações** de exceção do Marketing (RN-MKT-10) chegam pelo hub e aparecem no sino do Shell.
- **Andamento na tela:** o mesmo evento de mudança de estado invalida as consultas da Lista ou da Varredura aberta, que recarrega da API (mesmo padrão do CRM, ADR-0010). **Não há consulta periódica.**
- Valem as regras da ADR-0010: o evento só avisa, o dado vem da API; o hub respeita o escopo e as permissões do destinatário; réplica única, sem backplane (ADR-0009).
- O evento nasce no Worker, que muda o estado no banco; a API o repassa ao hub. Com réplica única, basta a API saber da mudança, pelo mesmo mecanismo de aviso que o Worker usar (definido no esqueleto).

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Consulta periódica (P-11) | Nada novo no Marketing | Não entrega notificação fora da tela; requisições vazias | Notificações entraram na v1 |
| Notificações por consulta periódica do sino, sem hub no Marketing | Sem conexão permanente | Dois mecanismos no produto; atraso nos avisos | O hub já existe e já resolve |

## 4. Consequências

- **Ganhos:** um único mecanismo de tempo real no produto; o gestor sabe quando aprovar ou quando algo falhou sem vigiar a tela.
- **Custos e riscos aceitos:** mais conexões no hub da réplica única; o Worker precisa avisar a API das mudanças de estado.
- **O que muda nos documentos:** ADR-0010 (escopo); Componentes do Marketing (sai a P-11 e a regra de consulta periódica); Componentes da API (Tempo real atende CRM e Marketing). Os C4 são atualizados depois do fechamento das ADRs.

## 5. Quando revisar

Junto com a ADR-0009 e a ADR-0010, se a API ganhar réplicas; ou se o volume de conexões do Marketing pesar no hub.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | P-11 rejeitada; notificações do Marketing entram na v1 por decisão do usuário. |
