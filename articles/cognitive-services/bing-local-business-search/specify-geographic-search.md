---
title: Użyj granicach geograficznych, aby przefiltrować wyniki z lokalnych firm interfejsu API wyszukiwania Bing | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Aby dowiedzieć się, jak filtrować wyniki wyszukiwania z lokalnych firm interfejsu API wyszukiwania Bing, należy użyć w tym artykule.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e87ef96184974d10f99028be13399d1422a1c774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796596"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Użyj granicach geograficznych, aby przefiltrować wyniki z lokalnych firm interfejsu API wyszukiwania Bing

Lokalnych firm interfejsu API wyszukiwania Bing pozwala ustawić granice dla określonego obszaru geograficznego, czy chcesz wyszukiwać za pomocą `localCircularView` lub `localMapView` parametry zapytania. Należy użyć tylko jeden parametr zapytania. 

Jeśli termin wyszukiwania zawiera jawne lokalizacji geograficznej, lokalnych firm interfejsu API usługi Bing automatycznie użyje go do określają granice dla wyników wyszukiwania. Na przykład, jeśli termin wyszukiwania jest `sailing in San Diego`, następnie `San Diego` będzie używana jako lokalizacja i inne określone lokalizacje w parametrach zapytania lub nagłówków użytkownika zostaną zignorowane. 

Jeśli lokalizacji geograficznej nie zostanie wykryte w termin wyszukiwania, a nie lokalizacji geograficznej jest określony za pomocą parametrów zapytania określić lokalizację, w żądaniu podejmie lokalnych firm interfejsu API wyszukiwania Bing `X-Search-ClientIP` lub `X-Search-Location` nagłówków. Jeśli zostanie określona żadna nagłówka, interfejs API określają lokalizację, z IP klienta żądania lub współrzędne GPS urządzenia przenośne.

## <a name="localcircularview"></a>localCircularView

`localCircularView` Parametr tworzy cykliczne obszaru geograficznego wokół zestaw współrzędnych szerokości/długości geograficznej, zdefiniowane przez usługi radius. Korzystając z tego parametru, odpowiedzi z lokalnych firm interfejsu API wyszukiwania Bing będzie zawierał tylko lokalizacje w tym okrąg, w odróżnieniu od `localMapView` parametrów, które mogą obejmować lokalizacje nieco poza obszarem wyszukiwania.

Aby określić obszaru cykliczne wyszukiwanie geograficzne, wybierz i szerokość geograficzną, która będzie służyć jako środka okręgu i promienia w metrach. Ten parametr może następnie być dołączona ciągu zapytania, na przykład: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Pełnej kwerendy:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` Parametr określa prostokątny obszar geograficzny do wyszukiwania, za pomocą dwa zestawy współrzędnych w celu określania jej Południowo-Wschodnia i północno-zachodnie rogi. Korzystając z tego parametru, odpowiedzi z lokalnych firm interfejsu API wyszukiwania Bing mogą obejmować lokalizacje w obrębie i po prostu poza określonym obszarze, w przeciwieństwie do `localCircularView` parametr, który zawiera tylko lokalizacje w obszarze wyszukiwania.

Aby określić obszaru search prostokątny, wybierz dwa zestawy współrzędnych szerokości/długości geograficznej, która będzie służyć jako południowo-wschodnia i północno-zachodnie rogi granicy. Pamiętaj najpierw zdefiniować współrzędne południowo-, jak w poniższym przykładzie: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Pełnej kwerendy:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Kolejne kroki
- [Szybki Start Java wyszukiwania lokalnych firmach](quickstarts/local-search-java-quickstart.md)
- [Wyszukiwanie lokalnych firmach C# Szybki Start](quickstarts/local-quickstart.md)
- [Szybki Start węzła wyszukiwania lokalnych firmach](quickstarts/local-search-node-quickstart.md)
- [Lokalnych firm wyszukiwania Python Szybki Start](quickstarts/local-search-python-quickstart.md)
