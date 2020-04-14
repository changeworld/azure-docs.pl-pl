---
title: Pobieranie operacji INTERFEJSU API | Azure Marketplace
description: Pobiera wszystkie operacje w ofercie lub uzyskać określoną operację dla określonego identyfikatora operacji.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255877"
---
# <a name="retrieve-operations"></a>Pobieranie operacji

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Pobiera wszystkie operacje w ofercie lub uzyskać określoną operację dla określonego identyfikatora operacji. Klient może używać parametrów kwerendy do filtrowania uruchomionych operacji.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>Parametry identyfikatora URI

|  **Nazwa**          |      **Opis**                                                                                           | **Typ danych** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  identyfikator wydawcy       |  Identyfikator wydawcy, na przykład`Contoso`                                                                   |  Ciąg       |
|  offerId           |  Identyfikator oferty                                                                                              |  Ciąg       |
|  operationId       |  Identyfikator GUID, który jednoznacznie identyfikuje operację w ofercie. OperationId mogą być pobierane przy użyciu tego interfejsu API i jest również zwracany w nagłówku HTTP odpowiedzi dla każdej długotrwałej operacji, takich jak interfejs API [oferty publikowania.](./cloud-partner-portal-api-publish-offer.md)  |   Guid (identyfikator GUID)   |
|  api-version       | Najnowsza wersja interfejsu API |    Date      |
|  |  |  |

## <a name="header"></a>Nagłówek


|  **Nazwa**          |  **Wartość**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autoryzacja     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Przykład ciała

### <a name="response"></a>Odpowiedź

#### <a name="get-operations"></a>Operacje GET

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operacja GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**                    |  **Opis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | Identyfikator GUID, który jednoznacznie identyfikuje operację                                                       |
|  rodzaj przesyłania              | Określa rodzaj operacji zgłaszanej dla oferty, na przykład`Publish/GoLive`      |
|  utworzonyDateTime             | Data daty czasu UTC podczas tworzenia operacji                                                       |
|  lastActionDateTime          | Data daty czasu UTC, gdy ostatnia aktualizacja została wykonana w operacji                                       |
|  status                      | Stan operacji, `not started` \| `running` \| `failed` \| `completed`albo . Tylko jedna operacja `running` może mieć stan w czasie. |
|  error                       | Komunikat o błędzie dla operacji nie powiodło się                                                               |
|  |  |

### <a name="response-step-properties"></a>Właściwości kroku odpowiedzi

|  **Nazwa**                    |  **Opis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | Szacowany czas trwania tej operacji |
| id | Unikatowy identyfikator procesu kroku |
| description | Opis kroku |
| nazwa kroku | Przyjazna nazwa kroku |
| status | Stan kroku, albo `notStarted` \| `running` \| `failed` \|`completed` |
| z chmury do urządzenia | Wszelkie powiadomienia lub ostrzeżenia napotkane podczas kroku. Tablica ciągów |
| Progresspercentage | Liczba całkowita od 0 do 100 wskazująca postęp kroku |
| | |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code**  |   **Opis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- Żądanie zostało pomyślnie przetworzone i zwrócono żądane działania.        |
|  400      | `Bad/Malformed request`- Treść odpowiedzi na błędy może zawierać więcej informacji.                    |
|  403      | `Forbidden`- Klient nie ma dostępu do określonego obszaru nazw.                          |
|  404      | `Not found`- Określona jednostka nie istnieje.                                                 |
|  |  |
