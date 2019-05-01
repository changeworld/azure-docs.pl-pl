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
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: bd93166cb878fbd961795492ed9a035c919429a8
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916623"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co to jest interfejs API wyszukiwania wizualnego Bing?

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje o obrazie. Możesz przekazać obraz lub podaj adres URL do jednego. Szczegółowe informacje będą podobnych obrazach, źródła zakupów, stron sieci Web, który zawiera obraz i nie tylko. Szczegółowe informacje zwrócone przez interfejs API wyszukiwania wizualnego Bing są podobne do takich, które jest wyświetlane na Bing.com/images.

Jeśli używasz [interfejsu API wyszukiwania obrazów Bing](../bing-image-search/overview.md), można użyć tokenów wgląd w wynikach wyszukiwania tego interfejsu API wyszukiwania wizualnego Bing zamiast przekazywania obrazu.

> [!IMPORTANT]
> Jeśli uzyskujesz szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów Bing, rozważ przejście do interfejsu API wyszukiwania wizualnego Bing, który zapewnia bardziej kompleksowe szczegółowe informacje.

## <a name="insights"></a>Insights

Poniższe szczegółowe informacje można odnajdywać za pomocą wyszukiwania wizualnego Bing:

| Szczegółowe informacje                              | Opis |
|--------------------------------------|-------------|
| Obrazy podobne wizualnie              | Lista obrazów, które wizualnie przypominają obraz wejściowy. |
| Produkty podobne wizualnie            | Produkty, które są wizualnie podobne do pokazanego produktu.            |
| Źródła zakupów                     | Miejsca, w których można kupić rzecz pokazaną na obrazie wejściowym.            |
| Powiązane wyszukiwania                     | Powiązane wyszukiwania wykonane przez innych użytkowników lub oparte na zawartości obrazu.            |
| Strony sieci Web, który zawiera obraz     | Strony internetowe, które zawierają obraz wejściowy.            |
| Przepisy                              | Strony sieci Web obejmują przepisy składania płytkę obrazie danych wejściowych.            |

Oprócz analizy wyszukiwania wizualnego Bing zwraca szereg warunków (oznacza to, znaczniki) pochodzące z obrazu wejściowego. Tagi umożliwiają użytkownikom Eksploruj pojęcia znalezione na obrazie. Na przykład jeśli obrazu wejściowego sławę wykresie jeden tag może być nazwą wykresie, inny znacznik może być sportu. Lub, w przypadku obrazu wejściowego koła firmy apple, tagi mogą być kołowy firmy Apple, wycinków i wybrano desery.

Wyniki wyszukiwania wizualnego Bing także blokujących pola dla regionów zainteresowania na obrazie. Na przykład jeśli obraz zawiera kilka osobistości, wyniki mogą obejmować blokujących pola dla każdego z rozpoznanym osobistości. Lub, jeśli Bing rozpoznaje produktu lub odzieży w obrazie, wynik może zawierać obwiedni elementu rozpoznane.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wizualnego Bing jest usługą internetową zgodną z wzorcem REST, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Można użyć interfejsu API REST lub zestawu SDK dla usługi.

1. Tworzenie [konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dostęp do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [bezpłatnie utworzyć konto](https://azure.microsoft.com/free/). Możesz uzyskać klucz subskrypcji z [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#access-your-resource) po utworzeniu konta, lub [witryny sieci Web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) po aktywowaniu bezpłatnej wersji próbnej.
2. Wyślij żądanie do interfejsu API przy użyciu zapytania wyszukiwania prawidłowe.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Kolejne kroki

Wypróbowanie interfejsu API wyszukiwania wizualnego Bing [interaktywna demonstracja](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Wersja demonstracyjna pokazuje, jak można szybko dostosować zapytania wyszukiwania i wyszukuj obrazy w sieci web.

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz przewodniki Szybki start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Zobacz także

* [Obrazy — wyszukiwanie wizualne](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) opisuje definicje i informacje na temat punktów końcowych, nagłówki, odpowiedzi na żądania i wyniki wyszukiwania parametrów zapytania, które służy do żądania na podstawie obrazu.

* [Użyj interfejsu API wyszukiwania Bing i wyświetlają wymagania dotyczące](../bing-web-search/use-display-requirements.md) Określ dopuszczalne zastosowań zawartość i informacje uzyskane za pośrednictwem interfejsów API wyszukiwania Bing.
