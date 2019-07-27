---
title: Przetestuj aplikację usługi LUIS
titleSuffix: Azure Cognitive Services
description: Testowanie jest procesem świadczenie usługi LUIS wypowiedzi próbki i uzyskiwanie odpowiedzi rozpoznawane LUIS intencje i podmioty.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 2a37c1a38beb48a5a0545cde7bf1b8bc7c8ef2cf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563880"
---
# <a name="testing-example-utterances-in-luis"></a>Testowanie przykład wypowiedzi w usługi LUIS

Testowanie jest procesem świadczenie usługi LUIS wypowiedzi próbki i uzyskiwanie odpowiedzi rozpoznawane LUIS intencje i podmioty. 

Możesz [test](luis-interactive-test.md) LUIS interaktywnie jeden wypowiedź w czasie, lub podaj [partii](luis-concept-batch-test.md) z wypowiedzi. Za pomocą testowania, możesz porównać bieżącą [active](luis-concept-version.md#active-version) modelu do opublikowanego modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Co to jest ocena podczas testowania?
Zobacz [prognozowania oceny](luis-concept-prediction-score.md) pojęcia, aby dowiedzieć się więcej o wyniki prognozy.

## <a name="interactive-testing"></a>Testowanie interakcyjne
Testowanie interaktywne odbywa się z **testu** panelu witryny sieci Web. Można wprowadzić wypowiedź, aby zobaczyć, jak intencje i podmioty zostały zidentyfikowane i są oceniane. Jeśli usługa LUIS nie jest prognozowanie intencje i podmioty, zgodnie z oczekiwaniami na wypowiedź w okienku testowania, skopiuj go do **intencji** stronę jako nowy wypowiedź. Etykieta części tego wypowiedź i szkolenie usługi LUIS. 

## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [testowania partii](luis-concept-batch-test.md) Jeśli testujesz wypowiedź więcej niż jednego naraz.

## <a name="endpoint-testing"></a>Testowanie punktu końcowego
Można przetestować za pomocą [punktu końcowego](luis-glossary.md#endpoint) maksymalnie dwie wersje aplikacji. Z wersją aplikacji Ustaw jako główny lub na żywo **produkcji** punktu końcowego, drugiej wersji, aby dodać **przemieszczania** punktu końcowego. Takie podejście zapewnia trzy wersje wypowiedź: bieżący model, w okienku testu [LUIS](luis-reference-regions.md) witryny sieci Web i dwie wersje w dwóch różnych punktów końcowych. 

Wszystkie punkt końcowy testowania liczby kierunku limitu przydziału użycia. 

## <a name="do-not-log-tests"></a>Rejestruj testy
Jeśli testujemy współpracę z punktu końcowego, a nie chcesz wypowiedź rejestrowane, pamiętaj, aby używać `logging=false` Konfiguracja ciągu zapytania.

## <a name="where-to-find-utterances"></a>Gdzie można znaleźć wypowiedzi
Usługa LUIS przechowuje wypowiedzi wszystkich zarejestrowanych w dzienniku zapytań dostępnych do pobrania na [LUIS](luis-reference-regions.md) witryny sieci Web **aplikacje** strony listy, a także usługi LUIS [Tworzenie interfejsów API](https://go.microsoft.com/fwlink/?linkid=2092087). 

Wypowiedzi usługa LUIS jest pewności, jakie są wymienione w **[Przejrzyj wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md)** strony [LUIS](luis-reference-regions.md) witryny sieci Web. 

![Przeglądanie wypowiedzi punktu końcowego](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Pamiętaj, aby uczyć
Pamiętaj, aby [szkolenie](luis-how-to-train.md) LUIS po wprowadzeniu zmian do modelu. Zmiany w aplikacji usługi LUIS nie są widoczne podczas badania, dopóki skonfigurowanych pod kątem aplikacji. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [testowania](luis-interactive-test.md) swoje wypowiedzi.
