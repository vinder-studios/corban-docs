# SmartCorbans — Documentação

Especificação de produto e arquitetura do **SmartCorbans**, a plataforma de gestão comercial para correspondentes bancários de crédito consignado. É uma instância única (single-tenant) da Smartconsig, e os parceiros entram como Lojas. São três aplicações (**Admin**, **CRM** e **Marketing**) sobre uma API em .NET 10.

A documentação de front-end (arquitetura do monorepo, UX e design system) fica em outro repositório: `FrontEnd/docs`.

## Por onde começar

| Quero entender… | Leia |
|---|---|
| O produto, o escopo e as regras gerais | [`Product Specification/index.html`](Product%20Specification/index.html) · [`spec.md`](Product%20Specification/spec.md) |
| Uma aplicação | `Product Specification/<App>/spec.md` (Documento de Produto da aplicação) |
| Um módulo | `Product Specification/<App>/<Módulo>/spec.md` e a `Matriz de Permissões.md` ao lado |
| Como as entidades se relacionam | `Product Specification/<App>/Relacionamentos e Regras de Negócio.md` |
| Como o sistema é construído | [`Architecture/C4`](Architecture/C4), do Contexto à Implantação |
| Por que uma decisão de arquitetura foi tomada | [`Architecture/ADR/README.md`](Architecture/ADR/README.md) |
| As tabelas de cada módulo | [`Architecture/Dados`](Architecture/Dados/index.html) |

## Estrutura

```
Docs/
├── Product Specification/
│   ├── spec.md                       Especificação Geral (visão, escopo, RNFs, regras transversais RN-SC)
│   ├── Admin/                        15 módulos: Cargos, Usuários, Lojas, Funis, Ofertas, Regras…
│   ├── CRM/                          14 módulos: Leads, Clientes, Simulações, Propostas, Créditos…
│   └── Marketing/                    Prospecção, Carteira Ativa, Dashboard
│       Cada aplicação tem: spec.md (Documento de Produto), um diretório por módulo
│       (spec.md + Matriz de Permissões.md) e Relacionamentos e Regras de Negócio.md
└── Architecture/
    ├── C4/                           Contexto, Containers, Componentes (API, Admin, CRM, Marketing), Implantação
    ├── ADR/                          Decisões de arquitetura (ADR-0001 a ADR-0025), modelo e índice
    └── Dados/                        Modelo de dados lógico, um documento por módulo da API
```

## Arquitetura em uma página

| Tema | Decisão | ADR |
|---|---|---|
| Identidade | Keycloak existente, sem FeijucaAuth; realm único, Loja = Organização; login pela API | 0001, 0002 |
| Banco | PostgreSQL Flexible Server privado; migração completa do MongoDB atual | 0003, 0018 |
| Hospedagem | API e Worker no Azure Container Apps (Brazil South); três sites na Netlify | 0004, 0016, 0017, 0021 |
| Backend | .NET 10, monolito modular (um projeto por camada), MediatR 12, EF Core com um DbContext e um schema por módulo | 0005, 0006, 0012, 0013, 0014 |
| Processamento | Worker separado, fila de jobs no PostgreSQL | 0007, 0008 |
| Escala | API com réplica única | 0009 |
| Tempo real | SignalR sem backplane, nas três aplicações; sino de notificações único por usuário | 0010, 0015, 0025 |
| Integrações | Webhooks (Huggy, AbacatePay) na própria API, com idempotência | 0011 |
| Dados entre módulos | Sem chave estrangeira entre schemas; referência por id e snapshot | 0023 |
| Arquivos | Azure Blob Storage privado, entregue por URL assinada | 0024 |
| Segredos e rede | Key Vault com Managed Identity, banco sem senha; NAT Gateway só se exigido | 0019, 0020 |
| Ambientes | Homologação com a mesma forma de produção, em IaC | 0022 |

Sistemas externos ativos: Keycloak, Huggy, AbacatePay (só Pix), API do Banco Central e ViaCEP.

## Diagramas publicados

As versões desenhadas dos diagramas C4 estão publicadas como páginas interativas no [Hub do C4 Model](Architecture/C4/index.html) (Contexto, Containers, Componentes da API, Admin, CRM e Marketing, Implantação). Para navegar por toda a documentação, acesse o [Portal Inicial da Documentação](index.html). Os arquivos `.md` em `Architecture/C4` trazem a fonte em Mermaid e são a referência: em caso de diferença, vale o `.md`.

## Convenções

- **Idioma:** documentos em português. No banco e no código, nomes em inglês (`snake_case` no banco), conforme `Architecture/Dados/README.md` e o glossário de `naming.md` do front.
- **Versionamento dos documentos:** cada documento tem versão e um histórico de revisões no topo; mudanças de regra registram a origem ("decisão do usuário", ADR, outro módulo).
- **Códigos de regra:** `RN-<MÓDULO>-NN` (regras de negócio), `RF-<MÓDULO>-NN` (requisitos funcionais), `RNF00N` (não funcionais), `RN-SC-NN` (transversais). Número removido fica reservado, nunca reaproveitado.
- **Permissões:** formato `recurso:ação:escopo`, recurso no plural, escopos `self` ⊂ `team` ⊂ `store` ⊂ `all`.
- **ADRs:** uma decisão por arquivo, no modelo `_modelo.md`. ADR aceita não é editada no mérito: uma nova a substitui ou estende.
- **Divergência:** entre um documento consolidado (Relacionamentos, Especificação Geral) e o documento do módulo, vale o módulo.

## Situação

- **Especificação:** sem itens abertos na Especificação Geral.
- **C4 e ADRs:** níveis 1 a 3 e Implantação fechados; 25 ADRs aceitas.
- **Modelo de dados:** 12 módulos validados.
- **Em aberto:** o módulo **Acesso** ficou fora do modelo de dados porque usuários, Cargos e permissões ficarão com o Keycloak; o desenho e a ADR correspondente serão debatidos depois da modelagem (`Architecture/Dados/01-acesso.md` fica só como registro). Também falta o plano de migração do MongoDB.
