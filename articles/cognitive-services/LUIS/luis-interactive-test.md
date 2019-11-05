---
title: Testowanie aplikacji w portalu LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby w sposób ciągły pracować w aplikacji, aby udoskonalić ją i ulepszyć jej zrozumienie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500279"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testowanie aplikacji LUIS w portalu LUIS

[Testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po przekształceniu aplikacji LUIS przetestuj ją z przykładową wyrażenia długości, aby sprawdzić, czy zamiary i jednostki są rozpoznawane poprawnie. Jeśli tak nie jest, wprowadź ponownie aktualizacje aplikacji LUIS, uczenia i testowania. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testowanie wypowiedź

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje** . 

1. Aby uzyskać dostęp do panelu slajdu **testu** , wybierz opcję **test** w górnym panelu aplikacji.

    ![Strona uczenie & aplikacji testowej](./media/luis-how-to-interactive-test/test.png)

1. Wprowadź wypowiedź w polu tekstowym, a następnie wybierz klawisz ENTER. W **teście**można wpisać dowolną liczbę wyrażenia długości testowych, ale tylko jeden wypowiedź jednocześnie.

1. Wypowiedź, jego górny cel i Ocena są dodawane do listy wyrażenia długości w polu tekstowym.

    ![Testowanie interaktywne identyfikuje niewłaściwy cel](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Sprawdź ocenę

Sprawdzasz szczegóły wyniku testu w panelu **Inspekcja** . 
 
1. Po otwarciu panelu slajdu **testowego** zaznacz opcję **Sprawdź** , czy wypowiedź chcesz porównać. 

    ![Wybierz przycisk Sprawdź, aby zobaczyć więcej szczegółów na temat wyników testu](./media/luis-how-to-interactive-test/inspect.png)

1. Zostanie wyświetlony panel **Inspekcja** . Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.

    ![Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Popraw najważniejsze przeznaczenie oceny

1. Jeśli najważniejsze przeznaczenie oceny jest nieprawidłowe, wybierz przycisk **Edytuj** .

1.  Z listy rozwijanej wybierz odpowiedni cel dla wypowiedź.

    ![Wybierz odpowiedni cel](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetl wyniki tonacji

Jeśli na stronie **[Publikowanie](luis-how-to-publish-app.md#enable-sentiment-analysis)** jest skonfigurowana **tonacji Analysis** , wyniki testu obejmują tonacji znaleziony w wypowiedź. 

![Obraz okienka testowania z analizą tonacji](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw dopasowanie dopasowanego wzorca

Jeśli używasz [wzorców](luis-concept-patterns.md) i wypowiedź dopasowane do wzorca, ale przewidywalno niewłaściwy cel, wybierz łącze **Edytuj** przez wzorzec, a następnie wybierz odpowiedni cel.

## <a name="compare-with-published-version"></a>Porównaj z opublikowaną wersją

Aktywną wersję aplikacji można przetestować przy użyciu opublikowanej wersji [punktu końcowego](luis-glossary.md#endpoint) . W panelu **Inspekcja** wybierz pozycję **Porównaj z opublikowanym**. Wszystkie testy dotyczące opublikowanego modelu są odejmowane od salda przydziału subskrypcji platformy Azure. 

![Porównaj z opublikowanym](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Wyświetl kod JSON punktu końcowego w panelu testów
Możesz wyświetlić kod JSON punktu końcowego zwrócony do porównania, wybierając **Widok Pokaż JSON**.

![Opublikowana odpowiedź JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Ustawienia dodatkowe w panelu testu

### <a name="luis-endpoint"></a>Punkt końcowy LUIS

Jeśli masz kilka punktów końcowych LUIS, Użyj linku **Ustawienia dodatkowe** w okienku opublikowanym test, aby zmienić punkt końcowy używany do testowania. Jeśli nie masz pewności, który punkt końcowy jest używany, wybierz wartość domyślną **Starter_Key**. 

![Panel testowy z wyróżnionymi dodatkowymi linkami ustawień](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Wyświetlanie poprawek sprawdzanie pisowni Bing w panelu testowania

Wymagania dotyczące wyświetlania poprawek pisowni: 

* Opublikowana aplikacja
* Sprawdzanie pisowni Bing [klucz usługi](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Klucz usługi nie jest przechowywany i należy go zresetować dla każdej sesji przeglądarki. 

Poniższa procedura umożliwia uwzględnienie [Sprawdzanie pisowni Bing usługi wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w wynikach okienka testów. 

1. W okienku **test** wprowadź wypowiedź. Gdy wypowiedź jest przewidywany, wybierz pozycję **[Sprawdź](#inspect-score)** pod wprowadzoną wypowiedź. 

1. Gdy zostanie otwarty panel **Inspekcja** , wybierz pozycję **[Porównaj z opublikowanym](#compare-with-published-version)** . 

1. Po otwarciu panelu **opublikowanego** wybierz pozycję **[dodatkowe ustawienia](#additional-settings-in-test-panel)** .

1. W podręcznym oknie dialogowym zaznacz pole wyboru **włącz sprawdzanie pisowni Bing** i wprowadź klucz, a następnie wybierz pozycję **gotowe**. 
    ![wprowadzić sprawdzanie pisowni Bing klucz usługi](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Wprowadź zapytanie z nieprawidłową pisownią, taką jak `book flite to seattle`, i wybierz klawisz ENTER. Niepoprawna pisownia wyrazu `flite` jest zastępowana w zapytaniu wysłanym do LUIS, a otrzymany kod JSON pokazuje zarówno oryginalne zapytanie, jak `query`, jak i poprawioną pisownię w zapytaniu, jako `alteredQuery`.

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
Zobacz [koncepcje](luis-concept-batch-test.md) testowania partii i Dowiedz się, [jak](luis-how-to-batch-test.md) przetestować partię wyrażenia długości.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz obejść, aby poprawić dokładność aplikacji LUIS, dodając etykiety więcej wyrażenia długości lub dodając funkcje. 

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md) 
