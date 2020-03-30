---
title: Zagadnienia dotyczące api | Azure Marketplace
description: Problemy z przechowywaniem wersji, obsługą błędów i autoryzacją podczas korzystania z interfejsów API portalu marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4e04f521ed2023dfb9cd562549cb2e1bcd319b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288635"
---
# <a name="api-considerations"></a>Zagadnienia dotyczące interfejsu API


<a name="api-versioning"></a>Przechowywanie wersji interfejsu API
--------------

Może istnieć wiele wersji interfejsu API, które są dostępne w tym samym czasie. Klienci muszą wskazać, która wersja, którą `api-version` chcą wywołać, użyj, podając parametr jako część ciągu zapytania.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpowiedzią na żądanie z nieznaną lub nieprawidłową wersją interfejsu API jest kod HTTP 400. Ten błąd zwraca kolekcję znanych wersji interfejsu API w treści odpowiedzi.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

Interfejs API reaguje na błędy za pomocą odpowiednich kodów stanu HTTP i opcjonalnie dodatkowe informacje w odpowiedzi serializowanej jako JSON.
Po wyświetleniu błędu, zwłaszcza błąd klasy 400, nie ponowić próbę żądania przed naprawieniem przyczyny. Na przykład w powyższej odpowiedzi przykładowej naprawić parametr wersji interfejsu API przed ponownym wysłaniem żądania.

<a name="authorization-header"></a>Nagłówek autoryzacji
--------------------

Dla wszystkich interfejsów API w tym odwołaniu należy przekazać nagłówek autoryzacji wraz z tokenem nośnika uzyskanym z usługi Azure Active Directory (Azure AD). Ten nagłówek jest wymagany do otrzymania prawidłowej odpowiedzi; jeśli nie jest `401 Unauthorized` obecny, zwracany jest błąd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
