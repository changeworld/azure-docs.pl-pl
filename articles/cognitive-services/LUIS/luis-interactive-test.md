---
title: Testowanie aplikacji w portalu usługi LUIS
description: Language Understanding (LUIS) umożliwia ciągłe prace w swojej aplikacji, aby dostosować go i zwiększyć jej interpretacji języka.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361161"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testowanie aplikacji LUIS w portalu LUIS

[Testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po szkolenia aplikacją usługi LUIS, należy ją przetestować przy użyciu wypowiedzi próbki, aby sprawdzić, czy intencje i podmioty są rozpoznawane prawidłowo. Jeśli nie jesteś, aktualizowanie aplikacją usługi LUIS, szkolenie i test ponownie.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Uczenie przed testowaniem

Aby przeprowadzić test względem najnowszej wersji aktywnej aplikacji, wybierz opcję **uczenie** z górnego menu przed przetestowaniem.

## <a name="test-an-utterance"></a>Testowanie wypowiedź

Test wypowiedź nie powinien być dokładnie taki sam, jak w przypadku każdego przykładu wyrażenia długości w aplikacji. Test wypowiedź powinien obejmować wybór wyrazów, Długość frazy oraz użycie jednostek, których oczekujesz dla użytkownika.

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje** .

1. Aby uzyskać dostęp do panelu slajdu **testu** , wybierz opcję **test** w górnym panelu aplikacji.

    > [!div class="mx-imgBorder"]
    > ![uczenie & stronie aplikacji testowej](./media/luis-how-to-interactive-test/test.png)

1. Wprowadź wypowiedź w polu tekstowym i naciśnij klawisz Enter. W **teście**można wpisać dowolną liczbę wyrażenia długości testowych, ale tylko jeden wypowiedź jednocześnie.

1. Wypowiedź, jego przeznaczenie najważniejsze i oceny są dodawane do listy wypowiedzi poniżej pola tekstowego.

    ![Testowanie interaktywne identyfikuje niewłaściwego intencji](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Sprawdź wynik

Sprawdzasz szczegóły wyniku testu w panelu **Inspekcja** .

1. Po otwarciu panelu slajdu **testowego** zaznacz opcję **Sprawdź** , czy wypowiedź chcesz porównać.

    ![Wybierz przycisk Sprawdź, aby zobaczyć więcej szczegółów na temat wyników testu](./media/luis-how-to-interactive-test/inspect.png)

1. Zostanie wyświetlony panel **Inspekcja** . Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.

    ![Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Poprawne górnej oceniania intencji

1. Jeśli najważniejsze przeznaczenie oceny jest nieprawidłowe, wybierz przycisk **Edytuj** .

1.  Na liście rozwijanej wybierz poprawny przeznaczenie wypowiedź.

    ![Wybierz prawidłowe intencji](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetlanie wyników tonacji

Jeśli na stronie **[Publikowanie](luis-how-to-publish-app.md#enable-sentiment-analysis)** jest skonfigurowana **tonacji Analysis** , wyniki testu obejmują tonacji znaleziony w wypowiedź.

![Obraz przedstawiający okienko testowania za pomocą analizy opinii](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw dopasowany wzorzec intencji

Jeśli używasz [wzorców](luis-concept-patterns.md) i wypowiedź dopasowane do wzorca, ale przewidywalno niewłaściwy cel, wybierz łącze **Edytuj** przez wzorzec, a następnie wybierz odpowiedni cel.

## <a name="compare-with-published-version"></a>Porównaj z opublikowanej wersji

Aktywną wersję aplikacji można przetestować przy użyciu opublikowanej wersji [punktu końcowego](luis-glossary.md#endpoint) . W panelu **Inspekcja** wybierz pozycję **Porównaj z opublikowanym**. Wszelkie testowanie opublikowanego modelu jest odejmowany od salda limit przydziału subskrypcji platformy Azure.

![Porównaj z opublikowane](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Wyświetl punkt końcowy JSON w panelu testu
Możesz wyświetlić kod JSON punktu końcowego zwrócony do porównania, wybierając **Widok Pokaż JSON**.

![Opublikowane odpowiedź w formacie JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Dodatkowe ustawienia w panelu testu

### <a name="luis-endpoint"></a>Punkt końcowy usługi LUIS

Jeśli masz kilka punktów końcowych LUIS, Użyj linku **Ustawienia dodatkowe** w okienku opublikowanym test, aby zmienić punkt końcowy używany do testowania. Jeśli nie masz pewności, który punkt końcowy jest używany, wybierz domyślną **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![panelu testowego z wyróżnionym linkiem dodatkowe ustawienia](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [koncepcje](luis-concept-batch-test.md) testowania partii i Dowiedz się, [jak](luis-how-to-batch-test.md) przetestować partię wyrażenia długości.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacją usługi LUIS nie rozpoznaje poprawne intencje i podmioty, możesz podjąć zwiększyć dokładność aplikacją usługi LUIS etykietowania więcej wypowiedzi lub dodając funkcje.

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md)
