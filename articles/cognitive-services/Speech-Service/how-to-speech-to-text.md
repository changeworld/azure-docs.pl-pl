---
title: Użyj mowy na tekst | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji rozpoznawania mowy na tekst w usłudze mowy
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356228"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Użyj "Mowy Text" w usłudze mowy

Można użyć **mowy na tekst** w aplikacjach na dwa różne sposoby.

| Metoda | Opis |
|-|-|
| [SDK](speech-sdk.md) | Najprostszą metodą deweloperzy C/C++, C# i Java * |
| [REST](rest-apis.md) | Rozpoznaje krótkich zniesławiających za pomocą żądania HTTP POST | 

\* *Zestaw SDK Java jest częścią [mowy urządzeń SDK](speech-devices-sdk.md).*

## <a name="using-the-sdk"></a>Używanie zestawu SDK

[Mowy SDK](speech-sdk.md) zapewnia Najprostszym sposobem użycia **mowy na tekst** w aplikacji z zachowaniem pełnej funkcjonalności.

1. Utwórz fabrykę mowy udostępnianie klucz mowy usługi subskrypcji lub token autoryzacji. Można również skonfigurować opcje, takie jak język rozpoznawania lub niestandardowe punktu końcowego dla własnych modeli rozpoznawania mowy, w tym momencie.

2. Pobierz aparat rozpoznawania z fabryki. Dostępne są trzy różne typy aparatów rozpoznawania. Każdy typ aparatu rozpoznawania służy mikrofon domyślnego urządzenia, strumieniem audio lub audio z pliku.

    Aparat rozpoznawania | Funkcja
    -|-
    Aparat rozpoznawania mowy|Udostępnia przekształcania tekstu mowy
    Aparat rozpoznawania konwersji|Pochodzi zamiar prelegenta za pośrednictwem [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) po rozpoznawania\*
    Aparat rozpoznawania tłumaczenia|Wykonuje translację transcribed tekst na inny język (zobacz [tłumaczenia mowy](how-to-translate-speech.md))

    \* *Rozpoznawania konwersji należy użyć oddzielnych klucza subskrypcji LUIS podczas tworzenia fabryki mowy dla konwersji aparatu rozpoznawania.*
    
4. W razie potrzeby pochłaniać zdarzenia dla operacji asynchronicznej. Aparat rozpoznawania następnie wywołuje programu obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacja będzie odbierać końcowego przekształcania wyniku.

5. Uruchom rozpoznawania.

### <a name="sdk-samples"></a>Przykłady zestawu SDK

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Przy użyciu interfejsu API REST

Interfejs API REST jest najprostszym sposobem rozpoznawanie mowy, jeśli nie jest używany język obsługiwany przez zestaw SDK. Wprowadzone żądanie HTTP POST do punktu końcowego usługi przekazywanie całego utterance w treści żądania. Otrzymasz odpowiedź rozpoznany tekst.

> [!NOTE]
> Zniesławiających są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.


Aby uzyskać więcej informacji na temat **mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text). Aby zobaczyć ją w akcji, Pobierz [przykłady interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
