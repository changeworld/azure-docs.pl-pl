---
title: Anuluj operację interfejsu API | Portal Azure Marketplace
description: Anulowanie operacji.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935561"
---
# <a name="cancel-operation"></a>Anuluj operację 

Ten interfejs API anuluje bieżącą operację w przypadku oferty. Użyj [pobrać operacje interfejsu API](./cloud-partner-portal-api-retrieve-operations.md) można pobrać `operationId` do przekazania do tego interfejsu API. Anulowanie jest zwykle Operacja synchroniczna, jednak w niektórych scenariuszach złożonych nową operację może być konieczne anulowanie istniejącą grupę. W tym przypadku treści odpowiedzi HTTP zawiera lokalizacji operacji, które mają być używane do wykonywania zapytań stanu.

Możesz podać rozdzielana przecinkami lista adresów e-mail z żądaniem, a interfejs API powiadomi te adresy o postępie operacji.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identyfikator wydawcy, na przykład `contoso`         |   String          |
| offerId      |  Identyfikator oferty                                     |   String          |
| wersja interfejsu API  |  Bieżąca wersja interfejsu API                               |    Date           |
|  |  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartość**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  TOKEN YOUR elementu nośnego |
|  |  |


<a name="body-example"></a>Przykład treści
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
|  powiadomienia e-mail     | Lista identyfikatorów, aby otrzymywać powiadomienia o postępie operację publikowania e-mail rozdzielone przecinkami. |
|  |  |


### <a name="response"></a>Odpowiedź

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                       |
|  ---------            |    ----------                      |
| Operacja lokalizacji    | Adres URL, które mogą być przeszukiwane w celu określenia stanu bieżącej operacji. |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod**  |  **Opis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Żądanie zostało pomyślnie przetworzone i operacja została anulowana synchronicznie. |
|  202      | Zaakceptowane. Żądanie zostało pomyślnie przetworzone i operacji jest w trakcie anulowania. Lokalizacja operacja anulowania jest zwracany w nagłówku odpowiedzi. |
|  400      | Żądanie nieprawidłowego/Malformed. Treść odpowiedzi błędu może dostarczyć dodatkowych informacji.  |
|  403      | Dostęp zabroniony. Klient nie ma dostępu do przestrzeni nazw określonej w żądaniu. |
|  404      | Nie znaleziono. Określonej jednostki nie istnieje. |
|  |  |
