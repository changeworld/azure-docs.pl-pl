---
title: Pobieranie ofert INTERFEJSU API | Azure Marketplace
description: Interfejs API pobiera podsumowaną listę ofert w obszarze nazw wydawcy.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255961"
---
<a name="retrieve-offers"></a>Pobieranie oferty
===============

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Pobiera podsumowaną listę ofert w obszarze nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  identyfikator wydawcy     | Identyfikator wydawcy, na przykład`contoso` |   Ciąg    |
|  api-version     | Najnowsza wersja interfejsu API                    |    Date        |
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
