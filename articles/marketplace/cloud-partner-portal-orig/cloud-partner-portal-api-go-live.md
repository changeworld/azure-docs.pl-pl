---
title: Idź na żywo | Azure Marketplace
description: Interfejs API Go Live inicjuje proces aukcji na żywo oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256318"
---
# <a name="go-live"></a>Przejdź na żywo

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Ten interfejs API uruchamia proces wypychania aplikacji do produkcji. Ta operacja jest zwykle długotrwałe. To wywołanie używa listy wiadomości e-mail z listy [publikowania](./cloud-partner-portal-api-publish-offer.md) interfejsu API operacji.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| identyfikator wydawcy    | Identyfikator wydawcy dla oferty do pobrania, na przykład`contoso`       |  Ciąg       |
| offerId        | Identyfikator oferty do pobrania                                   |  Ciąg       |
| api-version    | Najnowsza wersja interfejsu API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**       |     **Wartość**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autoryzacja   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Przykład ciała

### <a name="response"></a>Odpowiedź

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niezmiagrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |      **Wartość**                                                            |
|  --------             |      ----------                                                           |
| Lokalizacja    |  Ścieżka względna do pobierania stanu tej operacji            |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Wniosek został pomyślnie przyjęty. Odpowiedź zawiera lokalizację do śledzenia stanu operacji. |
|  400     | `Bad/Malformed request`- W treści odpowiedzi znajdują się dodatkowe informacje o błędzie. |
|  404     |  `Not found`- Określona jednostka nie istnieje.                                       |
|  |  |
