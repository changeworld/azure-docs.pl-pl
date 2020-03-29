---
title: Testowanie wsadowe — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Użyj testowania wsadowego, aby stale pracować nad aplikacją, aby ją udoskonalić i poprawić jej zrozumienie języka.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220051"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Testowanie wsadowe z 1000 wypowiedzi w portalu usługi LUIS

Testowanie wsadowe sprawdza poprawność aktywnej wersji uczonej, aby zmierzyć dokładność przewidywania. Test wsadowy pomaga wyświetlić dokładność każdego zamiaru i jednostki w aktywnej wersji, wyświetlając wyniki z wykresem. Przejrzyj wyniki testu wsadowego, aby podjąć odpowiednie działania w celu zwiększenia dokładności, takie jak dodawanie więcej wypowiedzi przykład do intencji, jeśli aplikacja często nie można zidentyfikować poprawne intencji lub etykietowania jednostek w wypowiedź.

## <a name="group-data-for-batch-test"></a>Dane grupy dla testu wsadowego

Ważne jest, że wypowiedzi używane do testowania wsadowego są nowe usługi LUIS. Jeśli masz zestaw danych wypowiedzi, podzielić wypowiedzi na trzy zestawy: wypowiedzi przykład dodane do intencji, wypowiedzi odebrane z opublikowanego punktu końcowego i wypowiedzi używane do testu wsadowego usługi LUIS po jego uczone. 

## <a name="a-data-set-of-utterances"></a>Zestaw danych wypowiedzi

Prześlij plik wsadowy wypowiedzi, znany jako *zestaw danych,* do testowania wsadowego. Zestaw danych jest plikiem w formacie JSON zawierającym maksymalnie 1000 wypowiedzi bez **duplikatów.** Możesz przetestować maksymalnie 10 zestawów danych w aplikacji. Jeśli chcesz przetestować więcej, usuń zestaw danych, a następnie dodaj nowy.

|**Zasady**|
|--|
|*Brak zduplikowanych wypowiedzi|
|1000 wypowiedzi lub mniej|

*Duplikaty są traktowane jako dokładne dopasowania ciągów, a nie dopasowania, które są najpierw tokenizowane. 

## <a name="entities-allowed-in-batch-tests"></a>Jednostki dozwolone w testach wsadowych

Wszystkie encje niestandardowe w modelu są wyświetlane w filtrze jednostek testu wsadowego, nawet jeśli w danych pliku wsadowego nie ma odpowiednich jednostek.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Format pliku wsadowego

Plik wsadowy składa się z wypowiedzi. Każdy wypowiedź musi mieć przewidywanie intencji oczekiwanych wraz z wszelkich [jednostek maszyny,](luis-concept-entity-types.md#types-of-entities) które oczekują, że zostaną wykryte. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Szablon składni partii dla intencji z encjami

Użyj następującego szablonu, aby uruchomić plik wsadowy:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Plik wsadowy używa właściwości **startPos** i **endPos,** aby zanotować początek i koniec jednostki. Wartości są oparte na wartości zero i nie powinny rozpoczynać się ani kończyć na spacji. Różni się to od dzienników kwerend, które używają właściwości startIndex i endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Szablon składni partii dla intencji bez encji

Użyj następującego szablonu, aby uruchomić plik wsadowy bez encji:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Jeśli nie chcesz testować jednostek, `entities` dołącz właściwość i ustaw `[]`wartość jako pustą tablicę, .


## <a name="common-errors-importing-a-batch"></a>Typowe błędy podczas importowania partii

Typowe błędy obejmują: 

> * Ponad 1000 wypowiedzi
> * Wypowiedź JSON obiektu, który nie ma właściwości jednostki. Właściwość może być pustą tablicą.
> * Programy Word(y) oznaczone w wielu encjach
> * Etykieta jednostki rozpoczynająca się lub kończąca spację.

## <a name="batch-test-state"></a>Stan testu wsadowego

Usługa LUIS śledzi stan ostatniego testu każdego zestawu danych. Obejmuje to rozmiar (liczba wypowiedzi w partii), data ostatniego uruchomienia i ostatni wynik (liczba pomyślnie przewidywanych wypowiedzi).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Wyniki testów wsadowych

Wynik testu wsadowego jest wykresem punktowym, znanym jako macierz błędów. Ten wykres jest 4-sposób porównania wypowiedzi w pliku wsadowym i przewidywanych intencji bieżącego modelu i jednostek. 

Punkty danych w sekcjach **Fałszywie dodatni** i **Fałszywy ujemny** wskazują błędy, które należy zbadać. Jeśli wszystkie punkty danych znajdują się w sekcjach **True Positive** i **True Negative,** dokładność aplikacji jest idealna w tym zestawie danych.

![Cztery sekcje wykresu](./media/luis-concept-batch-test/chart-sections.png)

Ten wykres pomaga znaleźć wypowiedzi, które usługa LUIS przewiduje niepoprawnie na podstawie jego bieżącego szkolenia. Wyniki są wyświetlane dla regionu wykresu. Wybierz poszczególne punkty na wykresie, aby przejrzeć informacje o wypowiedź lub wybierz nazwę regionu do przeglądu wyników wypowiedź w tym regionie.

![Testowanie wsadowe](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Błędy w wynikach

Błędy w teście wsadowym wskazują intencje, które nie są przewidywane, jak wspomniano w pliku wsadowym. Błędy są wskazane w dwóch czerwonych sekcjach wykresu. 

Sekcja fałszywie dodatnia wskazuje, że wypowiedź odpowiada intencji lub jednostki, gdy nie powinien mieć. Fałszywy ujemny wskazuje, że wypowiedź nie pasuje do intencji lub jednostki, kiedy powinna mieć. 

## <a name="fixing-batch-errors"></a>Naprawianie błędów partii

Jeśli istnieją błędy w testowaniu wsadowym, można dodać więcej wypowiedzi do intencji i/lub etykiety więcej wypowiedzi z jednostką, aby pomóc usługi LUIS dokonać dyskryminacji między intencjami. Jeśli dodano wypowiedzi i oznaczone je i nadal uzyskać błędy przewidywania w testowaniu wsadowym, należy rozważyć dodanie funkcji [listy fraz](luis-concept-feature.md) ze słownikiem specyficznym dla domeny, aby pomóc usługi LUIS szybciej. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przetestować partię](luis-how-to-batch-test.md)
