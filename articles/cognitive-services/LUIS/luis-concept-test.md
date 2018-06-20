---
title: Testowanie aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) umożliwia ciągłą pracę w swojej aplikacji, aby dostosować go i zwiększyć jej opis języka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266332"
---
# <a name="testing-in-luis"></a>Testowanie w LUIS

Testowanie polega na zapewnienie zniesławiających próbki LUIS i uzyskiwanie odpowiedzi rozpoznany LUIS intencje i jednostek. 

Możesz [test](interactive-test.md) LUIS interaktywnego, co utterance w czasie, lub podaj [partii](luis-concept-batch-test.md) z zniesławiających. Z testowaniem, porównanie bieżącej [active](luis-concept-version.md#active-version) modelu do opublikowanego modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Co to jest wynikiem podczas testowania?
Zobacz [wynik prognozowania](luis-concept-prediction-score.md) pojęcia, aby dowiedzieć się więcej na temat wyniki prognozowania.

## <a name="interactive-testing"></a>Testowanie interakcyjne
Testowanie interakcyjne odbywa się z **testu** panelu witryny sieci Web. Można wprowadzić utterance intencje i jednostki są zidentyfikowane i oceny. Jeśli nie jest LUIS przewidywania intencje i jednostek, zgodnie z oczekiwaniami na utterance w okienku testowania, skopiuj go do **zamiar** strony jako nowe utterance. Etykieta części tego utterance i uczenia LUIS. 

## <a name="batch-testing"></a>Testowanie usługi partia zadań
Zobacz [testowania partii](luis-concept-batch-test.md) podczas testowania więcej niż jeden utterance naraz.

## <a name="endpoint-testing"></a>Testowanie punktu końcowego
Można przetestować przy użyciu [punktu końcowego](luis-glossary.md#endpoint) maksymalnie dwie wersje aplikacji. Z wersją głównego lub dynamicznych aplikacji Ustaw jako **produkcji** punktu końcowego, druga wersja, aby dodać **przemieszczania** punktu końcowego. Takie podejście umożliwia trzy wersje utterance: bieżącego modelu w okienku testu [LUIS] [ LUIS] witryny sieci Web, a dwie wersje w dwóch różnych punktów końcowych. 

Wszystkie punktu końcowego testowania liczniki kierunku limitu użycia. 

## <a name="do-not-log-tests"></a>Nie należy logować testów
Jeśli przetestować punktu końcowego, a nie ma utterance rejestrowane, pamiętaj, aby użyć `logging=false` konfiguracji ciągu zapytania.

## <a name="where-to-find-utterances"></a>Gdzie można znaleźć zniesławiających
LUIS przechowuje zniesławiających wszystkich zarejestrowanych w dzienniku zapytań, dostępna do pobrania na [LUIS] [ LUIS] witryny sieci Web **aplikacje** strony listy, a także LUIS [tworzenia interfejsów API ](https://aka.ms/luis-authoring-apis). 

Wszelkie zniesławiających LUIS jest pewności, jakie są wymienione w **[Przejrzyj zniesławiających punktu końcowego](label-suggested-utterances.md)** strony [LUIS] [ LUIS] witryny sieci Web. 

![Przejrzyj zniesławiających punktu końcowego](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Pamiętaj, aby uczenia
Pamiętaj, aby [uczenia](luis-how-to-train.md) LUIS po wprowadzeniu zmian w modelu. Zmiany w aplikacji LUIS nie są widoczne podczas testowania, dopóki nie jest uczony aplikacji. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [testowania](interactive-test.md) Twojego zniesławiających.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions