---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 74b9da1b3b81f9f35f231ef5caef8eafedc9aefc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343038"
---
<!-- N.B. no header, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia Najprostszym sposobem użycia **tłumaczenia mowy** w aplikacji.
Zestaw SDK udostępnia pełną funkcjonalność usługi. Podstawowy proces podczas wykonywania tłumaczeń mowy dokonywanych obejmuje następujące kroki:

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi mowy lub token autoryzacji i [region](~/articles/cognitive-services/speech-service/regions.md).
   
1. Utwórz rozpoznawania tłumaczenia z fabryki mowy. Można skonfigurować języków tłumaczeń źródłowym i docelowym, a także określenie, czy chcesz, aby dane wyjściowe tekstu i mowy. Istnieją różne podtypy aparatów rozpoznawania tłumaczenia na podstawie źródła audio, którego używasz.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe, a także zdarzeń syntezy, opcjonalny danych wyjściowych audio. W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Rozpocznij rozpoznawania. Na korzystanie z pojedynczego zrzutu tłumaczenia RecognizeAsync(), która zwraca pierwszy wypowiedź, które są rozpoznawane. Dla długotrwałych tłumaczenia należy użyć StartContinuousRecognitionAsync() i powiązać zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz fragmenty kodu poniżej dla scenariuszy osobistego tłumaczenia mowy przy użyciu zestawu SDK rozpoznawania mowy.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
