---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a3cbfc3677c5b1a19b83a82da9bcd6bed3108152
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346248"
---
<!-- N.B. no header, language-agnostic -->

[Zestaw SDK rozpoznawania mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) zapewnia sposób, rozpoznawał **intencji z wypowiedzi**, jest to obsługiwane przez usługę rozpoznawania mowy i [Language Understanding Intelligent service (LUIS)](https://luis.ai).

1. Tworzenie fabryki mowy, zapewniając klucz subskrypcji usługi Language Understanding i [region](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition). Klucz subskrypcji usługi Language Understanding nosi nazwę **klucza punktu końcowego** w dokumentacji usługi. Nie można użyć usługa interpretacji języka, tworzenia klucza. Zobacz też **Uwaga** poniżej.

1. Uzyskaj intencji rozpoznawania z fabryki mowy. Aparat rozpoznawania służy mikrofon domyślnego urządzenia, strumień audio lub dźwięk z pliku.

1. Pobierz model interpretacji języka, które są oparte na identyfikator swojej aplikacji i dodawanie intencji, które są wymagane. 

1. Blokując zdarzenia dla operacji asynchronicznej, jeśli to konieczne. Aparat rozpoznawania następnie wywołuje inne programy obsługi zdarzeń, gdy ma ona wyniki tymczasowe i końcowe (łącznie z opcjami). W przeciwnym razie aplikacji zostanie wyświetlony wynik końcowy transkrypcji.

1. Uruchom rozpoznawanie intencji. Rozpoznawanie pojedynczego zrzutu, takich jak rozpoznawanie polecenie lub zapytanie, można użyć `RecognizeAsync()`, która zwraca pierwszy wypowiedź, które są rozpoznawane. Rozpoznawanie długotrwałych można użyć `StartContinuousRecognitionAsync()` , blokując zdarzenia asynchroniczne rozpoznawanie wyników.

Zobacz fragmenty kodu poniżej dla scenariuszy funkcja rozpoznawania celu przy użyciu zestawu SDK rozpoznawania mowy. Zastąp własnym kluczem subskrypcji interpretacji języka (klucza punktu końcowego) [region subskrypcji](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition)i AppId intencji modelu w odpowiednich miejscach w przykładach.

> [!NOTE]
> W przeciwieństwie do innych usług obsługiwanych przez zestaw SDK rozpoznawania mowy funkcja rozpoznawania celu wymaga klucza subskrypcji określonego (klucza punktu końcowego usługi Language Understanding). [W tym miejscu](https://www.luis.ai) można znaleźć dodatkowe informacje na temat technologii rozpoznawanie intencji. Jak uzyskać **klucza punktu końcowego** opisano [tutaj](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
