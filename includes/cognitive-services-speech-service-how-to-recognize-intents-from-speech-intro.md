---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805495"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia sposób, rozpoznawał **intencji z wypowiedzi** i jest obsługiwany przez usług Cognitive Services [Language Understanding Intelligent service (LUIS)](https://www.luis.ai/home).

1. Utwórz konfigurację mowy z kluczem subskrypcji usługi LUIS i [region](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) jako parametry. Klucz subskrypcji usługi LUIS jest nazywany **klucza punktu końcowego** w dokumentacji usługi. Nie można użyć usługa LUIS tworzenia klucza. (Zobacz uwagi w dalszej części tej sekcji).

1. Utwórz rozpoznawania intencji na podstawie konfiguracji mowy. Podaj audio konfiguracji, jeśli chcesz, aby rozpoznać ze źródła innego niż mikrofon domyślne (na przykład strumienia audio lub plik audio).

1. Pobierz model interpretacji języka, który opiera się na swoje **AppId**. Dodawanie intencji, które są wymagane.

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe (z uwzględnieniem intencje). Jeśli nie blokowały zdarzeń, aplikacji otrzymuje tylko wynik końcowy transkrypcji.

1. Uruchom rozpoznawanie intencji. Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeOnceAsync()` metody. Ta metoda zwraca pierwszy wypowiedź rozpoznane. Rozpoznawanie długotrwałych, można użyć `StartContinuousRecognitionAsync()` metody. Umożliwia powiązanie zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz poniższe fragmenty kodu w przypadku scenariuszy rozpoznawanie intencji, które zestaw SDK rozpoznawania mowy. Zastąp wartości w próbce własny klucz subskrypcji usługi LUIS (klucza punktu końcowego) [region subskrypcji](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)i **AppId** intencji modelu.

> [!NOTE]
> W przeciwieństwie do innych usług obsługiwanych przez zestaw SDK rozpoznawania mowy funkcja rozpoznawania celu wymaga klucza subskrypcji określonego (klucza punktu końcowego usługi LUIS). Aby uzyskać informacji na temat technologii rozpoznawanie intencji, zobacz [witryny sieci Web usługi LUIS](https://www.luis.ai). Aby uzyskać informacje na temat sposobu uzyskania **klucza punktu końcowego**, zobacz [tworzenia klucza punktu końcowego usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
