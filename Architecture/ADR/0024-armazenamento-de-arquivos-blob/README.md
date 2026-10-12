# ADR-0024 — Arquivos no Azure Blob Storage privado, entregues por URL assinada

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** Modelo de dados · Estrutura (`Dados/01-estrutura.md`, §6)
**Afeta:** C4 · Containers e Implantação; Componentes da API (Persistência); modelo de dados de Estrutura, Clientes, Propostas e Marketing

## 1. Contexto

O produto guarda arquivos que não cabem como dado de tabela:

- o **logo da Loja** (Admin · Lojas);
- os **Documentos do Cliente** e os **Documentos da Proposta** (CRM · Clientes, CRM · Propostas, RN-PRP-11);
- os **arquivos de Lista** (CSV/XLSX) importados no Marketing, processados pelo Worker (Prospecção, RF-PRS-01), e os arquivos da Lista de bloqueio (RF-PRS-07).

Os documentos têm dado pessoal (RNF003) e precisam ficar no Brasil (ADR-0021). O C4 não tinha onde guardá-los.

## 2. Decisão

**Os arquivos ficam num Azure Storage Account (Blob Storage) privado, em Brazil South.**

- **Sem acesso público** à conta. API e Worker acessam por **Managed Identity** (ADR-0019), cada um só aos containers de que precisa.
- **Upload e download pela API, com URL assinada de curta duração** (SAS de delegação de usuário, minutos): a API confere a permissão e o escopo do usuário (RNF002) e devolve a URL; o navegador envia ou baixa direto do Blob, sem o arquivo passar pela API.
- **O banco guarda só a chave do arquivo** (`*_file_key`) e os metadados (nome original, tipo, tamanho, quem enviou, quando). Nunca o conteúdo.
- **Um container por finalidade** (`store-logos`, `client-documents`, `proposal-documents`, `marketing-imports`), para dar permissões e regras de retenção separadas.
- **Exclusão suave** segue a regra do produto (RN-SC-08): o arquivo permanece enquanto o registro existir.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Arquivo no PostgreSQL (`bytea`) | Um lugar só; transação junto com o registro | Banco cresce rápido; backup e restauração mais lentos; arquivo passa pela API | Custo e desempenho do banco |
| Upload e download sempre pela API (proxy) | Controle total em cada byte | Ocupa a réplica única (ADR-0009) com tráfego de arquivo | A URL assinada mantém o controle de acesso sem esse custo |
| Armazenamento da Netlify | Já usado no front | Feito para estático público; dado pessoal fora da Azure | RNF003 e ADR-0021 |

## 4. Consequências

- **Ganhos:** arquivos fora do banco; acesso controlado pela API sem tráfego pela API; dado no Brasil; sem segredo novo.
- **Custos e riscos aceitos:** mais um recurso na Azure e no IaC (ADR-0022); CORS do Storage liberado só para os três domínios da Netlify; a URL assinada vale por alguns minutos mesmo se o acesso do usuário mudar nesse intervalo.
- **O que muda nos documentos:** Containers e Implantação ganham o Blob Storage; Componentes da API cita esta ADR em Persistência; o modelo de dados usa `*_file_key`.

## 5. Quando revisar

Se houver exigência de antivírus nos uploads, de retenção legal por tempo fixo, ou se o volume de arquivos justificar camadas de armazenamento (frio, arquivo).

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Proposta no modelo de dados de Estrutura; aceita pelo usuário. |
