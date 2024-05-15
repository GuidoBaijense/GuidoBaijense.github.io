---
id: 83
title: 'Filter op tijd in Power BI met een Slicer'
date: '2020-06-17T17:10:18+02:00'
author: Guido
excerpt: 'Gebruik een uur-getal in plaats van een tijdswaarde om de slicer in Power Bi te kunnen gebruiken voor het filteren op tijd.'
layout: post
guid: 'http://pentiago-365.local/?p=83'
permalink: /filter-op-tijd-in-power-bi-met-een-slicer/
image: /assets/img/2020/06/new-column-uur.png
categories:
    - 'Power Bi'
    - 'Power Query'
tags:
    - microsoft
    - 'power bI'
    - slicer
    - tijd
---

Filteren van datum- en tijdsgegevens met een Slicer met tijd in Power BI gaat standaard niet. Power BI maakt van de tijd een datum. Dit zorgt ervoor dat de slicer niet geschikt is voor het filteren op bijvoorbeeld een gedeelte van de dag. In deze blog laat ik zien hoe je met een simpele aanpassing aan je brongegevens de slicer wél bruikbaar kunt maken voor het filteren van tijd.

Microsoft Power Bi draait om het visualiseren van data. In [veel van mijn rapporten](/tijdstippen-groeperen-in-power-bi/) komen visualisaties voor van waarden die zijn afgezet [tegenover datum en tijd.](https://youtu.be/3tjkWnFQ9-Y) De brondata bestaat meestal uit meetwaarden met een interval van zo’n vijf minuten. Deze metingen gaan 24/7 door terwijl ik juist de waarden van overdag extra goed wil bekijken.

De Slicer in is een handig hulpmiddel om in de visualisatie te filteren op een bepaalde periode. Deze Slicer in Power BI kan niet overweg met tijd, maar wel met getallen en data. Met de volgende simpele stappen maak je in Power BI de slicer geschikt om tijd te filteren:

## Het stappenplan voor een Slicer met tijd in Power BI

**1.** Open de Query Editor en selecteer de kolom met gegevens die je met de slicer wilt filteren.

**2.** Ga naar het tabblad ‘Kolom toevoegen’, klik op ‘Tijd’ -&gt; ‘Uur’ en kies voor ‘Uur’.

Hiermee wordt een nieuwe kolom toegevoegd met daarin het uur van de oorspronkelijke tijdswaarde als getal.

![image](/assets/img/2020/06/new-column-uur-1024x208.png)
_Nieuwe kolom maken met de functie tijd om waarde ‘Uur’ weer te geven_

**3.** Sla de aanpassing op en ga naar de rapport weergave.

**4.** Voeg nu een Slicer toe aan je rapport en gebruik de nieuw gemaakte kolom als gegevensveld.

![image](/assets/img/2020/06/slicer-vis.png)
_De slicer tussen de visualisatieopties._


Doordat je nu geen gebruik maakt van een tijdswaarde maar van een getal kun je de visualisaties wel op uren filteren.

## Tot slot

Je kunt het geheel nog afmaken door de slicer een titel te geven en een extra slicer toe te voegen voor de datumwaarde. Hiermee wordt de visualisatie nog overzichtelijker.

Hoe vaak maak jij visualisaties met tijd? En waartegen loop jij aan bij je Power Bi rapporten? Laat het weten in de reacties