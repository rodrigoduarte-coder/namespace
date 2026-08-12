# Troubleshooting — erros comuns na criação e instalação de namespaces

Diagnóstico por sintoma. Componentes de suporte: **XX-SER-DNSP** (Development Namespaces for Customers and Partners — reserva/registro) e **BC-CP-ABA** (plataforma ABAP cloud — instalação/Landscape Portal). O Landscape Portal também tem criação de ticket embutida.

## Na reserva (Request Namespace / SAP for Me)

| Sintoma | Causa provável | Solução |
|---|---|---|
| *"License missing. Your company is missing a development license to access this page"* | Empresa sem development license em contrato | Verificar contrato (ABAP Workbench User, Standalone Development Systems, SAP Developer User etc.). KBA 2581772 |
| Criação de namespace indisponível apesar de haver licença | Contrato de development license registrado em **outro customer number**; ou customer number não liberado para funções corporativas sob o corporate customer number | Solicitar sob o customer number do contrato — SAP Order Management identifica qual é (KBA 1660069). KBA 3113391 |
| Validação rejeita o User ID no diálogo do Landscape Portal | S-user sem a autorização **"Reserve Namespaces"** (Note 1271482) ou não pertencente ao parceiro | Pedir a autorização ao administrador de usuários da empresa; usar S-user do customer number do parceiro |
| *"Namespace already exists"* / nome rejeitado por duplicidade | Unicidade é **global** — o nome já foi registrado por alguém no mundo, mesmo que nunca usado por você | Escolher outro nome; validar com `regras-nomenclatura.md` |
| Nome rejeitado por formato | Viola as regras do diálogo (comprimento 5–10 com barras, caracteres, etc.) | Corrigir conforme checklist de `regras-nomenclatura.md` |
| (SAP for Me) Namespace sumiu depois de "Accepted" | Namespace aceito não foi **reservado** ("I Agree") e expirou | Repetir a solicitação e concluir a reserva imediatamente (Note 105132) |
| Precisa deletar/renomear namespace reservado | Renomeação **não existe**; deleção só em casos restritos | KBA 2098877 (deleção/decline), KBA 3004591 (troca de owner). Dentro de um sistema, namespace com objetos não pode ser removido — deletar todos os objetos antes (KBA 2320690) |

## Na instalação (Maintain Namespaces → Install Namespace)

| Sintoma | Causa provável | Solução |
|---|---|---|
| Botão **Install Namespace** desabilitado | (a) namespace já instalado; (b) sistema sem lifecycle status "Live"; (c) sistema não classificado | Conferir status do sistema na aba Systems; classificar se o botão Classify System estiver habilitado (⚠️ irreversível) |
| Botão **Classify System** ausente em 3SL | Cenário scalable delivery: sistemas já classificados pelo system business type | Normal — instalar apenas no Partner Add-On Development System e Partner Add-On Test System |
| Status **"Error during installation"** (vermelho) | Falha assíncrona da instalação | Selecionar o namespace e clicar Install Namespace novamente (retry documentado). Persistindo, ticket em BC-CP-ABA ou via ticket do próprio portal |
| Instalação parece travada em "Installing" (azul) | Processo assíncrono, sem SLA documentado | Usar Refresh e aguardar; planejar a instalação antes da janela de upgrade, não tratá-la como instantânea |
| Namespace não aparece no sistema recém-criado | Improvável — provisioning instala todos os namespaces automaticamente | Refresh no app; conferir se a reserva foi concluída de fato |

## No desenvolvimento e transporte

| Sintoma | Causa provável | Solução |
|---|---|---|
| ADT recusa criar pacote/objeto `/XXX/...` no dev | Namespace não instalado no sistema de desenvolvimento | Maintain Namespaces → instalar no dev (Fase 3 do processo) |
| Import do transporte falha no Test/Prod: namespace inexistente | Namespace criado **após** o provisioning e não instalado no sistema alvo — pitfall clássico | Instalar o namespace no sistema alvo ANTES de reimportar |
| *"Superpackage ZCUSTOM_DEVELOPMENT is not a valid package"* / pacote não existe | (a) sistema sem transport target configurado (KBA 3330453); (b) landscape Partner TDD, onde a hierarquia é `ZPARTNER` | Confirmar o landscape e o pacote-estrutura correto |
| Sem autorização para criar objetos no ADT | Falta business role do template `SAP_BR_DEVELOPER` no client 080 | Atribuir a role; KBAs 3237141 e 3297889 |
| Custom fields nascem com `YY1_` e não entram no add-on | Key user extensibility não ativada para o namespace / usuário não atribuído ao namespace | Maintain Namespaces → **Activate Key User Extensibility** + atribuir usuários; criar campos no app Custom Fields do client 100 |
| Transporte recusado por sequência | Import fora da ordem de criação | Importar na sequência original (Import Collection) |
| (On-premise) *"No valid change license available for namespace"* | License key do namespace ausente/inválida em `V_TRNSPACE` | Reinserir a chave (SE03); KBA 2503211 |

## Regras de ouro na condução

1. **Nunca** deixe o usuário submeter um nome sem passar pelo checklist de `regras-nomenclatura.md` — não há undo.
2. **Nunca** classifique um sistema sem confirmação explícita do usuário — é imutável.
3. Namespace criado depois do go-live do landscape? Instale **em todos os sistemas** que receberão transportes ANTES de liberar o primeiro transporte.
4. Reserve o namespace **o mais cedo possível** no projeto — idealmente antes do primeiro provisioning (Steampunk) ou logo no início do onboarding (Public Edition partner), para aproveitar a instalação automática.
5. Não confunda os dois Landscape Portals (Public Edition × BTP ABAP Environment) — pré-requisitos de acesso diferentes, mesma app Maintain Namespaces.

## SAP Notes e KBAs de referência rápida

| Nota/KBA | Assunto |
|---|---|
| 105132 | Reserva de namespaces (nota-mestre clássica) |
| 395083 | Restrições de comprimento de namespace |
| 84282 | Conceito: development namespaces para clientes e parceiros |
| 16466 | Customer name range (Z*/Y*) |
| 1271482 | Autorização S-user "Reserve Namespaces" |
| 2006427 | Como solicitar development namespace no SAP for Me |
| 2581772 | Erro "License missing" |
| 3113391 | Razões de falha na criação de namespace |
| 1660069 | Identificar customer number do contrato |
| 2098877 | Deleção/decline de namespace reservado |
| 3004591 | Troca de owner do namespace |
| 2320690 | Remoção de vendor namespace |
| 3477044 | Open Source Namespace |
| 2822365 | Generation namespaces (BW) |
| 2503211 | "No valid change license available for namespace" (on-prem) |
| 2309060 | SSCR keys não existem em S/4HANA (não confundir com namespace keys) |
| 2787809 | Template namespace para key user extensibility |
| 2914822 | Desenvolver e exportar extensões (Public Edition) |
| 3330453 | ZCUSTOM_DEVELOPMENT ausente no dev tenant 3SL |
| 3401738 / 3482742 / 3126032 | Transport management no Public Cloud (FAQs/central) |
| 2532813 / 2174416 | Usuário técnico de comunicação (AAKaaS) |
| 2805811 | Certificado PSE (Maintain Credentials) |
| 3237141 / 3297889 | Erros de autorização ao criar objetos no ADT |
