---
id: 32
title: 'Tijdzonecorrectie met Power Query M en DAX'
date: '2020-06-11T16:56:49+02:00'
author: Guido
layout: post
guid: 'http://pentiago-365.local/?p=32'
permalink: /tijdzonecorrectie-met-power-query-m/
image: /assets/img/2020/06/time-function.png
categories:
    - Excel
    - 'Power Bi'
    - 'Power Query'
tags:
    - Excel
    - M
    - 'Power Query'
---

*Visualiseren van meetwaarden gaat niet goed wanneer bij metingen op hetzelfde moment niet dezelfde tijdstippen (timestamps) staan genoteerd. Er zijn verschillende manieren om een tijdzonecorrectie door te voeren met Power Query M en DAX. In de video en blogpost laat ik er drie zien.*

Onlangs werd ik door een collega gevraagd om twee databronnen met logging te combineren. Er waren wat uitdagingen met betrekking tot [performance van de werkplek](/tijdstippen-groeperen-in-power-bi/) en door inzicht te krijgen in meetwaarden en logbestanden werd gehoopt meer grip te krijgen op de situatie.

De datum- en tijdnotatie in de logbestanden verschilden van elkaar aangezien deze van verschillende servers in verschillende tijdzones afkomsting waren. De kans is groot dat je zoiets hebt meegemaakt wanneer je werkt met een complexe infrastructuur.

{% include embed/youtube.html id='3tjkWnFQ9-Y' %}
_Tijdzonecorrectie met Power Query M in Excel of Power BI – Pentiago 365 op YouTube_

### DAX versus Power Query M

Wanneer je geen verdere verwerking van de gegevens hoeft te doen en de volgende stap visualisatie in Power BI is, is de beste en snelste methode om DAX te gebruiken. Het aanpassen van alle waarden in een kolom heeft negatieve impact op de duur van de query. Probeer dit daarom zo laat mogelijk in je verwerkingsproces te doen en het liefst pas na de query.

Power Query M is bedoeld om de data te vervormen tot een werkbaar formaat. DAX is met name de taal en methode om daadwerkelijk te gaan werken met de gegevens. Meer informatie hierover lees je in [deze post van radacad.](https://radacad.com/m-or-dax-that-is-the-question)

### Tijdzonecorrectie in DAX

Om de tijdzone te corrigeren met behulp van DAX in Power BI schakel je over naar de dataview.

![Dataview icon](assets/img/2020/06/dataview-icon.png){: width="41" height="39" }
_Dataview icon_

**1.** Klik hiervoor op het icoon voor de dataview.

**2.** Klik daarna op het tabblad Table tools en kies voor New column.

![asdf](/assets/img/2020/06/new-column.png)
_Tabblad Table tools – New column_
**3.** In de formulebalk die nu verschijnt type je allereerst de naam voor de nieuwe kolom met daarachter ‘= \[kolomnaam met huidige datum\]+TIME(aantal uren vooruit, 0, 0)

In dit geval is de naam van de kolom die ik wil corrigeren **\[CollectedDate\]** en gaat het om een verschil van 2 uur. Wil je terug in de tijd? Kies dan voor een negatief getal.

**4.** Klik op de V van accepteren of druk op enter. In de nieuwe kolom staat nu de gecorrigeerde tijd.

![Time Function in Power BI](/assets/img/2020/06/time-function.png)
_New column met de formule + TIME(Hour, Minute, Second)
_
Dit is de meest simpele methode en voegt alleen een bepaald aantal uren toe aan de waarde. Een betere methode is om aan het datamodel de juiste tijdzone toe te voegen en deze daarna in de lokale tijdzone weer te geven.

### Tijdzonecorrectie in Power Query M

![image](/assets/img/2020/06/timestamp.png)
_Basis Datum/Tijd waarde (timestamp_

In een simpele databron staat wellicht wel een datum/tijd waarde, maar zit in deze waarde geen tijdzone verwerkt.

Wanneer je weet in welke tijdzone de gegevens zijn geregistreerd is het gemakkelijk om dit toe te voegen aan de waarde. Wanneer je dit bij alle datum/tijd-kolommen doet, kun je daarna alles weergeven in de lokale tijdzone.

![image](/assets/img/2020/06/new-column-2.png)
_Kolom toevoegen in Power Query_
**1.** Open de Query Editor, kies de juiste query en voeg een nieuwe kolom toe. Kies hiervoor het tabblad ‘Kolom toevoegen’ en daarna de knop ‘Aangepaste kolom’.

Het scherm wat daarna opent wordt hieronder weergegeven:

![image](/assets/img/2020/06/new-column-window.png)
_Nieuwe aangepaste kolom met de formule voor tijdzonecorrectie_

**2.** Geef de nieuwe kolom een naam en typ deze formule in het formulevak:

```
= DateTime.AddZone([kolomnaam], oorspronkelijke tijdzone in uren)
```

In mijn geval gaat het om een ‘timestamp’ vanuit de zone GMT+0. Daarom heb ik een 0 als tijdzonewaarde ingevuld. Indien je een waarde hebt uit de Nederlandse zomertijd, vul je een 2 in.

**3.** Kies voor OK. Een nieuwe kolom is met de gekozen tijdzone is toegevoegd:

![image](/assets/img/2020/06/timezonetimestamp.png)
_Datum/tijd-waarde met tijdzone_

In veel gevallen ben je nu klaar. Power Query begrijpt het principe van tijdzones, waardoor je nu kunt rekenen met verschillende waarden. Met één simpele knop kun je de waarden nog omzetten naar je lokale tijd.

**4.** Klik op het tabblad ‘Transformeren’ en kies voor ‘Tijd’ en dan ‘Lokale tijd’.

![image](/assets/img/2020/06/localtimegif.gif)

### Tijd toevoegen aan datum/tijd waarde in Power Query M

Een derde methode om tijd te corrigeren is ook aan de hand van Power Query. Tijdzonenotaties toevoegen aan waarden kan ook minder overzichtelijk werken. Met deze methode voeg je alleen tijd toe. Dit kan ook een negatieve waarde zijn waardoor je terug gaat in tijd.


**1.** Open de Query Editor, kies de juiste query en voeg een nieuwe kolom toe. Kies hiervoor het tabblad ‘Kolom toevoegen’ en daarna de knop ‘Aangepaste kolom’.

![image](/assets/img/2020/06/new-column-2.png)
_Kolom toevoegen in Power Query_

Het scherm wat daarna opent wordt hieronder weergegeven:

![image](/assets/img/2020/06/newcolumn-duration.png)
_Nieuwe aangepaste kolom met de formule voor het toevoegen van tijd_
**2.** Geef de nieuwe kolom een naam en typ deze formule in het formulevak:

```
= [CollectedDate] + #duration(0,2,0,0)
```

In dit geval is \[CollectedDate\] de oorspronkelijke kolom. Aan de hand van de formule `#duration(dagen,uren,minuten,seconden)` voeg je een tijdsduur toe aan de oorspronkelijke tijdswaarde.

**3.** Kies voor OK. Een nieuwe kolom is met de aangepaste tijd is toegevoegd.

<!-- #### Misschien ook interessant?

- ![image](/assets/img/2024/01/wordtgeendinges-blijfsmsen-150x150.png)[Lifecycle Workflows: Stuur informatie per SMS naar je eindgebruikers](/lifecycle-workflows-stuur-informatie-per-sms-naar-je-eindgebruikers/)<div class="wp-block-latest-posts__post-excerpt">In deze blog laat ik zien hoe je Custom Extensions in Microsoft Entra Identity Governance (Lifecycle workflows &amp; access packages) kunt gebruiken om informatie uit je Identity Governance proces naar een externe API te sturen. Hiermee zijn de mogelijkheden eindeloos en is er altijd een passende oplossing om jouw proces optimaal te kunnen laten werken. Een tijdje geleden gaf ik tijdens een evenement voor klanten een demo waarin ik liet zien hoe je de functionaliteit van Microsoft Entra Identity Governance kunt uitbreiden met het versturen van op maat gemaakte brieven naar je eindgebruikers via PostNL. Deze demo was bedoeld om de geest te prikkelen en de […]
- [Lifecycle Workflows: Een efficiëntere IT onboarding](/efficientere-onboarding-met-lifecycle-workflows/)<div class="wp-block-latest-posts__post-excerpt">Het is moeilijk om aan goed personeel te komen. Heb je eenmaal mensen gevonden, dan moeten die ook zo snel mogelijk aan het werk. Met het nieuwe Lifecycle Workflows in Microsoft’s Azure AD verloopt de IT onboarding van je medewerkers efficiënter. Dit zorgt niet alleen voor een goede start van je nieuwe collega’s, maar ook voor minder werk voor de IT-afdeling. VerantwoordingDeze blog schreef ik eerder voor de website van mijn werkgever InSpark. Aangezien ik meer wil schrijven over Lifecycle Workflows, leek me dit een goede introductie in het onderwerp. Binnenkort volgt een blog over het geautomatiseerd aanmaken van accounts […]
- [Entra: Een rebranding?](/is-entra-alleen-een-rebranding/)<div class="wp-block-latest-posts__post-excerpt">Een nieuwe naam Entra is volgens Microsoft geen rebranding maar een hoog nodige overkoepelende naam voor alles wat te maken heeft met Identity binnen het security portfolio van Microsoft. Dit komt overeen met de namenstrategie die gehanteerd wordt binnen Microsoft Security waarbij we de afgelopen jaren al meerdere nieuwe namen langs hebben zien komen. Identity Security Compliance Privacy Management Microsoft Entra Microsoft Defender Microsoft Purview Microsoft Priva Microsoft Endpoint Manager Microsoft Sentinel Microsoft Security Product Categorieën Er zit een centrale gedachtegang achter deze nieuwe naamgeving. Onze IT omgevingen blijven uitbreiden en er komen steeds meer toegangspunten […]
- [Privileged Identity Management: Waarom je dit moet inzetten in je omgeving](/privileged-identity-management-waarom-je-dit-moet-inzetten-in-je-omgeving/)<div class="wp-block-latest-posts__post-excerpt">In deze blog uit de serie over Privileged Identity Management (PIM) ga ik in op wat PIM is en waarom je PIM móet inzetten wanneer je streeft naar een veilige cloud-omgeving. Vaak zijn organisaties organisaties huiverig voor het inzetten van PIM, maar de voordelen wegen zeker op tegen de potentiële nadelen. Privileged Identity Management is een functionaliteit waarmee je toegang tot belangrijke resources en rechten kunt beheren, controleren en monitoren. De functie is beschikbaar voor Azure Active Directory, maar ook te gebruiken voor Azure resources en diensten als Microsoft 365 en Intune. Key features: Provide just-in-time privileged access to Azure AD and […]
- [Privileged Identity Management: Vereiste voor een veilige omgeving](/privileged-identity-management-vereiste-voor-een-veilige-omgeving/)<div class="wp-block-latest-posts__post-excerpt">Veel organisaties maken gebruik van Microsoft Azure Active Directory voor het managen van hun cloud identities. Het beheer van deze omgeving is verdeeld over meerdere personen en de rechten die nodig zijn voor dit beheer zijn permanent toegekend. Met het toekennen van rechten, introduceer je een veiligheids risico. Dit risico kun je beperken door gebruik te maken van Privileged Identity Management (PIM) in Azure AD. De komende tijd zal ik schrijven over zaken die te maken hebben met PIM. In deze serie wil ik ingaan op: Waarom je Privileged Identity Management moet inzetten Wat er nodig is om PIM te […] -->