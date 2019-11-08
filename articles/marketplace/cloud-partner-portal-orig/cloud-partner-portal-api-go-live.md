---
title: Przejdź na żywo | Portal Azure Marketplace
description: Interfejs API przejdź na żywo inicjuje proces oferty na żywo.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819680"
---
<a name="go-live"></a>Przejdź na żywo
=======

Ten interfejs API uruchamia proces wypychania aplikacji do środowiska produkcyjnego. Ta operacja jest zwykle długotrwała. To wywołanie używa listy wiadomości e-mail powiadomień z operacji [publikowania](./cloud-partner-portal-api-publish-offer.md) interfejsu API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identyfikator wydawcy dla oferty do pobrania, na przykład `contoso`       |  Ciąg       |
| OfferId        | Identyfikator oferty oferty do pobrania                                   |  Ciąg       |
| wersja interfejsu API    | Najnowsza wersja interfejsu API                                                   |  Date         |
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
| Lokalizacja operacji    |  Adres URL do zapytania w celu określenia bieżącego stanu operacji            |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kodu** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` — żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację do śledzenia stanu operacji. |
|  400     | `Bad/Malformed request` — w treści odpowiedzi znajdują się dodatkowe informacje o błędzie. |
|  404     |  `Not found` — określona jednostka nie istnieje.                                       |
|  |  |
