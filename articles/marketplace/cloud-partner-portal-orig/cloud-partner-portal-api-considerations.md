---
title: Zagadnienia dotyczące api | Azure Marketplace
description: Problemy z przechowywaniem wersji, obsługą błędów i autoryzacją podczas korzystania z interfejsów API portalu marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256335"
---
# <a name="api-considerations"></a>Zagadnienia dotyczące interfejsu API

<a name="api-versioning"></a>Przechowywanie wersji interfejsu API
--------------

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

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
