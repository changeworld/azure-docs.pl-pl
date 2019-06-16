---
title: Zagadnienia dotyczące interfejsu API | Portal Azure Marketplace
description: Przechowywanie wersji, obsługa błędów i autoryzacji problemy dotyczące korzystania z interfejsów API portalu marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935574"
---
# <a name="api-considerations"></a>Zagadnienia dotyczące interfejsu API


<a name="api-versioning"></a>Obsługa wersji interfejsu API
--------------

Może istnieć wiele wersji interfejsu API, które są dostępne w tym samym czasie. Klienci muszą wskazywać wersję chcą wywoływać użycie, zapewniając `api-version` parametr jako część ciągu zapytania.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpowiedź na żądanie z nieznanej lub nieprawidłowej wersji interfejsu API jest kod HTTP 400. Ten błąd zwraca kolekcję znane wersje interfejsu API w treści odpowiedzi.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Błędy
------

Interfejs API reaguje na błędy za pomocą odpowiednich kodów stanu HTTP i, opcjonalnie, dodatkowe informacje w odpowiedzi zserializowanym w formacie JSON.
Jeśli otrzymasz komunikat o błędzie, szczególnie błąd 400 klasy, nie należy wykonywać ponowień żądania przed naprawieniem podstawowych przyczyn. Na przykład w przykładowej odpowiedzi powyżej, Usuń parametr wersji interfejsu API przed ponownym wysłaniem żądania.

<a name="authorization-header"></a>Nagłówek autoryzacji
--------------------

Dla wszystkich interfejsów API w ramach tego odwołania należy przekazać nagłówka autoryzacji, wraz z tokenu elementu nośnego uzyskane z usługi Azure Active Directory (Azure AD). Tego pliku nagłówkowego jest wymagany na prawidłową odpowiedź; Jeśli nie występuje `401 Unauthorized` zostanie zwrócony błąd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
