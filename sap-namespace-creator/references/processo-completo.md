# Processo completo: reserva e instalação de namespace SAP

Passo a passo detalhado dos dois caminhos de reserva (Landscape Portal e SAP for Me) e da instalação nos sistemas. Leia junto com `regras-nomenclatura.md` antes de submeter qualquer nome — a reserva é permanente.

## Índice

1. [Fase 0 — Qualificação do cenário](#fase-0)
2. [Fase 1 — Pré-requisitos](#fase-1)
3. [Fase 2 — Reserva do namespace](#fase-2)
4. [Fase 3 — Instalação nos sistemas (Landscape Portal)](#fase-3)
5. [Fase 4 — Verificação e primeiros objetos](#fase-4)
6. [Fase 5 — Key user extensibility no namespace (opcional)](#fase-5)
7. [Transportes e o ciclo D → T → P](#transportes)

<a name="fase-0"></a>
## Fase 0 — Qualificação do cenário

Antes de qualquer clique, determine o cenário. Errar aqui gera trabalho desnecessário (reservar namespace que não era preciso) ou bloqueio futuro (entregar add-on em Z*).

| Cenário | Precisa de namespace reservado? | Onde reservar |
|---|---|---|
| Cliente GROW fazendo developer extensibility para uso próprio | **Não** — usa customer name range `Z*`/`Y*` sob `ZCUSTOM_DEVELOPMENT` | n/a |
| Cliente GROW usando key user extensibility | **Não** — itens recebem prefixo `YY1_` automaticamente | n/a |
| **Parceiro construindo add-on** para S/4HANA Cloud Public Edition (Natively ABAP Cloud / clean core, Scalable Solutions) | **Sim — obrigatório.** TODOS os objetos do add-on devem estar em namespace registrado (requisito de certificação SAP, junto com: rodar on-stack, suportar desinstalação e passar no security code scan do ADT) | Landscape Portal → Maintain Namespaces |
| Parceiro/cliente em SAP BTP ABAP Environment (Steampunk) construindo add-on/SaaS | **Sim** | Idealmente ANTES do primeiro provisioning (SAP for Me); depois, Landscape Portal → Maintain Namespaces |
| Cliente/parceiro on-premise (S/4HANA private, ECC) | Sim, se quiser proteção de nome | SAP for Me (me.sap.com/namespaces) |

Contexto adicional:
- Developer extensibility no Public Edition **só existe no landscape de 3 sistemas** (3SL: Development, Test, Production — padrão desde o release 2208/ago-2022) e **só no development tenant, client 080**, do sistema de desenvolvimento. Client 100 é o customizing tenant e não permite desenvolvimento.
- Desde o release 2408.4 (dez/2024), add-ons ABAP Cloud de parceiros podem ser entregues nativamente no stack do Public Edition, com lifecycle via AAKaaS embutido no Landscape Portal (delivery "Scalable Solutions", GA em 10/12/2024).
- Um conflito clássico que o namespace resolve: parceiro e cliente trabalhando ambos em `Z*` podem criar objetos com nomes idênticos — objetos em namespace registrado carregam o prefixo `/XXX/` e são tratados como propriedade do detentor do namespace, protegidos em patches e upgrades.

<a name="fase-1"></a>
## Fase 1 — Pré-requisitos

Checklist a validar com o usuário ANTES de iniciar:

**Contratuais / SAP for Me:**
- [ ] A empresa possui **development license** em contrato (ex.: ABAP Workbench User, Standalone Development Systems, SAP Developer User para parceiros). Sem ela: erro *"License missing. Your company is missing a development license to access this page"* (KBA 2581772).
- [ ] O contrato de development license está no **mesmo customer number** usado na solicitação — contrato em outro customer number é a causa nº 1 de falha (KBA 3113391; SAP Order Management identifica o customer number correto, KBA 1660069).
- [ ] **S-user com autorização "Reserve Namespaces"** (SAP Note 1271482), concedida pelo administrador de usuários da empresa. Esse S-user será usado no campo "User ID" do diálogo do Landscape Portal e deve pertencer ao parceiro.
- [ ] (BTP ABAP Environment) O namespace deve ser reservado sob o **mesmo customer number que licencia os tenants** do ABAP Environment.

**Acesso ao Landscape Portal:**
- [ ] **Public Edition (partner):** acesso à URL do Landscape Portal do parceiro/landscape (comunicada no onboarding; ver `SKILL.md` seção "Acesso ao Landscape Portal"). Página SAP Help de referência: "Setting Up the Landscape Portal for SAP S/4HANA Cloud, ABAP environment" e "Setting Up the Landscape Portal as a Partner".
- [ ] **BTP ABAP Environment (Steampunk):** subaccount BTP **na região cf-eu10** (restrição documentada), entitlement "Landscape Portal" adicionado, aplicação subscrita em Instances and Subscriptions, e role collection **`LandscapePortalAdminRoleCollection`** atribuída ao usuário no identity provider default do subaccount (`LandscapePortalUserRoleCollection` é somente leitura + criação de Support Users — não serve para requisitar/instalar). O booster "Landscape Portal for SAP BTP ABAP Environment" automatiza esse setup.
- ⚠️ **Não misture os dois contextos**: entitlement/subscription/cf-eu10/role collections são do Steampunk; o portal do Public Edition é acessado pela URL do landscape do parceiro.

**Landscape:**
- [ ] 3-system landscape provisionado (via SAP for Me → Systems & Provisioning), com CBC e IAS/IPS configurados.
- [ ] Sistemas alvo com lifecycle status **"Live"**.

<a name="fase-2"></a>
## Fase 2 — Reserva do namespace

### Caminho A — Landscape Portal (cenário cloud: Public Edition partner e Steampunk pós-provisioning)

1. Faça logon no Landscape Portal do seu sistema/parceiro.
2. Na seção **Systems**, clique no tile **Maintain Namespaces**.
3. Aba **Available Namespaces** (lista seus namespaces de parceiro com descrições) → clique em **Request Namespace**.
4. No diálogo, informe:
   - **Namespace name** — valide ANTES contra `regras-nomenclatura.md` (formato `/XXX/`, 5–10 caracteres no total incluindo as barras; recomendado 5–8; maiúsculas; referência reconhecível à empresa).
   - **Description** — descrição conforme as regras listadas no próprio diálogo.
   - **User ID** (obrigatório) — S-user com autorização "Reserve Namespaces" e pertencente ao parceiro.
5. O sistema executa 4 validações automáticas: (a) o User ID tem autorização para criar namespaces; (b) o User ID pertence ao parceiro; (c) o nome cumpre todos os requisitos de namespace válido; (d) o namespace **ainda não existe** — a checagem de unicidade é **global**, contra todos os namespaces já registrados na SAP no mundo (incluindo reservas on-premise clássicas).
6. Após a validação passar, **confirme que leu a notice sobre namespaces** (ela existe porque a reserva é permanente) e clique **Request Namespace** novamente para submeter.
7. O namespace recém-criado aparece na lista de Available Namespaces. Não há workflow de aprovação manual documentado para este caminho — a reserva é efetivada após as validações automáticas.

No cloud, os **developer key e repair key são criados e atribuídos automaticamente** pelo sistema ABAP — nenhuma chave é digitada manualmente. Na Namespace application do SAP for Me é possível conferir as atribuições de chave filtrando pelo installation number `CLOUDSYSTEM`.

### Caminho B — SAP for Me (cenário clássico/on-premise; Steampunk antes do 1º provisioning)

1. Logon em **me.sap.com** com o S-user autorizado.
2. Navegue: **Systems & Provisioning → aba Keys → card "Development Namespaces"** (a antiga Namespace application do ONE Support Launchpad, aposentado em 2023; URL direta: me.sap.com/namespaces).
3. Clique no ícone **"+"** para adicionar namespace: informe o nome (`/XXX/`), a descrição (com referência reconhecível à empresa) e o customer number / installation number.
4. Aceite o texto legal do acordo de namespace e submeta. A SAP verifica a solicitação e, se os pré-requisitos forem cumpridos, o status vira **"Accepted"** (SAP Note 105132; fontes de terceiros citam ~3 dias úteis, sem SLA oficial).
5. **Reserve o namespace aceito**: clique no namespace e selecione **"I Agree"** (função "Reserve an accepted namespace"). ⚠️ Namespaces aceitos mas **não reservados são recusados** automaticamente após um período.
6. (On-premise) Gere as chaves: **development license key** por installation number (não transferível entre instalações) e **repair key** de 20 caracteres (transportada aos sistemas seguintes).
7. (On-premise) No sistema de desenvolvimento, registre o namespace em **SE03 → Administration → Display/Change Namespaces** (ou SM30, view `V_TRNSPACE`): crie a entrada, informe as chaves, defina o papel (`P`/producer no dev de origem; `C`/recipient nos sistemas consumidores) e marque como "Modifiable".

> Steampunk: se o namespace foi registrado na SAP **antes** do primeiro provisioning, ele é entregue automaticamente durante o provisioning do ambiente — o Maintain Namespaces só é necessário para namespaces criados depois.

<a name="fase-3"></a>
## Fase 3 — Instalação nos sistemas (Landscape Portal)

Um namespace só pode receber objetos num sistema **depois de instalado nele**. Transportes com objetos `/XXX/...` são bloqueados no import se o namespace não estiver instalado no sistema alvo.

1. No app **Maintain Namespaces**, abra a aba **Systems**. Colunas exibidas: system number, system ID, description, namespaces (installed/available), lifecycle status, system classification.
2. Clique no sistema alvo para ver os detalhes: cada namespace aparece **verde (instalado)** ou **laranja (não instalado)**.
3. **Classificação do sistema** (quando aplicável): se o sistema ainda não está classificado, o botão **Classify System** fica habilitado — selecione **Development** ou **Test** no drop-down e confirme com OK.
   - ⚠️ **A classificação só pode ser definida UMA vez e nunca pode ser alterada.** Confirme com o usuário antes.
   - Em cenários de **scalable delivery com 3SL**, os sistemas já vêm classificados pelo seu system business type — não é possível (nem necessário) classificar manualmente; a instalação só é permitida no **Partner Add-On Development System** e no **Partner Add-On Test System**.
4. Selecione o namespace com status "Not installed" (ou "Error during installation", para retry) e clique **Install Namespace**.
   - O botão só habilita se: o namespace ainda não está instalado no sistema **e** o sistema tem lifecycle status **"Live"** (e está classificado).
   - **Um namespace por vez** — instalação em massa não é possível.
5. Acompanhe o status com **Refresh**: `Not installed` → `Installing` (azul) → `Installed` (verde). Status `Error during installation` (vermelho) é recuperável: selecione e clique Install Namespace de novo. A instalação é assíncrona ("after some time") — não há SLA documentado.
6. **Repita para cada sistema** que receberá objetos do namespace (tipicamente Development e Test).
7. **Provisioning e upgrades**: quando um novo sistema é provisionado ou um sistema existente é atualizado, **todos os namespaces da conta são instalados automaticamente**. O Maintain Namespaces existe justamente para namespaces criados **depois** do provisioning, necessários antes do próximo upgrade.

> Produção: as opções de classificação documentadas são apenas Development e Test, e no scalable delivery a instalação manual é restrita aos sistemas de dev/test do parceiro. Sistemas novos/atualizados recebem namespaces automaticamente. Se um transporte para produção for bloqueado por namespace ausente entre upgrades, verifique o status no Maintain Namespaces e, persistindo, abra ticket (ver `troubleshooting.md`).

<a name="fase-4"></a>
## Fase 4 — Verificação e primeiros objetos

Após status "Installed" no(s) sistema(s):

1. **Criar o software component** no development tenant: app Fiori **Manage Software Components** → **Create** → preencha **Name** no padrão `/NAMESPACE/COMPONENT` (só maiúsculas), **Description**, **Type** (ex.: Development) → **Create** → na página de detalhes, **Clone**. A seção History mostra `Running` → `Success`; o botão Clone vira **Pull**.
2. **Conectar o ADT**: Eclipse → File → New → **ABAP Cloud Project** → opção **"SAP S/4HANA Cloud ABAP Environment"** (não "SAP BTP ABAP Environment") → URL do development tenant (client 080). O usuário precisa de business role derivada do template **`SAP_BR_DEVELOPER`**.
3. **Criar pacote** sob o software component do namespace, com objetos usando o prefixo `/XXX/`, linguagem **ABAP for Cloud Development** (só APIs liberadas — árvore "Released Objects" no Project Explorer do ADT).
4. Teste rápido de sanidade: criar um objeto qualquer (ex.: uma classe `/XXX/CL_TEST`) e verificar que o ADT aceita o prefixo. Se o ADT recusar a criação por namespace inexistente, o namespace não está instalado nesse sistema — volte à Fase 3.

<a name="fase-5"></a>
## Fase 5 — Key user extensibility no namespace (opcional, cenário add-on)

Se o add-on vai conter itens de key user extensibility (ex.: custom fields):

1. No **Maintain Namespaces**, use o botão **Activate Key User Extensibility** para o namespace → um **software component dedicado** exclusivo para key user extensions é criado automaticamente.
2. **Atribua os (key) users ao namespace** — sem isso, os custom fields continuam nascendo com prefixo `YY1_` e **não podem** ser incluídos no add-on.
3. Custom fields são criados/mantidos no app **Custom Fields** no **customizing tenant (client 100)** — nunca via ADT no client 080 (é um "don't" documentado pela SAP).

Referências: SAP Note 2787809 (template namespace para key user items), blog SAP "Dos and Don'ts: Key User Custom Fields in Add-Ons Based on SAP S/4HANA Cloud Public Edition".

<a name="transportes"></a>
## Transportes e o ciclo D → T → P

- Transportes de developer extensibility são workbench requests criados e liberados no **Transport Organizer do ADT** (não existe SE09/SE10 no public cloud).
- Import no test e forward para produção: app Fiori **Import Collection**. Customizing (CBC/BC) é exportado pelo app **Export Customizing Transports**.
- ⚠️ Transportes devem ser exportados/importados **na sequência em que foram criados**.
- **Pré-condição de namespace**: o namespace deve estar instalado no sistema alvo ANTES do import de qualquer transporte contendo objetos dele. Sintoma clássico de violação: import falha no Test com "namespace não existe" — solução na Fase 3.
- KBAs úteis: 3401738 (FAQ Transport Management público), 3482742 (KBA central), 3126032 (software collections FAQ), 2914822 (desenvolver e exportar extensões).

## Cenário parceiro: do namespace ao add-on entregável

Sequência macro após o namespace instalado e o desenvolvimento pronto:
1. **Register Product** (app do Landscape Portal) — registra o produto e global accounts.
2. **Maintain Credentials** — credenciais usadas nos estágios do build (certificado PSE por SAP Note 2805811; secrets tipo "Secret Text").
3. **Build Product Version** — build via pipelines AAKaaS/CI-CD baseados em templates (releases, support packages, patches). Usuário técnico de comunicação: SAP Notes 2532813 / 2174416 — deve estar no mesmo customer number do namespace.
4. **Check Product Version** → **Deploy Product** — verificação de prontidão e entrega; o cliente registra e instala o add-on pelo próprio Landscape Portal.
5. Naming do produto: máx. 30 caracteres, padrão `/NAMESPC/PRODUCTX`; software components `/NAMESPACE/COMPONENT` (maiúsculas).
