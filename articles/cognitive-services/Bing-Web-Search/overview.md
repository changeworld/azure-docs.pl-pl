---
title: Co to jest interfejs API wyszukiwania w sieci Web Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania w Internecie Bing jest usługą RESTful, która zapewnia błyskawiczne odpowiedzi na zapytania użytkowników. Wyniki wyszukiwania można łatwo konfigurować tak, aby obejmowały strony internetowe, obrazy, wideo, wiadomości, tłumaczenia i więcej. Wyniki są podawane w formacie JSON oraz oparte na istotności wyszukiwania i subskrypcjach wyszukiwania w Internecie Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a7b2627b5837a124ebbcd76783bb49679cbe6313
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77650286"
---
# <a name="what-is-the-bing-web-search-api"></a>Co to jest interfejs API wyszukiwania w sieci Web Bing?

Interfejs API wyszukiwania w Internecie Bing jest usługą RESTful, która zapewnia błyskawiczne odpowiedzi na zapytania użytkowników. Wyniki wyszukiwania można łatwo konfigurować tak, aby obejmowały strony internetowe, obrazy, wideo, wiadomości, tłumaczenia i więcej. Wyszukiwanie w sieci Web bing udostępnia wyniki jako JSON na podstawie trafności wyszukiwania i subskrypcji wyszukiwania W sieci Web Bing.

Ten interfejs API jest optymalny dla aplikacji, które muszą mieć dostęp do całej zawartości odpowiedniej dla zapytania wyszukiwania użytkownika. Jeśli tworzysz aplikację, która wymaga określonego typu wyniku, rozważ użycie [interfejsu API wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md), [interfejsu API wyszukiwania wideo Bing](../Bing-Video-Search/search-the-web.md) lub [interfejsu API wyszukiwania wiadomości Bing](../Bing-News-Search/search-the-web.md). Aby uzyskać pełny wykaz interfejsów API wyszukiwania Bing, zobacz [Interfejsy API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services).

Chcesz zobaczyć, jak to działa? Wypróbuj [Pokaz interfejsu API wyszukiwania w Internecie Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funkcje  

Wyszukiwanie w sieci Web Bing nie tylko zapewnia dostęp do natychmiastowych odpowiedzi. Zapewnia również dodatkowe funkcje i funkcje, które umożliwiają dostosowanie wyników wyszukiwania dla użytkowników.

| Funkcja | Opis |
|---------|-------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](../bing-autosuggest/get-suggested-search-terms.md) | Ulepsz działanie aplikacji przy użyciu interfejsu API automatycznego sugerowania Bing, aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |
| [Filtrowanie i ograniczanie wyników według typu zawartości](filter-answers.md) | Dostosuj i zawęź wyniki wyszukiwania za pomocą filtrów i parametrów zapytania dla stron internetowych, obrazów, wideo, bezpiecznego wyszukiwania itd. |
| [Wyróżnianie trafień dla znaków Unicode](hit-highlighting.md) | Identyfikuj i usuwaj niepożądane znaki Unicode z wyników wyszukiwania przed wyświetleniem ich dla użytkowników za pomocą wyróżniania trafień. |
| [Lokalizowanie wyników wyszukiwania według kraju, regionu i/lub rynku](supported-countries-markets.md) | Wyszukiwanie w Internecie Bing obsługuje ponad trzydzieści krajów lub regionów. Użyj tej funkcji, aby zawęzić wyniki wyszukiwania dla określonego kraju/regionu lub rynku. |
| [Analizowanie metryk wyszukiwania za pomocą statystyk Bing](bing-web-stats.md) | Statystyka Bing jest płatną subskrypcją, która zapewnia analizę woluminu wywołań, najważniejszych ciągów zapytań, rozmieszczenia geograficznego itd. |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania w Internecie Bing jest łatwo wywołać z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować odpowiedzi w formacie JSON. Usługa jest dostępna przy użyciu [interfejsu API REST](quickstarts/python.md) lub bibliotek klienta wyszukiwania sieci Web [Bing.](./quickstarts/client-libraries.md)

1. [Tworzenie zasobu platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dla interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Wyślij [żądanie do interfejsu API wyszukiwania sieci Web usługi Bing](quickstarts/python.md).
3. Przeanalizuj odpowiedź w formacie JSON.

## <a name="next-steps"></a>Następne kroki

* Użyj naszego artykułu [Szybki start dla języka Python](./quickstarts/client-libraries.md?pivots=programming-language-python), aby wykonać swoje pierwsze wywołanie interfejsu API wyszukiwania w Internecie Bing.  
* [Tworzenie jednostronicowej aplikacji sieci Web](tutorial-bing-web-search-single-page-app.md).
* Przejrzyj dokumentację [interfejsu API wyszukiwania w Internecie w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Dowiedz się więcej na temat [wymagań dotyczących użycia i wyświetlania](UseAndDisplayRequirements.md) dla wyszukiwania w Internecie Bing.  
