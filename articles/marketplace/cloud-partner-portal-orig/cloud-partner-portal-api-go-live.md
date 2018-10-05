---
title: Rozpocznij na żywo | Dokumentacja firmy Microsoft
description: Przejdź na żywo interfejs API inicjuje oferty na żywo, wyświetlanie listy procesów.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c7d643f0c7885e64636a107d22ce332b1ba9371c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810274"
---
<a name="go-live"></a>Rozpocznij na żywo
=======

Ten interfejs API rozpoczyna się proces wypychania aplikacji do środowiska produkcyjnego. Ta operacja jest zwykle długoterminowych. Lista powiadomień e-mail z korzysta z tego wywołania [Publikuj](./cloud-partner-portal-api-publish-offer.md) operacji interfejsu API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identyfikator wydawcy, oferty pobrać, na przykład `contoso`       |  Ciąg       |
| identyfikatora oferty        | Oferuje identyfikator oferty do pobrania                                   |  Ciąg       |
| wersja interfejsu API    | Najnowszą wersję interfejsu API                                                   |  Date         |
|  |  |  |


<a name="header"></a>Nagłówek
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

| **Kod** |  ** Opis **                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Pomyślnie zaakceptowano żądanie. Odpowiedź zawiera lokalizację, aby śledzić stan operacji. |
|  400     | `Bad/Malformed request` -Dodatkowe informacje o błędzie można znaleźć w treści odpowiedzi. |
|  404     |  `Not found` -Określonej jednostki nie istnieje.                                       |
|  |  |
