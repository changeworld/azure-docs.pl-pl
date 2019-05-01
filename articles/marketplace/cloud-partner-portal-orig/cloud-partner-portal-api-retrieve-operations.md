---
title: Pobierz operacje interfejsu API | Portal Azure Marketplace
description: Pobiera wszystkie operacje, w ramach oferty lub w celu uzyskania określoną operację na określony identyfikator operationId.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 1fbcc1d50dbc4488c4123be64e85de612233ccc3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935775"
---
<a name="retrieve-operations"></a>Pobieranie operacji
===================

Pobiera wszystkie operacje, w ramach oferty lub w celu uzyskania określoną operację na określony identyfikator operationId. Klient może używać parametrów zapytania do filtrowania długotrwałych operacji.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**          |      **Opis**                                                                                           | **Typ danych** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identyfikator wydawcy, na przykład `Contoso`                                                                   |  String       |
|  offerId           |  Identyfikator oferty                                                                                              |  String       |
|  operationId       |  Identyfikator GUID, który unikatowo identyfikuje operacji w przypadku oferty. Identyfikatorowi operationId może zostać pobrane za pomocą tego interfejsu API i jest także zwracany w nagłówku HTTP w odpowiedzi na wszelkie długotrwałej operacji, takich jak [publikacji oferty](./cloud-partner-portal-api-publish-offer.md) interfejsu API.  |   Guid   |
|  filteredStatus    | Parametr opcjonalny zapytania używane do filtrowania według stanu (na przykład `running`) w zbiorze zwróconym przez ten interfejs API.  |   String |
|  wersja interfejsu API       | Najnowszą wersję interfejsu API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**          |  **Wartość**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autoryzacja     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="response"></a>Odpowiedź

#### <a name="get-operations"></a>Pobierz operacje

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

#### <a name="get-operation"></a>Operacja pobrania

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
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**                    |  **Opis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | Identyfikator GUID, który unikatowo identyfikuje operację                                                       |
|  submissionType              | Określa typ operacji zgłaszane dla tej oferty, na przykład `Publish/GGoLive`      |
|  createdDateTime             | Daty/godziny UTC utworzenia operacji                                                       |
|  lastActionDateTime          | Daty/godziny UTC po ostatniej aktualizacji zostało zrobione na operację                                       |
|  status                      | Stan operacji, albo `not started` \| `running` \| `failed` \| `completed`. Tylko jedna operacja może mieć stan `running` w danym momencie. |
|  error                       | Komunikat o błędzie w przypadku operacji zakończonych niepowodzeniem                                                               |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod**  |   **Opis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` Żądanie zostało pomyślnie przetworzone i operacje żądane zostały zwrócone.        |
|  400      | `Bad/Malformed request` -Treść odpowiedzi błędu może zawierać więcej informacji.                    |
|  403      | `Forbidden` -Klient nie ma dostępu do określonego obszaru nazw.                          |
|  404      | `Not found` -Określonej jednostki nie istnieje.                                                 |
|  |  |
