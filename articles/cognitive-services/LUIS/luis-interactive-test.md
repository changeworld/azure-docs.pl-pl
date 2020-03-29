---
title: Testowanie aplikacji w portalu usługi LUIS
description: Użyj języka zrozumienia (LUIS) do ciągłej pracy nad aplikacją, aby uściślić go i poprawić jego zrozumienie języka.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219820"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testowanie aplikacji usługi LUIS w portalu usługi LUIS

[Testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po przeszkoleniu aplikacji usługi LUIS, przetestować go z wypowiedzi próbki, aby sprawdzić, czy intencje i jednostki są rozpoznawane poprawnie. Jeśli tak nie jest, należy ponownie wprowadzać aktualizacje aplikacji usługi LUIS, szkolić i testować.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Trenuj przed testami

Aby przetestować na najnowszej wersji aktywnej aplikacji, wybierz **Train** z górnego menu, przed testowaniem.

## <a name="test-an-utterance"></a>Testowanie wypowiedź

Wypowiedź testu nie powinna być dokładnie taka sama, jak wszelkie wypowiedzi przykład w aplikacji. Wypowiedź testu powinna zawierać wybór wyrazu, długość frazy i użycie jednostki, których oczekujesz od użytkownika.

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje.**

1. Aby uzyskać dostęp do panelu Wysuwanie **testu,** wybierz **opcję Testuj** w górnym panelu aplikacji.

    > [!div class="mx-imgBorder"]
    > ![Strona aplikacji testu & pociągu](./media/luis-how-to-interactive-test/test.png)

1. Wprowadź wypowiedź w polu tekstowym i wybierz pozycję Enter. Można wpisać dowolną liczbę wypowiedzi testowych w **testach,** ale tylko jedną wypowiedź naraz.

1. Wypowiedź, jego najwyższej intencji i wynik są dodawane do listy wypowiedzi pod polem tekstowym.

    ![Interaktywne testy identyfikują niewłaściwy zamiar](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Sprawdź wynik

Szczegółowe informacje o wyniku testu można sprawdzić w panelu **Inspekcja.**

1. Po otwarciu panelu Wysuń **testowy** wybierz **pozycję Sprawdź, czy** chcesz porównać wypowiedź.

    ![Wybierz przycisk Sprawdź, aby wyświetlić więcej szczegółów na temat wyników testów](./media/luis-how-to-interactive-test/inspect.png)

1. Pojawi się panel **Inspekcji.** Panel zawiera najlepsze intencji punktacji, jak również wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedź.

    ![Panel zawiera najlepsze intencji punktacji, jak również wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedź.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Popraw ą intencję najwyższej punktacji

1. Jeśli intencja oceniania jest nieprawidłowa, wybierz przycisk **Edytuj.**

1.  Na liście rozwijanej wybierz poprawną intencję wypowiedź.

    ![Wybierz poprawną intencję](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetlanie wyników tonacji

Jeśli **analiza tonacji** jest skonfigurowana na stronie **[Publikowanie,](luis-how-to-publish-app.md#enable-sentiment-analysis)** wyniki testów obejmują tonację znalezioną w wypowiedź.

![Obraz okienka testowego z analizą tonacji](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw intencję dopasowanego wzorca

Jeśli używasz [wzorców](luis-concept-patterns.md) i wypowiedź dopasowane wzorca, ale niewłaściwy zamiar został przewidzony, wybierz **Edytuj** łącze przez wzorzec, a następnie wybierz poprawną intencję.

## <a name="compare-with-published-version"></a>Porównaj z opublikowaną wersją

Możesz przetestować aktywną wersję aplikacji przy próbie opublikowanej wersji [punktu końcowego.](luis-glossary.md#endpoint) W panelu **Inspekcja** wybierz pozycję **Porównaj z opublikowanym .** Wszelkie testy dotyczące opublikowanego modelu są odejmowane od salda przydziału subskrypcji platformy Azure.

![Porównaj z opublikowanymi](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobacz punkt końcowy JSON w panelu testowym
Można wyświetlić punkt końcowy JSON zwrócony do porównania, wybierając **pokaż widok JSON**.

![Opublikowana odpowiedź JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Dodatkowe ustawienia w panelu testowym

### <a name="luis-endpoint"></a>Punkt końcowy usługi LUIS

Jeśli masz kilka punktów końcowych usługi LUIS, użyj łącza **Ustawienia dodatkowe** w okienku Opublikowano test, aby zmienić punkt końcowy używany do testowania. Jeśli nie masz pewności, którego punktu końcowego użyć, wybierz domyślny **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Panel testowy z wyróżnionym łączem Ustawienia dodatkowe](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [pojęcia testowania wsadowego](luis-concept-batch-test.md) i [dowiedz się, jak przetestować](luis-how-to-batch-test.md) partię wypowiedzi.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja usługi LUIS nie rozpoznaje prawidłowych intencji i jednostek, można pracować, aby poprawić dokładność aplikacji usługi LUIS, etykietując więcej wypowiedzi lub dodając funkcje.

* [Etykieta sugerowane wypowiedzi z usługi LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystanie z funkcji w celu zwiększenia wydajności aplikacji usługi LUIS](luis-how-to-add-features.md)
