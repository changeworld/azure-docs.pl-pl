---
title: Filtrowanie wyników z interfejsu API wyszukiwania lokalnego firmy usługi Bing za pomocą granic geograficznych
titleSuffix: Azure Cognitive Services
description: Ten artykuł służy do filtrowania wyników wyszukiwania w interfejsie API wyszukiwania lokalnego firmy Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906244"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Filtrowanie wyników z interfejsu API wyszukiwania lokalnego firmy usługi Bing za pomocą granic geograficznych

Interfejs API wyszukiwania lokalnego firmy Bing umożliwia ustawienie granic określonego obszaru geograficznego, `localCircularView` `localMapView` który chcesz przeszukiwać przy użyciu parametrów lub zapytań. Pamiętaj, aby używać tylko jeden parametr w zapytaniach. 

Jeśli wyszukiwany termin zawiera jawną lokalizację geograficzną, interfejs API firmy lokalnej usługi Bing automatycznie użyje go do ustawienia granic wyników wyszukiwania. Na przykład, jeśli wyszukiwany termin jest `sailing in San Diego`, a następnie `San Diego` będą używane jako lokalizacja i inne określone lokalizacje w parametrach zapytania lub nagłówki użytkownika zostaną zignorowane. 

Jeśli lokalizacja geograficzna nie zostanie wykryta w wyszukiwanym terminie, a przy użyciu parametrów zapytania nie określono lokalizacji geograficznej, `X-Search-ClientIP` `X-Search-Location` interfejs API wyszukiwania lokalnego firmy Bing podejmie próbę określenia lokalizacji na podstawie żądania lub nagłówków. Jeśli żaden z nagłówków nie zostanie określony, interfejs API określi lokalizację na podstawie adresu IP żądania klienta lub współrzędne GPS dla urządzeń przenośnych.

## <a name="localcircularview"></a>localCircularView

Parametr `localCircularView` tworzy okrągły obszar geograficzny wokół zestawu współrzędnych szerokości/długości geograficznej, zdefiniowanych przez promień. Podczas korzystania z tego parametru odpowiedzi z interfejsu API wyszukiwania lokalnego firmy `localMapView` Bing będą obejmować tylko lokalizacje w tym kręgu, w przeciwieństwie do parametru, który może zawierać lokalizacje nieco poza obszarem wyszukiwania.

Aby określić okrągły obszar wyszukiwania geograficznego, wybierz szerokość i długość geograficzną, aby służyć jako środek okręgu, oraz promień w metrach. Ten parametr można następnie dołączyć do ciągu zapytania, `q=Restaurants&localCircularView=47.6421,-122.13715,5000`na przykład: .

Kompletna kwerenda:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView (Widok na localMapa)

Parametr `localMapView` określa prostokątny obszar geograficzny do wyszukiwania, używając dwóch zestawów współrzędnych do określenia narożników południowo-wschodnich i północno-zachodnich. W przypadku korzystania z tego parametru odpowiedzi z interfejsu API wyszukiwania lokalnego firmy `localCircularView` Bing mogą obejmować lokalizacje w określonym obszarze i tuż poza nim, w przeciwieństwie do parametru, który obejmuje tylko lokalizacje w obszarze wyszukiwania.

Aby określić prostokątny obszar wyszukiwania, wybierz dwa zestawy współrzędnych szerokości/długości geograficznej, które będą służyć jako południowo-wschodnie i północno-zachodnie narożniki granicy. Pamiętaj, aby najpierw zdefiniować współrzędne `localMapView=47.619987,-122.181671,47.6421,-122.13715`południowo-wschodnie, jak w poniższym przykładzie: .

Kompletna kwerenda:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Następne kroki
- [Szybki start wyszukiwania w firmie lokalnej](quickstarts/local-search-java-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](quickstarts/local-quickstart.md)
- [Szybki start lokalnego węzła wyszukiwania firm](quickstarts/local-search-node-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](quickstarts/local-search-python-quickstart.md)
