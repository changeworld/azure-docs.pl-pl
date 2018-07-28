---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1b55576581ebddc90c0af6b99a04dbe66d2e3b87
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330909"
---
<!-- N.B. no header, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) umożliwia rozpoznawanie intencji z funkcji rozpoznawania mowy obsługiwane przez usługę rozpoznawania mowy i [Language Understanding Intelligent service (LUIS)](https://luis.ai).

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi Language Understanding i [region](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition).


1. Uzyskaj intencji rozpoznawania z fabryki mowy.
   Aparat rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne.
   Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe.
   W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania.
   Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()`, która zwraca pierwszy wypowiedź, które są rozpoznawane.
   Rozpoznawanie długotrwałych, takich jak transkrypcji, można użyć `StartContinuousRecognitionAsync()` , blokując zdarzenia asynchroniczne rozpoznawanie wyników.

Poniżej przedstawiono kilka fragmentów kodu dla scenariuszy funkcja rozpoznawania celu przy użyciu zestawu SDK rozpoznawania mowy.

> [!NOTE]
> Najpierw Uzyskaj klucz subskrypcji.
> W przeciwieństwie do innych usług obsługiwanych przez zestaw SDK rozpoznawania mowy funkcja rozpoznawania celu wymaga klucza określonej subskrypcji.
> [W tym miejscu](https://www.luis.ai) możesz znaleźć dodatkowe informacje na temat technologii funkcja rozpoznawania celu, a także informacje o tym, jak uzyskać klucz subskrypcji.
> Zastąp własnym kluczem subskrypcji Language Understanding [region subskrypcji](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)i AppId intencji modelu w odpowiednich miejscach w przykładach.
