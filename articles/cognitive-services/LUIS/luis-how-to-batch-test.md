---
title: Test usługi Batch
titleSuffix: Language Understanding - Azure Cognitive Services
description: Użyj partii Language Understanding (LUIS), zestawy testów, aby znaleźć wypowiedzi z niepoprawne intencje i podmioty.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: d615eb2a23b953bad5e41859357d9ae9e9be2ba6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197702"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testowanie za pomocą zestawu wypowiedzi przykład usługi Batch

 Testowanie usługi Batch jest kompleksowe testu, bieżące uczonego modelu do pomiaru jego wydajności w usługi LUIS. Zestawy danych używane do testowania usługi batch nie powinien zawierać wypowiedzi przykład intencji lub wypowiedzi otrzymany z punktem końcowym runtime prognozy. 

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importuj plik zestawu danych do testowania usługi batch

1. Wybierz **testu** w górnym pasku, a następnie wybierz pozycję **Batch testowania panelu**.

    ![Link testowanie usługi Batch](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Wybierz **Importowanie zestawu danych**. **Nowy zestaw danych importu** pojawi się okno dialogowe. Wybierz **wybierz plik** i zlokalizuj plik JSON z prawidłowymi [formatu JSON](luis-concept-batch-test.md#batch-file-format) zawierający *nie więcej niż 1000* wypowiedzi do testowania.

    Importuj błędy są zgłaszane na pasku powiadomień czerwony u góry strony w przeglądarce. Podczas importowania zawiera błędy, jest tworzony żaden zestaw danych. Aby uzyskać więcej informacji, zobacz [typowych błędów](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. W **Nazwa zestawu danych** wprowadź nazwę pliku zestawu danych. Plik dataset zawiera **tablicy wypowiedzi** tym *etykietą intencji* i *jednostek*. Przegląd [przykładowy plik wsadowy](luis-concept-batch-test.md#batch-file-format) składni. 

4. Wybierz pozycję **Done** (Gotowe). Plik zestawu danych zostanie dodany.

## <a name="run-rename-export-or-delete-dataset"></a>Uruchom, zmiana nazwy, eksportowanie lub usuwanie zestawu danych

Aby uruchomić, Zmień nazwę, wyeksportować lub usunąć zestaw danych, użyj wielokropka (***...*** ) przycisk na końcu wiersza zestawu danych.

![Akcje zestawu danych](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Uruchom test usługi batch w aplikacji uczonego

Aby uruchomić test, wybierz nazwę zestawu danych. Po zakończeniu testu, w tym wierszu wyświetlana wyniku testu, zestawu danych.

![Wynik testu usługi Batch](./media/luis-how-to-batch-test/run-test.png)

Do pobrania zestaw danych jest tego samego pliku, który został przekazany do testowania usługi batch.

|Stan|Znaczenie|
|--|--|
|![Ikona zielonego okręgu pomyślnego testowego](./media/luis-how-to-batch-test/batch-test-result-green.png)|Wszystkie wypowiedzi zostały wykonane pomyślnie.|
|![Ile niepowodzeniem testu czerwony x ikony](./media/luis-how-to-batch-test/batch-test-result-red.png)|Celem co najmniej jeden wypowiedź nie odpowiada prognozowania.|
|![Aby rozpocząć testowanie ikony](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test jest gotowy do uruchomienia.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Wyświetl wyniki testu dla usługi batch 

Aby przejrzeć wyniki testów usługi batch, wybrać **wyniki**.

![Wyniki testu usługi Batch](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtruj wyniki na wykresie

Aby filtrować wykres według określonych przeznaczenie lub jednostki, wybierz przeznaczenie lub jednostki w panelu filtrowania po prawej stronie. Na wykresie, zgodnie z wybraną aktualizację punktów danych oraz ich dystrybucji. 
 
![Wynik testu wizualizowany usługi Batch](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Wyświetlanie wypowiedź pojedynczym punktem danych

Na wykresie umieść kursor nad punktem danych, aby zobaczyć wynik pewności jej prognozy. Wybierz punkt danych, aby pobrać jego odpowiedniego wypowiedź na liście wypowiedzi w dolnej części strony. 

![Wybrane wypowiedź](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Wyświetlanie części danych

Na wykresie czterech części wybierz nazwę sekcji, takich jak **fałszywie dodatnie** w prawym górnym rogu wykresu. Pod wykresem wszystkie wypowiedzi w tej sekcji są wyświetlane poniżej wykresu na liście. 

![Wybrane wypowiedzi przez sekcję](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Na poprzedniej ilustracji, a wypowiedź `switch on` ma etykietę z zamiarem TurnAllOn, ale Odebrano obiekt prognozowania wartości None intencji. Jest to wskazanie, że celem TurnAllOn wymaga więcej wypowiedzi przykład, aby mogła przewiduje oczekiwanego. 

Dwie sekcje wykresów na czerwono wskazują wypowiedzi jest niezgodna z oczekiwanym prognozy. Oznaczają one wypowiedzi LUIS, których potrzebuje więcej szkoleń. 

Dwie sekcje wykresów w kolorze zielonym był zgodny z oczekiwanym prognozy.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Kolejne kroki

Jeśli testy wskazuje, że aplikacją usługi LUIS nie rozpoznaje poprawne intencje i podmioty, możesz podjąć poprawianie wydajności aplikacją usługi LUIS poprzez etykietowanie więcej wypowiedzi lub dodawanie funkcji. 

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 
* [Omówienie usługi batch, testowanie za pomocą tego samouczka](luis-tutorial-batch-testing.md)
* [Dowiedz się, testowanie pojęcia usługi batch](luis-concept-batch-test.md).
