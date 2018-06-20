---
title: Wsadowe testowanie aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) partii testowania należy używać można znaleźć zniesławiających niepoprawne intencje i jednostek.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265517"
---
# <a name="batch-testing"></a>Testowanie usługi partia zadań
 Testowanie partii jest kompleksowe test na bieżącym uczonego modelu do mierzenia jego wydajność w LUIS. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Importowanie pliku zestawu danych do testowania partii

1. Wybierz **testu** w górnym pasku, a następnie wybierz opcję **partii testowania panelu**.

    ![Łącze testowania partii](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Wybierz **dataset importu**. **Nowy zestaw danych importu** zostanie wyświetlone okno dialogowe. Wybierz **wybierz plik** i Znajdź [JSON](luis-concept-batch-test.md#batch-file-format) pliku, który zawiera *nie więcej niż 1000* zniesławiających do testowania.

    ![Zaimportuj plik zestawu danych](./media/luis-how-to-batch-test/batchtest-importset.png)

    Na pasku powiadomień red u góry przeglądarki zostały zgłoszone błędy importu. Import zawiera błędy, zostanie utworzona żadnego zestawu danych. Aby uzyskać więcej informacji, zobacz [typowe błędy](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. W **nazwę zestawu danych** wprowadź nazwę pliku zestawu danych. Plik zestawu danych zawiera **tablicy zniesławiających** tym *etykietą zamiar* i *jednostek*. Przegląd [przykładowy plik wsadowy](luis-concept-batch-test.md#batch-file-format) składni. 

4. Wybierz **gotowe**. Zostanie dodany plik zestawu danych.

## <a name="run-rename-export-or-delete-dataset"></a>Uruchom, Zmień nazwę, eksportowanie lub usuwanie zestawu danych
Do uruchamiania, Zmień nazwę, wyeksportować lub usunąć zestaw danych, użyj wielokropkiem (**...** ) na końcu wiersza zestawu danych.

![Akcje zestawu danych](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Uruchom test partii w przeszkolone aplikacji

Aby uruchomić test, wybierz nazwę zestawu danych. Po zakończeniu testu, ten wiersz przedstawia wynik testu, zestawu danych.

![Wynik testu usługi partia zadań](./media/luis-how-to-batch-test/run-test.png)

Do pobrania zestawu danych jest tego samego pliku, który został przekazany do testowania partii.

|Stan|Znaczenie|
|--|--|
|![Ikona zielone koło pomyślnie testu](./media/luis-how-to-batch-test/batch-test-result-green.png)|Wszystkie zniesławiających zostały wykonane pomyślnie.|
|![Wystąpił błąd testu czerwony x ikony](./media/luis-how-to-batch-test/batch-test-result-red.png)|Co najmniej jeden utterance celem nie jest zgodny prognozowania.|
|![Aby rozpocząć testowanie ikony](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test jest gotowy do uruchomienia.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Wyświetlanie wyników testu partii 
Aby przejrzeć wyniki testu partii, wybierz **zobaczyć wyniki**.

![Wyniki testu partii](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## Filtrowanie wyników wykresu

Aby filtrować wykresu w określonym celem lub jednostki, wybierz opcje lub jednostce panel filtrowania po prawej stronie. Punkty danych i ich dystrybucję aktualizacji na wykresie zgodnie z wyborem. 
 
![Wynik testu wizualizowanego partii](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>Wyświetlanie utterance jednego punktu danych
Na wykresie umieść kursor nad punkt danych, aby zobaczyć wynik pewności jego przewidywania. Wybierz punkt danych, aby pobrać jego odpowiedniego utterance na liście zniesławiających w dolnej części strony. 

![Wybrane utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Widok danych sekcji
Na wykresie czterech sekcji, wybierz nazwę sekcji, takich jak **fałszywych** w prawym górnym rogu wykresu. Poniżej wykresu wszystkie zniesławiających w tej sekcji są wyświetlane poniżej wykresu w postaci listy. 

![Wybrane zniesławiających przez sekcję](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

W tym obrazie poprzedniego utterance `switch on` jest oznaczony przy użyciu zamiaru TurnAllOn, ale otrzymano Prognozowanie brak konwersji. Jest to wskazanie, że celem TurnAllOn wymaga więcej zniesławiających przykład, aby wprowadzić oczekiwanego prognozowania. 

Dwie sekcje wykresu w red wskazują zniesławiających, które nie pasuje do oczekiwanego prognozowania. Oznaczają one zniesławiających które LUIS potrzebuje więcej szkolenia. 

Dwie sekcje wykresu w zielony odpowiadało oczekiwanego prognozowania.

## <a name="next-steps"></a>Kolejne kroki

Jeśli testowania wskazuje, że aplikacja LUIS nie rozpoznaje poprawne intencje i jednostek, można pracować poprawić wydajność aplikacji LUIS etykietowania więcej zniesławiających lub dodając funkcje. 

* [Etykieta sugerowane zniesławiających z LUIS](Label-Suggested-Utterances.md) 
* [Użyj funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md) 
* [Zrozumienie partii testowanie za pomocą tego samouczka](luis-tutorial-batch-testing.md)
* [Dowiedz się partii testowania pojęcia](luis-concept-batch-test.md).

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision

