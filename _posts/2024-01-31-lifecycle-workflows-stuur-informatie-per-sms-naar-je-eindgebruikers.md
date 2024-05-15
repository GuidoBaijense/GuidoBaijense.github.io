---
id: 387
title: 'Lifecycle Workflows: Stuur informatie per SMS naar je eindgebruikers'
date: '2024-01-31T15:46:53+01:00'
author: Guido
layout: post
guid: '/?p=387'
permalink: /lifecycle-workflows-stuur-informatie-per-sms-naar-je-eindgebruikers/
# image: /assets/img/2024/01/wordtgeendinges-blijfsmsen.png
categories:
    - 'Identity Governance'
    - 'Microsoft 365'
    - 'Power Automate'
tags:
    - automation
    - entra
    - 'key vault'
    - 'logic Apps'
    - 'microsoft 365'
---

In deze blog laat ik zien hoe je **Custom Extensions** in Microsoft Entra Identity Governance (Lifecycle workflows &amp; access packages) kunt gebruiken om informatie uit je Identity Governance proces naar een **externe API** te sturen. Hiermee zijn de mogelijkheden eindeloos en is er *altijd een passende oplossing* om jouw proces optimaal te kunnen laten werken.

Een tijdje geleden gaf ik tijdens een evenement voor klanten een demo waarin ik liet zien hoe je de functionaliteit van Microsoft Entra Identity Governance kunt uitbreiden met het versturen van op maat gemaakte brieven naar je eindgebruikers via PostNL. Deze demo was bedoeld om de geest te prikkelen en de ongelimiteerde mogelijkheden van Custom Extensions uit te beelden. Nu begrijp ik dat een brief niet altijd handig is en we als hippe digital natives liever kiezen voor SMS of WhatsApp. Dat werkt nu eenmaal sneller. Daarom in deze blog alsnog een stap voor stap uitleg om te SMS’en naar je eindgebruikers.

![Reclame van Hi begin deze eeuw: Wordt geen dinges. Blijf sms'en.](/assets/img/2024/01/wordtgeendinges-blijfsmsen-1024x580.png)
_Still uit Hi!-reclame van begin deze eeuw._

#### Benodigdheden

- Microsoft tenant
- Microsoft Entra ID Governance Step-Up for Microsoft Entra ID P2 licentie
- Lifecycle Workflows Administrator
- Azure Subscription
- Contributor rechten op een resource group in Azure

## Het begin

Ik ga er vanuit dat je weet wat lifecycle workflows zijn. Zo niet, lees mijn [blog met een introductie](/efficientere-onboarding-met-lifecycle-workflows/) over deze functionaliteit. Wat ik hier laat zien is ongeveer één-op-één toepasbaar op custom extensions voor Access Packages.

We gaan functionaliteit toevoegen aan lifecycle workflows(LCW). Vanuit LCW is het niet mogelijk om een SMS te versturen naar een eindgebruiker. Wel kunnen we Custom Extensions activeren om dit alsnog mee te doen. Custom Extensions zijn Azure Logic Apps die worden aangeroepen vanuit een LCW en ongeveer hetzelfde doen als Power Automate flows.

Om een Custom Extension aan te maken ga je naar Entra Identity Governance &gt; Lifecycle workflows &gt; Custom extensions &gt; **Add a custom extension**. In het scherm wat nu opent vul je een naam en omschrijving in van je custom extension. Bij **Task behavior** kies je of de taak eerst moet zijn afgerond voordat de rest van de LCW gestart wordt, of dat het parallel mag draaien. Bij **Details** kies je waar in Azure de logic app aangemaakt moet worden. Doe dit in een subscription waar je contributor rechten hebt. Klik eerst op **Create a logic app** en daarna op **Review + create** &gt; **Create** om de custom extensions aan te maken.

## De SMS API

Op <https://developer.kpn.com> kun je je eenvoudig als developer registreren en gebruik maken van de gratis Sandbox. De basis API van KPN is de simpelste die ik ben tegengekomen. Daarnaast is het tarief van €0,08 (op dit moment) per sms pay-per-use binnen Nederland ook gewoon goed. Het is natuurlijk geen probleem om een andere API te gebruiken. Andere aanbieders zijn CM.com, SMStools.nl, Spryng (Top service. +/- €0,07 per SMS. Afname van minimaal 100 berichten per keer ), MessageBird, Vonage en nog vele anderen.

Als je je geregistreerd hebt vind je onder het tabblad ‘Sandbox’ je Client ID en Client secret voor authenticatie op de API. Deze gebruiken we straks om een token op te halen. Daarna rest (pun intended) ons niets anders dan een HTTP Post met een body naar de endpoint om een SMS te versturen.

De secret kun je opslaan in een Azure Keyvault om het een en ander veiliger te maken.

## De logic app

Een nieuwe custom extension logic app bevat niets anders dan een HTTP webhook trigger. De LCW roept deze aan en stuurt wat informatie over de user waarvoor de LCW draait mee. De rest is aan ons om in te vullen.

Allereerst activeren we de managed identity zodat we deze kunnen gebruiken om rechten te geven op de keyvault en de Authentication Administrator rol in Entra. Ga in de Logic App naar **Settings &gt; Identity** en zet de schuif naar **On.** Klik nu op **Save** om de managed identity te activeren.

Je kunt nu permissies uitdelen op de managed identity zoals je dat doet voor iedere andere identity in Microsoft Entra. Ga bijvoorbeeld naar Roles and Administrators in Entra, kies de Authentication Administrator rol en zoek bij de lijst met gebruikers naar de naam van je Logic App. Hetzelfde geldt voor de permissies op de Keyvault die je wellicht gebruikt.

Hierna gaan we de logic app inrichten. Dat doen we door een nieuwe stap toe te voegen om de secret uit de Keyvault op te halen. Klik hiervoor op Designer &gt; New Step en zoek naar Azure Key Vault. Selecteer de Key Vault action en kies voor Get Secret. Selecteer bij Authentication type **Managed Identity** en vul de overige gegevens in voor jouw key vault. Klik dan op **Create.**

Ga naar de code view en overschrijf alles voor "Parameters" (+/- regel 156) met de volgende code:

>Deze is ook te downloaden via [mijn github](https://github.com/GuidoBaijense/Blog/blob/main/LogicApp-TAPbySMS.json)
{: .prompt-tip}


```json

{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "HTTP": {
                "inputs": {
                    "authentication": {
                        "audience": "https://graph.microsoft.com",
                        "type": "ManagedServiceIdentity"
                    },
                    "body": {
                        "data": {
                            "operationStatus": "Completed"
                        },
                        "source": "sample",
                        "type": "lifecycleEvent"
                    },
                    "method": "POST",
                    "uri": "https://graph.microsoft.com/beta@{triggerBody()?['data']?['callbackUriPath']}"
                },
                "runAfter": {
                    "Scope_-_SMS": [
                        "Succeeded"
                    ]
                },
                "type": "Http"
            },
            "Scope_-_Get_userdata_and_TAP": {
                "actions": {
                    "HTTP_-_Get_TAP_for_user": {
                        "inputs": {
                            "authentication": {
                                "audience": "https://graph.microsoft.com",
                                "type": "ManagedServiceIdentity"
                            },
                            "body": {
                                "isUsableOnce": false,
                                "lifetimeInMinutes": 600,
                                "startDateTime": "@utcNow()"
                            },
                            "method": "POST",
                            "uri": "https://graph.microsoft.com/beta/users/@{triggerBody()?['data']?['subject']?['id']}/authentication/temporaryAccessPassMethods"
                        },
                        "runAfter": {
                            "Parse_JSON_-_Userdata": [
                                "Succeeded"
                            ]
                        },
                        "type": "Http"
                    },
                    "HTTP_-_Get_user_Private_Phonenumber": {
                        "inputs": {
                            "authentication": {
                                "audience": "https://graph.microsoft.com",
                                "type": "ManagedServiceIdentity"
                            },
                            "method": "GET",
                            "uri": "https://graph.microsoft.com/v1.0/users/@{triggerBody()?['data']?['subject']?['id']}?$select=mobilePhone,displayName"
                        },
                        "runAfter": {},
                        "type": "Http"
                    },
                    "Parse_JSON_-_TAP_data": {
                        "inputs": {
                            "content": "@body('HTTP_-_Get_TAP_for_user')",
                            "schema": {
                                "properties": {
                                    "@@odata.type": {
                                        "type": "string"
                                    },
                                    "createdDateTime": {
                                        "type": "string"
                                    },
                                    "id": {
                                        "type": "string"
                                    },
                                    "isUsable": {
                                        "type": "boolean"
                                    },
                                    "isUsableOnce": {
                                        "type": "boolean"
                                    },
                                    "lifetimeInMinutes": {
                                        "type": "integer"
                                    },
                                    "methodUsabilityReason": {
                                        "type": "string"
                                    },
                                    "startDateTime": {
                                        "type": "string"
                                    },
                                    "temporaryAccessPass": {
                                        "type": "string"
                                    }
                                },
                                "type": "object"
                            }
                        },
                        "runAfter": {
                            "HTTP_-_Get_TAP_for_user": [
                                "Succeeded"
                            ]
                        },
                        "type": "ParseJson"
                    },
                    "Parse_JSON_-_Userdata": {
                        "inputs": {
                            "content": "@body('HTTP_-_Get_user_Private_Phonenumber')",
                            "schema": {
                                "properties": {
                                    "@@odata.context": {
                                        "type": "string"
                                    },
                                    "displayName": {
                                        "type": "string"
                                    },
                                    "mobilePhone": {
                                        "type": "string"
                                    }
                                },
                                "type": "object"
                            }
                        },
                        "runAfter": {
                            "HTTP_-_Get_user_Private_Phonenumber": [
                                "Succeeded"
                            ]
                        },
                        "type": "ParseJson"
                    }
                },
                "runAfter": {},
                "type": "Scope"
            },
            "Scope_-_SMS": {
                "actions": {
                    "Get_secret_-_Get_KPN_API_secret": {
                        "inputs": {
                            "host": {
                                "connection": {
                                    "name": "@parameters('$connections')['keyvault']['connectionId']"
                                }
                            },
                            "method": "get",
                            "path": "/secrets/@{encodeURIComponent('KPN-SMS-API-secret')}/value"
                        },
                        "runAfter": {},
                        "runtimeConfiguration": {
                            "secureData": {
                                "properties": [
                                    "outputs"
                                ]
                            }
                        },
                        "type": "ApiConnection"
                    },
                    "HTTP_-_Get_token_for_KPN_SMS_API": {
                        "inputs": {
                            "authentication": {
                                "password": "@{body('Get_secret_-_Get_KPN_API_secret')?['value']}",
                                "type": "Basic",
                                "username": "xxxxxxxxxxxxxxxxxxxxxxxxxxxx"
                            },
                            "method": "POST",
                            "uri": "https://api-prd.kpn.com/oauth/client_credential/accesstoken?grant_type=client_credentials"
                        },
                        "runAfter": {
                            "Get_secret_-_Get_KPN_API_secret": [
                                "Succeeded"
                            ]
                        },
                        "type": "Http"
                    },
                    "HTTP_-_Send_SMS_via_KPN_SMS_API": {
                        "inputs": {
                            "body": {
                                "messages": [
                                    {
                                        "content": "Beste @{body('Parse_JSON_-_Userdata')?['displayName']}, welkom bij .... Je kunt tot @{formatDateTime(addMinutes(body('Parse_JSON_-_TAP_data')?['startDateTime'], body('Parse_JSON_-_TAP_data')?['lifetimeInMinutes']),'dd-MM-yyyy HH:mm')} inloggen met je mailadres en de code: @{body('Parse_JSON_-_TAP_data')?['temporaryAccessPass']}",
                                        "mobile_number": "@{body('Parse_JSON_-_Userdata')?['mobilePhone']}"
                                    }
                                ],
                                "sender": "xxxxxx"
                            },
                            "headers": {
                                "Authorization": "Bearer @{body('HTTP_-_Get_token_for_KPN_SMS_API')?['access_token']}"
                            },
                            "method": "POST",
                            "uri": "https://api-prd.kpn.com/communication/kpn/sms/send"
                        },
                        "runAfter": {
                            "HTTP_-_Get_token_for_KPN_SMS_API": [
                                "Succeeded"
                            ]
                        },
                        "runtimeConfiguration": {
                            "staticResult": {
                                "name": "HTTP_-_Send_SMS_via_KPN_SMS_API0",
                                "staticResultOptions": "Disabled"
                            }
                        },
                        "type": "Http"
                    }
                },
                "runAfter": {
                    "Scope_-_Get_userdata_and_TAP": [
                        "Succeeded"
                    ]
                },
                "type": "Scope"
            }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "parameters": {
            "$connections": {
                "defaultValue": {},
                "type": "Object"
            }
        },
        "staticResults": {
            "HTTP_-_Send_SMS_via_KPN_SMS_API0": {
                "outputs": {
                    "headers": {},
                    "statusCode": "OK"
                },
                "status": "Succeeded"
            }
        },
        "triggers": {
            "manual": {
                "inputs": {
                    "schema": {
                        "properties": {
                            "data": {
                                "properties": {
                                    "callbackUriPath": {
                                        "description": "CallbackUriPath used for Resume Action",
                                        "title": "Data.CallbackUriPath",
                                        "type": "string"
                                    },
                                    "subject": {
                                        "properties": {
                                            "displayName": {
                                                "description": "DisplayName of the Subject",
                                                "title": "Subject.DisplayName",
                                                "type": "string"
                                            },
                                            "email": {
                                                "description": "Email of the Subject",
                                                "title": "Subject.Email",
                                                "type": "string"
                                            },
                                            "id": {
                                                "description": "Id of the Subject",
                                                "title": "Subject.Id",
                                                "type": "string"
                                            },
                                            "manager": {
                                                "properties": {
                                                    "displayName": {
                                                        "description": "DisplayName parameter for Manager",
                                                        "title": "Manager.DisplayName",
                                                        "type": "string"
                                                    },
                                                    "email": {
                                                        "description": "Mail parameter for Manager",
                                                        "title": "Manager.Mail",
                                                        "type": "string"
                                                    },
                                                    "id": {
                                                        "description": "Id parameter for Manager",
                                                        "title": "Manager.Id",
                                                        "type": "string"
                                                    }
                                                },
                                                "type": "object"
                                            },
                                            "userPrincipalName": {
                                                "description": "UserPrincipalName of the Subject",
                                                "title": "Subject.UserPrincipalName",
                                                "type": "string"
                                            }
                                        },
                                        "type": "object"
                                    },
                                    "task": {
                                        "properties": {
                                            "displayName": {
                                                "description": "DisplayName for Task Object",
                                                "title": "Task.DisplayName",
                                                "type": "string"
                                            },
                                            "id": {
                                                "description": "Id for Task Object",
                                                "title": "Task.Id",
                                                "type": "string"
                                            }
                                        },
                                        "type": "object"
                                    },
                                    "taskProcessingResult": {
                                        "properties": {
                                            "createdDateTime": {
                                                "description": "CreatedDateTime for TaskProcessingResult Object",
                                                "title": "TaskProcessingResult.CreatedDateTime",
                                                "type": "string"
                                            },
                                            "id": {
                                                "description": "Id for TaskProcessingResult Object",
                                                "title": "TaskProcessingResult.Id",
                                                "type": "string"
                                            }
                                        },
                                        "type": "object"
                                    },
                                    "workflow": {
                                        "properties": {
                                            "displayName": {
                                                "description": "DisplayName for Workflow Object",
                                                "title": "Workflow.DisplayName",
                                                "type": "string"
                                            },
                                            "id": {
                                                "description": "Id for Workflow Object",
                                                "title": "Workflow.Id",
                                                "type": "string"
                                            },
                                            "workflowVerson": {
                                                "description": "WorkflowVersion for Workflow Object",
                                                "title": "Workflow.WorkflowVersion",
                                                "type": "integer"
                                            }
                                        },
                                        "type": "object"
                                    }
                                },
                                "type": "object"
                            },
                            "source": {
                                "description": "Context in which an event happened",
                                "title": "Request.Source",
                                "type": "string"
                            },
                            "type": {
                                "description": "Value describing the type of event related to the originating occurrence.",
                                "title": "Request.Type",
                                "type": "string"
                            }
                        },
                        "type": "object"
                    }
                },
                "kind": "Http",
                "type": "Request"
            }
        }
    },



```
{: .highlight pre}

Wanneer je hierna naar ‘Designer’ gaat zul je zien dat de logic app gevuld is. Nu moeten alleen de juiste variabelen nog worden ingesteld. Onder **Scope – SMS** staat de actie **Get secret**. Deze is nu ingesteld op een secret met de naam KPN-SMS-API-secret. Vervang dit door de naam van jouw secret.

In de actie daaronder (**HTTP – Get token for KPN SMS API**) staat bij Authentication een username. Deze moet worden vervangen door de client id van jouw API registratie. Daarnaast zie je bij **HTTP – Send SMS via KPN SMS API** hoe in de body de SMS wordt geconstrueerd. Hier kun je de tekst aanpassen, maar ook de afzender instellen.

Wanneer je bij de LCW hebt ingesteld dat andere stappen parralel mogen draaien, kun je de laatste HTTP actie van de logic app weggooien.

## Tot slot

In deze blog heb ik laten zien hoe je een SMS met Temporary Access Pass kunt versturen naar je eindgebruikers doormiddel van een Custom Extension voor Lifecycle Workflows. In principe kun je dit voorbeeld gebruiken om iedere API aan te roepen en is dit ook inzetbaar voor Access Packages.