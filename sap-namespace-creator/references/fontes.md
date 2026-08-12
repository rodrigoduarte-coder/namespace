# Fontes oficiais e leituras de apoio

## Páginas SAP Help centrais (base desta skill)

| Página | URL |
|---|---|
| **Maintain Namespaces** (Landscape Portal, S/4HANA Cloud ABAP environment) — página-base 1 | https://help.sap.com/docs/LANDSCAPEPORTAL_S4ABAP/bbef3473740144d38c8485d26871a1f0/59e9ddee17ee496fa14e2760c78bf9da.html |
| **About Namespaces** (SAP S/4HANA Cloud, guia de developer extensibility) — página-base 2 | https://help.sap.com/docs/SAP_S4HANA_CLOUD/6aa39f1ac05441e5a23f484f31e477e7/5a0693aa21b9428d846db3a284262802.html |
| Landscape Portal for SAP S/4HANA Cloud ABAP environment (visão geral do guia) | https://help.sap.com/docs/LANDSCAPEPORTAL_S4ABAP/bbef3473740144d38c8485d26871a1f0/b28b1d7e8919446e8a3b54cd54f74538.html |
| Setting Up the Landscape Portal for SAP S/4HANA Cloud, ABAP environment | https://help.sap.com/docs/SAP_S4HANA_CLOUD/6aa39f1ac05441e5a23f484f31e477e7/ea25cba1d0b74e1d9df72708596029af.html |
| Setting Up the Landscape Portal as a Partner | https://help.sap.com/docs/LANDSCAPEPORTAL_S4ABAP/bbef3473740144d38c8485d26871a1f0/005176e2e42b49b798709649cf20ea3f.html |
| Maintain Namespaces (variante SAP BTP ABAP Environment / Steampunk) | https://help.sap.com/docs/sap-btp-abap-environment/landscape-portal/maintain-namespaces |
| Developer Extensibility (Public Edition) | https://help.sap.com/docs/SAP_S4HANA_CLOUD/6aa39f1ac05441e5a23f484f31e477e7/1f353cdf00f44f23bd8a8006bef2cd4e.html |
| Maintain Credentials (Landscape Portal) | https://help.sap.com/docs/LANDSCAPEPORTAL_S4ABAP/bbef3473740144d38c8485d26871a1f0/e02cfd68efc5436b9e7d511ec3fa95e6.html |
| Feature Scope Description (PDF do guia completo, 2024-12) | https://help.sap.com/doc/08a1c025506340bdbab3a7383a3385af/SHIP/en-US/40894e45e5264c89a2280f5abb7124d3.pdf |

> A mesma página "Maintain Namespaces" (loio `59e9ddee17ee496fa14e2760c78bf9da`) existe em dois doc sets: `LANDSCAPEPORTAL_S4ABAP` (S/4HANA Cloud Public Edition, cenário parceiro/add-on) e `ABAP_ENVIRONMENT`/`sap-btp-abap-environment` (Steampunk). O app é o mesmo; o entorno (acesso, classificação, delivery) difere.

## Reserva de namespace na SAP

- SAP for Me — Namespace application: https://me.sap.com/namespaces (antigo launchpad.support.sap.com/#/namespaces; caminho: Systems & Provisioning → Keys → Development Namespaces)
- Espelhos públicos da documentação BTP (GitHub SAP-docs, conteúdo integral das páginas): https://github.com/SAP-docs/btp-cloud-platform/blob/main/docs/30-development/register-a-namespace-cc5a3c6.md e .../maintain-namespaces e .../access-to-landscape-portal-195a685.md
- Wiki SAP Support: Development Namespaces for Customers and Partners — https://wiki.scn.sap.com/wiki/display/SMP/Development+Namespaces+for+Customers+and+Partners

## Tutoriais e walkthroughs práticos

- SAP-samples, cross-stack partner reference extension — passo a passo real de instalação de namespace + criação de software component: https://github.com/SAP-samples/cross-stack-partner-reference-extension/blob/main/Tutorials/1-CreateSoftwareComponent.md
- Série de tutoriais SaaS add-on (developers.sap.com): abap-environment-saas-overview, abap-environment-saas-addon-setup, abap-environment-saas-addon-build, abap-environment-saas-addon-configure
- Project Piper — cenário abapEnvironmentAddons (naming de produto/software component): https://www.project-piper.io/scenarios/abapEnvironmentAddons/

## Blogs SAP Community relevantes

- Natively ABAP Cloud (Clean Core) Add-ons in SAP S/4HANA Cloud Public Edition (requisitos de certificação, release 2408.4): https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/natively-abap-cloud-clean-core-add-ons-in-sap-s-4hana-cloud-public-edition/ba-p/14093908
- Extensibility: General Availability of Scalable Solution for SAP S/4HANA Cloud (GA 10/12/2024): https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/extensibility-general-availability-of-scalable-solution-for-sap-s-4hana/ba-p/13963394
- Developer Extensibility in SAP S/4HANA Cloud ABAP Environment: https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/developer-extensibility-in-sap-s-4hana-cloud-abap-environment/ba-p/13558432
- Dos and Don'ts: Key User Custom Fields in Add-Ons (key user extensibility × namespace): https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/dos-and-don-ts-key-user-custom-fields-in-add-ons-based-on-sap-s-4hana-cloud/ba-p/14340833
- Transport Management in SAP S/4HANA Cloud Public Edition 3-System Landscape: https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/transport-management-in-sap-s-4hana-cloud-public-edition-3-system-landscape/ba-p/13625058
- BTP Landscape Portal for lifecycle management operations (walkthrough com screenshots): https://community.sap.com/t5/technology-blog-posts-by-members/btp-landscape-portal-for-lifecycle-management-operations/ba-p/13573420
- Partner TDD Onboarding — Guide for Common Issues: https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/partner-tdd-onboarding-for-sap-s-4hana-cloud-public-edition-guide-for/ba-p/13575249
- Creating your own namespace — how? (fluxo clássico com prints): https://blogs.sap.com/2018/09/19/creating-your-own-namespace-how/

## Limitações conhecidas desta base de conhecimento

- As **regras exatas exibidas dentro do diálogo "Request Namespace"** (conjunto de caracteres, limites do campo descrição) e o **texto da notice de confirmação** não estão indexados publicamente — só aparecem no app. Sempre leia as regras do diálogo com o usuário antes de submeter.
- Não há SLA/duração oficial documentada para a instalação de namespace ("Installing" → "Installed").
- A URL exata do Landscape Portal do Public Edition é fornecida no onboarding do parceiro; padrão não documentado publicamente.
- Semântica de instalação no sistema de **produção** do 3SL: documentação cobre instalação manual só em Dev/Test; produção recebe namespaces automaticamente em provisioning/upgrade. Casos-limite entre upgrades: validar com SAP.
