---
title: Jak edytować wiedzy - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak edytować bazy wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348680"
---
# <a name="edit-a-knowledge-base"></a>Edytuj bazy wiedzy

Maker — strona główna służy do zarządzania zawartością bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.

## <a name="edit-your-knowledge-base-content"></a>Edytuj zawartość wiedzy

1.  Wybierz **Moje baz wiedzy** w górnym pasku nawigacyjnym. 

    Widać wszystkie usługi utworzone lub udostępnionych Tobie posortowane w kolejności malejącej **ostatniej modyfikacji** daty.

    ![Moje baz wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Wybierz określonego wiedzy, aby wprowadzić zmiany.

3. Po zakończeniu wprowadzania zmian w bazie wiedzy, kliknij **Zapisz i uczenia** w prawym górnym rogu strony, aby zachować zmiany.    

    ![Zapisz i szkolenia](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Opuszczenie strony przed kliknięciem przycisku przy zapisie i pociągu nie utrwala zmiany.

## <a name="add-a-qna-pair"></a>Dodaj parę — strona główna

Wybierz **pytań Dodaj parę** można dodać nowy wiersz do tabeli bazy wiedzy knowledge base.

![Dodaj parę — strona główna](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Usuń parę — strona główna

Aby usunąć — strona główna, kliknij przycisk **usunąć** ikonę na prawym końcu wiersza — strona główna.

![Usuń pary — strona główna](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Dodaj alternatywne pytania do istniejącej pary — strona główna zwiększające prawdopodobieństwo zgodny z zapytaniem użytkownika.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Dodawanie metadanych


Dodawanie pary metadanych, wybierając ikonę filtru

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Upewnij się okresowo zapisać i uczenia po wprowadzeniu zmian w celu uniknięcia utraty zmian w bazie wiedzy.

## <a name="manage-large-knowledge-bases"></a>Zarządzanie dużych baz wiedzy

1. Są QnAs **pogrupowane** przez źródło danych, w którym zostały wyodrębnione. Można rozwinąć lub zwinąć źródła danych.
2. Możesz **wyszukiwania** w bazie wiedzy, wpisując polecenie w polu tekstowym w górnej części tabeli bazy wiedzy Knowledge Base. Kliknij przycisk enter, aby wyszukiwanie zawartości pytanie, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.
3. **Podział na strony** umożliwia zarządzanie dużych baz wiedzy

    ![Wyszukiwanie, z podziałem na strony, grupy](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współpraca w bazie wiedzy](./collaborate-knowledge-base.md)
