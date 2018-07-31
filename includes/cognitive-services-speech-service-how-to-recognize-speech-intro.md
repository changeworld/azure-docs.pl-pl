---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: d7d73d9c5e85ac550b24c73797d536dc4d0fc6ed
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346272"
---
<!-- N.B. no header, no intents here, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **zamiana mowy na tekst** w aplikacji z pełnym zestawem funkcji.

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi mowy lub token autoryzacji i [region](~/articles/cognitive-services/speech-service/regions.md). Można również określić niestandardowy punkt końcowy do określania niestandardowej punktu końcowego.

1. Uzyskaj rozpoznawania mowy z fabryki mowy. Można skonfigurować język, a także format danych wyjściowych.  Aparat rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe.  W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()`, która zwraca pierwszy wypowiedź, które są rozpoznawane. Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` , blokując zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz fragmenty kodu poniżej scenariuszach rozpoznawania mowy, przy użyciu zestawu SDK rozpoznawania mowy.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
