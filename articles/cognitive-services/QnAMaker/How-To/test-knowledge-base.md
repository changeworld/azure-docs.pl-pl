---
title: Jak przetestować bazę wiedzy - QnA Maker
description: Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego w celu zwiększenia dokładności zwracanych odpowiedzi. Możesz przetestować bazę wiedzy za pomocą ulepszonego interfejsu czatu, który umożliwia również wprowadzanie zmian.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927274"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Sprawdź swoją bazę wiedzy w QnA Maker

Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego w celu zwiększenia dokładności zwracanych odpowiedzi. Możesz przetestować bazę wiedzy za pomocą ulepszonego interfejsu czatu, który umożliwia również wprowadzanie zmian.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktywne testowanie w portalu QnA Maker

1. Uzyskaj dostęp do bazy wiedzy, wybierając jej nazwę na stronie **Moje bazy wiedzy.**
1. Aby uzyskać dostęp do panelu Wysuwanie testu, wybierz **opcję Testuj** w górnym panelu aplikacji.
1. Wprowadź kwerendę w polu tekstowym i wybierz pozycję Enter.
1. Najlepiej dopasowana odpowiedź z bazy wiedzy jest zwracana jako odpowiedź.

### <a name="clear-test-panel"></a>Wyczyść panel testowy

Aby wyczyścić wszystkie wprowadzone zapytania testowe i ich wyniki z konsoli testowej, wybierz **rozpocznij od nowa** w lewym górnym rogu panelu testowego.

### <a name="close-test-panel"></a>Zamknij panel testowy

Aby zamknąć panel Test, ponownie wybierz przycisk **Testuj.** Gdy panel Test jest otwarty, nie można edytować zawartości bazy wiedzy.

### <a name="inspect-score"></a>Sprawdź wynik

Szczegółowe informacje o wyniku testu można sprawdzić w panelu Inspekcja.

1.  Po otwarciu panelu Wysuń testu wybierz pozycję **Sprawdź, aby uzyskać** więcej informacji na temat tej odpowiedzi.

    ![Sprawdzanie odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Pojawi się panel Inspekcji. Panel zawiera najlepsze intencji punktacji, jak również wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedź.

### <a name="correct-the-top-scoring-answer"></a>Poprawianie odpowiedzi na najlepszą punktację

Jeśli odpowiedź na najlepszy punkt klasyfikacji jest nieprawidłowa, wybierz poprawną odpowiedź z listy i wybierz **pozycję Zapisz i trenuj**.

![Poprawianie odpowiedzi na najlepszą punktację](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Dodawanie pytań alternatywnych

Do danej odpowiedzi można dodać alternatywne formy pytania. Wpisz alternatywne odpowiedzi w polu tekstowym i kliknij enter, aby je dodać. Wybierz **pozycję Zapisz i trenuj,** aby zapisać aktualizacje.

![Dodawanie pytań alternatywnych](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Dodawanie nowej odpowiedzi

Można dodać nową odpowiedź, jeśli którykolwiek z istniejących odpowiedzi, które zostały dopasowane są niepoprawne lub odpowiedź nie istnieje w bazie wiedzy (nie dobre dopasowanie znaleziono w KB).

U dołu listy odpowiedzi użyj pola tekstowego, aby wprowadzić nową odpowiedź, a następnie naciśnij klawisz Enter, aby ją dodać.

Wybierz **pozycję Zapisz i trenuj,** aby utrwalić tę odpowiedź. Nowa para pytań i odpowiedzi została dodana do twojej bazy wiedzy.

> [!NOTE]
> Wszystkie zmiany w bazie wiedzy są zapisywane tylko po naciśnięciu przycisku **Zapisz i trenuj.**

### <a name="test-the-published-knowledge-base"></a>Przetestuj opublikowaną bazę wiedzy

Opublikowaną wersję bazy wiedzy można przetestować w okienku testowym. Po opublikowaniu bazy wiedzy zaznacz pole **Opublikowana baza wiedzy** i wyślij zapytanie, aby uzyskać wyniki z opublikowanej bazy wiedzy.

![Test na opublikowanej KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Test wsadowy za pomocą narzędzia

Użyj narzędzia do testowania wsadowego, jeśli chcesz:

* określić najlepszą odpowiedź i wynik dla zestawu pytań
* sprawdzania poprawności oczekiwanej odpowiedzi na zestaw pytań

Przeczytaj [samouczek](../Quickstarts/batch-testing.md) testowania wsadowego, aby uzyskać instrukcje krok po kroku.

Testowanie wsadowe jest dostarczane z narzędziem do testowania wsadowego. To narzędzie jest dostępne jako [plik wykonywalny do](https://aka.ms/qnamakerbatchtestingtool) pobrania lub jako [kod źródłowy języka C#.](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)

[Dokumentacja referencyjna dotycząca narzędzia](../reference-tsv-format-batch-testing.md) zawiera:

* przykład wiersza polecenia narzędzia
* format plików wejściowych i wychodzących TSV

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
