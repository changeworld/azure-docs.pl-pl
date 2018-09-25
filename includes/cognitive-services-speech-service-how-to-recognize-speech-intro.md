---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 3394fc2f6395799a252b645635d982b4573296c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47000719"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **zamiana mowy na tekst** w aplikacji z pełnym zestawem funkcji.

1. Utwórz konfigurację mowy i podać klucz subskrypcji usługi mowy (lub token autoryzacji) i [region](~/articles/cognitive-services/speech-service/regions.md) jako parametry. Zmień konfigurację, zgodnie z potrzebami. Na przykład podać niestandardowy punkt końcowy do określania niestandardowej punktu końcowego lub wybierz język mówiony lub format danych wyjściowych.

1. Utwórz rozpoznawania mowy z konfiguracji mowy. Podaj audio konfiguracji, jeśli chcesz, aby rozpoznać ze źródła innego niż mikrofon domyślne (na przykład strumienia audio lub plik audio).

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacja odbiera tylko wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeOnceAsync()` metody. Ta metoda zwraca pierwszy wypowiedź rozpoznane. Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` metody. Umożliwia powiązanie zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz poniższe fragmenty kodu w scenariuszach rozpoznawania mowy, korzystających z zestawu SDK rozpoznawania mowy.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
