---
title: Jak przetestować bazy wiedzy knowledge base - Maker — strona główna - usług kognitywnych Azure | Dokumentacja firmy Microsoft
description: Testowanie bazy wiedzy przed jej opublikowaniem.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348681"
---
# <a name="test-your-knowledge-base"></a>Testowanie bazy wiedzy

Testowanie Maker — strona główna bazy wiedzy jest ważnym elementem procesem iteracyjnym w celu zwiększenia dokładności odpowiedzi zostały zwrócone. Można przetestować w bazie wiedzy za pomocą rozmowy rozszerzone interfejs, który umożliwia też dokonaj edycji.

## <a name="test-answer-matching"></a>Dopasowanie odpowiedzi testu

1.  Dostęp do bazy wiedzy, wybierając jego nazwę na **Moje baz wiedzy** strony.
2.  Aby uzyskać dostęp do panelu poza slajdów testu, wybierz **testu** w górnym panelu aplikacji.

    ![Test dostępu](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Wprowadź zapytanie w polu tekstowym, a następnie wybierz Enter.

4.  Najlepiej dopasowanej odpowiedzi z bazy wiedzy jest zwracana jako odpowiedzi.

## <a name="clear-test-panel"></a>Panel wyczyść testu

Aby wyczyścić wszystkie zapytania wprowadzone testu i ich wyniki z testów konsoli, wybierz **rozpoczęcie** w lewym górnym rogu panelu testu.

## <a name="close-test-panel"></a>Panel Zamknij testu

Aby zamknąć panel testu, wybierz **testu** przycisk ponownie. Po otwarciu Panelu testu nie można edytować zawartość bazy wiedzy Knowledge Base.

## <a name="inspect-score"></a>Sprawdź wynik

Należy sprawdzić szczegóły wyniku testu w panelu inspekcję.

1.  Otwórz Panel slajdów poza testu wybierz **inspekcję** uzyskać więcej informacji dotyczących tej odpowiedzi.

    ![Sprawdź odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Pojawi się panel inspekcji. Panel zawiera górnej oceniania opcje, a także wszelkich zidentyfikowanych jednostek. Panel przedstawia wynik utterance wybrane.

## <a name="correct-the-top-scoring-answer"></a>Popraw górnej oceniania odpowiedzi

Jeśli górnej oceniania odpowiedzi jest nieprawidłowy, wybierz poprawną odpowiedź z listy i wybierz **Zapisz i uczenia**.

![Test dostępu](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Inne formy zapytania można dodać do danej odpowiedzi. Typ odpowiedzi alternatywnego w polu tekstowym i kliknij przycisk enter, aby dodać je. Wybierz **Zapisz i uczenia** do przechowywania aktualizacji.

![Test dostępu](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Dodaj nową odpowiedź

Można dodać nową odpowiedź, jeśli istniejące odpowiedzi, które były zgodne są niepoprawne lub odpowiedzi nie istnieje w bazie wiedzy knowledge base (nie dobrej znaleziono dopasowania w KB). Wprowadź nową odpowiedź na pytanie bieżącego w polu tekstowym, a następnie naciśnij klawisz enter, aby dodać go. 

Wybierz **Zapisz i uczenia** utrwalić tej odpowiedzi. Teraz dodano nową parę pytań i odpowiedzi do bazy wiedzy.

![Test dostępu](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Wszystkie zmiany do bazy wiedzy tylko są zapisywane po naciśnięciu **Zapisz i uczenia** przycisku.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
