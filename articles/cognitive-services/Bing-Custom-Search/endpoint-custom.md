---
title: Punkt końcowy wyszukiwania niestandardowego Bing
titleSuffix: Azure Cognitive Services
description: Twórz dostosowane środowiska wyszukiwania dla tematów, na których Ci zależy. Użytkownicy widzą wyniki wyszukiwania dostosowane do treści, na których im zależy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072788"
---
# <a name="custom-search"></a>Wyszukiwanie niestandardowe
Wyszukiwanie niestandardowe Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla interesujących Cię tematów. Użytkownicy zobaczą wyniki wyszukiwania dostosowane do zawartości, która ich interesuje, zamiast przewijać kolejne strony ogólnych wyników wyszukiwania zawierające nieistotną zawartość.

## <a name="custom-search-endpoint"></a>Niestandardowy punkt końcowy wyszukiwania
Aby uzyskać wyniki przy użyciu interfejsu API `GET` wyszukiwania niestandardowego Bing, wyślij żądanie do następującego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

Punkt końcowy. `?q=""`
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Przykłady opisujące sposób konfigurowania źródeł wyszukiwania niestandardowego można znaleźć w [samouczku](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Aby uzyskać szczegółowe informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów itp., zobacz odwołanie do [interfejsu API wyszukiwania niestandardowego Bing w wersji 7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)

## <a name="custom-search-response-json"></a>Niestandardowa odpowiedź wyszukiwania JSON
Niestandardowe żądanie wyszukiwania zwraca wyniki jako obiekty JSON, zobacz [Obiekty odpowiedzi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Niestandardowa autosuggest
Niestandardowy interfejs API autosugerowania umożliwia wysłanie częściowego terminu zapytania do usługi Bing i odzyskanie listy sugerowanych zapytań, które można skonfigurować. Za pomocą niestandardowego autosupicestu można dodać sugestie zwracane przez interfejs API i opcjonalnie określić, czy mają być dołączane sugestie generowane przez Bing.

## <a name="custom-autosuggest-endpoint"></a>Niestandardowy punkt końcowy autosugestyji
Aby zażądać sugestii kwerend niestandardowych, wyślij żądanie GET do:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Aby uzyskać informacje dotyczące definiowania sugestii niestandardowych, zobacz [Definiowanie niestandardowych sugestii wyszukiwania](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Niestandardowe wyszukiwanie obrazów
Interfejs API wyszukiwania obrazów niestandardowych umożliwia wysłanie zapytania wyszukiwania do usługi Bing i odzyskanie listy odpowiednich obrazów z wystąpienia wyszukiwania niestandardowego.

## <a name="custom-image-search-endpoint"></a>Niestandardowy punkt końcowy wyszukiwania obrazów
Aby zażądać obrazów z wystąpienia wyszukiwania niestandardowego, wyślij żądanie GET na następujący adres URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Aby uzyskać informacje dotyczące konfigurowania wystąpienia wyszukiwania niestandardowego, zobacz [Konfigurowanie niestandardowego środowiska wyszukiwania](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Następne kroki
Interfejsy API **usługi Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem.Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON. Wszystkie punkty końcowe obsługują kwerendy, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i promienia wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy, zobacz strony odwołań dla każdego typu.
Przykłady żądań podstawowych przy użyciu interfejsu API wyszukiwania niestandardowego można znaleźć w części [Szybkie uruchamianie wyszukiwania niestandardowego](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
