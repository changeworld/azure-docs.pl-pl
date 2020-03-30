---
title: Anulowanie interfejsu API operacji | Azure Marketplace
description: Anuluj operacje .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280545"
---
# <a name="cancel-operation"></a>Anuluj operację 

Ten interfejs API anuluje operację aktualnie w toku oferty. Użyj [interfejsu API operacji pobierania,](./cloud-partner-portal-api-retrieve-operations.md) aby uzyskać `operationId` przekazać do tego interfejsu API. Anulowanie jest zwykle operacją synchroniczową, jednak w niektórych złożonych scenariuszach może być wymagana nowa operacja do anulowania istniejącej. W takim przypadku treść odpowiedzi HTTP zawiera lokalizację operacji, która powinna służyć do wykonywania zapytań o stan.

Za pomocą żądania można podać listę adresów e-mail oddzielonych przecinkami, a interfejs API powiadomi te adresy o postępie operacji.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| identyfikator wydawcy  |  Identyfikator wydawcy, na przykład,`contoso`         |   Ciąg          |
| offerId      |  Identyfikator oferty                                     |   Ciąg          |
| api-version  |  Bieżąca wersja interfejsu API                               |    Data           |
|  |  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartość**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  NA OKAZICIELA TWÓJ ŻETON |
|  |  |


<a name="body-example"></a>Przykład ciała
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                       |
|  ---------            |    ----------                      |
| Operacja-lokalizacja    | Adres URL, który można zbadać, aby określić bieżący stan operacji. |
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
