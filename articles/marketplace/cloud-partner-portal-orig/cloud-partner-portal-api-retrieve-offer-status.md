---
title: Pobierz stan oferty | Portal Azure Marketplace
description: Interfejs API Pobiera bieżący stan oferty.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819667"
---
<a name="retrieve-offer-status"></a>Pobieranie stanu oferty 
=====================

Pobiera bieżący stan oferty.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**       |   **Opis**                            |  **Typ danych** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identyfikator wydawcy, na przykład `Contoso`  |     Ciąg     |
|  OfferId        | Identyfikator GUID, który jednoznacznie identyfikuje ofertę      |     Ciąg     |
|  wersja interfejsu API    | Najnowsza wersja interfejsu API                        |     Date       |
|  |  |


<a name="header"></a>Nagłówek
------

|  Nazwa           |  Wartość               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autoryzacja  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład treści
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
|  status               | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz temat [status oferty](#offer-status) poniżej. |
|  z chmury do urządzenia             | Tablica komunikatów skojarzonych z ofertą                                                    |
|  kroki                | Tablica kroków, przez jaką oferta przechodzi w ramach publikacji oferty                      |
|  estimatedTimeFrame   | Oszacowanie czasu, jaki mógłby wykonać ten krok, w przyjaznym formacie                       |
|  id                   | Identyfikator kroku                                                                         |
|  krokname             | Nazwa kroku                                                                               |
|  description          | Opis kroku                                                                        |
|  status               | Stan kroku. Aby uzyskać listę możliwych wartości, zobacz [Step status](#step-status) poniżej.    |
|  z chmury do urządzenia             | Tablica komunikatów dotyczących kroku                                                          |
|  processPercentage    | Procent wykonania kroku                                                              |
|  previewLinks         | *Obecnie nie zaimplementowane*                                                                    |
|  liveLinks            | *Obecnie nie zaimplementowane*                                                                    |
|  notificationEmails   | Rozdzielana przecinkami lista adresów e-mail, które mają być powiadamiane o postępie operacji        |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kodu** |   **Opis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` — żądanie zostało pomyślnie przetworzone i został zwrócony bieżący stan oferty. |
|  400     | `Bad/Malformed request` — treść odpowiedzi błędu może zawierać więcej informacji.                 |
|  404     | `Not found` — określona jednostka nie istnieje.                                                |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    |    **Opis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Oferta nie została nigdy opublikowana.                          |
|  NotStarted                  | Oferta jest nowa i nie została uruchomiona.                            |
|  WaitingForPublisherReview   | Oferta oczekuje na zatwierdzenie przez wydawcę.                 |
|  Działanie                     | Przesyłanie oferty jest przetwarzane.                     |
|  Powodzenie                   | Przesyłanie oferty zostało zakończone.               |
|  Anulowane                    | Przesyłanie oferty zostało anulowane.                           |
|  Niepowodzenie                      | Nie można przesłać oferty.                                 |
|  |  |


### <a name="step-status"></a>Stan kroku

|  **Nazwa**                    |    **Opis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nie został uruchomiony.                        |
|  Toku                  | Krok jest uruchomiony.                             |
|  WaitingForPublisherReview   | Krok oczekuje na zatwierdzenie przez wydawcę.      |
|  WaitingForApproval          | Krok oczekuje na zatwierdzenie procesu.        |
|  Blokada                     | Krok jest zablokowany.                             |
|  Odrzucono                    | Krok został odrzucony.                            |
|  Zakończ                    | Krok został ukończony.                            |
|  Anulowane                    | Krok został anulowany.                           |
|  |  |
