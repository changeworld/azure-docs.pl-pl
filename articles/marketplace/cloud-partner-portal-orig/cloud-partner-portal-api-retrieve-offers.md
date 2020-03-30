---
title: Pobieranie ofert INTERFEJSU API | Azure Marketplace
description: Interfejs API pobiera podsumowaną listę ofert w obszarze nazw wydawcy.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a1f15e269481b9706f81fd02f19effc9ad37df32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280494"
---
<a name="retrieve-offers"></a>Pobieranie oferty
===============

Pobiera podsumowaną listę ofert w obszarze nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  identyfikator wydawcy     | Identyfikator wydawcy, na przykład`contoso` |   Ciąg    |
|  api-version     | Najnowsza wersja interfejsu API                    |    Data        |
|  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**        |         **Wartość**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autoryzacja   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Przykład ciała
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
|  identyfikator wydawcy    | Identyfikator, który jednoznacznie identyfikuje wydawcę                                                                      |
|  status         | Status oferty. Aby uzyskać listę możliwych wartości, zobacz [Stan oferty](#offer-status) poniżej.                         |
|  id             | Identyfikator GUID, który jednoznacznie identyfikuje ofertę w obszarze nazw wydawcy.                                                    |
|  version        | Aktualna wersja oferty. Właściwość version nie może być modyfikowana przez klienta. Jest zwiększany po każdym publikowaniu. |
|  definicja     | Zawiera podsumowany widok rzeczywistej definicji obciążenia. Aby uzyskać szczegółową definicję, należy użyć interfejsu API [pobierania określonej oferty.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  zmienionaCzas    | Czas UTC, kiedy oferta została ostatnio zmodyfikowana                                                                              |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code**  |  **Opis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Żądanie zostało pomyślnie przetworzone, a wszystkie oferty pod wydawcą zostały zwrócone klientowi.  |
|  400      | `Bad/Malformed request`- Treść odpowiedzi na błędy może zawierać więcej informacji.                                    |
|  403      | `Forbidden`- Klient nie ma dostępu do określonego obszaru nazw.                                          |
|  404      | `Not found`- Określona jednostka nie istnieje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    | **Opis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished (NigdyPublished)              | Oferta nigdy nie została opublikowana.                  |
|  NotStarted (Nierozpoczęcie)                  | Oferta jest nowa, ale nie została uruchomiona.                 |
|  OczekiwanieforPublisherReview   | Oferta czeka na zatwierdzenie przez wydawcę.         |
|  Działanie                     | Rozpatrywane jest składanie ofert.             |
|  Powodzenie                   | Złożenie oferty zakończyło się przetwarzaniem.       |
|  Anulowane                    | Składanie ofert zostało anulowane.                   |
|  Niepowodzenie                      | Składanie ofert nie powiodło się.                         |
|  |  |
