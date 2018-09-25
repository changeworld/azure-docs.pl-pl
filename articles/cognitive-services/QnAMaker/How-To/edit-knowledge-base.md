---
title: Edytowanie wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ca35e13c502efadad07ef78077271923951ae4b1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038225"
---
# <a name="edit-a-knowledge-base"></a>Edytowanie bazy wiedzy

Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.

## <a name="edit-your-knowledge-base-content"></a>Edytuj zawartość bazy wiedzy

1.  Wybierz **moich baz wiedzy** w górnym pasku nawigacyjnym. 

    Możesz zobaczyć wszystkie usługi, utworzony lub udostępnione Tobie posortowane malejąco według **Data ostatniej modyfikacji** daty.

    ![Moje bazy wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Wybierz określoną wiedzy, aby wprowadzić zmiany.

3. Po zakończeniu wprowadzania zmian w bazie wiedzy Knowledge base, kliknij pozycję **Zapisz i szkolenie** w prawym górnym rogu strony aby utrwalić zmiany.    

    ![Zapisz i uczenie](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Opuszczenie strony przed kliknięciem przycisku zapisywania i szkolenie nie utrwala zmiany.

## <a name="add-a-qna-pair"></a>Dodaj parę pytań i odpowiedzi

Wybierz **pary dodawanie pytań i odpowiedzi** Aby dodać nowy wiersz do tabeli w bazie wiedzy knowledge base.

![Dodaj parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Usuń parę pytań i odpowiedzi

Aby usunąć pytań i odpowiedzi, kliknij przycisk **Usuń** ikonę na prawym końcu wiersza pytań i odpowiedzi.

![Usuń parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Dodaj alternatywne pytania do istniejącej pary pytań i odpowiedzi, aby zwiększyć prawdopodobieństwo aby dopasowanie zakończyło się zapytania użytkownika.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Dodawanie metadanych


Dodaj pary metadanych, wybierając ikonę filtru

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Upewnij się okresowo zapisywać i uczenie bazy wiedzy knowledge base po wprowadzeniu zmian w celu uniknięcia utraty zmian.

## <a name="manage-large-knowledge-bases"></a>Zarządzanie bazami wiedzy duże

1. Są znacznie **pogrupowane** przez źródło danych, w którym zostały wyodrębnione. Można rozwinąć lub zwinąć źródła danych.
2. Możesz **wyszukiwania** bazy wiedzy knowledge base, wpisując w polu tekstowym w górnej części w tabeli w bazie wiedzy Knowledge Base. Naciśnij klawisz enter, aby wyszukać zawartość pytanie i odpowiedź metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.
3. **Podział na strony** umożliwia zarządzanie duże bazy wiedzy

    ![Wyszukiwanie, stronicowanie i grupy](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współpracuj nad bazy wiedzy](./collaborate-knowledge-base.md)
