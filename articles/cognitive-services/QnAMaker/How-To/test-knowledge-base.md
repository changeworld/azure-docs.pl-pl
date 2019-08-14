---
title: Jak przetestować wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Testowanie bazy wiedzy usługi QnA Maker jest ważną częścią proces iteracyjny, aby poprawić dokładność odpowiedzi zostały zwrócone. Możesz przetestować wiedzy za pośrednictwem czatu rozszerzonego interfejsu, który umożliwia także, że możesz wprowadzać zmiany.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b9c6f96cc351831bb784bb89db1bce76b01190b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966671"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testowanie bazy wiedzy interaktywnie w usługi QnA Maker

Testowanie bazy wiedzy usługi QnA Maker jest ważną częścią proces iteracyjny, aby poprawić dokładność odpowiedzi zostały zwrócone. Możesz przetestować wiedzy za pośrednictwem czatu rozszerzonego interfejsu, który umożliwia także, że możesz wprowadzać zmiany.

## <a name="test-answer-matching"></a>Dopasowanie odpowiedzi testu

1. Uzyskaj dostęp do bazy wiedzy, wybierając jej nazwę na stronie **Moje bazy wiedzy** .
1. Aby uzyskać dostęp do panelu slajdu testu, wybierz opcję **test** w górnym panelu aplikacji.
1. Wprowadź zapytanie w polu tekstowym i naciśnij klawisz Enter.
1. Najlepiej dopasowany odpowiedź z bazy wiedzy są zwracane jako odpowiedzi.

## <a name="clear-test-panel"></a>Testu zwykłego panelu

Aby wyczyścić wszystkie wprowadzone zapytania testowe i ich wyniki z konsoli testowej, wybierz pozycję **Rozpocznij** w lewym górnym rogu panelu test.

## <a name="close-test-panel"></a>Test Zamknij panel

Aby zamknąć Panel test, ponownie wybierz przycisk **Testuj** . Gdy Test jest otwarty panel, nie można edytować zawartość bazy wiedzy Knowledge Base.

## <a name="inspect-score"></a>Sprawdź wynik

Sprawdzasz szczegóły wyniku testu w panelu Inspekcja.

1.  Po otwarciu panelu slajdu testowego wybierz pozycję **Sprawdź** , aby uzyskać więcej informacji na temat tej odpowiedzi.

    ![Sprawdź odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zostanie wyświetlony panel Inspekcja. Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.

## <a name="correct-the-top-scoring-answer"></a>Popraw górnej oceniania odpowiedzi

Jeśli u góry oceniania odpowiedzi jest niepoprawny, wybierz poprawną odpowiedź z listy i wybierz przycisk **Zapisz i szkolenie**.

![Popraw górnej oceniania odpowiedzi](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Możesz dodać inne formy pytanie do danej odpowiedzi. Typ odpowiedzi alternatywna w polu tekstowym i kliknij przycisk enter, aby je dodać. Wybierz **Zapisz i szkolenie** do przechowywania aktualizacji.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Dodaj nową odpowiedź

Jeśli któreś z istniejących odpowiedzi, które zostały dopasowane są niepoprawne lub odpowiedź nie istnieje w bazie wiedzy knowledge base (nie dobre znaleziono odpowiedników w KB), możesz dodać nową odpowiedź. 

W dolnej części listy odpowiedzi Użyj pola tekstowego, aby wprowadzić nową odpowiedź, i naciśnij klawisz ENTER, aby go dodać. 

Wybierz **Zapisz i szkolenie** można utrwalić tej odpowiedzi. Nową parę odpowiedź na pytanie został dodany do bazy wiedzy. 

> [!NOTE]
> Wszystkie zmiany do bazy wiedzy tylko są zapisywane po naciśnięciu klawisza **Zapisz i szkolenie** przycisku.

## <a name="test-the-published-knowledge-base"></a>Testowanie opublikowanej bazy wiedzy

Opublikowaną wersję bazy wiedzy można testować w okienku testów. Po opublikowaniu bazy wiedzy wybierz pole opublikowana **Baza wiedzy** i Wyślij zapytanie w celu uzyskania wyników z opublikowanej bazy wiedzy.

![Testuj względem opublikowanej bazy wiedzy](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
