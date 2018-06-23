---
title: Wyszukiwania usługi Bing zestawu SDK | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Bing wyszukiwania zestawu SDK sieci Web dla aplikacji, które wyszukiwanie w sieci web.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349476"
---
# <a name="bing-search-sdk"></a>Wyszukiwania usługi Bing zestawu SDK
Scenariusze zawierają przykłady interfejsu API Bing sieci Web wyszukiwania który:
1. Użyj zapytania jednowyrazowej wyniki i wydrukuj nazwy i adresu URL dla pierwszego dnia każdego sieci web, obrazów, wiadomości i wideo wyników.
2. Zapytanie na frazę, sprawdź liczbę wyników i wydrukuj nazwy i adresu URL pierwszego wyniku.
3. Zapytanie dotyczące terminu wyszukiwania z odpowiedzią filtruje zestaw do `news` i drukowanie szczegółów wyników wiadomości.
4. Zapytanie dotyczące terminu wyszukiwania z `answerCount` i `promote` parametry, a następnie wydrukuj szczegółów wyników.

Zestawy SDK wyszukiwania usługi Bing należy funkcji wyszukiwania w sieci web łatwo dostępne w następujących językach programowania:
* Rozpoczynanie pracy z [próbek .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * Zobacz też [bibliotek .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch) definicje i zależności.
* Rozpoczynanie pracy z [Node.js — przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Zobacz też [bibliotek Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch) definicje i zależności.
* Rozpoczynanie pracy z [próbek Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Zobacz też [bibliotek Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) definicje i zależności.
* Rozpoczynanie pracy z [próbek Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Zobacz też [bibliotek języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch) definicje i zależności.

Przykłady zestawu SDK dla każdego języka obejmują plik ReadMe o szczegółowe informacje o wymaganiach wstępnych oraz instalowania/uruchomiony próbek.
