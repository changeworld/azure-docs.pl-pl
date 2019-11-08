---
title: Zagadnienia dotyczące interfejsów API | Portal Azure Marketplace
description: Przechowywanie wersji, obsługa błędów i problemy z autoryzacją w przypadku korzystania z interfejsów API portalu Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819738"
---
# <a name="api-considerations"></a>Zagadnienia dotyczące interfejsów API


<a name="api-versioning"></a>Obsługa wersji interfejsu API
--------------

Może istnieć wiele wersji interfejsu API, które są dostępne w tym samym czasie. Klienci muszą wskazać, której wersji chcesz wywołać, podając parametr `api-version` w ramach ciągu zapytania.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpowiedź na żądanie z nieznaną lub nieprawidłową wersją interfejsu API to kod HTTP 400. Ten błąd zwraca kolekcję znanych wersji interfejsu API w treści odpowiedzi.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Błędy
------

Interfejs API reaguje na błędy przy użyciu odpowiednich kodów stanu HTTP i opcjonalnie dodatkowe informacje w przypadku serializacji odpowiedzi jako dane JSON.
Po otrzymaniu błędu, szczególnie błędu klasy 400, nie należy ponawiać próby żądania przed ustaleniem przyczyny podstawowej. Na przykład w powyższej odpowiedzi przykładowo Popraw parametr wersji interfejsu API przed ponownym wysłaniem żądania.

<a name="authorization-header"></a>Nagłówek autoryzacji
--------------------

Dla wszystkich interfejsów API w tym odwołaniu należy przekazać nagłówek autoryzacji wraz z tokenem okaziciela uzyskanym z Azure Active Directory (Azure AD). Ten nagłówek jest wymagany do otrzymania prawidłowej odpowiedzi; Jeśli nie istnieje, zwracany jest błąd `401 Unauthorized`. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
