---
id: 342
title: 'Entra: Een rebranding?'
date: '2022-09-15T14:59:17+02:00'
# author: Guido
layout: post
guid: 'https://pentiago365.nl/?p=342'
permalink: /is-entra-alleen-een-rebranding/
categories:
    - 'Microsoft Entra ID'
    - 'Microsoft 365'
---

## Een nieuwe naam

**[Entra](https://learn.microsoft.com/en-us/entra/)** is volgens Microsoft geen rebranding maar een hoog nodige overkoepelende naam voor alles wat te maken heeft met Identity binnen het security portfolio van Microsoft. Dit komt overeen met de namenstrategie die gehanteerd wordt binnen Microsoft Security waarbij we de afgelopen jaren al meerdere nieuwe namen langs hebben zien komen.

| **Identity** | **Security** | **Compliance** | **Privacy** | **Management** |
|---|---|---|---|---|
| Microsoft **Entra** | Microsoft **Defender** | Microsoft **Purview** | Microsoft **Priva** | Microsoft **Endpoint Manager** |
|  | Microsoft **Sentinel** |  |  |  |

_Microsoft Security Product Categorieën_

Er zit een centrale gedachtegang achter deze nieuwe naamgeving. Onze IT omgevingen blijven uitbreiden en er komen steeds meer toegangspunten tot onze omgeving bij. Vroeger konden we een zogenaamd hek om onze omgeving heen zetten en waren we voor een tijdje weer klaar met het nemen van beveiligingsmaatregelen.

Het is lastig om het overzicht te houden over wie waar bij moet kunnen én wie waar bij kan. We proberen ‘in control’ te blijven door steeds nieuwe ‘Identity and Access solutions’ in gebruik te nemen. Maar deze oplossingen overlappen elkaar vaak en geven juist ruis. Daarnaast blijven het aantal en de complexheid van aanvallen toenemen en is het vaak een nummer 1 prioriteit om de ‘bad guys’ buiten te houden.

We moeten identity zien als een ‘control plane.’ Identity staat in het midden van een IT omgeving. Alles is ermee verbonden en moet er ook mee verbonden zijn. De opzet van de Microsoft Identity suite sluit niet meer aan bij de realiteit waarin we leven. Het is verspreid in verschillende portalen en overzichten en moet toegankelijk zijn vanaf een centrale plek.

Het doel is niet om een enkel product of ‘central authority’ te bouwen waarmee alles kan, maar om een ‘mesh’ te creëren waar vanuit alle verschillende onderdelen te beheren zijn.

![image](/assets/img/2022/09/image.png)
_Microsoft Entra als een Mesh_
Daarnaast moet de naam ook meer de lading dekken dan dat [Azure AD](https://learn.microsoft.com/en-us/azure/active-directory/) nu doet voor de verschillende identity functionaliteiten die het biedt. We hebben vanuit Microsoft Security de mogelijkheid om identities te managen voor multicloud. Waarom zit er dan Azure in de naam? Dat geeft niet de juiste boodschap mee. Entra zegt: We geven toegang tot iedere applicatie of organisatie. We beveiligen die. Iedere soort identiteit. Niet alleen je eigen medewerkers, maar ook klanten of partners waarmee je samenwerkt. Niet alleen Apps, maar ook services.

## Wat valt er onder Entra?

Microsoft Entra is een portfolionaam voor alles wat te maken heeft met identiteiten en toegang. De producten die vallen onder Microsoft Entra zijn:

![image](/assets/img/2022/09/image-1-1024x401.png)
_Microsoft Entra Portfolio_

### Azure Active Directory

[Azure Active Directory](https://learn.microsoft.com/en-us/azure/active-directory/) (Azure AD) is de bekendste van de drie Entra onderdelen. Dit is een cloud-based identity en access management dienst en voor een deel vergelijkbaar met de on-premises Active Directory. Deze dienst maakt het mogelijk dat gebruikers toegang krijgen tot externe producten zoals Microsoft 365, de Azure portal of duizenden andere SaaS-applicaties.

### Microsoft Entra Permissions Management

[Microsoft Entra Permissions Management](https://learn.microsoft.com/en-us/azure/active-directory/cloud-infrastructure-entitlement-management/) is een relatief nieuw product wat gericht is op management van permissies verspreid over verschillende cloud-omgevingen. Dit product ging eerder door het leven onder de naam Cloud Knox.

| Wereldwijd beschikbaar per juli 2022 |
|---|
| **€8,80 ex. btw. per bron (compute, database, cluster, etc.) per maand** |
| Losse licentie en SKU dus geen afhankelijkheid van Azure Active Directory P1 of P2 licentie. |

_Prijzen Entra Permissions Management_

#### Waarom Microsoft Entra Permissions Management?

De adoptie van multicloud, hybride omgevingen en DevOps heeft geleid tot een enorme groei in verschillende permissies, waarmee de ‘Attack surface’/het aanvalsoppverlak exponentieel is gegroeid en ook het risico op inbraak of een negatieve audit is vergroot.

Identity en Security Admins hebben geen bedrijfsbreed inzicht in permissies en kunnen ook niet bijblijven bij de snelheid en grootte van cloudadoptie.

Access management modellen van verschillende public clouds zijn inconsistent en maken het ingewikkeld permissies rondom security en identity te managen of een model van ‘least privileged access’ af te dwingen op de gehele omgeving.

Traditionele Privileged Access Management en Identity Governance and Administration oplossingen zijn niet aangepast aan de snelle veranderingen die clouds doormaken. Daarnaast bieden ze geen fijnmazig inzicht en hebben ze beperkte mogelijkheden tot automatisering.

Microsoft Entra Permissions Management biedt dit allemaal wel door de data uit de verschillende clouds te centralizeren.

#### Wat zijn de belangrijkste mogelijkheden van Permissions Management binnen Microsoft Entra?

**Inzicht:** Permissions Management geeft een granulair inzicht in iedere actie die wordt uitgevoerd door welke identiteit of op welke resource in welke cloud (AWS, GCP en Microsoft Azure) dan ook en beoordeelt direct het risico wat je loopt met deze ‘permissions’.

**Herstel:** Versimpel Identity en Access Governance met een uniforme cross cloud interface en automatiseer het principe van Least Privilege Enforcement consistent in je gehele cloud infrastructuur.

**Monitoring:** Voorkom datalekken veroorzaakt door misbruik en kwaadwillige uitbuiting van rechten met detectie van afwijkend gedrag.

#### Hoe gebruik ik Microsoft Entra Permissions Management?

Allereerst moet je een (trial) licentie activeren om de portal te kunnen openen. Het kan een tijdje duren voordat de Permissions Management knoppen beschikbaar komen in de portalen.

Nadat alles actief is geworden kun je vanaf de Azure AD overview pagina of de entra-portal doorklikken naar Permissions Management:

![image](/assets/img/2022/09/image-2.png)
_Permissions Management knop in de Azure Portal_

##### Initieel: Data Collectors aanmaken

Wanneer je voor het eerst inlogt op Permissions Management dien je een Data Collector aan te maken voor alle clouds die je wilt koppelen. Hierbij heb je voor Azure de keuze of je alle subscriptions wilt koppelen of dat je deze wilt specficeren. Permissions Management biedt scripts voor iedere cloud om deze koppeling te maken. Voor Azure is het van belang dat je Permissions Management reader rechten geeft op de subscriptions die je wilt managen met Permissions Management.

Na het aanmaken van de Data Collector duurt het even voordat deze gebruikt kan worden. De status zal op een gegeven moment veranderen van ‘Discovering’ naar ‘Onboarded’. Daarna zal ook ‘Authorization Systems Status’ veranderen naar ‘Online’. Hierna zijn alle functies in Permissions Management actief.

Nu kun je via ‘Dashboard’ naar het centrale dashboard van Permissions Management. Dit dashboard geeft een overzicht in de risico’s die er zijn rondom permissies als gevolg van overmatige toegang. Er wordt hierbij gebruik gemaakt van de Permission Creep Index (PCI) om de meest kritische risico’s te identificeren in AWS, GCP en Azure.

##### Dashboard

De Permission Creep Index is een getal van 0 tot 100 dat het risico vertegenwoordigt van gebruikers met toegang tot risicovolle permissies. Het houdt rekening met de hoeveelheid resources die voor een gebruiker toegankelijk zijn en kijkt zo naar accounts die toegang hebben tot risicovolle permissies maar ze niet actief gebruiken.

De PCI visualisatie kan één of meerdere bubbels weergeven. Iedere bubbel vertegenwoordigt het nummer aan identiteiten dat als risicovol wordt gezien. Hoog risico refereert aan het aantal gebruikers dat meer permissies heeft dan hun normale of benodigde gebruik.

![image](/assets/img/2022/09/image-3.png)
_Permission Creep Index in Entra Permissions Management laat het aantal risico lopende identiteiten zien_
Datzelfde dashboard geeft nadat je op een bubbel klikt de zogenaamde PCI Heat Map weer. Dit is een breakdown van het totale aantal identiteiten en hoeveel daarvan hoog, gemiddeld of laag risicovol zijn. Ook laat dit een trend zien over de laatste weken.

![image](/assets/img/2022/09/image-4.png)
_Permission PCI trend in Entra Permissions Management_
Onderaan het dashboard vind je de Identity and Resource findings. Dit is een samenvatting van top risico’s en andere bevindingen. Vanuit dit overzicht kun je doorklikken naar meer informatie over de gevonden punten.

![image](/assets/img/2022/09/image-5.png)
_Identity and Resource findings in Entra Permissions Management_

##### Analytics

Onder de Analytics tab vind je gedetailleerde informatie over gebruikers, groepen, actieve resources, taken, access keys en serverless functions.

![image](/assets/img/2022/09/image-6.png)
_Gedetailleerde informatie over gebruikers en groepen in Entra Permissions Management_

Wanneer je kiest voor ‘Users’ zie je een overzicht van iedere gebruiker en de bijbehorende PCI, maar ook hoeveel taken of resources ze kunnen benaderen en hoeveel ze daarvan daadwerkelijk gebruiken. Het openklikken van een account geeft nog meer details over het account en de permissies.

##### Remediation

Via de remediation tab heb je de mogelijkheid om op twee manieren ongebruikte permissies in te trekken:

- Aan de hand van ‘least privilege policies’ (remediation)
- Op basis van permissions-on-demand

Met remediation kan een administrator policies aanmaken die geautomatiseerd ongebruikte permissies kan intrekken waardoor least privilege kan worden bereikt over de volledige multicloud omgeving. Dit staat ook wel bekend als ‘right-sizing permissions’.

![image](/assets/img/2022/09/image-7.png)
_Ongebruikte permissies in trekken met Entra Permissions Management_
Met een wizard wordt een JSON of YAML gebouwd die in AWS of GCP kan worden doorgevoerd. Het script wat hiervoor moet worden uitgevoerd wordt ook weergegeven. Wanneer een controller is gekoppeld kan ook gekozen worden voor het direct doorvoeren via de submit knop.

![image](/assets/img/2022/09/image-8.png)
_Right-sizing permissions Json_
Permissions-on-demand is een workflow functie die gebruikers de mogelijkheid geeft om permissies aan te vragen voor een tijdgelimiteerde periode of op een ‘as-needed’-basis. Deze functie is te vergelijken met Privileged Identity Management in Azure AD, maar dan voor multicloud.

![image](/assets/img/2022/09/image-9.png)
_Entra Permissions Management – Permissions-on-demand requests_
##### Alerts

Via het alerts-icoon (belletje) wordt de alerts pagina geopend. Hier kunnen alert-triggers geconfigureerd worden, maar ook het overzicht van gedetecteerde afwijkingen en vreemd gedrag wordt hier getoond.

Als een alert getriggerd wordt, wordt je via e-mail op de hoogte gesteld. In die mail is specifieke information over de alert weergegeven: datum/tijd, maar ook welke actie werd uitgevoerd waardoor de alert getriggerd werd.

##### Reports

Als laatste de reports: Onder dit tabblad vind je verschillende rapporten die gebruikt kunnen worden voor bijvoorbeeld forensisch onderzoek. Alle rapporten zijn volledig aan te passen en kunnen ook via e-mail verstuurd worden.

Het Permissions Analytics Report genereert een samenvatting van ‘key permission risks’ zoals privilege escalation scenarios van alle identiteiten, een snapshot van alle inactieve groepen, super identities, over-permissioned active identities en nog meer. Tegelijkertijd geeft het advies om hiermee aan de slag te gaan.

![image](/assets/img/2022/09/image-10.png)
_Entra Permissions Management – Permissions Analytics Report_

### Microsoft Entra Verified ID

[Verified ID](https://learn.microsoft.com/en-us/azure/active-directory/verifiable-credentials/) is een manier om als organisatie snel aan te sluiten op ‘decentralized identity solutions.’ Verfied ID is gebaseerd op open standaarden en automatiseert de verificatie van ‘identity credentials’ waardoor het mogelijk wordt om privacy beschermd interactie te hebben met eindgebruikers.

![image](/assets/img/2022/09/image-13.png)
_Grafisch overzicht van de werking van Microsoft Entra Verified ID_

De service maakt het gemakkelijk om:

**Uitgeven:**  
In enkele minuten inloggegevens uit te geven die uniek, veilig en in het bezit zijn van de eindgebruiker.

**Aanpassen:**  
Inloggegevens te genereren die aansluiten bij jouw unieke vereisten voor aanvullende authenticiteit.

**Verifieer:** Er zeker van te zijn dat gebruikers-identiteiten slecht één keer verificatie nodig hebben voor universeel gebruik. Dit zorgt voor een werkelijk veilige en open manier van beheren van hun digitale identiteit.

**Intrekken:** Verhoog het security niveau in je organisatie met de mogelijkheid om snel inloggegevens in te trekken die niet meer nodig zijn.

**Build:** Bouw custom oplossingen die aansluiten bij de wensen van je bedrijf doormiddel van een devloper kit en API’s.

#### Onboarding van medewerkers, waar dan ook

De Verified ID service helpt je bij het verbeteren van het medewerkers onboarding proces door het mogelijk te maken om identiteiten te verifieren en werplek inloggegevens sneller en veiliger uit te geven die daarnaast ook nog eens universeel inzetbaar zijn. Dit samen met een hoger niveau van medewerker privacy.

##### Kortere time-to-hire

Gebruik vertrouwde partners in het identity verification ecosystem om credentials te valideren en uit te geven gedurende het pre-hire proces.

Verifieer de identity credential van de kandidaat automatisch ten opzichte van functievereisten terwijl je de risico op fraude vermindert.

##### Kortere time-to-productivity

Geef werkplek credentials uit aan new-hires in enkele minuten. Dit maakt het voor hen mogelijk om direct de resources te benaderen die ze nodig hebben zonder enige vertraging.

##### Universele overdraagbaarheid van inloggegevens

Omdat de verfiable credential is gebaseerd op open standaarden en beheerd wordt door de eigenaar/persoon, kan het gratis aan iedere verifier worden aangeboden.

#### Ondersteun nieuwe medewerkers vanaf dag één 

Naast de mogelijkheid om snel en veilig nieuwe medewerkers aan de slag te helpen, kunnen verified credentials werknemers naadloos toegang geven tot aanvullende middelen. Maak het mogelijk voor je medewerkers om hun arbeidsgegevens gemakkelijk te presenteren in een vertrouwensstructuur van zakenpartners of leveranciers van secundaire arbeidsvoorwaarden.

**Medewerkers kunnen snel:**

- Samenwerken met partners buiten het bedrijfsnetwerk
- Bewijs van arbeidsstatus laten zien aan financiële dienstverleners
- Zakelijke aanbiedingen van partner organisaties verkrijgen

#### Veilige manier van toegang tot high-value apps en resources

Nu de moderne werkplek verandert in een hybride model blijft het beheren van hoe en wanneer missie-kritische applicaties zijn benaderd, maar ook wie er toegang heeft, één van de belangrijkste dingen.

Voor veel organisaties kunnen beveiligingsproblemen en -beperkingen dit beheer omslachtig maken. Met Microsoft Entra Verified ID kunt u veilige, directe toegang bieden tot de applicaties en bronnen die uw werknemers nodig hebben door hun eigen digitale identiteit te verifiëren en hen controle te geven.

**Een Verified ID kan gebruikt worden voor toegang tot:**

- Werkplekapplicaties (klantdatabases, verkoopcijfers, CRM-systemen, etc)
- Werkplekapplicaties voor partners of inhuurkrachten
- Business-to-Business toegang: Geverifieerde medewerkers van andere bedrijven toegang geven tot je eigen ‘high-value’ applicaties zonder direct een trust of federatie op te zetten met de gehele omgeving van de partner.

Het is ook mogelijk om de juiste mate van toegang te verlenen aan de juiste gebruikers. Nu remote en hybride werken geaccepteerd raakt, is er een oplossing nodig waarmee organisaties hun digitale identiteiten gemakkelijk en veilig kunnen verstrekken en controleren. En niet alleen de digitale identiteiten van hun medewerkers, maar ook die van tijdelijke krachten of partner organisaties.

**Voorbeeld voor B2B collaboration met Verified ID:**

> ***Jouw bedrijf start een samenwerking met een externe organisatie.***
> 
> De host-organisatie maakt een access package aan waarmee ‘least-privileged access’ wordt gegeven tot de zaken (tools/bestanden) die nodig zijn voor de samenwerking.
> 
> De partnerorganisatie ontvangt een uitnodiging om toegang te verkrijgen tot de apps in de host-organisatie voor dit project. Gebruikers uit de partner organisatie kunnen credentials ophalen en tonen via self-service aan de hand van Verfied ID.
> 
> Nadat credentials zijn getoond kunnen leden van de partnerorganisatie interne kanalen en bestanden benaderen in de host-organisatie om zo direct samen te werken met leden van de host-organisatie. Op het kantoor van de host-organisatie kunnen gebruikers toegang krijgen tot de Wi-Fi met hun credentials.

![image](/assets/img/2022/09/image-12.png)
_Schematische weergave van het gebruik van Entra Verified ID om toegang te geven tot resources voor Externe Medewerkers_

##### Hoe werkt het?

Verified ID kan helpen bij het snel terugkrijgen van toegang tot gelockte accounts zonder persoonlijke informatie te delen met een medewerker van de support afdeling. Het voorbeeld hieronder laat zien hoe gevraagd zou kunnen worden naar een bewijs van identiteit aan de hand van een verifiable credential.

Wanneer de account eigenaar kiest voor ‘verify my identity’ volgt er een permissie aanvraag in de Authenticator App (of een andere ‘digital wallet’) om een specifiek microcredential te verifiëren bij een externe verificatie instantie. Dit kan bijvoorbeeld gaan om naam, adres en geboortedatum. De eindgebruiker kan kiezen om deze verificatie toe te staan waardoor het bedrijf veilig een wachtwoord reset kan doorvoeren.

![image](/assets/img/2022/09/image-11.png)
_Password reset op basis van Verified ID en Microsoft Entra_

Dit voorbeeld maakt gebruik van verschillende afhankelijkheden. Aan de ene kant staat een issuer van een verified ID die vertrouwd is door (aan de andere kant) de organisatie. De issuer heeft een verified ID verstrekt op basis van OpenID die door de eindgebruiker is toegevoegd aan de Microsoft Authenticator App als een portable credential.

In Azure AD is deze portable credential toegevoegd aan het account van de gebruiker.

![image](/assets/img/2022/09/image-14.png)
_Overzicht van benodigde systemen rondom Verified Credentials_

Er zijn dus altijd drie partijen aanwezig rondom een Verifiable Credential oplossing:

---

## 1

### *Issuer*

Dit is de organisatie die een ‘issuance solution’ opzet en allereerst informatie vraagt van de eindgebruiker. Deze informatie wordt gebruikt om de identiteit van de eindgebruiker te verifiëren.

**Een voorbeeld:**

Een bedrijf geeft Verifiable Credentials uit aan alle medewerkers. De medewerker gebruikt de Authenticator App om in te loggen met zijn gebruikersnaam en wachtwoord. De app stuurt een ID token naar de uitgifte service. Het bedrijf valideert de token en de service maakt een Verifiable Credential aan waarin de ‘claims’ over de gebruiker zijn meegenomen en ondertekent deze met het bedrijfs DID. De medewerker heeft nu een verifiable credential dat is ondertekent door de werkgever en waarin de DID van de medewerker en van het onderwerp zijn meegenomen.

---

## 2

### *User*

Dit is de aanvrager van de Verifiable Credential (VC.)

**Een voorbeeld:**

Anna is een nieuwe medewerker van Pentiago365 en heeft daarom een ‘proof of employment’ VC gekregen. Wanneer Anna ergens moet aantonen dat ze in dienst is om korting te krijgen, kan ze toegang verlenen tot de credential in de Authenticator app door een verifiable presentation te ondertekenen die aantoont dat Alice de eigenaar is van de DID.

De partij die om de bevestiging heeft gevraagd kan deze credential dan valideren bij Pentiago365, terwijl Anna nog steeds de eigenaar is van de credential en deze ook zou kunnen intrekken.

---

## 3

### *Verifier*

De ‘verifier’ is een organisatie die claims moet verifieren van ‘issuers’ die ze vertrouwen.

**Een voorbeeld:**

Een energie maatschappij vertrouwt erop dat een bank op de juiste manier zijn klanten verifieert op ‘echtheid’ en alleen correcte en valide VC’s uitgeeft.

Wanneer Anne een contract wil afsluiten, kan de energiemaatschappij gebruik maken van standaarden zoals SIOP en Presentation Exchange om om credentials aan te vragen bij Anne. Anne kan aantonen dat ze echt Anne is omdat de bank dat in het verleden al heeft gedaan en daar een VC van heeft verstrekt.

Hiervoor scant ze met haar telefoon een QR-code die een aanvraag initieert voor een specifieke VC. De Authenticator app geeft de mogelijkheid om die aanvraag te honereren of te weigeren.

In dit voorbeeld is de bank de issuer en is de energiemaatschappij de verifier.
