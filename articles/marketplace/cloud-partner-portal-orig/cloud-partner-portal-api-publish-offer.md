---
title: Publikowanie oferty | Azure Marketplace
description: INTERFEJSU API, aby opublikować określoną ofertę.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280528"
---
<a name="publish-an-offer"></a>Publikowanie oferty
================

Rozpoczyna proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  identyfikator wydawcy   | Identyfikator wydawcy, na przykład`contoso`      |   Ciąg       |
|  offerId       | Identyfikator oferty                                 |   Ciąg       |
|  api-version   | Najnowsza wersja interfejsu API                        |   Data         |
|  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**        |    **Wartość**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład ciała
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
|  wiadomości e-mail z powiadomieniami    | Oddzielona przecinkami lista adresów e-mail, które mają być powiadamiane o postępie operacji publikowania. |
|  |  |


### <a name="response"></a>Odpowiedź

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operacja-lokalizacja    | Adres URL, który można zbadać, aby określić bieżący stan operacji.    |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Wniosek został pomyślnie przyjęty. Odpowiedź zawiera lokalizację, która może służyć do śledzenia operacji, która jest uruchomiona. |
| 400   | `Bad/Malformed request`- Jednostka reagowania na błędy może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity`- Wskazuje, że jednostka, która ma zostać opublikowana nie powiodło się sprawdzanie poprawności.                                                        |
| 404   | `Not found`- Jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
