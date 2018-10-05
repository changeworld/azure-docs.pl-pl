---
title: Wyszukiwanie wiadomości Bing zestawu SDK
titleSuffix: Azure Cognitive Services
description: SDK wyszukiwania wiadomości Bing dla aplikacji, które wyszukiwanie w sieci web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801237"
---
# <a name="bing-search-sdk"></a>Wyszukiwanie Bing zestawu SDK
Przykłady interfejsu API wyszukiwania wiadomości Bing obejmują scenariusze który:
1. Kwerendy wiadomości wyszukiwane terminy z `market` i `count` parametry, sprawdź liczbę wyników, a następnie wydrukuj `totalEstimatedMatches`, nazwę, adres URL, opis, czasu opublikowania i nazwę dostawcy pierwszego wyniku wiadomości.
2. Kwerendy najnowsze wiadomości wyszukiwane terminy z `freshness` i `sortBy` parametry, sprawdź liczbę wyników, a następnie wydrukuj `totalEstimatedMatches`, adres URL, opis, czasu opublikowania i nazwę dostawcy pierwszego wyniku wiadomości.
3. Kategoria wiadomości dla zapytania `movie` i `TV entertainment` przy użyciu bezpiecznego wyszukiwania, sprawdź liczbę wyników, a następnie wydrukuj kategorii, nazwa, adres URL, opis, czasu opublikowania i nazwę dostawcy pierwszego wyniku wiadomości.
4. Zapytanie popularne tematy wiadomości w usłudze Bing, sprawdź liczbę wyników i wydrukuj nazwy, tekst zapytania, `webSearchUrl`, `newsSearchUrl` i adres URL pierwszego wyniku wiadomości.

Zestawy SDK wyszukiwania Bing należy funkcji wyszukiwania w sieci web łatwo dostępne w następujących językach programowania:
* Rozpoczynanie pracy z usługą [przykłady dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Zobacz też [bibliotek .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykłady dla platformy Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Zobacz też [biblioteki technologii Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykładów w języku Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Zobacz też [biblioteki Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) dla definicji i zależności.
* Rozpoczynanie pracy z usługą [przykłady w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Zobacz też [biblioteki języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) dla definicji i zależności.

Przykłady zestawu SDK dla każdego z języków zawierają pliku ReadMe ze szczegółowymi informacjami o wymaganiach wstępnych oraz instalowanie/uruchamiania przykładów.