---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ca342a812f6a8de19c732b5df4fab14a825f6c51
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330927"
---
<!-- N.B. no header, no intents here, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **zamiana mowy na tekst** w aplikacji z pełnym zestawem funkcji.

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi mowy lub token autoryzacji i [region](~/articles/cognitive-services/speech-service/regions.md).
   Można również skonfigurować opcje, takie jak język rozpoznawania lub niestandardowy punkt końcowy dla własnych modeli rozpoznawania mowy.

1. Uzyskaj rozpoznawania mowy z fabryki mowy.
   Aparat rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne.
   Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe.
   W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania.
   Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()`, która zwraca pierwszy wypowiedź, które są rozpoznawane.
   Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` , blokując zdarzenia asynchroniczne rozpoznawanie wyników.

Poniżej przedstawiono kilka fragmentów kodu w scenariuszach rozpoznawania mowy, przy użyciu zestawu SDK rozpoznawania mowy.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
