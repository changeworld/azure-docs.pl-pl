---
title: Testowanie aplikacji usługi LUIS
titleSuffix: Azure Cognitive Services
description: Testowanie jest procesem dostarczania wypowiedzi próbki do usługi LUIS i uzyskiwania odpowiedzi intencji i jednostek rozpoznawanych przez usługę LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486686"
---
# <a name="testing-example-utterances-in-luis"></a>Testowanie przykładowych wypowiedzi w usłudze LUIS

Testowanie jest procesem dostarczania wypowiedzi próbki do usługi LUIS i uzyskiwania odpowiedzi intencji i jednostek rozpoznawanych przez usługę LUIS. 

Można przetestować usługi LUIS interaktywnie, jeden wypowiedź w czasie lub podać wypowiedzi. Podczas testowania można porównać odpowiedź przewidywania bieżącego modelu aktywnego na odpowiedź przewidywania opublikowanego modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Co to jest wynik w testach?
Zobacz [Koncepcje wyników prognozowania,](luis-concept-prediction-score.md) aby dowiedzieć się więcej o wynikach prognozowania.

## <a name="interactive-testing"></a>Testowanie interakcyjne
Testy interaktywne są wykonywane z panelu **testowego** portalu usługi LUIS. Można wprowadzić wypowiedź, aby zobaczyć, jak intencje i jednostki są identyfikowane i oceniane. Jeśli usługa LUIS nie przewiduje intencji i jednostek, zgodnie z oczekiwaniami w sprawie wypowiedź w panelu testowania, skopiuj go do **intencji** strony jako nowy wypowiedź. Następnie etykiety części tej wypowiedzi dla jednostek i pociągu usługi LUIS. 

## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [testowania wsadowego,](luis-concept-batch-test.md) jeśli testujesz więcej niż jeden wypowiedź w czasie.

## <a name="endpoint-testing"></a>Testowanie punktów końcowych
Można przetestować przy użyciu [punktu końcowego](luis-glossary.md#endpoint) z maksymalnie dwóch wersji aplikacji. W głównej lub aktywnej wersji aplikacji ustawionej jako **produkcyjny** punkt końcowy dodaj drugą wersję do **przemieszczania** punktu końcowego. Takie podejście zapewnia trzy wersje wypowiedź: bieżący model w okienku testowym w witrynie sieci Web [usługi LUIS](luis-reference-regions.md) i dwie wersje w dwóch różnych punktach końcowych. 

Wszystkie testy punktu końcowego wliczają się do przydziału użycia. 

## <a name="do-not-log-tests"></a>Nie rejestruj testów
Jeśli testujesz względem punktu końcowego i nie chcesz wypowiedź `logging=false` rejestrowane, należy pamiętać, aby użyć konfiguracji ciągu kwerendy.

## <a name="where-to-find-utterances"></a>Gdzie znaleźć wypowiedzi
Usługa LUIS przechowuje wszystkie zarejestrowane wypowiedzi w dzienniku zapytań, dostępne do pobrania w portalu usługi LUIS ze strony listy **Aplikacje,** a także [interfejsy API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087)usługi LUIS. 

Wszelkie wypowiedzi usługi LUIS nie jest pewien są wymienione w **[przeglądu wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md)** strony w witrynie sieci Web [usługi LUIS.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Pamiętaj, aby trenować
Pamiętaj, aby [trenować](luis-how-to-train.md) usługę LUIS po wszczęciem zmian w modelu. Zmiany w aplikacji usługi LUIS nie są widoczne w testach, dopóki aplikacja nie zostanie przeszkolona. 

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze wskazówki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [testowaniu](luis-interactive-test.md) wypowiedzi.
