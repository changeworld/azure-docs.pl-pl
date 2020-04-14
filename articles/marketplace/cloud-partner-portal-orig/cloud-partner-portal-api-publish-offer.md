---
title: Publikowanie oferty | Azure Marketplace
description: INTERFEJSU API, aby opublikować określoną ofertę.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255944"
---
# <a name="publish-an-offer"></a>Publikowanie oferty

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Rozpoczyna proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  identyfikator wydawcy   | Identyfikator wydawcy, na przykład`contoso`      |   Ciąg       |
|  offerId       | Identyfikator oferty                                 |   Ciąg       |
|  api-version   | Najnowsza wersja interfejsu API                        |   Date         |
|  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**        |    **Wartość**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Przykład ciała
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

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niezmiagrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Lokalizacja    | Ścieżka względna do pobierania stanu tej operacji     |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Wniosek został pomyślnie przyjęty. Odpowiedź zawiera lokalizację, która może służyć do śledzenia operacji, która jest uruchomiona. |
| 400   | `Bad/Malformed request`- Jednostka reagowania na błędy może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity`- Wskazuje, że jednostka, która ma zostać opublikowana nie powiodło się sprawdzanie poprawności.                                                        |
| 404   | `Not found`- Jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
