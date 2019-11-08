---
title: Pobieranie interfejsu API ofert | Portal Azure Marketplace
description: Interfejs API pobiera podsumowującą listę ofert w przestrzeni nazw wydawcy.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819624"
---
<a name="retrieve-offers"></a>Pobieranie oferty
===============

Pobiera podsumowującą listę ofert w przestrzeni nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identyfikator wydawcy, na przykład `contoso` |   Ciąg    |
|  wersja interfejsu API     | Najnowsza wersja interfejsu API                    |    Date        |
|  |  |


<a name="header"></a>Nagłówek
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
|  offerTypeId    | Identyfikuje typ oferty                                                                                           |
|  publisherId    | Identyfikator, który jednoznacznie identyfikuje wydawcę                                                                      |
|  status         | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz temat [status oferty](#offer-status) poniżej.                         |
|  id             | Identyfikator GUID, który jednoznacznie identyfikuje ofertę w przestrzeni nazw wydawcy.                                                    |
|  version        | Bieżąca wersja oferty. Nie można zmodyfikować właściwości Version przez klienta. Zwiększa się po każdej publikacji. |
|  Definicji     | Zawiera sumaryczny widok rzeczywistej definicji obciążenia. Aby uzyskać szczegółową definicję, użyj interfejsu API [pobierania określonej oferty](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Czas UTC ostatniej modyfikacji oferty                                                                              |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kodu**  |  **Opis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` — żądanie zostało pomyślnie przetworzone i wszystkie oferty w ramach wydawcy zostały zwrócone do klienta.  |
|  400      | `Bad/Malformed request` — treść odpowiedzi błędu może zawierać więcej informacji.                                    |
|  403      | `Forbidden` — klient nie ma dostępu do określonego obszaru nazw.                                          |
|  404      | `Not found` — określona jednostka nie istnieje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    | **Opis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Oferta nie została nigdy opublikowana.                  |
|  NotStarted                  | Oferta jest nowa, ale nie została uruchomiona.                 |
|  WaitingForPublisherReview   | Oferta oczekuje na zatwierdzenie przez wydawcę.         |
|  Działanie                     | Przesyłanie oferty jest przetwarzane.             |
|  Powodzenie                   | Przesyłanie oferty zostało zakończone.       |
|  Anulowane                    | Przesyłanie oferty zostało anulowane.                   |
|  Niepowodzenie                      | Nie można przesłać oferty.                         |
|  |  |
