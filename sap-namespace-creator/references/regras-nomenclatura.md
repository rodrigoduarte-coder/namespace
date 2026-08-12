# Regras de nomenclatura e tipos de namespace SAP

Valide o nome proposto contra TODAS as regras abaixo antes de submeter — a reserva é global e permanente (não renomeável; deleção altamente restrita, KBA 2098877).

## Formato do development namespace (namespace de prefixo)

| Regra | Valor | Fonte |
|---|---|---|
| Delimitadores | Começa e termina com `/` (ex.: `/ROITAI/`) | SAP Help / Note 105132 |
| Comprimento total | Mínimo **5**, máximo **10** caracteres **incluindo as duas barras** | Notes 105132/84282, AAK 5.0 |
| Prefixo (entre as barras) | 3 a 8 caracteres | derivado da regra acima |
| Comprimento recomendado | **5–8 caracteres no total** — namespaces maiores quebram restrições de comprimento de objetos (ex.: nomes de tabelas de banco têm máx. **16** caracteres; software components e produtos add-on máx. 30 **incluindo** o `/NAMESPACE/`) | Notes 105132 e 395083 |
| Caixa | Maiúsculas (todos os namespaces reais de parceiro observados usam apenas A–Z e 0–9 maiúsculos) | convenção SAP + screenshots do app |
| Conteúdo | Nome com referência reconhecível à empresa detentora | recomendação SAP |
| Unicidade | **Global e mundial** — checada contra todos os namespaces já registrados na SAP (cloud e on-premise) | SAP Help (Maintain Namespaces) |
| Caracteres permitidos | Letras A–Z e dígitos 0–9, começando com letra (nome iniciado por dígito indica generation namespace interno SAP, ex. `/1BCDWB/`). Underscore não confirmado em namespaces reais — evite. Sequências "SAP" e "R3" indisponíveis (fonte secundária). ⚠️ O conjunto exato exigido pelo diálogo "Request Namespace" é exibido apenas no próprio app — mostre as regras do diálogo ao usuário e valide lá antes de submeter | ABAP naming conventions; screenshots do app; diálogo do app (fonte final) |

**Orçamento de 30 caracteres:** o prefixo `/XXX/` é descontado do limite de nome dos objetos de repositório (30 caracteres para a maioria dos tipos). Um namespace de 10 caracteres deixa só 20 para o nome do objeto — mais um motivo para preferir prefixos curtos.

**Exemplos válidos:** `/ABC/` (5), `/ROIT/` (6), `/ROITAI/` (8), `/MYCOMPNY/` (10 — evitar).
**Inválidos:** `/AB/` (4 — curto demais), `/TOOLONGNM/X` (não termina em `/` na posição certa), `roit/` (sem barra inicial, minúsculas).

## Tipos de namespace — não confunda

| Tipo | Formato | Uso | Licença |
|---|---|---|---|
| **Customer name range** | Objetos iniciando em `Z*` ou `Y*` (Note 16466) | Desenvolvimento próprio do cliente; **sem reserva na SAP**; sem proteção global de colisão. Letras A–X iniciais são reservadas à SAP | Nenhuma extra |
| **Development namespace** | `/XXX/` reservado | Desenvolvimento próprio protegido; obrigatório para add-ons de parceiro | Development license; keys (on-prem) |
| **Generation namespace** | `/B.../` máx. 7 chars, 1º char após a barra = `B`, resto numérico (ex.: `/B24/`); `/B00/`–`/B99/` já usados pela SAP | **Só objetos gerados** (ex.: BW). Nunca crie objetos próprios nele — o sistema pode sobrescrevê-los (KBA 2822365) | Só repair license, papel `C` |
| **Key user namespace** | Prefixo `YY1_` | Itens de key user extensibility no Public Edition (default) | n/a |
| **Partner namespace legado** | Nomes iniciando em `J` (Note 38781) | Fallback histórico de parceiros sem prefix namespace | n/a |
| **Open Source namespace** | tipo próprio | Projetos open source (KBA 3477044) | processo próprio |

## Decisão: Z/Y ou namespace reservado?

- **Cliente desenvolvendo para si** (developer extensibility GROW): `Z*` sob `ZCUSTOM_DEVELOPMENT` é o padrão SAP. Namespace reservado é desnecessário para implementação normal.
- **Parceiro entregando IP a clientes**: namespace reservado **obrigatório** — todos os objetos do add-on devem estar nele (requisito de certificação clean core). Trabalhar em `Z*` arrisca colisão com objetos Z do cliente.
- **Parceiro em sistema do cliente (foreign system)**: namespace reservado recomendado pela mesma razão.
- **Partner TDD landscape** (Test/Demo/Development, SKU 8012331): pacotes sob `ZPARTNER` (não `ZCUSTOM_DEVELOPMENT`).

## Irreversibilidades — avise o usuário ANTES de cada uma

1. **Reserva do nome**: permanente e global. Não há renomeação (confirmado em múltiplas fontes SAP Community); deleção só em casos restritos via SAP for Me (KBA 2098877) e nunca com objetos existentes no namespace (KBA 2320690). Troca de owner só via SAP (KBA 3004591).
2. **Classificação de sistema** (Development/Test no Maintain Namespaces): definida **uma única vez**, imutável.
3. **Instalação de namespace num sistema**: não há função documentada de desinstalação no app.
4. No caminho SAP for Me clássico: namespace "Accepted" mas não reservado **expira** e é recusado após um período — conclua a etapa "Reserve an accepted namespace".

## Checklist de validação do nome (aplicar com o usuário)

```
[ ] Começa e termina com "/"
[ ] 5–10 caracteres no total (contando as barras); ideal 5–8
[ ] 3–8 caracteres entre as barras, maiúsculas, começando com letra
[ ] Referência reconhecível à empresa (ex.: /ROIT/, /ROITAI/)
[ ] Não é formato de generation namespace (/B+números/)
[ ] Nome verificado como plausivelmente único (marca/nome da empresa)
[ ] Usuário ciente de que a reserva é permanente e irrenomeável
[ ] Prefixo curto o bastante para o orçamento de 30 chars dos objetos
```
