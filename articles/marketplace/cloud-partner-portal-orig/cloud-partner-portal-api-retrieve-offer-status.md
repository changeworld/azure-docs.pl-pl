---
title: Pobieranie stanu oferty | Dokumentacja firmy Microsoft
description: Interfejs API pobiera bieżący stan oferty.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093985"
---
<a name="retrieve-offer-status"></a>Pobieranie stanu oferty 
=====================

Pobiera bieżący stan oferty.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**       |   **Opis**                            |  **Typ danych** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identyfikator wydawcy, na przykład `Contoso`  |     String     |
|  offerId        | Identyfikator GUID, który unikatowo identyfikuje oferty      |     String     |
|  wersja interfejsu API    | Najnowszą wersję interfejsu API                        |     Date       |
|  |  |


<a name="header"></a>Nagłówek
------

|  Name (Nazwa)           |  Wartość               |
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
|  status               | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz [status oferty](#offer-status) poniżej. |
|  z chmury do urządzenia             | Tablica komunikatów skojarzonych z tej oferty                                                    |
|  kroki                | Tablica czynności, które oferty przechodzi przez podczas publikowania oferty                      |
|  estimatedTimeFrame   | Szacowany czas, jaki zajęłoby w celu ukończenia tego kroku, w formacie przyjazne                       |
|  id                   | Identyfikator kroku                                                                         |
|  stepName             | Nazwa kroku                                                                               |
|  description          | Opis kroku                                                                        |
|  status               | Stan kroku. Aby uzyskać listę możliwych wartości, zobacz [krok stan](#step-status) poniżej.    |
|  z chmury do urządzenia             | Tablica komunikatów związane z kroku                                                          |
|  processPercentage    | Procent ukończenia kroku                                                              |
|  previewLinks         | *Aktualnie nie zaimplementowane*                                                                    |
|  liveLinks            | *Aktualnie nie zaimplementowane*                                                                    |
|  notificationEmails   | Rozdzielana przecinkami lista adresów e-mail, aby otrzymywać powiadomienia o postępie operacji        |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod** |   **Opis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` Żądanie zostało pomyślnie przetworzone i bieżący stan oferta została zwrócona. |
|  400     | `Bad/Malformed request` -Treść odpowiedzi błędu może zawierać więcej informacji.                 |
|  404     | `Not found` -Określonej jednostki nie istnieje.                                                |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    |    **Opis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Nigdy nie został opublikowany oferty.                          |
|  NotStarted                  | Oferta jest nowy i nie jest uruchomiona.                            |
|  WaitingForPublisherReview   | Oferty oczekuje na zatwierdzenie wydawcy.                 |
|  Działanie                     | Przesłania oferty jest przetwarzana.                     |
|  Powodzenie                   | Przesłania oferty zakończeniu przetwarzania.               |
|  Anulowane                    | Przesyłanie oferta została anulowana.                           |
|  Niepowodzenie                      | Wprowadzenie oferty nie powiodło się.                                 |
|  |  |


### <a name="step-status"></a>Stan kroku

|  **Nazwa**                    |    **Opis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nie została uruchomiona.                        |
|  W toku                  | Etap jest uruchomiony.                             |
|  WaitingForPublisherReview   | Krok oczekuje na zatwierdzenie wydawcy.      |
|  WaitingForApproval          | Krok czeka, aż proces zatwierdzania.        |
|  Zablokowany                     | Krok jest zablokowany.                             |
|  Odrzucone                    | Krok został odrzucony.                            |
|  Zakończ                    | Krok został ukończony.                            |
|  Anulowane                    | Krok zostało anulowane.                           |
|  |  |
