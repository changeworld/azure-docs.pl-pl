---
title: Co nowego w interfejs API analizy tekstu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ten artykuł zawiera informacje o nowych wersjach i funkcjach usługi Azure Cognitive Services analiza tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 7e4eb8f7f9077ce04ee8138580a836637b89cf8c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835595"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co nowego w interfejsie API analizy tekstu?

Interfejs API analizy tekstu jest regularnie aktualizowana. Aby zachować aktualność dzięki najnowszym zmianom, ten artykuł zawiera informacje o nowych wersjach i funkcjach.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Nazwaną jednostkę rozpoznawania v3 — publiczna wersja zapoznawcza — październik 2019

Kolejna wersja usługi nazwanego rozpoznawania jednostek (NER) jest teraz dostępna w publicznej wersji zapoznawczej i zapewnia rozszerzoną funkcję wykrywania i kategoryzacji jednostek znalezionych w tekście. Oferuje ona następujące możliwości:

* Rozpoznawanie następujących typów nowych jednostek:
    * Numer telefonu
    * Adres IP

* Nowy punkt końcowy do rozpoznawania typów jednostek informacji osobistych (tylko w języku angielskim)
* Oddziel punkty końcowe do rozpoznawania jednostek i łączenia jednostek.

Łączenie jednostek obsługuje język angielski i hiszpański. Obsługa języka NER zależy od typu jednostki. Aby uzyskać więcej informacji, Skorzystaj z poniższego linku. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat nazwanego rozpoznawania jednostek v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Analiza tonacji v3 (publiczna wersja zapoznawcza) — październik 2019

Następna wersja analiza tonacji jest teraz dostępna w publicznej wersji zapoznawczej i oferuje znaczące ulepszenia w zakresie i szczegółach związanych z kategoryzacją i ocenianiem tekstu w interfejsie API. Ponadto zapewnia:

* Automatyczne etykietowanie dla różnych mową w tekście.
* Tonacji analizę i dane wyjściowe na poziomie dokumentu i zdania. 

Obsługuje język angielski (`en`), japoński (`ja`), chiński uproszczony (`zh-Hans`), chiński tradycyjny (`zh-Hant`), francuski (`fr`), włoski (`it`), hiszpański (`es`), holenderski (`nl`), portugalski (`pt`), niemiecki (`de`) i dostępny w następujących regionach: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`i `West US 2`.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o analiza tonacji wersji 3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API analizy tekstu?](overview.md)  
* [Przykładowe scenariusze użytkownika](text-analytics-user-scenarios.md)
* [Analiza tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Wykrywanie języka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznawanie jednostek](how-tos/text-analytics-how-to-entity-linking.md)
* [Wyodrębnianie kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md)
