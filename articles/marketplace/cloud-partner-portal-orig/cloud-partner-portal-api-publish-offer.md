---
title: Publikowanie oferty | Portal Azure Marketplace
description: Interfejs API umożliwiający opublikowanie określonej oferty.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819653"
---
<a name="publish-an-offer"></a>Publikowanie oferty
================

Uruchamia proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identyfikator wydawcy, na przykład `contoso`      |   Ciąg       |
|  OfferId       | Identyfikator oferty                                 |   Ciąg       |
|  wersja interfejsu API   | Najnowsza wersja interfejsu API                        |   Date         |
|  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**        |    **Wartość**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Żądanie

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Właściwości treści żądania

|  **Nazwa**               |   **Opis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  powiadomienie — wiadomości e-mail    | Rozdzielana przecinkami lista adresów e-mail, które mają być powiadamiane o postępie operacji publikowania. |
|  |  |


### <a name="response"></a>Odpowiedź

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Lokalizacja operacji    | Adres URL, do którego można wykonać zapytanie w celu ustalenia bieżącego stanu operacji.    |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kodu** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` — żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację, która może służyć do śledzenia uruchomionej operacji. |
| 400   | `Bad/Malformed request` — treść odpowiedzi na błąd może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity`-wskazuje, że nie można sprawdzić poprawności jednostki do opublikowania.                                                        |
| 404   | `Not found` — jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
