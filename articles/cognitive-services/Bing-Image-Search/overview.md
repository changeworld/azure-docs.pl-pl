---
title: Czym jest funkcja wyszukiwania obrazów Bing? | Microsoft Docs
description: Dowiedz się, jak używać interfejsu API wyszukiwania obrazów Bing do przeszukiwania Internetu pod kątem obrazów.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.openlocfilehash: ebf8c28b843768a9081a0086b10e4adf04572ea1
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42889273"
---
# <a name="what-is-bing-image-search"></a>Czym jest funkcja wyszukiwania obrazów Bing?

Interfejs API wyszukiwania obrazów Bing umożliwia używanie poznawczego wyszukiwania obrazów Bing w Twojej aplikacji. Wysyłając zapytania wyszukiwania użytkownika za pomocą interfejsu API, możesz pobrać i wyświetlać odpowiednie obrazy wysokiej jakości podobne do [obrazów Bing](https://www.bing.com/images).

Należy pamiętać, że interfejs API wyszukiwania obrazów Bing udostępnia tylko wyniki wyszukiwania obrazów. Użyj [interfejsu API wyszukiwania w Internecie Bing](../bing-web-search/search-the-web.md), [interfejsu API wyszukiwania wideo](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) i [interfejsu API wyszukiwania wiadomości](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search) do innych typów zawartości internetowej.

## <a name="bing-image-search-features"></a>Funkcje wyszukiwania obrazów Bing

Chociaż wyszukiwanie obrazów Bing przede wszystkim znajduje i zwraca odpowiednie obrazy z zapytania wyszukiwania, usługa udostępnia również kilka dodatkowych funkcji do inteligentnego i ukierunkowanego pobierania obrazów w Internecie.


| Cecha                                                                                                                                                                                 | Opis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#using-and-suggesting-search-terms) | Ulepsz działanie aplikacji przy użyciu [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md), aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |
| [Filtrowanie i ograniczanie wyników obrazu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | Filtruj obrazy, które zwraca usługa Bing, edytując parametry zapytania.                                                                                                       |
| [Przycinanie, zmienianie rozmiaru i wyświetlania miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Edytuj i wyświetlaj podgląd miniatur obrazów zwróconych przez wyszukiwanie obrazów Bing.                                                                                      |
| [Przestawianie i rozwijanie zapytań wyszukiwania użytkowników](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | Zwiększ możliwości wyszukiwania, dołączając i wyświetlając sugerowane przez usługę Bing terminy wyszukiwania do zapytania.                                                                    |
| [Pobieranie popularnych obrazów](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Dostosuj wyszukiwanie popularnych obrazów z całego świata.                                                                                                          |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania obrazów Bing jest usługą internetową RESTful, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Możesz użyć tej usługi za pomocą [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) lub [zestawu SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). 
2. Wyślij żądanie do interfejsu API przy użyciu prawidłowego [zapytania wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON. 

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) interfejsu API wyszukiwania obrazów Bing.
Ta demonstracja pokazuje, jak można szybko dostosować zapytanie wyszukiwania i wyszukać obrazy w Internecie.

Gdy wszystko jest gotowe do wywołania interfejsu API, utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Aby szybko rozpocząć pracę z pierwszym żądaniem interfejsu API, możesz nauczyć się:

* [Wysyłać zapytania wyszukiwania do usługi Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) przy użyciu interfejsu API REST lub
* [Żądać i filtrować](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) obrazy zwracane przez usługę Bing przy użyciu zestawu SDK.

## <a name="see-also"></a>Zobacz też

* Sekcja dokumentacji [interfejsu API wyszukiwania obrazów Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) zawiera definicje i informacje na temat punktów końcowych, nagłówków, odpowiedzi interfejsu API i parametrów zapytania, których możesz użyć do żądania wyników wyszukiwania na podstawie obrazu.

* [Wymagania dotyczące użycia i wyświetlania Bing](./useanddisplayrequirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.

* Temat [Pobieranie obrazów z Internetu za pomocą interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) opisuje sposób wyszukiwania i pobierania obrazów z Internetu.

* W temacie [Wysyłanie i praca z zapytaniami wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) opisano sposób tworzenia, dostosowywania i przestawiania zapytań wyszukiwania. 
