---
title: Jak przetestować bazę wiedzy QnA Maker
titleSuffix: Azure Cognitive Services
description: Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego, aby poprawić dokładność zwracanych odpowiedzi. Bazę wiedzy można przetestować za pośrednictwem rozszerzonego interfejsu czatu, który umożliwia również edycję.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491215"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Przetestuj bazę wiedzy w QnA Maker

Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego, aby poprawić dokładność zwracanych odpowiedzi. Bazę wiedzy można przetestować za pośrednictwem rozszerzonego interfejsu czatu, który umożliwia również edycję.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktywny test w portalu QnA Maker

1. Uzyskaj dostęp do bazy wiedzy, wybierając jej nazwę na stronie **Moje bazy wiedzy** .
1. Aby uzyskać dostęp do panelu slajdu testu, wybierz opcję **test** w górnym panelu aplikacji.
1. Wprowadź zapytanie w polu tekstowym i wybierz klawisz ENTER.
1. Najlepsza dopasowana odpowiedź z bazy wiedzy jest zwracana jako odpowiedź.

### <a name="clear-test-panel"></a>Wyczyść panel testu

Aby wyczyścić wszystkie wprowadzone zapytania testowe i ich wyniki z konsoli testowej, wybierz pozycję **Rozpocznij** w lewym górnym rogu panelu test.

### <a name="close-test-panel"></a>Zamknij Panel testu

Aby zamknąć Panel test, ponownie wybierz przycisk **Testuj** . Gdy Panel testowy jest otwarty, nie można edytować zawartości bazy wiedzy.

### <a name="inspect-score"></a>Sprawdź ocenę

Sprawdzasz szczegóły wyniku testu w panelu Inspekcja.

1.  Po otwarciu panelu slajdu testowego wybierz pozycję **Sprawdź** , aby uzyskać więcej informacji na temat tej odpowiedzi.

    ![Inspekcja odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zostanie wyświetlony panel Inspekcja. Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.

### <a name="correct-the-top-scoring-answer"></a>Popraw górną odpowiedź oceniania

Jeśli Górna odpowiedź oceniania jest niepoprawna, wybierz odpowiednią odpowiedź z listy i wybierz pozycję **Zapisz i pouczenie**.

![Popraw górną odpowiedź oceniania](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Możesz dodać alternatywne formy pytania do danej odpowiedzi. Wpisz alternatywne odpowiedzi w polu tekstowym, a następnie kliknij klawisz ENTER, aby je dodać. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać aktualizacje.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Dodaj nową odpowiedź

Można dodać nową odpowiedź, jeśli którykolwiek z dopasowanych odpowiedzi jest nieprawidłowy lub odpowiedź nie istnieje w bazie wiedzy (nie znaleziono dobrego dopasowania w KB). 

W dolnej części listy odpowiedzi Użyj pola tekstowego, aby wprowadzić nową odpowiedź, i naciśnij klawisz ENTER, aby go dodać. 

Wybierz pozycję **Zapisz i poszkol,** aby zachować tę odpowiedź. Nowa para pytań i odpowiedzi została teraz dodana do bazy wiedzy. 

> [!NOTE]
> Wszystkie zmiany w bazie wiedzy są zapisywane tylko po naciśnięciu przycisku **Zapisz i pouczenie** .

### <a name="test-the-published-knowledge-base"></a>Testowanie opublikowanej bazy wiedzy

Opublikowaną wersję bazy wiedzy można testować w okienku testów. Po opublikowaniu bazy wiedzy wybierz pole **opublikowana baza wiedzy** i Wyślij zapytanie w celu uzyskania wyników z opublikowanej bazy wiedzy.

![Testuj względem opublikowanej bazy wiedzy](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Test wsadowy z narzędziem

Użyj narzędzia testowania wsadowego, gdy chcesz:

* Określanie najważniejszych odpowiedzi i wyników dla zestawu pytań
* Weryfikuj oczekiwaną odpowiedź dla zestawu pytań

Testy wsadowe są udostępniane za pomocą narzędzia do testowania wsadowego. To narzędzie jest dostępne jako [spakowany plik wykonywalny](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) do pobrania lub jako [ C# kod źródłowy](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[Dokumentacja dotycząca narzędzia](../reference-tsv-format-batch-testing.md) zawiera następujące informacje:

* przykładowy wiersz polecenia narzędzia
* Format plików wejściowych i wyjściowych TSV 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
