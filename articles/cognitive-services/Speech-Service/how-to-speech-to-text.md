---
title: Używaj mowy na tekst
description: Dowiedz się, jak używać zamiana mowy na tekst w usłudze rozpoznawania mowy
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 5e7916660275ab45c4556f1169f3e68fe1d3cb85
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282300"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Użyj "Mowy na tekst" usługi mowy

Możesz użyć **zamiana mowy na tekst** w aplikacjach na dwa różne sposoby.

| Metoda | Opis |
|-|-|
| [SDK](speech-sdk.md) | Najprostszą metodą dla deweloperów języka C/C++, C# i Java |
| [REST](rest-apis.md) | Rozpoznawanie krótkich wypowiedzi za pomocą żądania HTTP POST | 

## <a name="using-the-sdk"></a>Używanie zestawu SDK

[Zestaw SDK rozpoznawania mowy](speech-sdk.md) zapewnia Najprostszym sposobem użycia **zamiana mowy na tekst** w aplikacji z pełnym zestawem funkcji.

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi rozpoznawania mowy i [region](regions.md) lub token autoryzacji. Można również skonfigurować opcje, takie jak język rozpoznawania lub niestandardowy punkt końcowy dla własnych modeli rozpoznawania mowy, w tym momencie.

2. Pobierz aparat rozpoznawania z fabryki. Dostępne są trzy różne typy aparatów rozpoznawania gestów. Każdy typ żądającego aparatu rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

    Aparat rozpoznawania | Funkcja
    -|-
    Aparat rozpoznawania mowy|Zawiera tekst transkrypcja mowy
    Aparat rozpoznawania elementu Intent|Pochodzi zamiar osoby mówiącej za pośrednictwem [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) po rozpoznawania\*
    Aparat rozpoznawania tłumaczenia|Wykonuje translację uzyskanego tekstu na inny język (zobacz [tłumaczenia mowy](how-to-translate-speech.md))

    \* *Dla rozpoznawanie intencji należy użyć oddzielnych klucz subskrypcji usługi LUIS podczas tworzenia fabryka mowy dla rozpoznawania intencji.*
    
4. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

5. Rozpocznij rozpoznawania.
   Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()`, która zwraca pierwszy wypowiedź, które są rozpoznawane.
   Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` , blokując zdarzenia asynchroniczne rozpoznawanie wyników.

### <a name="sdk-samples"></a>Przykłady zestawu SDK

Aby uzyskać najnowszy zestaw przykładów, zobacz [Cognitive Services mowy SDK przykładowe repozytorium usługi GitHub](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Za pomocą interfejsu API REST

Interfejs API REST jest najprostszym sposobem na rozpoznawanie mowy, jeśli nie używasz języka obsługiwanych przez zestaw SDK. Wprowadzone żądanie HTTP POST do punktu końcowego usługi przekazywania całego wypowiedź w treści żądania. Otrzymasz odpowiedź rozpoznany tekst.

> [!NOTE]
> Wypowiedzi są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.

Aby uzyskać więcej informacji na temat **zamiana mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text). Aby zobaczyć go w działaniu, Pobierz [przykładów interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy, języka C++](quickstart-cpp-windows.md)
- [Jak rozpoznawać mowę w języku C#](quickstart-csharp-dotnet-windows.md)
- [Rozpoznawanie mowy w języku Java](quickstart-java-android.md)
