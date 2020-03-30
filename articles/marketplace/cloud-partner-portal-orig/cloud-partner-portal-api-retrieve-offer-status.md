---
title: Pobierz stan oferty | Azure Marketplace
description: Interfejs API pobiera bieżący stan oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2f5211716145d6c05bbfb0132c4a6ba2f9cceabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280511"
---
<a name="retrieve-offer-status"></a>Pobieranie stanu oferty 
=====================

Pobiera bieżący stan oferty.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**       |   **Opis**                            |  **Typ danych** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  identyfikator wydawcy    | Identyfikator wydawcy, na przykład`Contoso`  |     Ciąg     |
|  offerId        | Identyfikator GUID, który jednoznacznie identyfikuje ofertę      |     Ciąg     |
|  api-version    | Najnowsza wersja interfejsu API                        |     Data       |
|  |  |


<a name="header"></a>Nagłówek
------

|  Nazwa           |  Wartość               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autoryzacja  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład ciała
------------

### <a name="response"></a>Odpowiedź

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
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
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**             |    **Opis**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Status oferty. Aby uzyskać listę możliwych wartości, zobacz [Stan oferty](#offer-status) poniżej. |
|  z chmury do urządzenia             | Tablica komunikatów skojarzonych z ofertą                                                    |
|  kroki                | Tablica kroków, które składa oferta podczas publikowania ofert                      |
|  estimatedTimeFrame   | Oszacowanie czasu na wykonanie tego kroku w przyjaznym formacie                       |
|  id                   | Identyfikator kroku                                                                         |
|  nazwa kroku             | Nazwa kroku                                                                               |
|  description          | Opis kroku                                                                        |
|  status               | Stan kroku. Aby uzyskać listę możliwych wartości, zobacz [Stan kroku](#step-status) poniżej.    |
|  z chmury do urządzenia             | Tablica komunikatów związanych z krokiem                                                          |
|  procesPercentage    | Procentowe zakończenie kroku                                                              |
|  previewLinks (linki do podglądu)         | *Obecnie nie wdrożony*                                                                    |
|  liveLinks (linki na żywo)            | *Obecnie nie wdrożony*                                                                    |
|  notificationEmails   | Oddzielona przecinkami lista adresów e-mail, które mają być powiadamiane o postępie operacji        |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |   **Opis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- Żądanie zostało pomyślnie przetworzone, a aktualny stan oferty został zwrócony. |
|  400     | `Bad/Malformed request`- Treść odpowiedzi na błędy może zawierać więcej informacji.                 |
|  404     | `Not found`- Określona jednostka nie istnieje.                                                |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    |    **Opis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished (NigdyPublished)              | Oferta nigdy nie została opublikowana.                          |
|  NotStarted (Nierozpoczęcie)                  | Oferta jest nowa i nie została rozpoczęta.                            |
|  OczekiwanieforPublisherReview   | Oferta czeka na zatwierdzenie przez wydawcę.                 |
|  Działanie                     | Rozpatrywane jest składanie ofert.                     |
|  Powodzenie                   | Złożenie oferty zakończyło się przetwarzaniem.               |
|  Anulowane                    | Składanie ofert zostało anulowane.                           |
|  Niepowodzenie                      | Składanie ofert nie powiodło się.                                 |
|  |  |


### <a name="step-status"></a>Stan kroku

|  **Nazwa**                    |    **Opis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted (Nierozpoczęcie)                  | Krok się nie rozpoczął.                        |
|  InProgress (Ruch przychodzący)                  | Krok jest uruchomiony.                             |
|  OczekiwanieforPublisherReview   | Krok czeka na zatwierdzenie przez wydawcę.      |
|  Oczekiwaniezaaprowa          | Krok czeka na zatwierdzenie procesu.        |
|  Zablokowane                     | Krok jest zablokowany.                             |
|  Odrzucone                    | Krok jest odrzucany.                            |
|  Complete                    | Krok jest zakończony.                            |
|  Anulowane                    | Krok został anulowany.                           |
|  |  |
