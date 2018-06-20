---
title: Uczenie i testowanie aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) umożliwia ciągłą pracę w swojej aplikacji, aby dostosować go i zwiększyć jej opis języka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: fb4c3bb117d1ea60c9cc28d2b193ee3c01f6c945
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221635"
---
# <a name="test-your-luis-app"></a>Testowanie aplikacji LUIS
<a name="train-your-app"></a>
[Uczenie](luis-how-to-train.md) i [testowania](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po uczenie LUIS aplikacji, przetestować go z przykładowych zniesławiających, aby sprawdzić, czy intencje i jednostki są rozpoznawane poprawnie. Nie są one aby aktualizacji aplikacji LUIS pociągu i test ponownie. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="interactive-testing"></a>
## <a name="test-an-utterance"></a>Test utterance

1. Dostęp do aplikacji przez wybranie jego nazwę na **Moje aplikacje** strony. 

2. Aby uzyskać dostęp do **testu** panelu poza slajdów, wybierz opcję **testu** w górnym panelu aplikacji.

    ![Strony pociągu i przetestuj aplikację](./media/luis-how-to-interactive-test/test.png)

3. W polu tekstowym wprowadź utterance i wybierz Enter. Można wpisać zniesławiających wiele testów w **Test**, ale tylko jeden utterance w czasie.

4. Utterance, jego celem najwyższego i wynik zostaną dodane do listy zniesławiających poniżej pola tekstowego.

    ![Testowanie interakcyjne identyfikuje niewłaściwy celem](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>Panel wyczyść testu
Aby wyczyścić wszystkie zniesławiających wprowadzone testu i ich wyniki z testów konsoli, wybierz **rozpoczęcie** w lewym górnym rogu **panelu testu**. 

## <a name="close-test-panel"></a>Panel Zamknij testu
Aby zamknąć **testu** panelu, wybierz opcję **testu** przycisk ponownie.

## <a name="inspect-score"></a>Sprawdź wynik
Sprawdź szczegóły wyniku testu w **inspekcję** panelu. 
 
1. Z **testu** Otwórz panel poza slajdów, wybierz opcję **inspekcję** dla utterance chcesz porównać. 

    ![Sprawdź przycisk](./media/luis-how-to-interactive-test/inspect.png)

2. **Kontroli** pojawi się panel. Panel zawiera górnej oceniania opcje, a także wszelkich zidentyfikowanych jednostek. Panel przedstawia wynik utterance wybrane.

    ![Sprawdź przycisk](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Poprawne top oceniania zamiar

1. Jeśli górnej oceniania celem jest niepoprawny, wybierz **Edytuj** przycisku.

2.  Na liście rozwijanej wybierz prawidłowe opcje utterance.

    ![Wybierz prawidłowe opcje](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetl wyniki wskaźniki nastrojów klientów

Jeśli **analizy wskaźniki nastrojów klientów** jest skonfigurowany na **[publikowania](publishapp.md#enable-sentiment-analysis)** strony, wyniki testów zawierają wskaźniki nastrojów klientów w utterance. 

![Obraz okienka testu z analizą wskaźniki nastrojów klientów](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw wzorzec dopasowany zamiar
Jeśli używasz [wzorce](luis-concept-patterns.md) deseń dopasowywane utterance, ale został przewidzieć niewłaściwy zamiar, wybierz **Edytuj** połączyć za pomocą wzorca, a następnie wybierz prawidłowe opcje.

## <a name="compare-with-published-version"></a>Porównaj z opublikowanej wersji
Można przetestować aktywnej wersji aplikacji za pomocą publikowane [punktu końcowego](luis-glossary.md#endpoint) wersji. W **inspekcję** panelu, wybierz opcję **opublikowane Porównaj z elementem**. Wszelkie testowanie opublikowanego modelu jest odejmowany od saldo limitu przydziału subskrypcji platformy Azure. 

![Porównaj z elementem opublikowane](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Punkt końcowy widoku JSON w panelu testu
Można wyświetlić punkt końcowy JSON zwrócony do porównania, wybierając **JSON Pokaż widok**.

![Opublikowane odpowiedź w formacie JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Dodatkowe ustawienia w panelu testu

### <a name="luis-endpoint"></a>Punkt końcowy LUIS
Jeśli masz kilka LUIS punktów końcowych, użyj **dodatkowe ustawienia** łącza w teście opublikowane okienko, aby zmienić punkt końcowy do testowania. Jeśli nie masz pewności, który punkt końcowy do użycia, wybierz domyślną **Starter_Key**. 

![Panel testu z wyróżnionym łączem dodatkowe ustawienia](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Wyświetl korekty sprawdzania pisowni usługi Bing w panelu testu
Wymagania dotyczące wyświetlania poprawek pisowni: 

* Opublikowanych aplikacji
* Sprawdzanie pisowni usługi Bing [klucza usługi](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Klucz usługi nie są przechowywane i powinna zostać zresetowana w każdej sesji przeglądarki. 

Użyj poniższej procedury, aby uwzględnić [sprawdzania pisowni usługi Bing w wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w okienku wyników testu. 

1. W **testu** okienku, wprowadź utterance. Gdy utterance jest przydatna, wybierz **[inspekcję](#inspect-score)** pod utterance wprowadzona. 

2. Gdy **inspekcję** zostanie otwarty panel, wybierz opcję  **[porównania z opublikowaną](#compare-with-published-version)**. 

3. Gdy **opublikowano** zostanie otwarty panel, wybierz opcję  **[dodatkowe ustawienia](#additional-settings-in-test-panel)**.

4. W oknie podręcznym wprowadź Twojej **sprawdzania pisowni usługi Bing** klucza usługi. 
    ![Wprowadź klucz usługi sprawdzania pisowni usługi Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Wprowadź kwerendę niepoprawne sprawdzania pisowni takich jak `book flite to seattle` , a następnie wybierz opcję Wprowadź. Niepoprawne pisownię wyrazu `flite` zostanie zastąpiony w zapytaniu wysyłane do LUIS i wynikowy JSON zawiera zarówno oryginalne zapytanie, jako `query`i poprawiony pisowni w zapytaniu, jako `alteredQuery`.

    ![Skorygowane Pisownia JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

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
## <a name="batch-testing"></a>Testowanie usługi partia zadań
Zobacz testowania partii [pojęcia](luis-concept-batch-test.md) i Dowiedz się [jak](luis-how-to-batch-test.md) partii zniesławiających testu.

## <a name="next-steps"></a>Kolejne kroki

Testowanie wskazuje, że aplikacja LUIS nie rozpoznaje poprawne intencje i jednostek, możesz pracować aby poprawić precyzję aplikację LUIS etykietowania więcej zniesławiających lub dodając funkcje. 

* [Etykieta sugerowane zniesławiających z LUIS](Label-Suggested-Utterances.md) 
* [Użyj funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md) 
