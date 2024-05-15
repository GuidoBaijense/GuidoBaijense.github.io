---
id: 52
title: 'Tijdstippen groeperen in Power BI met DAX of Power Query'
date: '2020-06-12T19:09:00+02:00'
author: Guido
layout: post
guid: 'http://pentiago-365.local/?p=52'
permalink: /tijdstippen-groeperen-in-power-bi/
# image: /assets/img/2020/06/graphs-edited.png
categories:
    - 'Power Bi'
    - 'Power Query'
---

*Wanneer je meetwaarden hebt van verschillende bronnen is de kans klein dat de tijdstippen exact hetzelfde zijn. Ik wilde bij hetzelfde project als waar ik in [de vorige blog](https://pentiago365.nl/?p=32) over schreef gemiddelden weergeven van een meetwaarde over periodes van 5 minuten. Dit kan door tijdstippen te groeperen in Power BI. In deze blog en video laat ik twee methoden zien.*

{% include embed/youtube.html id='2BUsibVMOfI' %}
## Introductie
Om performance te analyseren van een Citrix VDI-werkplek wilden we een overzicht hebben van gemiddelde IcaRTT waarden op bepaalde tijdstippen. Een sessie stuurt iedere vijf minuten informatie op naar de delivery-controller. De tijdstippen hiervan staan niet vast. Ze zijn ongeveer gelieerd aan de starttijd van de sessie.

```
Starttijd: 07:53    Sessieinformatie op: 07:58 - 08:03 - 08:08
Starttijd: 07:55    Sessieinformatie op: 08:00 - 08:05 - 08:10
```

In dit voorbeeld heb ik afgerond op minuten. Wanneer je duizenden machines hebt en de informatie komt binnen op seconden nauwkeurig, kun je je wellicht voorstellen dat de uiteindelijke grafiek niet overzichtelijk is.

Daarom wilden we de gemiddelden kunnen zien over periodes van 5 minuten. Dit is de interval van nieuwe informatie waardoor alle data van alle systemen wordt meegenomen.

## Gemakkelijk groeperen of afronden van tijdstippen in dataview met behulp van Bins in Power BI

Om de snelheid van je query te kunnen behouden doe je het groeperen van tijdstippen in het gegevensoverzicht van Power Bi en niet in Power Query. Indien je nog meer ETL-stappen wilt doen, lees dan bij het kopje ‘Groepren of afronden in Power Query’ verder.



![image](/assets/img/2020/06/nieuwe-gegevensgroepen.png)
_Nieuwe gegevensgroepen in het gegevensoverzicht_

**1.** Open de dataview in Power Bi en selecteer de kolom die je wilt groeperen. Ga naar het tabblad Kolomhulpmiddelen en klik op Gegevensgroepen (Bins) -&gt; Nieuwe gegevensgroepen.

Hierna opent het onderstaande venster waarbij je de instellingen van je groep kunt ingeven.

![nieuwe groep](/assets/img/2020/06/nieuwe-groep.png)

**2.** Bij groepstype kies je voor Opslaglocatie. Type opslaglocatie zet je op Grootte van opslaglocaties.

**3.** Als laatste kies je bij ‘Grootte van opslaglocatie’ het formaat van de opslaglocatie. Opties zijn Jaar, Maanden, Dagen, Uren, Minuten of Seconden. Klik hierna op OK.

![Gegroepeerde tijden in dataview](/assets/img/2020/06/gegroepeerde-tijd-data-view.png)
_Gegroepeerde tijden in dataview_

Er wordt nu een nieuwe kolom aangemaakt met daarin Datum/Tijd-waarden afgerond op het formaat wat je hebt opgegeven.

In de afbeelding hieronder zie je het verschil tussen een grafiek die gebaseerd is op de originele datum/tijd-informatie en één waarbij de data is gegroepeerd.

![image](/assets/img/2020/06/graphs-edited-1024x274.png)
_Screenshot van grafiek met verwerkte datum/tijd. Links origineel – Rechts gegroepeerd._

## Afronden van tijd in Power Query

Wanneer je nog meer verwerking moet doen van de brondata is het wellicht beter om het afronden of groeperen al tijdens je Query te doen. Een nadeel hiervan is dat het langer duurt om de data op te halen en te verwerken. Daarom is de eerste methode mijn favoriet.

Indien je toch afhankelijk bent van Power Query, ga je als volgt te werk:

**1.** Open je query in de Query Editor. Kies voor het tabblad ‘Kolom toevoegen’ en klik op ‘Aangepaste kolom’.

![image](/assets/img/2020/06/new-column-2.png)
_Tabblad ‘Kolom toevoegen’ -&gt; ‘Aangepaste kolom’._

**2.** In het scherm wat opent kies je allereerst een nieuwe kolomnaam. Daarna vul je de benodigde formule in.

```
= DateTime.From(Number.RoundDown(Number.From([bron kolom])*288)/288)`
```
![image](/assets/img/2020/06/new-column-bins.png)
_Datum/tijd-waarden groeperen in periodes van 5 minuten in Power Query M_

#### Verklaring van de formule

Deze formule is een samentrekking van drie formules en twee berekeningen. Wanneer je wiskunde redelijk begrijpt, zul je ook begrijpen dat de eerste formule *Number.From()* is en niet *DateTime.From()*.  
Globaal werkt het als volgt:

1. Er wordt een datum/tijd-waarde omgezet in een getal: *Number.From(\[oorspronkelijke kolom\])*
2. Dit getal, met cijfers achter de komma, wordt vermenigvuldigd met 288 omdat we willen groeperen op periodes van 5 minuten. Er zijn nu eenmaal 288 periodes van 5 minuten in een dag.
3. Het resultaat van de vermenigvuldiging wordt afgerond en daarna gedeeld door 288. Hiervoor gebruiken we *Number.RoundDown()*.
4. Het getal wat overblijft wordt weer omgezet in een Datum/Tijd-waarde aan de hand van *DateTime.From()*.

**Number.From(\[StartDate\])** rekent de tijdwaarde terug naar een [‘OLE Automation date’.](https://docs.microsoft.com/en-us/dotnet/api/system.datetime.tooadate?view=netcore-3.1https://docs.microsoft.com/nl-nl/powerquery-m/number-from) Microsoft omschrijft het in het Nederlands als een ‘zwevende komma getal met dubbele precisie’. Lekkere vertaling… In het Engels is het een ‘floating-point number’. De integer (het volledige getal) weerspiegelt het aantal volledige dagen voor of na 30 december 1899 middernacht. Het deel achter de komma geeft de tijd op die dag gedeeld door 24 weer.

`Middernacht op 31 december 1899 is 1,0.`\
`6 uur 's ochtends op 1 januari 1900 is 2,25. etc.`

Omdat we het gedeelte achter de komma (de tijd) wél nodig hebben, maar niet direct af mogen ronden, vermenigvuldigen we de tijdswaarde met 288. 288 staat in dit geval voor een 5 minuten schaal. Hierna kunnen we afronden met *Number.RoundDown().*

De vermenigvuldiging van 288 is alleen nodig voor het afronden met behoud van groepen van 5 minuten. We moeten het resultaat hierna dus delen door 288 en opnieuw omzetten naar een datum/tijd-waarde met *Datetime.From()*.

De totale formule wordt als volgt:  
```
DateTime.From(Number.RoundDown(Number.From([oorspronkelijke kolom])\*288)/288)
```

Wil je afronden op minuten, dan vervang je 288 door 1440.


| Minuten | Getal |
| ----------- | ----------- |
| 1 minuut | 1440 |
| 5 minuten | 288 |
| 10 minuten | 144 |
| 15 minuten | 96 |
| 30 minuten | 48 |
| 60 minuten | 24 |
