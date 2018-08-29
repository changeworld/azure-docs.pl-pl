---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122641"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **zamiana mowy na tekst** w aplikacji z pełnym zestawem funkcji.

1. Tworzenie fabryki mowy i podać klucz subskrypcji usługi mowy (lub token autoryzacji) i [region](~/articles/cognitive-services/speech-service/regions.md) jako parametry. Możesz też podać niestandardowy punkt końcowy do określania niestandardowej punktu końcowego.

1. Uzyskaj rozpoznawania mowy z fabryki mowy. Można skonfigurować język i format danych wyjściowych. Aparat rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe. W przeciwnym razie aplikacja odbiera tylko wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()` metody. Ta metoda zwraca pierwszy wypowiedź rozpoznane. Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` metody. Umożliwia powiązanie zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz poniższe fragmenty kodu w scenariuszach rozpoznawania mowy, korzystających z zestawu SDK rozpoznawania mowy.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
