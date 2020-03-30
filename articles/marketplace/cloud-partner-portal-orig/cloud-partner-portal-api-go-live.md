---
title: Idź na żywo | Azure Marketplace
description: Interfejs API Go Live inicjuje proces aukcji na żywo oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288584"
---
<a name="go-live"></a>Przejdź na żywo
=======

Ten interfejs API uruchamia proces wypychania aplikacji do produkcji. Ta operacja jest zwykle długotrwałe. To wywołanie używa listy wiadomości e-mail z listy [publikowania](./cloud-partner-portal-api-publish-offer.md) interfejsu API operacji.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| identyfikator wydawcy    | Identyfikator wydawcy dla oferty do pobrania, na przykład`contoso`       |  Ciąg       |
| offerId        | Identyfikator oferty do pobrania                                   |  Ciąg       |
| api-version    | Najnowsza wersja interfejsu API                                                   |  Data         |
|  |  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**       |     **Wartość**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autoryzacja   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Przykład ciała
------------

### <a name="response"></a>Odpowiedź

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |      **Wartość**                                                            |
|  --------             |      ----------                                                           |
| Operacja-lokalizacja    |  Adres URL do kwerendy w celu określenia bieżącego stanu operacji            |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Wniosek został pomyślnie przyjęty. Odpowiedź zawiera lokalizację do śledzenia stanu operacji. |
|  400     | `Bad/Malformed request`- W treści odpowiedzi znajdują się dodatkowe informacje o błędzie. |
|  404     |  `Not found`- Określona jednostka nie istnieje.                                       |
|  |  |
