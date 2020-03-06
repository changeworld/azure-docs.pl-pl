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
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389443"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Edytowanie wiedzy w usługi QnA Maker

Usługa QnA Maker umożliwia zarządzanie zawartość bazy wiedzy, zapewniając łatwy w użyciu środowisko edycji.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Edytuj zawartość bazy wiedzy

1.  Na górnym pasku nawigacyjnym wybierz pozycję **Moje bazy wiedzy** . 

    Wszystkie utworzone lub udostępnione przez Ciebie usługi można zobaczyć w kolejności malejącej według daty **ostatniej modyfikacji** .

    ![Moje bazy wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wybierz określoną wiedzy, aby wprowadzić zmiany.
 
1. Wybierz pozycję **Ustawienia**. W tym miejscu można edytować wymaganym polem nazwy usługi.
  
    |Cel|Akcja|
    |--|--|
    |Dodawanie adresu URL|Możesz dodać nowe adresy URL, aby dodać nową zawartość często zadawanych pytań do bazy wiedzy, klikając pozycję **Zarządzaj bazą wiedzy — > "+ Dodaj adres URL"** .|
    |Usuń adres URL|Możesz usunąć istniejące adresy URL, wybierając ikonę usuwania, Kosza.|
    |Odśwież zawartość|Jeśli chcesz, aby baza wiedzy przeszukał najnowszą zawartość istniejących adresów URL, zaznacz pole wyboru **Odśwież** . Spowoduje to zaktualizowanie bazy wiedzy przy użyciu najnowszej zawartości adresu URL. Nie jest to ustawienie regularnego harmonogramu aktualizacji.|
    |Dodaj plik|Możesz dodać obsługiwany dokument pliku, który będzie częścią bazy wiedzy, wybierając pozycję **Zarządzaj bazą wiedzy**, a następnie wybierając pozycję **+ Dodaj plik**|
    |Import|Możesz również zaimportować każdą istniejącą bazę wiedzy, wybierając przycisk **Ímport Knowledge Base** . |
    |Aktualizacja|Aktualizacja bazy wiedzy zależy od **warstwy cenowej zarządzania** używanej podczas tworzenia usługi QNA Maker skojarzonej z bazą wiedzy. Możesz także zaktualizować warstwa zarządzania z witryny Azure portal, jeśli jest to wymagane.

1. Po zakończeniu wprowadzania zmian w bazie wiedzy wybierz pozycję **Zapisz i Wyszkol** w prawym górnym rogu strony, aby zachować zmiany.    

    ![Zapisz i uczenie](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Jeśli opuścisz stronę przed wybraniem opcji **Zapisz i pouczenie**, wszystkie zmiany zostaną utracone.

## <a name="add-a-qna-pair"></a>Dodawanie pary pytań i odpowiedzi

Na stronie **Edycja** wybierz pozycję **Dodaj parę QNA** , aby dodać nowy wiersz do tabeli bazy wiedzy.

![Dodaj parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Usuń parę pytań i odpowiedzi

Aby usunąć QnA, kliknij ikonę **Usuń** znajdującą się po prawej stronie wiersza QNA. To jest operacją nieodwracalną. Nie można cofnąć. Przed usunięciem par Rozważ wyeksportowanie bazy wiedzy ze strony **publikowania** . 

![Usuń parę pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Dodaj alternatywne pytania do istniejącej pary pytań i odpowiedzi, aby zwiększyć prawdopodobieństwo aby dopasowanie zakończyło się zapytania użytkownika.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Dodawanie metadanych

Dodaj pary metadanych, zaznaczając najpierw **Opcje widoku**, a następnie wybierając **Pokaż metadane**. Spowoduje to wyświetlenie kolumny metadanych. Następnie wybierz znak **+** , aby dodać parę metadanych. Ta para składa się z jednego klucza i jednej wartości.

![Dodawanie metadanych](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Upewnij się okresowo zapisywać i uczenie bazy wiedzy knowledge base po wprowadzeniu zmian w celu uniknięcia utraty zmian.

## <a name="manage-large-knowledge-bases"></a>Zarządzanie bazami wiedzy duże

* **Grupy źródeł danych**: bazami są pogrupowane według źródła danych, z którego zostały wyodrębnione. Można rozwinąć lub zwinąć źródła danych.

    ![Użyj paska źródła danych QnA Maker, aby zwinąć i rozwinąć pytania i odpowiedzi ze źródła danych](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Baza wiedzy wyszukiwania**: bazę wiedzy można wyszukać, wpisując w polu tekstowym w górnej części tabeli bazy wiedzy. Naciśnij klawisz enter, wyszukiwanie zawartości zapytania, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.

    ![Użyj pola wyszukiwania QnA Maker powyżej pytań i odpowiedzi, aby zmniejszyć widok tylko do elementów zgodnych z filtrem](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stronicowanie**: szybkie przechodzenie między źródłami danych w celu zarządzania dużymi bazami wiedzy

    ![Użyj QnA Maker funkcji stronicowania nad pytaniami i odpowiedziami, aby poruszać się po stronach pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Usuń z bazy wiedzy

Usuwanie bazy wiedzy (KB) jest operacją nieodwracalną. Nie można cofnąć. Przed usunięciem bazy wiedzy należy wyeksportować bazę wiedzy ze strony **Ustawienia** w portalu QNA Maker. 

Jeśli udostępnisz swoją KB [współpracownikom](collaborate-knowledge-base.md) , a następnie usuniesz ją, wszyscy straci dostęp do bazy wiedzy. 

## <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure 

Usunięcie wszystkich zasobów platformy Azure używane na potrzeby usługi QnA Maker baz danych, baz wiedzy przestaną działać. Przed usunięciem jakichkolwiek zasobów należy koniecznie wyeksportować bazy wiedzy na stronie **Ustawienia** . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpracuj z bazą wiedzy](./collaborate-knowledge-base.md)
