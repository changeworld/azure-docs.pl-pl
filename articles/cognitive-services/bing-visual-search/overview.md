---
title: Co to jest interfejs API wyszukiwania wizualnego Bing?
titleSuffix: Azure Cognitive Services
description: Wyszukiwanie wizualne Bing dostarcza informacje szczegółowe dotyczące obrazu, takie jak podobne obrazy lub źródła zakupów.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446657"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co to jest interfejs API wyszukiwania wizualnego Bing?

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje dotyczące obrazu. Możesz przesłać obraz lub podać adres URL do jednego z nich. Statystyki są wizualnie podobne obrazy, źródła zakupów, strony internetowe, które zawierają obraz, i więcej. Statystyki zwracane przez interfejs API wyszukiwania wizualnego Bing są podobne do tych wyświetlanych na Bing.com/images. 

Jeśli używasz [interfejsu API wyszukiwania obrazów Bing,](../bing-image-search/overview.md)możesz użyć tokenów wglądu z wyników wyszukiwania tego interfejsu API dla wyszukiwania wizualnego Bing zamiast przekazywania obrazu.

> [!IMPORTANT]
> Jeśli uzyskujesz szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów Bing, rozważ przejście do interfejsu API wyszukiwania wizualnego Bing, który zapewnia bardziej kompleksowe szczegółowe informacje.

## <a name="insights"></a>Insights

Poniższe szczegółowe informacje można znaleźć za pomocą usługi Bing Visual Search:

| Szczegółowe informacje                              | Opis |
|--------------------------------------|-------------|
| Obrazy podobne wizualnie              | Lista obrazów, które wizualnie przypominają obraz wejściowy. |
| Produkty podobne wizualnie            | Produkty, które są wizualnie podobne do pokazanego produktu.            |
| Źródła zakupów                     | Miejsca, w których można kupić rzecz pokazaną na obrazie wejściowym.            |
| Powiązane wyszukiwania                     | Powiązane wyszukiwania wykonane przez innych użytkowników lub oparte na zawartości obrazu.            |
| Strony sieci Web, które zawierają obraz     | Strony internetowe, które zawierają obraz wejściowy.            |
| Przepisy                              | Strony internetowe, które zawierają przepisy na wykonanie potrawy pokazane na obrazie wejściowym.            |
| Jednostki                             | Znani ludzie, miejsca i rzeczy. |

Oprócz szczegółowych informacji wyszukiwanie wizualne Bing zwraca różne terminy (czyli tagi) pochodzące z obrazu wejściowego. Tagi umożliwiają użytkownikom eksplorowanie pojęć znalezionych na obrazie. Na przykład, jeśli obraz wejściowy jest słynnego sportowca, jeden z tagów może być nazwa sportowca, inny tag może być Sport. Lub, jeśli obraz wejściowy jest szarlotka, tagi mogą być Szarlotka, Pies i Desery.

Wyniki wyszukiwania wizualnego Bing obejmują również obwiednie dla regionów interesujących obraz. Jeśli na przykład obraz zawiera kilka gwiazd, wyniki mogą zawierać obwiednie dla każdej z uznanych gwiazd. Lub jeśli bing rozpoznaje produkt lub odzież na obrazie, wynik może zawierać obwiednię dla rozpoznanego elementu.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wizualnego Bing jest usługą internetową zgodną z wzorcem REST, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Można użyć interfejsu API REST lub SDK dla usługi.

1. Utwórz [konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby uzyskać dostęp do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [bezpłatnie utworzyć konto](https://azure.microsoft.com/free/). Klucz subskrypcji można uzyskać z [witryny Azure portal](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) po utworzeniu konta lub [witryny sieci Web platformy Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) po aktywowaniu bezpłatnej wersji próbnej.
2. Wyślij żądanie do interfejsu API z prawidłową kwerendą wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywne demo interfejsu](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)API wyszukiwania wizualnego Bing .
Demo pokazuje, jak można szybko dostosować zapytanie wyszukiwania i przeszukać w internecie obrazów.

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz szybki start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Zobacz też

* [Images — Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) reference opisuje definicje i informacje o punktach końcowych, nagłówkach żądań, odpowiedziach i parametrach kwerendy, których można użyć do żądania wyników wyszukiwania opartych na obrazie.

* [Wymagania dotyczące używania i wyświetlania interfejsu API wyszukiwania Bing](../bing-web-search/use-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.

* Odwiedź [stronę centrum interfejsu API wyszukiwania Bing,](../bing-web-search/search-the-web.md) aby zapoznać się z innymi dostępnymi interfejsami API.