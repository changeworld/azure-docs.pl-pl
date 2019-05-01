---
title: Publikowanie oferty | Portal Azure Marketplace
description: Interfejs API do opublikowania oferty określony.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934975"
---
<a name="publish-an-offer"></a>Publikowanie oferty
================

Rozpoczyna się proces publikowania dla określonej oferty. To wywołanie jest operacją wymagającą dużo czasu.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identyfikator wydawcy, na przykład `contoso`      |   String       |
|  offerId       | Identyfikator oferty                                 |   String       |
|  wersja interfejsu API   | Najnowszą wersję interfejsu API                        |   Date         |
|  |  |


<a name="header"></a>Nagłówek
------

|  **Nazwa**        |    **Wartość**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Żądanie

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Właściwości treści żądania

|  **Nazwa**               |   **Opis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  powiadomienia e-mail    | Rozdzielana przecinkami lista adresów e-mail, aby otrzymywać powiadomienia o postępie operacji publikowania. |
|  |  |


### <a name="response"></a>Odpowiedź

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operacja lokalizacji    | Adres URL, który może być odpytywany do określenia stanu bieżącej operacji.    |
|  |  |


### <a name="response-status-codes"></a>Kody stanów odpowiedzi

| **Kod** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Pomyślnie zaakceptowano żądanie. Odpowiedź zawiera lokalizację, która może służyć do śledzenia operacji, który jest uruchamiany. |
| 400   | `Bad/Malformed request` -Treść odpowiedzi błędu może dostarczyć dodatkowych informacji.                                                               |
| 422   | `Un-processable entity` — Wskazuje, że jednostki do opublikowania nie powiodło się sprawdzanie poprawności.                                                        |
| 404   | `Not found` -Jednostki podanej przez klienta nie istnieje.                                                                              |
|  |  |
