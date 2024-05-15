---
id: 371
title: 'Lifecycle Workflows: Een efficiëntere IT onboarding'
date: '2022-09-20T21:49:03+02:00'
author: Guido
layout: post
guid: '/?p=371'
permalink: /efficientere-onboarding-met-lifecycle-workflows/
categories:
    - 'Microsoft Entra ID'
    - 'Identity Governance'
    - 'Microsoft 365'
tags:
    - 'entra'
    - 'graph api'
    - 'microsoft 365'
---

Het is moeilijk om aan goed personeel te komen. Heb je eenmaal mensen gevonden, dan moeten die ook zo snel mogelijk aan het werk. Met het nieuwe Lifecycle Workflows in Microsoft’s Azure AD verloopt de IT onboarding van je medewerkers efficiënter. Dit zorgt niet alleen voor een goede start van je nieuwe collega’s, maar ook voor minder werk voor de IT-afdeling.

>Deze blog schreef ik eerder voor de website van mijn werkgever InSpark. Aangezien ik meer wil schrijven over Lifecycle Workflows, leek me dit een goede introductie in het onderwerp. Binnenkort volgt een blog over het geautomatiseerd aanmaken van accounts in een (andere) Microsoft Azure (development) omgeving wanneer in de productie tenant een nieuw account wordt aangemaakt.
{: .prompt-info}

*[Lifecycle Workflows](https://learn.microsoft.com/en-us/azure/active-directory/governance/what-are-lifecycle-workflows)* (LCW) zijn de nieuwste ontwikkeling van Microsoft op het gebied van Identity Governance. Eerder schreef [Pim Jacobs](https://identity-man.eu) al over het belang van HR als centrale waarheid voor je identiteiten en het geautomatiseerd aanmaken van accounts. Met Lifecycle Workflows ga je een stap verder. Je krijgt hiermee de mogelijkheid om geautomatiseerd acties uit te voeren in Microsoft 365 en Azure AD voordat de indienstdatum wordt bereikt.

#### Wat is Identity Governance?

Identity Governance is de overkoepelende term voor alles waarmee je *in control* blijft van de identiteiten in je omgeving. Het omvat de volledige Joiner-Leaver-Mover lifecycle en kijkt naar hoe identiteiten worden aangemaakt, hoe rechten verkregen en weer ingetrokken worden, maar ook naar hoe je kunt aantonen dat een medewerker niet te veel rechten heeft.

![image](/assets/img/2022/09/image-16.png)
_Joiner-Mover-Leaver lifecycle_
Eerder schreef onze principal consultant Pim Jacobs al over [Identity Governance](https://www.inspark.nl/de-8-pijlers-voor-het-bepalen-van-jouw-identity-governance-strategie/) en ook in de [webinar](https://www.youtube.com/watch?v=r172NtSH6fI) van consultants Guus en Guido hebben we laten zien welke mogelijkheden Microsoft in Azure AD biedt rondom dit onderwerp.


Met Lifecycle Workflows gaat de IT onboarding van een medewerker efficiënter door de medewerker automatisch toe te voegen aan bijvoorbeeld de correcte Microsoft Teams en applicaties. Je kunt op het juiste moment licenties verstrekken of een welkomstmail versturen. Verder zijn door het koppelen van logic apps aan Lifecycle Workflows de mogelijkheden eindeloos.

### Hoe het werkt

Lifecycle Workflows zijn gebaseerd op zogenaamde ‘events’. Vandaag wordt er slechts gekeken naar twee waarden in de eigenschappen van een account: de datum van indiensttreding óf de datum van uitdiensttreding.

In de workflow configureer je vervolgens hoeveel dagen voor of na de gekozen categorie (*joiner* of *leaver*) de set aan acties moet worden uitgevoerd. Het is ook mogelijk om de volgorde van deze acties te bepalen. Op het moment dat de indiensttreding of uitdiensttreding in zicht komt wordt de workflow vanzelf uitgevoerd. Verder is het mogelijk om workflows ‘on demand’ te starten.

![Overzicht van de werking van Lifecycle Workflows](/assets/img/2022/09/image-15-1024x383.png)
_Overzicht van de werking van Lifecycle Workflows_

### Beperkingen aan Lifecycle Workflows

- Lifecycle Workflows vind je in de Azure AD portal onder Identity Governance. Hier is het nu alleen nog maar mogelijk om vooraf gedefinieerde templates te gebruiken. Door gebruik te maken van de [Microsoft Graph API](https://learn.microsoft.com/en-us/graph/api/resources/identitygovernance-lifecycleworkflows-overview?view=graph-rest-beta) kun je overigens wel zelf templates samenstellen.

- Je kunt nu alleen nog maar workflows aanmaken in twee categorieën: *‘joiner*‘ bij indiensttreding of *‘leaver’* bij uitdiensttreding. Wij hopen erg op een *‘mover’* categorie om de gehele lifecycle te kunnen bedienen.

- Voor [Identity Governance functionaliteiten](/categories/identity-governance/) is een Azure AD Premium P2 licentie vereist. Dit geldt dus ook voor het gebruik van Lifecycle Workflows.
