---
title: Wyszukiwania usługi Bing zestawu SDK | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Wyszukiwania usługi Bing zestawu SDK dla aplikacji, które wyszukiwanie w sieci web.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349464"
---
# <a name="bing-search-sdk"></a>Wyszukiwania usługi Bing zestawu SDK
Scenariusze zawierają przykłady interfejsu API wyszukiwania wiadomości Bing który:
1. Wykonanie kwerendy wiadomości wyszukiwanie `market` i `count` parametry, sprawdź liczbę wyników, a następnie wydrukuj `totalEstimatedMatches`, nazwa, adres URL, opis, opublikowanych czasu i nazwa dostawcy pierwszego wyniku wiadomości.
2. Wykonanie kwerendy najnowsze wiadomości wyszukiwanie `freshness` i `sortBy` parametry, sprawdź liczbę wyników, a następnie wydrukuj `totalEstimatedMatches`, adres URL, opis, opublikowanych czasu i nazwa dostawcy pierwszego wyniku wiadomości.
3. Kategoria wiadomości dla zapytania `movie` i `TV entertainment` z bezpiecznym wyszukiwania, sprawdź liczbę wyników, a następnie wydrukuj kategorii, nazwa, adres URL, opis, opublikowanych czasu i nazwa dostawcy pierwszego wyniku wiadomości.
4. Zapytanie trendów tematy wiadomości Bing, sprawdź liczbę wyników i wydrukuj nazwy, tekst zapytania, `webSearchUrl`, `newsSearchUrl` i adres URL pierwszego wyniku wiadomości.

Zestawy SDK wyszukiwania usługi Bing należy funkcji wyszukiwania w sieci web łatwo dostępne w następujących językach programowania:
* Rozpoczynanie pracy z [próbek .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Zobacz też [bibliotek .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) definicje i zależności.
* Rozpoczynanie pracy z [Node.js — przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Zobacz też [bibliotek Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) definicje i zależności.
* Rozpoczynanie pracy z [próbek Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Zobacz też [bibliotek Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) definicje i zależności.
* Rozpoczynanie pracy z [próbek Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Zobacz też [bibliotek języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) definicje i zależności.

Przykłady zestawu SDK dla każdego języka obejmują plik ReadMe o szczegółowe informacje o wymaganiach wstępnych oraz instalowania/uruchomiony próbek.