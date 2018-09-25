---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: dc2e025cdd9fcc153f3cb81988a9ca3ec729c934
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47021145"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **tłumaczenia mowy** w aplikacji.
Zestaw SDK udostępnia pełną funkcjonalność usługi. Podstawowy proces podczas wykonywania tłumaczeń mowy dokonywanych obejmuje następujące kroki:

1. Utwórz konfigurację tłumaczenia mowy i podać klucz subskrypcji usługi mowy (lub token autoryzacji) i [region](~/articles/cognitive-services/speech-service/regions.md) jako parametry. Zmień konfigurację, zgodnie z potrzebami. Na przykład możesz można skonfigurować języków tłumaczeń źródłowym i docelowym, a także określić, czy dane wyjściowe tekstu i mowy.

1. Utwórz rozpoznawania tłumaczenia z fabryki mowy. Podaj audio konfiguracji, jeśli chcesz, aby rozpoznać ze źródła innego niż mikrofon domyślne (na przykład strumienia audio lub plik audio).

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe, a także zdarzeń syntezy, opcjonalny danych wyjściowych audio. W przeciwnym razie aplikacja odbiera tylko wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Dla pojedynczego zrzutu tłumaczenia użyj `RecognizeOnceAsync()` metody, która zwraca pierwszy wypowiedź rozpoznane. W przypadku długotrwałych tłumaczenia, użyj `StartContinuousRecognitionAsync()` metody i tie zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz poniższe fragmenty kodu dla scenariuszy osobistego tłumaczenia mowy, które używają zestawu SDK rozpoznawania mowy.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
