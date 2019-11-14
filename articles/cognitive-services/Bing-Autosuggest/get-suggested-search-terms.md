---
title: Czym jest funkcja automatycznego sugerowania Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5b1e4cc8dfc89d6dcc5d29a368e089402b284352
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072868"
---
# <a name="what-is-bing-autosuggest"></a>Czym jest funkcja automatycznego sugerowania Bing?

Jeśli aplikacja wysyła zapytania do dowolnego z interfejsów API wyszukiwania Bing, możesz użyć interfejsu API automatycznego sugerowania Bing w celu ulepszenia środowiska wyszukiwania dla użytkowników. Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. W miarę wprowadzania znaków w polu wyszukiwania możesz wyświetlać sugestie na liście rozwijanej.

## <a name="bing-autosuggest-api-features"></a>Funkcje interfejsu API automatycznego sugerowania Bing

| Funkcja                                                                                                                                                                                 | Opis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](concepts/get-suggestions.md) | Ulepsz działanie aplikacji przy użyciu interfejsu API automatycznego sugerowania, aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |

## <a name="workflow"></a>Przepływ pracy

Interfejs API automatycznego sugerowania Bing jest usługą internetową RESTful łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. 

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Wyślij żądanie do tego interfejsu API za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania w aplikacji.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

Ten interfejs API jest zwykle wywoływany za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania w aplikacji. Im większa liczba wprowadzonych znaków, tym lepiej dopasowane sugerowane zapytania wyszukiwania zwraca interfejs API. Na przykład sugestie, które interfejs API może zwrócić dla pojedynczej litery `s`, będą prawdopodobnie mniej przydatne niż zwracane dla ciągu `sail`.

Poniższy przykład przedstawia pole wyszukiwania z listą rozwijaną sugerowanych terminów zapytania z interfejsu API automatycznego sugerowania Bing.

![Pole wyszukiwania z listą rozwijaną automatycznie sugerowanych terminów](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Gdy użytkownik wybierze sugestię z listy rozwijanej, możesz jej użyć, aby rozpocząć wyszukiwanie przy użyciu jednego z interfejsów API wyszukiwania Bing lub bezpośrednio przejść do strony wyników wyszukiwania Bing.

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz [Making Your First Query](quickstarts/csharp.md) (Tworzenie pierwszego zapytania).

Zapoznaj się z dokumentacją [interfejsu API automatycznego sugerowania Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach sugerowanych wyszukiwanych terminów, oraz definicje obiektów odpowiedzi.

Dowiedz się, jak wyszukiwać w Internecie za pomocą [interfejsu API wyszukiwania w sieci Web Bing](../bing-web-search/search-the-web.md).

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./useanddisplayrequirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.
