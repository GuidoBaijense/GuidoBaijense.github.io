---
id: 314
title: 'Privileged Identity Management: Waarom je dit moet inzetten in je omgeving'
date: '2022-07-26T21:50:45+02:00'
author: Guido
layout: post
guid: 'https://pentiago365.nl/?p=314'
permalink: /privileged-identity-management-waarom-je-dit-moet-inzetten-in-je-omgeving/
categories:
    - 'Microsoft Entra ID'
    - 'Identity Governance'
    - 'Microsoft 365'
tags:
    - 'entra'
    - 'microsoft 365'
    - 'privileged identity management'
---

In deze blog uit de serie over **Privileged Identity Management (PIM)** ga ik in op wat PIM is en waarom je PIM móet inzetten wanneer je streeft naar een veilige cloud-omgeving. Vaak zijn organisaties organisaties huiverig voor het inzetten van PIM, maar de voordelen wegen zeker op tegen de potentiële nadelen.

Privileged Identity Management is een functionaliteit waarmee je toegang tot belangrijke resources en rechten kunt beheren, controleren en monitoren. De functie is beschikbaar voor Azure Active Directory, maar ook te gebruiken voor Azure resources en diensten als Microsoft 365 en Intune.

#### Key features:

- Provide **just-in-time** privileged access to Azure AD and Azure resources
- Assign **time-bound** access to resources using start and end dates
- Require **approval** to activate privileged roles
- Enforce **multi-factor authentication** to activate any role
- Use **justification** to understand why users activate
- Get **notifications** when privileged roles are activated
- Conduct **access reviews** to ensure users still need roles
- Download **audit history** for internal or external audit
- Prevents removal of the **last active Global Administrator** and **Privileged Role Administrator** role assignments  
    <sub>source:</sub> [<sub>What is Privileged Identity Management? – Azure AD – Microsoft Entra | Microsoft Docs</sub>](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-configure#what-does-it-do)

## Just-in-time

PIM sluit aan op het principe van *Just-In-Time- (JIT)* en *Least Privilege Access*. Hierbij werk je in de basis zonder beheerdersrollen en activeer je alleen rechten en toegang die nodig zijn voor het werk dat je op dat moment wilt doen. Just-in-time is een van de basis *security principes* en daarbij ook de eerste reden voor het inzetten van PIM in je Microsoft Cloud omgeving.

Standing Access wordt geëlimineerd door een goede inrichting van PIM. [Gartner](https://www.gartner.com/smarterwithgartner/the-4-pillars-of-privileged-access-management) noemt doorvoeren van JIT één van de 4 pilaren van Privileged Access Management. Met PIM is het mogelijk om toegekende rechten te laten verlopen na een vastgestelde periode, waardoor rechten niet 24/7 actief blijven.

Met *Least Privilege Access* sluit je hierop aan door alleen de rechten te gebruiken die nodig zijn voor de handeling die je wilt uitvoeren. De Global Administrator rol is de allergrootste rol in Azure Active Directory. Veel admins werken standaard met deze rol actief, terwijl bijna alle handelingen met minder rechten kunnen worden uitgevoerd.

Wanneer je werkt met zo min mogelijk rechten voor de handeling die je uitvoert, beperk je de kans op fouten, maar verminder je ook het potentiële aanvalsoppervlak voor een hacker of cybercrimineel.

## Approval en Justification

Wanneer je het beheerwerk hebt verdeeld onder verschillende medewerkers of zelfs teams, kan het zijn dat je niet wilt dat iedereen alle mogelijke handelingen in de Azure Tenant kan uitvoeren. Hierbij kun je er voor kiezen om niet alle admin-rollen aan iedereen beschikbaar te stellen of aanvullende eisen te stellen voordat een rol geactiveerd wordt.

Om terug te komen op de Global Administrator rol: Er kunnen goede redenen zijn waarom iemand deze rol nodig heeft, maar omdat deze rol erg veel rechten geeft wil je vastgelegd hebben waarvoor de rol geactiveerd wordt, of zelfs een goedkeuring vereisen voordat de rol actief kan worden. Dit is allemaal mogelijk met PIM.

Aanvullend kun je instellen dat er notificaties verstuurd worden naar bepaalde gebruikers op het moment dat specifieke rollen geactiveerd worden. Dit zorgt ervoor dat je scherp blijft op het gebruik van *over powered* rollen en elkaar hierop aan kunt spreken.

## Tot slot

Door het gebruik van Privileged Identity Management beperk je het aanvalsoppervlak, heb je uitgebreide logging mogelijkheden rondom het gebruik van admin-rollen en wordt je optioneel op de hoogte gehouden wanneer privileged rollen worden ingezet.

Heb je vragen over PIM, hoe je dit succesvol implementeert, of wil je gewoon kletsen over Microsoft technologie? Weet mij te bereiken via [LinkedIn](https://linkedin.com/in/guidobaijense) of een reactie hieronder.

De volgende blog in deze [serie](/tags/privileged-identity-management/) zal gaan over wat er nodig is om PIM te kunnen gebruiken. Deze blog verschijnt binnenkort.