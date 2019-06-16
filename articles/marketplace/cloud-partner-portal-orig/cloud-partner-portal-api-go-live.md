---
title: Go Live | Azure Marketplace
description: Przejdź na żywo interfejs API inicjuje oferty na żywo, wyświetlanie listy procesów.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935489"
---
<a name="go-live"></a>Rozpocznij na żywo
=======

Ten interfejs API rozpoczyna się proces wypychania aplikacji do środowiska produkcyjnego. Ta operacja jest zwykle długoterminowych. Lista powiadomień e-mail z korzysta z tego wywołania [Publikuj](./cloud-partner-portal-api-publish-offer.md) operacji interfejsu API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identyfikator wydawcy, oferty pobrać, na przykład `contoso`       |  String       |
| offerId        | Oferuje identyfikator oferty do pobrania                                   |  String       |
| api-version    | Najnowszą wersję interfejsu API                                                   |  Date         |
|  |  |  |


<a name="header"></a>nagłówek
------

|  **Nazwa**       |     **Wartość**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autoryzacja   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="response"></a>Odpowiedź

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |      **Wartość**                                                            |
|  --------             |      ----------                                                           |
| Operacja lokalizacji    |  Adres URL do wykonywania zapytań w celu określenia stanu bieżącej operacji            |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Pomyślnie zaakceptowano żądanie. Odpowiedź zawiera lokalizację, aby śledzić stan operacji. |
|  400     | `Bad/Malformed request` -Dodatkowe informacje o błędzie można znaleźć w treści odpowiedzi. |
|  404     |  `Not found` -Określonej jednostki nie istnieje.                                       |
|  |  |
