---
title: Co to jest wyszukiwanie wizualne Bing?
titleSuffix: Azure Cognitive Services
description: Wyszukiwanie wizualne Bing dostarcza informacje szczegółowe dotyczące obrazu, takie jak podobne obrazy lub źródła zakupów.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 61a851b0efbcc4fdb55308e47447d218014ef9e0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154358"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co to jest interfejs API wyszukiwania wizualnego Bing?

Interfejs API wyszukiwania wizualnego Bing udostępnia szczegóły obrazu podobne do tych pokazanych w witrynie Bing.com/images. Po przekazaniu obrazu lub udostępnieniu jego adresu URL ten interfejs API pozwala zidentyfikować różne dotyczące go szczegóły, takie jak podobne obrazy, źródła zakupów, strony internetowe zawierające ten obraz i nie tylko. Jeśli używasz [interfejsu API wyszukiwania obrazów Bing](../bing-image-search/overview.md), zamiast przekazywania obrazu możesz używać tokenów szczegółowych informacji o obrazie dołączonych do wyników wyszukiwania interfejsu API.

## <a name="insights"></a>Insights

Poniżej przedstawiono szczegółowe informacje, które można odnaleźć za pomocą wyszukiwania wizualnego:

| Szczegółowe informacje                              | Opis |
|--------------------------------------|-------------|
| Obrazy podobne wizualnie              | Lista obrazów, które wizualnie przypominają obraz wejściowy. |
| Produkty podobne wizualnie            | Produkty, które są wizualnie podobne do pokazanego produktu.            |
| Źródła zakupów                     | Miejsca, w których można kupić rzecz pokazaną na obrazie wejściowym.            |
| Powiązane wyszukiwania                     | Powiązane wyszukiwania wykonane przez innych użytkowników lub oparte na zawartości obrazu.            |
| Strony internetowe, które zawierają obraz     | Strony internetowe, które zawierają obraz wejściowy.            |
| Przepisy                              | Strony internetowe zawierające przepisy na danie znajdujące się na obrazie wejściowym.            |

Oprócz tych szczegółowych informacji wyszukiwanie wizualne zwraca także zróżnicowany zestaw terminów (tagów) pobranych z obrazu wejściowego. Te tagi pozwalają użytkownikom na eksplorowanie pojęć znajdujących się na obrazie. Jeśli na przykład obraz wejściowy przedstawia sławnego sportowca, jednym z tagów może być imię i nazwisko tego sportowca, a drugim tagiem może być dyscyplina sportowa. Jeśli obraz wejściowy przedstawia szarlotkę, tagi mogą być następujące: szarlotka, ciasta, desery — dzięki temu użytkownicy mogą poznać pokrewne pojęcia.

Wyniki wyszukiwania wizualnego zawierają także pola ograniczenia dla regionów zainteresowania na obrazie. Jeśli na przykład obraz zawiera kilka znanych osób, wyniki mogą zawierać pola ograniczenia dla każdej rozpoznanej osoby na obrazie. Jeśli natomiast usługa Bing rozpozna produkt lub odzież na obrazie, wynik może zawierać pole ograniczenia dla rozpoznanego produktu lub elementu odzieży.

> [!IMPORTANT]
> Jeśli uzyskujesz szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów Bing, rozważ przejście do interfejsu API wyszukiwania wizualnego Bing, który zapewnia bardziej kompleksowe szczegółowe informacje.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wizualnego Bing jest usługą internetową zgodną z wzorcem REST, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Możesz użyć tej usługi za pomocą interfejsu API REST lub zestawu SDK.

1. Utwórz konto interfejsu API usług Cognitive Services z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć konto.
2. Wyślij żądanie do interfejsu API przy użyciu prawidłowego zapytania wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.


## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) interfejsu API wyszukiwania obrazów Bing.
Ta demonstracja pokazuje, jak można szybko dostosować zapytanie wyszukiwania i wyszukać obrazy w Internecie.

Gdy wszystko jest gotowe do wywołania interfejsu API, utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz przewodniki Szybki start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Zobacz też

* [Dokumentacja wyszukiwania wizualnego Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) zawiera definicje i informacje na temat punktów końcowych, nagłówków, odpowiedzi interfejsu API i parametrów zapytania, których możesz użyć do żądania wyników wyszukiwania na podstawie obrazu.

* [Wymagania dotyczące użycia i wyświetlania Bing](./use-and-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.
