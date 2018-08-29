---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144564"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **tłumaczenia mowy** w aplikacji.
Zestaw SDK udostępnia pełną funkcjonalność usługi. Podstawowy proces podczas wykonywania tłumaczeń mowy dokonywanych obejmuje następujące kroki:

1. Tworzenie fabryki mowy i podaj klucz subskrypcji usługi mowy lub token autoryzacji i [region](~/articles/cognitive-services/speech-service/regions.md) jako parametry.
   
1. Utwórz rozpoznawania tłumaczenia z fabryki mowy. Można skonfigurować języków tłumaczeń źródłowym i docelowym, a także określić, czy dane wyjściowe tekstu i mowy. Istnieją różne podtypy aparatów rozpoznawania tłumaczenia na podstawie źródła audio, którego używasz.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe, a także zdarzeń syntezy, opcjonalny danych wyjściowych audio. W przeciwnym razie aplikacja odbiera tylko wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Dla pojedynczego zrzutu tłumaczenia użyj `RecognizeAsync()` metody, która zwraca pierwszy wypowiedź rozpoznane. W przypadku długotrwałych tłumaczenia, użyj `StartContinuousRecognitionAsync()` metody i tie zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz poniższe fragmenty kodu dla scenariuszy osobistego tłumaczenia mowy, które używają zestawu SDK rozpoznawania mowy.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
