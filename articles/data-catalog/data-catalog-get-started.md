---
title: Tworzenie usługi Azure Data Catalog
description: Szybki Start dotyczące sposobu tworzenia usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: f00e9eaf56f3973b357792a8d1923a4b5998e0a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003393"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Szybki start: Tworzenie usługi Azure Data Catalog

Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania zasobów danych przedsiębiorstwa. Szczegółowe omówienie tej usługi można znaleźć w artykule [Co to jest usługa Azure Data Catalog?](overview.md).

Ten przewodnik Szybki Start ułatwia rozpoczęcie pracy od utworzenia usługi Azure Data Catalog.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, musisz mieć:

* A [Microsoft Azure](https://azure.microsoft.com/) subskrypcji.
* Musisz mieć własne [dzierżawy usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Aby skonfigurować wykazu danych, musi być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="create-a-data-catalog"></a>Utworzyć wykaz danych

W ramach organizacji — domeny usługi Azure Active Directory — można aprowizować tylko jeden wykaz danych. W związku z tym jeśli właściciel lub współwłaściciel subskrypcji platformy Azure, który należy do tej domeny usługi Azure Active Directory został już utworzony wykaz, następnie nie możesz utworzyć wykaz ponownie nawet jeśli masz wiele subskrypcji platformy Azure. Aby zobaczyć, czy w domenie usługi Azure Active Directory został utworzony wykaz danych, przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i sprawdź, czy wykaz jest widoczny. Jeśli wykaz został już utworzony, pomiń poniższą procedurę i przejdź do następnej sekcji.

1. Przejdź do [witryny Azure portal](https://portal.azure.com) > **Utwórz zasób** i wybierz **Data Catalog**.

    ![Create Data Catalog](media/data-catalog-get-started/data-catalog-create.png)

2. Określ **nazwa** wykazu danych **subskrypcji** chcesz użyć, **lokalizacji** dla katalogu i **warstwy cenowej**. Następnie wybierz przycisk **Utwórz**.

3. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i kliknij pozycję **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/data-catalog-get-started/data-catalog-publish-data.png)

   Można także uzyskać ze stroną główną wykazu danych z [strony usługi Data Catalog](https://azure.microsoft.com/services/data-catalog) , wybierając **wprowadzenie**.

   ![Usługa Azure Data Catalog — marketingowa strona docelowa](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Przejdź do **ustawienia** strony.

    ![Usługa Azure Data Catalog — aprowizowanie wykazu danych](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozwiń **ceny** i sprawdź usługi Azure Data Catalog **wersji** (bezpłatna lub standardowa).

    ![Usługa Azure Data Catalog — wybieranie wersji](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Jeśli wybierzesz *standardowa* edition jako warstwy cenowej, można rozwinąć **grup zabezpieczeń** i włączyć autoryzowania grup zabezpieczeń usługi Active Directory dostępu do wykazu danych i włączyć automatyczną korektę rozliczenia.

    ![Grupy zabezpieczeń wykazu danych na platformie Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozwiń sekcję **Użytkownicy wykazu** i kliknij pozycję **Dodaj**, aby dodać użytkowników wykazu danych. Automatycznie dodawane do tej grupy.

    ![Usługa Azure Data Catalog — użytkownicy](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Jeśli wybierzesz *standardowa* edition jako warstwy cenowej, można rozwinąć **administratorów glosariusza** i kliknij przycisk **Dodaj** do dodawania użytkowników administratora słownik. Automatycznie dodawane do tej grupy.

    ![Administratorzy glosariusza wykazu danych na platformie Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozwiń sekcję **Administratorzy wykazu** i kliknij pozycję **Dodaj**, aby dodać administratorów wykazu danych. Automatycznie dodawane do tej grupy.

    ![Usługa Azure Data Catalog — administratorzy](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozwiń **tytuł portalu** i dodać dodatkowy tekst, który będzie wyświetlany w tytule portalu.

    ![Tytuł portalu wykazu danych na platformie Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po ukończeniu **ustawienia** stronie, następnie przejdź do folderu **Publikuj** strony.

    ![Usługa Azure Data Catalog — utworzony wykaz](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Znajdowanie wykazu danych w portalu Azure

1. Na osobnej karcie lub w osobnym oknie przeglądarki sieci Web przejdź do [portalu Azure](https://portal.azure.com) i zaloguj się przy użyciu tego samego konta, którego użyto do utworzenia wykazu danych w poprzednim kroku.

2. Wybierz **wszystkich usług** a następnie kliknij przycisk **Data Catalog**.

    ![Usługa Azure Data Catalog — przeglądanie portalu Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Zostanie wyświetlony wykazu danych, który został utworzony.

    ![Usługa Azure Data Catalog — wykaz widoczny na liście](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kliknij utworzony wykaz. W portalu pojawi się blok **Wykaz danych**.

   ![Usługa Azure Data Catalog — blok w portalu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Właściwości wykazu danych można wyświetlać i aktualizować. Możesz na przykład kliknąć pozycję **Warstwa cenowa** i zmienić wersję.

    ![Usługa Azure Data Catalog — warstwa cenowa](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wyjaśniono sposób tworzenia usługi Azure Data Catalog dla Twojej organizacji. Możesz teraz zarejestrować źródła danych w wykazie danych.

> [!div class="nextstepaction"]
> [Rejestracja źródeł danych w usłudze Azure Data Catalog](data-catalog-how-to-register.md)
