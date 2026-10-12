# ADR-0003 — PostgreSQL como banco de dados

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** D-10 (`C4/02-containers.md`) — ADR retroativa
**Afeta:** C4 · Containers, Componentes da API (Persistência, Jobs), Implantação

## 1. Contexto

O SmartCorbans precisa de um banco para os dados de negócio, o Extrato append-only, a trilha de auditoria e a fila de jobs. O **sistema atual usa MongoDB**, e todos os dados dele migram para o SmartCorbans (D-06).

O modelo do SmartCorbans é fortemente relacional e transacional:

- **Dinheiro com atomicidade.** Atribuir uma Lead checa o saldo e debita os Créditos da Loja na mesma operação (RN-SC-05, RN-CRD-01), e o Extrato guarda o saldo resultante de cada lançamento.
- **Unicidade garantida pelo banco.** CPF do Cliente (RN-SC-07), e-mail do usuário na instância (RN-USR-04).
- **Muitas referências entre entidades.** Proposta → Oferta congelada → Promotora, Produto e Regras; Lead → Funil → Stage; Usuário → Cargo, Loja e Time.
- **Relatórios com agregação e cruzamento** por Loja, Time, Operador, Segmento, Promotora e período (Relatórios do Admin e do CRM).
- **Um schema por módulo** (Componentes da API, §5), com fronteiras explícitas.

## 2. Decisão

O banco é **PostgreSQL**, num único servidor, com **um schema por módulo**. O SmartCorbans **não usa MongoDB**; o MongoDB atual é só a **origem da migração**.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Manter MongoDB | Migração sem mudança de paradigma; o time já opera | Transações e integridade referencial menos naturais; unicidade e chaves estrangeiras dependem da aplicação; relatórios cruzados mais caros; provedor do EF Core para MongoDB menos maduro | O modelo novo é relacional e mexe com dinheiro; manter o banco só pela migração inverte a prioridade |
| SQL Server / Azure SQL | Integração nativa com a Azure e o .NET | Licença | Decisão do usuário pelo PostgreSQL |
| PostgreSQL com colunas JSONB para dados vindos do MongoDB | Facilita a carga | Leva a falta de estrutura do sistema atual para o novo | Usar JSONB só onde o dado é de fato livre (ex.: campos do Layout de Lead), não como atalho de migração |
| Banco por módulo | Isolamento físico | Transações entre módulos ficam distribuídas; mais custo | Monolito modular não precisa disso agora |

## 4. Consequências

- **Ganhos:** transação local para o débito de Créditos; unicidade e chaves estrangeiras garantidas pelo banco; relatórios em SQL; fila de jobs no próprio banco com trava por linha; sem custo de licença; bom suporte no .NET (Npgsql).
- **Custos e riscos aceitos:** a migração deixa de ser só uma cópia e vira uma **transformação de documento para relacional**:
  - documentos aninhados e arrays viram tabelas e relacionamentos;
  - os `ObjectId` do MongoDB precisam de um mapa para as novas chaves, preservado durante a carga;
  - como o MongoDB não impõe esquema, campos ausentes, tipos misturados e duplicidades (CPF, e-mail) precisam de limpeza **antes** da carga, ou a carga falha nas restrições do PostgreSQL;
  - a carga também precisa reconciliar o modelo antigo (multi-tenant, Grupos, Operação) com o novo (Lojas, Cargos, Times, Segmentos com Funil).
  
  Tudo isso entra no plano de migração (D-08).
- **O que muda nos documentos:** Containers e Implantação já citam o PostgreSQL; a base do sistema atual passa a ser identificada como MongoDB no Contexto, Containers e Implantação.

## 5. Quando revisar

Se algum módulo passar a exigir isolamento físico ou escala que um servidor só não atenda.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Registrada retroativamente a partir de D-10. |
| 2026-10-10 | Aceita | Revisão do contexto pelo usuário: o sistema atual usa MongoDB; migração passa a ser transformação de documento para relacional. |
