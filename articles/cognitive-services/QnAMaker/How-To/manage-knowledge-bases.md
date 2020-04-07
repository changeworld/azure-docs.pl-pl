---
title: Zarządzanie bazami wiedzy - QnA Maker
description: QnA Maker umożliwia zarządzanie bazami wiedzy, zapewniając dostęp do ustawień bazy wiedzy i zawartości.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756804"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Tworzenie bazy wiedzy i zarządzanie ustawieniami

QnA Maker umożliwia zarządzanie bazami wiedzy, zapewniając dostęp do ustawień bazy wiedzy i źródeł danych.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
> * [Zasób QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) utworzony w witrynie Azure portal. Zapamiętaj identyfikator usługi Azure Active Directory, subskrypcję, nazwę zasobu QnA wybraną podczas tworzenia zasobu.

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

1. Zaloguj się do portalu [QnAMaker.ai](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

1. Na stronie **Tworzenie** pomiń **krok 1,** jeśli masz już zasób QnA Maker.

    Jeśli zasób nie został jeszcze utworzony, wybierz pozycję **Utwórz usługę QnA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji. Zapamiętaj identyfikator usługi Azure Active Directory, subskrypcję, nazwę zasobu QnA wybraną podczas tworzenia zasobu.

    Po zakończeniu tworzenia zasobu w witrynie Azure portal, wróć do portalu QnA Maker, odśwież stronę przeglądarki i przejdź do **kroku 2**.

1. W **kroku 3**wybierz usługę Active Directory, subskrypcję, usługę (zasób) i język dla wszystkich baz wiedzy utworzonych w usłudze.

   ![Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. W **kroku 3**, `My Sample QnA KB`nazwij swoją bazę wiedzy.

1. W **kroku 4**skonfiguruj ustawienia za pomocą poniższej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz ekstrakcję wielu kolejek z plików URL, pdf lub docx.**|Zaznaczone|
    |**Domyślny tekst odpowiedzi**| `Quickstart - default answer not found.`|
    |**+ Dodaj adres URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-czat**|Wybierz **profesjonalny**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją bazę wiedzy .**

    Proces wyodrębniania zajmuje kilka chwil, aby przeczytać dokument i zidentyfikować pytania i odpowiedzi.

    Po pomyślnym utworzenie bazy wiedzy programu QnA Maker zostanie otwarta strona **bazy wiedzy.** Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="edit-knowledge-base"></a>Edytowanie bazy wiedzy

1.  Wybierz **pozycję Moje bazy wiedzy** na górnym pasku nawigacyjnym.

       Możesz zobaczyć wszystkie usługi utworzone lub udostępnione ci posortowane w kolejności malejącej **ostatniej zmodyfikowanej** daty.

       ![Moje bazy wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wybierz konkretną bazę wiedzy, aby wprowadzić do niej zmiany.

1.  Wybierz **pozycję Ustawienia**. Poniższa lista zawiera pola, które można zmienić.

       |Cel|Akcja|
       |--|--|
       |Dodaj adres URL|Możesz dodać nowe adresy URL, aby dodać nową zawartość często zadawanych pytań do bazy wiedzy, klikając link **Zarządzaj bazą wiedzy -> +Dodaj adres URL".**|
       |Usuń adres URL|Istniejące adresy URL można usunąć, wybierając ikonę usuwania, kosz.|
       |Odświeżanie zawartości|Jeśli chcesz, aby baza wiedzy indeksować najnowszą zawartość istniejących adresów URL, zaznacz pole wyboru **Odśwież.** Spowoduje to zaktualizowanie bazy wiedzy o najnowszą zawartość adresu URL raz. Nie jest to ustawienie regularnego harmonogramu aktualizacji.|
       |Dodaj plik|Obsługiwany dokument pliku można dodać, aby był częścią bazy wiedzy, wybierając **pozycję Zarządzaj bazą wiedzy,** a następnie wybierając **pozycję + Dodaj plik**|
    |Import|Można również zaimportować dowolną istniejącą bazę wiedzy, wybierając przycisk **Importuj bazę wiedzy.** |
    |Aktualizacja|Aktualizowanie bazy wiedzy zależy od **warstwy cenowej zarządzania** używanej podczas tworzenia usługi QnA Maker skojarzonej z bazą wiedzy. W razie potrzeby można również zaktualizować warstwę zarządzania z witryny Azure portal.

  1. Po zakończeniu wprowadzania zmian w bazie wiedzy wybierz **pozycję Zapisz i trenuj** w prawym górnym rogu strony, aby utrwalić zmiany.

       ![Oszczędzaj i trenuj](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Jeśli opuścisz stronę przed wybraniem **opcji Zapisz i trenuj,** wszystkie zmiany zostaną utracone.



## <a name="manage-large-knowledge-bases"></a>Zarządzanie dużymi bazami wiedzy

* **Grupy źródeł danych:** QnA są pogrupowane według źródła danych, z którego zostały wyodrębnione. Można rozwinąć lub zwinąć źródło danych.

    ![Zwężanie i rozszerzanie pytań i odpowiedzi dotyczących źródła danych za pomocą paska źródła danych programu QnA Maker](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Baza wiedzy wyszukiwania:** Możesz przeszukiwać bazę wiedzy, wpisując je w polu tekstowym u góry tabeli Bazy wiedzy. Kliknij enter, aby wyszukać zawartość pytania, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.

    ![Użyj pola wyszukiwania QnA Maker nad pytaniami i odpowiedziami, aby zmniejszyć widok tylko do elementów pasujących do filtrów](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Podział na strony:** szybkie przechodzenie przez źródła danych w celu zarządzania dużymi bazami wiedzy

    ![Skorzystaj z funkcji podziałki na strony QnA Maker nad pytaniami i odpowiedziami, aby poruszać się po stronach pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Usuwanie baz wiedzy

Usuwanie bazy wiedzy (KB) jest operacją trwałą. Nie można go cofnąć. Przed usunięciem bazy wiedzy należy wyeksportować bazę wiedzy ze strony **Ustawienia** portalu QnA Maker.

Jeśli udostępniasz swoją bazę wiedzy współpracownikom,](współpraca-wiedza-base.md), a następnie ją usuwasz, wszyscy tracą dostęp do bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzaniu językiem](language-knowledge-base.md) wszystkich baz wiedzy w zasobie.

* Edytuj pary QnA
* Zarządzanie zasobami platformy Azure używanymi przez program QnA Maker