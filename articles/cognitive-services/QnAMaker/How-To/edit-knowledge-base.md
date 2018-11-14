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
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: fa5b5e1998d54c7b22d01e7cc70e84059d482cfb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623550"
---
# <a name="edit-a-knowledge-base"></a>Edytowanie bazy wiedzy

Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.

## <a name="edit-your-knowledge-base-content"></a>Edytuj zawartość bazy wiedzy

1.  Wybierz **moich baz wiedzy** w górnym pasku nawigacyjnym. 

    Możesz zobaczyć wszystkie usługi, utworzony lub udostępnione Tobie posortowane malejąco według **Data ostatniej modyfikacji** daty.

    ![Moje bazy wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wybierz określoną wiedzy, aby wprowadzić zmiany.
 
1. Wybierz **ustawienia**. W tym miejscu można edytować wymaganym polem nazwy usługi.
  
    |Cel|Akcja|
    |--|--|
    |Dodawanie adresu URL|Możesz dodać nowe adresy URL, aby dodać nową zawartość — często zadawane pytania na bazie wiedzy Knowledge base, klikając **wiedzy Zarządzaj -> "+ Dodaj adres URL"** łącza.|
    |Usuń adres URL|Możesz usunąć istniejące adresy URL, wybierając ikonę usuwania, Kosza.|
    |Odśwież zawartość adresu URL|Wiedzy do przeszukiwania najnowszej zawartości istniejące adresy URL, zaznacz **Odśwież** pola wyboru. Spowoduje to zaktualizowanie bazy wiedzy o najnowszą zawartość adresu URL.|
    |Dodaj plik|Można dodać dokument obsługiwany plik należeć wiedzy, wybierając **Zarządzanie wiedzy**, a następnie wybierając pozycję **+ Dodaj plik**|
    |Import|Możesz również zaimportować wszystkie istniejące bazy wiedzy knowledge base, wybierając **Ímport wiedzy** przycisku. |
    |Aktualizacja|Aktualizowanie bazy wiedzy knowledge base zależy od **warstwę cenową zarządzania** używane podczas tworzenia usługi QnA Maker skojarzone z bazy wiedzy. Możesz także zaktualizować warstwa zarządzania z witryny Azure portal, jeśli jest to wymagane.

1. Po zakończeniu wprowadzania zmian w bazie wiedzy knowledge base, wybierz opcję **Zapisz i szkolenie** w prawym górnym rogu strony aby utrwalić zmiany.    

    ![Zapisz i uczenie](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Jeśli zechcesz opuścić stronę przed wybraniem **Zapisz i szkolenie**, wszystkie zmiany zostaną utracone.

## <a name="add-a-qna-pair"></a>Dodawanie pary pytań i odpowiedzi

Wybierz **pary dodawanie pytań i odpowiedzi** Aby dodać nowy wiersz do tabeli w bazie wiedzy knowledge base.

![Dodaj parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Usuń parę pytań i odpowiedzi

Aby usunąć pytań i odpowiedzi, kliknij przycisk **Usuń** ikonę na prawym końcu wiersza pytań i odpowiedzi. To jest operacją nieodwracalną. Nie można cofnąć. Należy wziąć pod uwagę swoje KB z eksportowania **Publikuj** strony przed usunięciem pary. 

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
2. Możesz **wyszukiwania** bazy wiedzy knowledge base, wpisując w polu tekstowym w górnej części w tabeli w bazie wiedzy Knowledge Base. Naciśnij klawisz enter, wyszukiwanie zawartości zapytania, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.
3. **Podział na strony** umożliwia zarządzanie duże bazy wiedzy

    ![Wyszukiwanie, stronicowanie i grupy](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="delete-knowledge-bases"></a>Usuń z bazy wiedzy

Usuwanie bazy wiedzy (KB) jest operacją nieodwracalną. Nie można cofnąć. Przed usunięciem bazy wiedzy, należy wyeksportować wiedzy z **ustawienia** stronie portalu narzędzia QnA Maker. 

Jeśli udostępniasz wiedzy o [współpracowników](collaborate-knowledge-base.md) usuń go, wszyscy utraci dostęp w KB. 

## <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure 

Usunięcie wszystkich zasobów platformy Azure używane na potrzeby usługi QnA Maker baz danych, baz wiedzy przestaną działać. Przed usunięciem wszystkich zasobów, upewnij się, możesz wyeksportować swoje baz wiedzy z **ustawienia** strony. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współpracuj nad bazy wiedzy](./collaborate-knowledge-base.md)
