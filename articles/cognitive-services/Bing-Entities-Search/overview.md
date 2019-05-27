---
title: Czym jest interfejs API wyszukiwania jednostek Bing?
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API wyszukiwanie jednostek Bing, aby wyodrębniać i wyszukiwać jednostki oraz miejsca z poziomu zapytań wyszukiwania.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 0755688d1cc2f00ffef253b7695d9cb504f9e169
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "64866844"
---
# <a name="what-is-bing-entity-search-api"></a>Czym jest interfejs API wyszukiwania jednostek Bing?

Interfejs API wyszukiwania jednostek Bing wysyła zapytanie wyszukiwania do usługi Bing i pobiera wyniki, które zawierają jednostki i miejsca. Wyniki dotyczące miejsc to między innymi restauracje, hotele i inne lokalne firmy. Wyszukiwarka Bing zwraca wyniki dotyczące miejsc, jeśli w zapytaniu określono nazwę lokalnej firmy lub zadano pytanie dotyczące typu firmy (na przykład „restaurants near me” — restauracje w pobliżu). Wyszukiwarka Bing zwraca jednostki, jeśli zapytanie określa dobrze znanych osób, miejsc (atrakcji, Stany, kraje/regiony itp.) lub rzeczy.

|Cecha  |Opis  |
|---------|---------|
|[Sugestie dotyczące wyszukiwania w czasie rzeczywistym](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Podaj sugestie dotyczące wyszukiwania, które mogą być wyświetlane jako lista rozwijana podczas wpisywania przez użytkowników.       | 
| [Uściślanie jednostki](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Uzyskaj wiele jednostek dla zapytań z wieloma możliwymi znaczeniami. |
| [Znajdowanie miejsc](concepts/search-for-entities.md#find-places) | Wyszukaj i zwróć informacje dotyczące lokalnych firm i jednostek  |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania jednostek Bing jest usługą internetową RESTful łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować kod JSON. Możesz użyć tej usługi za pomocą interfejsu API REST lub zestawu SDK.

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Wyślij żądanie do interfejsu API przy użyciu prawidłowego zapytania wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Kolejne kroki

* Wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) interfejsu API wyszukiwania jednostek Bing. 
* Aby szybko rozpocząć pracę z pierwszym żądaniem, wypróbuj przewodnik [Szybki start](quickstarts/csharp.md).
* Sekcja dokumentacji [interfejsu API wyszukiwania jednostek Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).
* [Wymagania dotyczące użycia i wyświetlania Bing](./use-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.
