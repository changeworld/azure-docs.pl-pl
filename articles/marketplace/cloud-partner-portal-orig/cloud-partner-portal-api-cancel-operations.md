---
title: Anuluj interfejs API operacji | Portal Azure Marketplace
description: Anuluj operacje.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819782"
---
# <a name="cancel-operation"></a>Anuluj operację 

Ten interfejs API anuluje aktualnie wykonywaną operację na ofercie. Użyj [interfejsu API pobierania operacji](./cloud-partner-portal-api-retrieve-operations.md) , aby uzyskać `operationId` do przekazania do tego interfejsu API. Anulowanie jest zazwyczaj operacją synchroniczną, ale w niektórych złożonych scenariuszach może być wymagana Nowa operacja, aby anulować istniejącą operację. W takim przypadku treść odpowiedzi HTTP zawiera lokalizację operacji, która powinna być używana do wykonywania zapytań o stan.

Możesz podać rozdzieloną przecinkami listę adresów e-mail z żądaniem, a interfejs API powiadomi te adresy o postępie operacji.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identyfikator wydawcy, na przykład `contoso`         |   Ciąg          |
| OfferId      |  Identyfikator oferty                                     |   Ciąg          |
| wersja interfejsu API  |  Bieżąca wersja interfejsu API                               |    Date           |
|  |  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartość**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  Okaziciela — TOKEN |
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
|  powiadomienie — wiadomości e-mail     | Rozdzielana przecinkami lista identyfikatorów e-mail do powiadamiania o postępie operacji publikowania. |
|  |  |


### <a name="response"></a>Odpowiedź

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                       |
|  ---------            |    ----------                      |
| Lokalizacja operacji    | Adres URL, do którego można wykonać zapytanie w celu określenia bieżącego stanu operacji. |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kodu**  |  **Opis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Żądanie zostało pomyślnie przetworzone i operacja została anulowana synchronicznie. |
|  202      | Przyjmować. Żądanie zostało pomyślnie przetworzone i trwa proces anulowania operacji. Lokalizacja operacji anulowania jest zwracana w nagłówku odpowiedzi. |
|  400      | Nieprawidłowe lub źle sformułowane żądanie. Treść odpowiedzi na błąd może dostarczyć więcej informacji.  |
|  403      | Dostęp zabroniony. Klient nie ma dostępu do przestrzeni nazw określonej w żądaniu. |
|  404      | Nie znaleziono. Określona jednostka nie istnieje. |
|  |  |
