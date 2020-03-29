---
title: Jak wykonać test wsadowy — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Użyj zestawów testowania wsadowego (Language Understanding) (LUIS), aby znaleźć wypowiedzi z niepoprawnymi intencjami i jednostkami.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904357"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testowanie wsadowe z zestawem wypowiedzi przykładowych

 Testowanie wsadowe jest kompleksowy test na bieżący model przeszkolony do pomiaru jego wydajności w usłudze LUIS. Zestawy danych używane do testowania wsadowego nie powinny zawierać wypowiedzi przykład w intencji lub wypowiedzi otrzymane z punktu końcowego środowiska wykonawczego przewidywanie. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importowanie pliku zestawu danych do testowania wsadowego

1. Wybierz **pozycję Testuj** na górnym pasku, a następnie wybierz **pozycję Panel testowania wsadowego**.

    ![Łącze testowania wsadowego](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Wybierz **pozycję Importuj zestaw danych**. Zostanie wyświetlone okno dialogowe **Importowanie nowego zestawu danych.** Wybierz **pozycję Wybierz plik** i znajdź plik JSON z poprawnym [formatem JSON,](luis-concept-batch-test.md#batch-file-format) który zawiera *nie więcej niż 1000* wypowiedzi do przetestowania.

    Błędy importu są zgłaszane na czerwonym pasku powiadomień w górnej części przeglądarki. Gdy import ma błędy, nie jest tworzony żaden zestaw danych. Aby uzyskać więcej informacji, zobacz [Typowe błędy](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. W polu **Nazwa zestawu danych** wprowadź nazwę pliku zestawu danych. Plik zestawu danych zawiera **tablicę wypowiedzi,** w tym *oznaczone intencji* i *jednostek*. Przejrzyj [przykładowy plik wsadowy](luis-concept-batch-test.md#batch-file-format) dla składni. 

4. Wybierz pozycję **Done** (Gotowe). Plik zestawu danych zostanie dodany.

## <a name="run-rename-export-or-delete-dataset"></a>Uruchamianie, zmienianie nazw, eksportowanie lub usuwanie zestawu danych

Aby uruchomić, zmienić nazwę, wyeksportować lub usunąć zestaw danych, użyj przycisku wielokropek (***...***) na końcu wiersza zestawu danych.

![Akcje zestawu danych](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Uruchamianie testu wsadowego w uczonej aplikacji

Aby uruchomić test, wybierz nazwę zestawu danych. Po zakończeniu testu w tym wierszu jest wyświetlany wynik testu zestawu danych.

![Wynik testu wsadowego](./media/luis-how-to-batch-test/run-test.png)

Zestaw danych do pobrania jest tym samym plikiem, który został przekazany do testowania wsadowego.

|Stan|Znaczenie|
|--|--|
|![Pomyślna ikona zielonego koła testu](./media/luis-how-to-batch-test/batch-test-result-green.png)|Wszystkie wypowiedzi są pomyślne.|
|![Niepowodzenie testu czerwona x ikona](./media/luis-how-to-batch-test/batch-test-result-red.png)|Co najmniej jeden zamiar wypowiedź nie pasuje do przewidywania.|
|![Ikona Gotowe do testowania](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test jest gotowy do uruchomienia.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Wyświetlanie wyników testów wsadowych 

Aby przejrzeć wyniki testów wsadowych, wybierz pozycję **Zobacz wyniki**.

![Wyniki testów wsadowych](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrowanie wyników wykresu

Aby filtrować wykres według określonego zamiaru lub encji, wybierz intencję lub encję w panelu filtrowania po prawej stronie. Punkty danych i ich dystrybucja są aktualizowane na wykresie zgodnie z wybranym wyborem. 
 
![Zwizualizowany wynik testu wsadowego](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Wyświetlanie danych wypowiedź jednopunktowa

Na wykresie umieść wskaźnik myszy na punkcie danych, aby zobaczyć wynik pewności jego przewidywania. Wybierz punkt danych, aby pobrać jego odpowiadającą wypowiedź na liście wypowiedzi u dołu strony. 

![Wybrana wypowiedź](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Wyświetlanie danych sekcji

Na wykresie czteroekułkaowym wybierz nazwę sekcji, taką jak **Fałszywy alarm** w prawym górnym rogu wykresu. Poniżej wykresu wszystkie wypowiedzi w tej sekcji są wyświetlane poniżej wykresu na liście. 

![Wybrane wypowiedzi według sekcji](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

W tym poprzednim obrazie `switch on` wypowiedź jest oznaczona z intencji TurnAllOn, ale otrzymał przewidywanie None intencji. Jest to wskazanie, że intencji TurnAllOn potrzebuje więcej wypowiedzi przykład w celu dokonania oczekiwanego przewidywania. 

Dwie sekcje wykresu na czerwono wskazują wypowiedzi, które nie pasują do oczekiwanego przewidywania. Wskazują one wypowiedzi, które usługa LUIS wymaga więcej szkoleń. 

Dwie sekcje wykresu w kolorze zielonym były zgodne z oczekiwaną prognozą.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja usługi LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz pracować nad poprawą wydajności aplikacji usługi LUIS, etykietując więcej wypowiedzi lub dodając funkcje. 

* [Etykieta sugerowane wypowiedzi z usługi LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Korzystanie z funkcji w celu zwiększenia wydajności aplikacji usługi LUIS](luis-how-to-add-features.md) 
* [Poznaj testowanie wsadowe za pomocą tego samouczka](luis-tutorial-batch-testing.md)
* [Poznaj pojęcia dotyczące testowania wsadowego](luis-concept-batch-test.md).
