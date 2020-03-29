---
title: Co nowego w interfejsie API analizy tekstu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ten artykuł zawiera informacje o nowych wersjach i funkcjach usługi Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188801"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co nowego w interfejsie API analizy tekstu?

Interfejs API analizy tekstu jest aktualizowany na bieżąco. Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje o nowych wersjach i funkcjach.

## <a name="february-2020"></a>Luty 2020 r.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Obsługa SDK dla interfejsu API usługi Text Analytics w wersji 3 Public Preview

W ramach [ujednoliconej wersji SDK platformy Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)zestaw SDK interfejsu API analizy tekstu w wersji 3 jest teraz dostępny jako publiczna wersja zapoznawcza dla następujących języków programowania:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o SDK interfejsu API usługi Text Analytics w wersji 3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Publiczna wersja zapoznawcza rozpoznawania nazwanych jednostek w wersji 3

Dodatkowe typy jednostek są teraz dostępne w publicznej usłudze rozpoznawania jednostek (NER) w wersji 3 w wersji publicznej, ponieważ rozszerzamy wykrywanie jednostek informacji ogólnych i osobistych znalezionych w tekście. Ta aktualizacja wprowadza [wersję](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`modelu , która obejmuje:

* Uznanie następujących typów jednostek ogólnych (tylko w języku angielskim):
    * Typ osoby
    * Product (Produkt)
    * Wydarzenie
    * Jednostka geopolityczna (GPE) jako podtyp w obszarze Lokalizacja
    * Umiejętności

* Rozpoznawanie następujących typów jednostek danych osobowych (tylko w języku angielskim):
    * Person (Osoba)
    * Organizacja
    * Wiek jako podtyp w obszarze Ilość
    * Data jako podtyp w obszarze DateTime
    * Adres e-mail 
    * Numer telefonu (tylko USA)
    * Adres URL
    * Adres IP

> [!div class="nextstepaction"]
> [Dowiedz się więcej o rozpoznawaniu nazwanych encji w wersji 3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Październik 2019 r.

#### <a name="named-entity-recognition-ner"></a>Rozpoznawanie nazwanych jednostek (NER)

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) do rozpoznawania typów encji informacji osobistych (tylko w języku angielskim)

* Oddzielne punkty końcowe dla [rozpoznawania jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) i [łączenia jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Wersja modelu](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, która zawiera:
    * Rozszerzone wykrywanie i kategoryzacja jednostek znalezionych w tekście. 
    * Rozpoznawanie następujących nowych typów jednostek:
        * Numer telefonu
        * Adres IP

Łączenie jednostek obsługuje angielski i hiszpański. Obsługa języka NER zależy od typu jednostki.

#### <a name="sentiment-analysis-v3-public-preview"></a>Analiza tonacji w wersji 3 publiczna wersja zapoznawcza

* [Nowy punkt końcowy](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) do analizowania tonacji.
* [Wersja modelu](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, która zawiera:

    * Znaczące ulepszenia w dokładności i szczegółowości kategoryzacji i ocenianiu tekstu interfejsu API.
    * Automatyczne etykietowanie różnych odczuć w tekście.
    * Analiza tonacji i dane wyjściowe na poziomie dokumentu i zdania. 

Obsługuje angielski`en`( ),`ja`japoński (`zh-Hans`), chiński`zh-Hant`uproszczony`fr`( ),`it`chiński`es`tradycyjny`nl`( ),`pt`francuski (`de`), włoski ( ), hiszpański `Australia East`( `Central Canada` `Central US`), holenderski ( ), portugalski ( ), i niemiecki () i jest dostępny w następujących regionach: , , , `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, , `West Europe`, , i `West US 2`. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o analizie tonacji w wersji 3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Następne kroki

* [Czym jest interfejs API analizy tekstu?](overview.md)  
* [Przykładowe scenariusze dotyczące użytkowników](text-analytics-user-scenarios.md)
* [Analiza tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Wykrywanie języka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznawanie jednostek](how-tos/text-analytics-how-to-entity-linking.md)
* [Wyodrębnianie fraz kluczowych](how-tos/text-analytics-how-to-keyword-extraction.md)
