---
id: 221
title: 'Krijg grip op overbodige gastgebruikers in Azure AD met Power Automate'
date: '2021-08-27T14:01:35+02:00'
author: Guido
layout: post
guid: 'https://pentiago365.nl/?p=221'
permalink: /krijg-grip-op-overbodige-gastgebruikers-in-azure-ad-met-power-automate/
image: /assets/img/2021/08/image-16.png
categories:
    - 'Microsoft 365'
    - 'Power Automate'
tags:
    - approvals
    - 'entra'
    - 'graph api'
    - 'key vault'
    - 'microsoft 365'
    - teams
---

Breng overbodige gastgebruikers in je Microsoft 365 tenant in beeld. Start een ‘Approval-flow’ en laat de leden van een Team besluiten of een gastaccount uit de tenant verwijderd moet worden. In deze blog beschrijf ik hoe je dit aan de hand van Microsoft [Power Automate](https://flow.microsoft.com), [Graph API](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0) en Teams voor elkaar krijgt.

## Introductie

Wanneer je gastgebruikers toestaat in Microsoft Teams zul je dit vroeger of later tegenkomen: gast-accounts in Azure AD die geen toegang meer hebben tot enig team of SharePoint site maar nog wel steeds inzicht hebben in je gebruikerslijst. Dit noem ik wees-accounts. Ze zijn nergens meer voor nodig en slechts vervuiling van je directory.

De eerste aanzet tot het beperken van overbodige toegang kun je doen door het inregelen van [Azure AD access review](https://docs.microsoft.com/en-us/azure/active-directory/governance/manage-guest-access-with-access-reviews). Hiermee regel je in dat teameigenaren na een bepaalde tijd moeten aangeven of B2B-guests toegang moeten behouden tot een team. Het probleem met deze tool is dat wanneer toegang wordt ingetrokken het account niet verwijdert wordt, maar slechts de toegang tot een bepaald team verliest. En wanneer je gast accounts niet meer ziet, zul je er ook niet aan denken om ze te verwijderen.

Dit lossen we op door een proces te automatiseren dat alle gastgebruikers controleert op lidmaatschappen en bij accounts die geen lidmaatschappen meer hebben en al een maand geen login-activiteit meer hebben vertoond een ‘Approval’ te starten voor verwijdering van het account. Deze approval wordt getoond in een team zodat de verantwoordelijkheid voor dit proces niet bij een enkel persoon komt te liggen.

---

## De benodigdheden:

- Azure AD app-registratie met client secret om toegang te krijgen tot de Graph API vanuit Power Automate. [Laura Kokkarinen heeft hier een uitgebreide blog over geschreven.](https://laurakokkarinen.com/how-to-set-up-an-azure-ad-application-registration-for-calling-microsoft-graph/) De volgende permissies voor Graph API zijn vereist:
    - Directory.Read.All -&gt; We willen weten van welke groepen een gebruiker lid is.
    - AuditLogs.Read.All -&gt; Om de lastsignin te kunnen lezen
    - User.ReadWrite.All -&gt; Om overbodige gebruikers te kunnen verwijderen
- [Power Automate licentie](https://powerautomate.microsoft.com/en-us/pricing/) -&gt; De flow maakt gebruik van de HTTP-actie. Dat is een premium connector. [Vanaf oktober 2021 gaan de prijzen door de helft!](https://www.microsoft.com/en-us/licensing/news/pricing_and_licensing_updates_coming_to_power_apps)

## Aan de slag

Ga naar [flow.microsoft.com](https://flow.microsoft.com), log in, ga naar ‘create’ en kies voor een scheduled cloud flow.

![image](/assets/img/2021/08/image-2.png)
_De knop voor ‘Scheduled cloud flow’_

Geef de stroom een naam en zet de frequentie op iets als ééns in de 6 maanden. Zo weet je zeker dat de stroom niet ongewenst blijft lopen terwijl je hem nog niet af hebt. Vergeet niet om de frequentie aan te passen wanneer je hem volledig in gebruik neemt.

![image](/assets/img/2021/08/image-3.png)
_Formulier om een stroom aan te maken in Microsoft Power Automate_

### Opbouw van de stroom – variabelen

Begin je stroom met het ophalen van de variabelen die je nodig hebt voor Graph API: de client-id, tenant-id en client-secret. Als je hier op een veilige manier mee omgaat heb je deze opgeslagen in de [Azure Key Vault](https://azure.microsoft.com/nl-nl/services/key-vault/#product-overview). Peter Klapwijk legt [in deze blog uit](https://www.inthecloud247.com/use-azure-key-vault-to-secure-your-graph-api-connection-in-power-automate/) hoe je hier gebruik van kunt maken in Power Automate. Indien je geen Azure Key Vault hebt kun je ze vastleggen als variabelen of in een Compose (samenstellen) actie.

Maak ook (string) variabelen aan voor het Team ID van het team waarin je straks de approvals wilt laten uitkomen, het Channel ID waarin de approvals komen te staan en en lege variabele voor de ApproversIDs.

Je stroom ziet er nu ongeveer zo uit:

![image](/assets/img/2021/08/image-6.png)
_Overzicht van de variabelen in de flow_

De volgende stap is om alle teamleden van het beoordelingsteam op te halen en de Id’s van deze gebruikers toe te voegen aan de variabele ‘ApproverIDs’. Gebruik hiervoor de actie ‘List group members’. Stel ‘Group ID’ in op de variabele waar je het TeamID hebt ingevuld.

Voeg daarna een ‘Append to string variable’-actie toe waarin je ApproversIDs gaat vullen met het resultaat van ‘List group members’. Vergeet niet de ‘;’ achter het User Id in te vullen.

![image](/assets/img/2021/08/image-5.png)
_List group members en de Append to string variable om ApproverIDs te vullen._

### Opbouw van de stroom – Data ophalen

Nu je de benodigde variabelen hebt opgehaald kunnen we aan de slag met de Graph API. Allereerst moeten we authentiseren en de access token ophalen. Neem daarvoor de gegevens uit de screenshot over:

![image](/assets/img/2021/08/image-8.png)
_HTTP actie voor de Access token van Graph API_

Het antwoord wat we zoeken is de access token. Deze kun je bruikbaar maken door een Parse Json-actie in te zetten. De content is de ‘body’ die we terugkregen van de HTTP-actie. Als schema kun je dit gebruiken:

```json
{
    "type": "object",
    "properties": {
        "token_type": {
            "type": "string"
        },
        "expires_in": {
            "type": "string"
        },
        "ext_expires_in": {
            "type": "string"
        },
        "expires_on": {
            "type": "string"
        },
        "not_before": {
            "type": "string"
        },
        "resource": {
            "type": "string"
        },
        "access_token": {
            "type": "string"
        }
    }
}
```

Nu kun je alle gastgebruikers ophalen uit Graph API. Doe hiervoor een HTTP-request naar dit adres:

`https://graph.microsoft.com/beta/users`

Vergeet niet in de headers ConsistencyLevel op ‘eventual’ te zetten en onder Queries de $filter en $select parameters mee te geven zoals ze in de screenshot staan. Helaas is het niet mogelijk om in dezelfde aanvraag een filter te doen op lidmaatschappen van de gebruikers aangezien deze pas zichtbaar zijn na een *$expand*. Daarom pakken we die later op. We maken gebruik van de Graph beta-endpoint omdat siginActivity nog niet in de v1.0 is opgenomen. De actie stel je verder zo in:

![image](/assets/img/2021/08/image-9.png)
_Guest Users uit Microsoft Graph met een HTTP request_

Het antwoord vanuit de HTTP-actie verwerken we opnieuw met een ‘Parse JSON’ actie. Gebruik het volgende schema:

```json
{
    "type": "object",
    "properties": {
        "@@odata.context": {
            "type": "string"
        },
        "value": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "@@odata.id": {
                        "type": "string"
                    },
                    "displayName": {
                        "type": "string"
                    },
                    "mail": {
                        "type": "string"
                    },
                    "userPrincipalName": {
                        "type": "string"
                    },
                    "userType": {
                        "type": "string"
                    },
                    "id": {
                        "type": "string"
                    },
                    "signInActivity": {
                        "type": "object",
                        "properties": {
                            "lastSignInDateTime": {
                                "type": "string"
                            },
                            "lastSignInRequestId": {
                                "type": "string"
                            },
                            "lastNonInteractiveSignInDateTime": {
                                "type": "string"
                            },
                            "lastNonInteractiveSignInRequestId": {}
                        }
                    }
                },
                "required": [
                    "@@odata.id",
                    "displayName",
                    "mail",
                    "userPrincipalName",
                    "userType",
                    "id"
                ]
            }
        }
    }
}
```

### Opbouw van de stroom – Data verwerken

De bulk data is binnen: Alle gastgebruikers in de Azure Active Diretory zijn bekend. De volgende stap is per gebruiker kijken of ze lid zijn van een groep of rol in de Microsoft 365 tenant. Wanneer ze geen lidmaatschappen of rollen hebben is het een weesgebruiker en kijken we naar de LastSignInDateTime. Indien die ver genoeg in het verleden ligt starten we een goedkeuringsflow op om goedkeuring te krijgen voor verwijdering van het account.

Om de lidmaatschappen te controleren doen we per gastgebruiker een HTTP-Post naar

`https://graph.microsoft.com/v1.0/users/{user id of UPN} /getMemberobjects`

Let erop dat de methode geen GET maar Post dient te zijn en dat je ook de body uit de screenshot overneemt. Hiermee verifieer je namelijk ook administratorrollen.

![image](/assets/img/2021/08/image-10.png)
_Haal nog wat extra gegevens op van de gebruiker om lidmaatschappen en rollen te verifiëren_

De verkregen informatie nemen we mee naar de Condition-actie. Hierin kijken we of de gebruiker lid is van een Team of groep, of de gebruiker een admin-rol vervult en of de gebruiker in de laatste maand nog ingelogd is geweest.

Voor de lidmaatschap en rollen check voer je de volgende formule in:

`greater(length(outputs('HTTP_-_Get_Memberships_for_User').body.value),0)`

Deze greater formule kijkt of de inhoud van de getMemberobjects groter is dan 0.

De volgende formule checkt of de LastSignInDateTime leeg is. Dit object is alleen gevuld wanneer er ná [april 2020 een succesvolle loginpoging is geweest. ](https://docs.microsoft.com/en-us/azure/active-directory/reports-monitoring/howto-manage-inactive-user-accounts#what-does-a-blank-property-value-mean)

`empty(items('Apply_to_each_-_Check_user_and_start_approval')?['signInActivity']?['lastSignInDateTime'])`

De laatste formule bekijkt of de ingevulde LastSignInDateTime in de afgelopen maand heeft plaatsgevonden:

`formatDateTime(addDays(utcNow(),-30),'yyyy-MM-dd')`

![image](/assets/img/2021/08/image-11.png)
_Overzicht van de Condition-actie om te verifiëren of een gebruiker actief is_

### Opbouw van de stroom – Approval starten

Indien het resultaat van deze condition ‘false’ is kunnen we de approval starten.

Stel de approval in op ‘Approve/Reject – First to respond’ en kies bij ‘Assigned to’ voor de variabele met ApproversIDs. Vul verder bij Details de informatie in die je mee wilt sturen met het verzoek.

Voeg daarna de actie ‘Post adaptive card in a chat or channel’ toe zodat je de approvals in een Teams-kanaal kunt publiceren. Zet achter Team je voorafingestelde TeamId variabele en bij channel het bijbehorende Teams Kanaal Id. Als laatst kies je bij ‘Adaptive Card’ voor de ‘Teams Adaptive Card’ die in de approval-actie is aangemaakt.

De volgende actie die je instelt is een ‘Wait for an approval’ waarbij je als ID het ID van de aangemaakte approval instelt. Een overzicht van de instellingen vind je in deze screenshot:

![image](/assets/img/2021/08/image-13-564x1024.png)
_Overzicht van de approval-flow in Power Automate_

De adaptive card komt er dan zo uit te zien in Teams:

![image](/assets/img/2021/08/image-16.png)

### Opbouw van de stroom – weesgebruikers verwijderen

Nu de approval is gepubliceerd in Teams dienen alleen de ‘goedgekeurde’ weesgebruikers te worden verwijderd. Maak een condition aan die controleert of de uitkomst van de approval gelijk is aan ‘Approve’. Stuur daarna via een HTTP-actie de opdracht naar Microsoft Graph om de wees-gebruiker te verwijderen.

![image](/assets/img/2021/08/image-14.png)![image](/assets/img/2021/08/image-15.png)

## Tot slot

Vond je deze post nuttig, of heb je juist aanvullende vragen? Laat het weten in de reacties of benader mij op [LinkedIn](https://www.linkedin.com/in/guidobaijense/).