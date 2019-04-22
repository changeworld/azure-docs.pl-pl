---
title: Testowanie aplikacji w portalu usługi LUIS
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) umożliwia ciągłe prace w swojej aplikacji, aby dostosować go i zwiększyć jej interpretacji języka.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 51c6a58567b35c9b8486d8634b0bed1af7218994
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893628"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Przetestuj swoją aplikację usługi LUIS w portalu usługi LUIS
<a name="train-your-app"></a>
[Testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po szkolenia aplikacją usługi LUIS, należy ją przetestować przy użyciu wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testowanie wypowiedź

1. Dostęp do aplikacji, wybierając jego nazwę na **Moje aplikacje** strony. 

2. Aby uzyskać dostęp do **testu** panelu slajdów w poziomie, wybierz opcję **testu** w Twojej aplikacji na górnym panelu.

    ![Strona szkolenie i testowanie aplikacji](./media/luis-how-to-interactive-test/test.png)

3. Wprowadź wypowiedź w polu tekstowym i naciśnij klawisz Enter. Można wpisać jako wiele wypowiedzi testowania, jak chcesz **Test**, ale tylko jeden wypowiedź w danym momencie.

4. Wypowiedź, jego przeznaczenie najważniejsze i oceny są dodawane do listy wypowiedzi poniżej pola tekstowego.

    ![Testowanie interaktywne identyfikuje niewłaściwego intencji](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Sprawdź wynik

Sprawdź szczegóły wyniku testu w **Sprawdź** panelu. 
 
1. Za pomocą **testu** Otwórz panel slajdów w poziomie, wybierz opcję **Sprawdź** dla wypowiedź, które chcesz porównać. 

    ![Wybierz przycisk Sprawdź, aby wyświetlić więcej szczegółów na temat wyników testów](./media/luis-how-to-interactive-test/inspect.png)

2. **Kontroli** zostanie wyświetlony panel. Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.

    ![Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Poprawne górnej oceniania intencji

1. Jeśli u góry oceniania celem jest niepoprawny, wybierz **Edytuj** przycisku.

2.  Na liście rozwijanej wybierz poprawny przeznaczenie wypowiedź.

    ![Wybierz prawidłowe intencji](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetlanie wyników tonacji

Jeśli **analizę tonacji** jest skonfigurowany na **[Publikuj](luis-how-to-publish-app.md#enable-sentiment-analysis)** stronie wyników testów obejmują wskaźniki nastrojów klientów w wypowiedź. 

![Obraz przedstawiający okienko testowania za pomocą analizy opinii](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw dopasowany wzorzec intencji

Jeśli używasz [wzorców](luis-concept-patterns.md) wypowiedź dopasowany wzorzec, ale przewidzieć był nieprawidłowy zamiar, wybierz **Edytuj** połączyć za pomocą wzorca, a następnie wybierz poprawny intencji.

## <a name="compare-with-published-version"></a>Porównaj z opublikowanej wersji

Możesz przetestować aktywnej wersji Twojej aplikacji za pomocą opublikowanego [punktu końcowego](luis-glossary.md#endpoint) wersji. W **Sprawdź** panelu wybierz **Porównaj z opublikowanych**. Wszelkie testowanie opublikowanego modelu jest odejmowany od salda limit przydziału subskrypcji platformy Azure. 

![Porównaj z opublikowane](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Wyświetl punkt końcowy JSON w panelu testu
Można wyświetlić punkt końcowy JSON zwrócony do porównywania, wybierając **JSON Pokaż widok**.

![Opublikowane odpowiedź w formacie JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Dodatkowe ustawienia w panelu testu

### <a name="luis-endpoint"></a>Punkt końcowy usługi LUIS

Jeśli masz kilka punktów końcowych usługi LUIS, użyj **dodatkowe ustawienia** łącze na Test opublikowane w okienku można zmienić punktu końcowego używanego do testowania. Jeśli nie masz pewności, który punkt końcowy do użycia, wybierz domyślną **Starter_Key**. 

![Panel testu z wyróżnionym linkiem dodatkowe ustawienia](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Wyświetl korekty sprawdzania pisowni Bing w panelu testu

Wymagania, aby wyświetlić korekty pisowni: 

* Opublikowana aplikacja
* Sprawdzanie pisowni Bing [klucza usługi](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Klucz usługi nie są przechowywane i powinna zostać zresetowana dla każdej sesji przeglądarki. 

Użyj poniższej procedury, aby uwzględnić [sprawdzania pisowni Bing w wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w okienku wyników testu. 

1. W **testu** okienku, wprowadź wypowiedź. Gdy przewiduje wypowiedź wybierz **[Sprawdź](#inspect-score)** poniżej wypowiedź wprowadzony. 

2. Gdy **Sprawdź** zostanie otwarty panel, wybierz opcję  **[porównania z opublikowaną](#compare-with-published-version)**. 

3. Gdy **opublikowano** zostanie otwarty panel, wybierz opcję  **[dodatkowe ustawienia](#additional-settings-in-test-panel)**.

4. W wyskakującym oknie dialogowym Wprowadź swoje **sprawdzania pisowni Bing** klucza usługi. 
    ![Wprowadź klucz usługi sprawdzania pisowni Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Wprowadź kwerendę niepoprawne sprawdzania pisowni takich jak `book flite to seattle` i wprowadź select. Niepoprawne pisownię wyrazu `flite` jest zastępowany zapytania wysyłane do usługi LUIS i wynikowy JSON zarówno oryginalnego zapytania, jest wyświetlany jako `query`i pisownię poprawiony w zapytaniu, jako `alteredQuery`.

    ![Poprawione Pisownia JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz testowania partii [pojęcia](luis-concept-batch-test.md) i Dowiedz się, [jak](luis-how-to-batch-test.md) partii wypowiedzi testowania.

## <a name="next-steps"></a>Kolejne kroki

Jeśli testowanie wskazuje, że aplikacją usługi LUIS nie rozpoznaje poprawne intencje i podmioty, możesz podjąć zwiększyć dokładność aplikacją usługi LUIS etykietowania więcej wypowiedzi lub dodając funkcje. 

* [Etykieta sugerowane wypowiedzi z użyciem usługi LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS](luis-how-to-add-features.md) 
