---
title: Edytowanie wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1450d6b41d9a193f8069509aacd3343ae646c6ac
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307023"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Edytowanie wiedzy w usługi QnA Maker

Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.

<a name="add-datasource"></a>

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

Na stronie **Edycja** wybierz pozycję **Dodaj parę QNA** , aby dodać nowy wiersz do tabeli bazy wiedzy.

![Dodaj parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Usuń parę pytań i odpowiedzi

Aby usunąć pytań i odpowiedzi, kliknij przycisk **Usuń** ikonę na prawym końcu wiersza pytań i odpowiedzi. To jest operacją nieodwracalną. Nie można cofnąć. Należy wziąć pod uwagę swoje KB z eksportowania **Publikuj** strony przed usunięciem pary. 

![Usuń parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Dodaj alternatywne pytania do istniejącej pary pytań i odpowiedzi, aby zwiększyć prawdopodobieństwo aby dopasowanie zakończyło się zapytania użytkownika.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Dodawanie metadanych

Dodaj pary metadanych, zaznaczając najpierw **Opcje widoku**, a następnie wybierając **Pokaż metadane**. Spowoduje to wyświetlenie kolumny metadanych. Następnie wybierz **+** znak, aby dodać parę metadanych. Ta para składa się z jednego klucza i jednej wartości.

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Upewnij się okresowo zapisywać i uczenie bazy wiedzy knowledge base po wprowadzeniu zmian w celu uniknięcia utraty zmian.

## <a name="manage-large-knowledge-bases"></a>Zarządzanie bazami wiedzy duże

* **Grupy źródeł danych**: Bazami są pogrupowane według źródła danych, z którego zostały wyodrębnione. Można rozwinąć lub zwinąć źródła danych.

    ![Użyj paska źródła danych QnA Maker, aby zwinąć i rozwinąć pytania i odpowiedzi ze źródła danych](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Baza wiedzy wyszukiwania**: Bazę wiedzy można wyszukać, wpisując w polu tekstowym w górnej części tabeli bazy wiedzy. Naciśnij klawisz enter, wyszukiwanie zawartości zapytania, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.

    ![Użyj pola wyszukiwania QnA Maker powyżej pytań i odpowiedzi, aby zmniejszyć widok tylko do elementów zgodnych z filtrem](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Podział na strony**: Szybkie przechodzenie między źródłami danych w celu zarządzania dużymi bazami wiedzy

    ![Użyj QnA Maker funkcji stronicowania nad pytaniami i odpowiedziami, aby poruszać się po stronach pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Usuń z bazy wiedzy

Usuwanie bazy wiedzy (KB) jest operacją nieodwracalną. Nie można cofnąć. Przed usunięciem bazy wiedzy, należy wyeksportować wiedzy z **ustawienia** stronie portalu narzędzia QnA Maker. 

Jeśli udostępniasz wiedzy o [współpracowników](collaborate-knowledge-base.md) usuń go, wszyscy utraci dostęp w KB. 

## <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure 

Usunięcie wszystkich zasobów platformy Azure używane na potrzeby usługi QnA Maker baz danych, baz wiedzy przestaną działać. Przed usunięciem wszystkich zasobów, upewnij się, możesz wyeksportować swoje baz wiedzy z **ustawienia** strony. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współpracuj nad bazy wiedzy](./collaborate-knowledge-base.md)
