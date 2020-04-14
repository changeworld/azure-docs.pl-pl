---
title: Anulowanie interfejsu API operacji | Azure Marketplace
description: Anuluj operacje .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256437"
---
# <a name="cancel-operation"></a>Anuluj operację

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Ten interfejs API anuluje operację aktualnie w toku oferty. Użyj [interfejsu API operacji pobierania,](./cloud-partner-portal-api-retrieve-operations.md) aby uzyskać `operationId` przekazać do tego interfejsu API. Anulowanie jest zwykle operacją synchroniczową, jednak w niektórych złożonych scenariuszach może być wymagana nowa operacja do anulowania istniejącej. W takim przypadku treść odpowiedzi HTTP zawiera lokalizację operacji, która powinna służyć do wykonywania zapytań o stan.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| identyfikator wydawcy  |  Identyfikator wydawcy, na przykład,`contoso`         |   Ciąg          |
| offerId      |  Identyfikator oferty                                     |   Ciąg          |
| api-version  |  Bieżąca wersja interfejsu API                               |    Date           |
|  |  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartość**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  NA OKAZICIELA TWÓJ ŻETON |
|  |  |

## <a name="body-example"></a>Przykład ciała
------------

### <a name="request"></a>Żądanie

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Właściwości treści żądania

|  **Nazwa**                |  **Opis**                                               |
|  --------                |  ---------------                                               |
|  wiadomości e-mail z powiadomieniami     | Przecinek oddzielona lista identyfikatorów e-mail, które mają być powiadamiane o postępie operacji publikowania. |
|  |  |

### <a name="response"></a>Odpowiedź

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niezmiagrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                       |
|  ---------            |    ----------                      |
| Lokalizacja    | Ścieżka względna do pobierania stanu tej operacji. |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code**  |  **Opis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Żądanie zostało pomyślnie przetworzone, a operacja jest anulowana synchronicznie. |
|  202      | Akceptowane. Żądanie zostało pomyślnie przetworzone, a operacja jest w trakcie anulowania. Lokalizacja operacji anulowania jest zwracana w nagłówku odpowiedzi. |
|  400      | Złe/zniekształcone żądanie. Treść odpowiedzi na błędy może dostarczyć więcej informacji.  |
|  403      | Dostęp zabroniony. Klient nie ma dostępu do obszaru nazw określonego w żądaniu. |
|  404      | Nie znaleziono. Określona jednostka nie istnieje. |
|  |  |
