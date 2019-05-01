---
title: Jak przetestować wiedzy — QnA Maker
titlesuffix: Azure Cognitive Services
description: Testowanie bazy wiedzy usługi QnA Maker jest ważną częścią proces iteracyjny, aby poprawić dokładność odpowiedzi zostały zwrócone. Możesz przetestować wiedzy za pośrednictwem czatu rozszerzonego interfejsu, który umożliwia także, że możesz wprowadzać zmiany.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: ea2381287729579e10d457912c4147f59716bcc4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875710"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testowanie bazy wiedzy interaktywnie w usługi QnA Maker

Testowanie bazy wiedzy usługi QnA Maker jest ważną częścią proces iteracyjny, aby poprawić dokładność odpowiedzi zostały zwrócone. Możesz przetestować wiedzy za pośrednictwem czatu rozszerzonego interfejsu, który umożliwia także, że możesz wprowadzać zmiany.

## <a name="test-answer-matching"></a>Dopasowanie odpowiedzi testu

1.  Dostęp do bazy wiedzy, wybierając jego nazwę na **moich baz wiedzy** strony.
2.  Aby uzyskać dostęp do panelu slajdów w poziomie testu, wybierz pozycję **testu** w Twojej aplikacji na górnym panelu.

    ![Dostęp do panelu testu](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Wprowadź zapytanie w polu tekstowym i naciśnij klawisz Enter.

4.  Najlepiej dopasowany odpowiedź z bazy wiedzy są zwracane jako odpowiedzi.

## <a name="clear-test-panel"></a>Testu zwykłego panelu

Aby wyczyścić wszystkie zapytania wprowadzony testu i ich wyniki z konsoli testów, wybierz **zacząć od początku** w lewym górnym rogu panelu testu.

## <a name="close-test-panel"></a>Test Zamknij panel

Aby zamknąć panel testu, wybierz **testu** ponownie przycisk. Gdy Test jest otwarty panel, nie można edytować zawartość bazy wiedzy Knowledge Base.

## <a name="inspect-score"></a>Sprawdź wynik

Możesz sprawdzić szczegóły wyniku testu w panelu Sprawdź.

1.  Otwórz panel slajdów w poziomie testu wybierz **Sprawdź** więcej informacji na temat tej odpowiedzi.

    ![Sprawdź odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zostanie wyświetlony panel inspekcji. Na panelu są dostępne u góry oceniania przeznaczenie, a także żadnych określonych jednostek. Zespół przedstawia wynik wybranego wypowiedź.

## <a name="correct-the-top-scoring-answer"></a>Popraw górnej oceniania odpowiedzi

Jeśli u góry oceniania odpowiedzi jest niepoprawny, wybierz poprawną odpowiedź z listy i wybierz przycisk **Zapisz i szkolenie**.

![Popraw górnej oceniania odpowiedzi](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Możesz dodać inne formy pytanie do danej odpowiedzi. Typ odpowiedzi alternatywna w polu tekstowym i kliknij przycisk enter, aby je dodać. Wybierz **Zapisz i szkolenie** do przechowywania aktualizacji.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Dodaj nową odpowiedź

Jeśli któreś z istniejących odpowiedzi, które zostały dopasowane są niepoprawne lub odpowiedź nie istnieje w bazie wiedzy knowledge base (nie dobre znaleziono odpowiedników w KB), możesz dodać nową odpowiedź. Wprowadź nową odpowiedź do bieżącego zapytania w polu tekstowym i naciśnij klawisz enter, aby dodać go. 

Wybierz **Zapisz i szkolenie** można utrwalić tej odpowiedzi. Nową parę odpowiedź na pytanie został dodany do bazy wiedzy.

![Dodaj nową parę pytań i odpowiedzi](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Wszystkie zmiany do bazy wiedzy tylko są zapisywane po naciśnięciu klawisza **Zapisz i szkolenie** przycisku.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
