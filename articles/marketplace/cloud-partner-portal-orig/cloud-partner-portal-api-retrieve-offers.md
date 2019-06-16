---
title: Pobieranie oferuje interfejs API | Portal Azure Marketplace
description: Interfejs API pobiera jest skrócona lista ofert w ramach danego obszaru nazw wydawcy.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 67109c3605ea96123ff41cb88d5ac328a09991e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935328"
---
<a name="retrieve-offers"></a>Pobieranie oferty
===============

Pobiera jest skrócona lista ofert w ramach danego obszaru nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identyfikator wydawcy, na przykład `contoso` |   String    |
|  api-version     | Najnowszą wersję interfejsu API                    |    Date        |
|  |  |


<a name="header"></a>nagłówek
------

|  **Nazwa**        |         **Wartość**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autoryzacja   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="response"></a>Odpowiedź

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**       |       **Opis**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Określa typ oferty                                                                                           |
|  publisherId    | Identyfikator, który unikatowo identyfikuje wydawcy                                                                      |
|  status         | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz [status oferty](#offer-status) poniżej.                         |
|  id             | Identyfikator GUID, który unikatowo identyfikuje oferty w przestrzeni nazw wydawcy.                                                    |
|  version        | Bieżąca wersja oferty. Nie można zmodyfikować właściwości wersji przez klienta. Ten numer jest zwiększany po każdym opublikowaniu. |
|  Definicja     | Zawiera widok podsumowania definicji rzeczywistego obciążenia. Aby uzyskać szczegółowe definicji, użyj [pobieranie daną ofertą](./cloud-partner-portal-api-retrieve-specific-offer.md) interfejsu API. |
|  changedTime    | Czas UTC ostatniej modyfikacji tej oferty                                                                              |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod**  |  **Opis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` Żądanie zostało pomyślnie przetworzone i zwrócono wszystkich ofert w ramach wydawcy do klienta.  |
|  400      | `Bad/Malformed request` -Treść odpowiedzi błędu może zawierać więcej informacji.                                    |
|  403      | `Forbidden` -Klient nie ma dostępu do określonego obszaru nazw.                                          |
|  404      | `Not found` -Określonej jednostki nie istnieje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    | **Opis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Nigdy nie został opublikowany oferty.                  |
|  NotStarted                  | Oferta jest nowy, ale nie została uruchomiona.                 |
|  WaitingForPublisherReview   | Oferty oczekuje na zatwierdzenie wydawcy.         |
|  Działanie                     | Przesłania oferty jest przetwarzana.             |
|  Powodzenie                   | Przesłania oferty zakończeniu przetwarzania.       |
|  Anulowane                    | Przesyłanie oferta została anulowana.                   |
|  Niepowodzenie                      | Wprowadzenie oferty nie powiodło się.                         |
|  |  |
