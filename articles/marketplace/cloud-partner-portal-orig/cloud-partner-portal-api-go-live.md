---
title: Go Live | Microsoft Docs
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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624976"
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

| **Kod** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Pomyślnie zaakceptowano żądanie. Odpowiedź zawiera lokalizację, aby śledzić stan operacji. |
|  400     | `Bad/Malformed request` -Dodatkowe informacje o błędzie można znaleźć w treści odpowiedzi. |
|  404     |  `Not found` -Określonej jednostki nie istnieje.                                       |
|  |  |
