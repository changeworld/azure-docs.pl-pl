---
title: Tworzenie usługi Azure Data Catalog
description: W tym przewodniku Szybki start opisano sposób tworzenia usługi Azure Data Catalog przy użyciu witryny Azure portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "68976858"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Szybki start: tworzenie wykazu danych platformy Azure

Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania zasobów danych przedsiębiorstwa. Szczegółowe omówienie tej usługi można znaleźć w artykule [Co to jest usługa Azure Data Catalog?](overview.md).

Ten przewodnik Szybki start ułatwia rozpoczęcie tworzenia usługi Azure Data Catalog.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz mieć:

* Subskrypcja [platformy Microsoft Azure.](https://azure.microsoft.com/)
* Musisz mieć własną [dzierżawę usługi Azure Active Directory.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

Aby skonfigurować wykaz danych, musisz być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="create-a-data-catalog"></a>Tworzenie katalogu danych

W ramach organizacji — domeny usługi Azure Active Directory — można aprowizować tylko jeden wykaz danych. W związku z tym jeśli właściciel lub współwłaściciel subskrypcji platformy Azure, który należy do tej domeny usługi Azure Active Directory już utworzył katalog, nie można utworzyć katalogu ponownie, nawet jeśli masz wiele subskrypcji platformy Azure. Aby zobaczyć, czy w domenie usługi Azure Active Directory został utworzony wykaz danych, przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i sprawdź, czy wykaz jest widoczny. Jeśli wykaz został już utworzony, pomiń poniższą procedurę i przejdź do następnej sekcji.

1. Przejdź do [witryny Azure portal](https://portal.azure.com) > **Utwórz zasób** i wybierz **pozycję Wykaz danych**.

    ![Przycisk tworzenia usługi Azure Data Catalog](media/data-catalog-get-started/data-catalog-create.png)

2. Określ **nazwę** katalogu danych, **subskrypcji,** której chcesz użyć, **lokalizacji** katalogu i **warstwy cenowej**. Następnie wybierz pozycję **Utwórz**.

3. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i kliknij pozycję **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/data-catalog-get-started/data-catalog-publish-data.png)

   Można również przejść do strony głównej wykazu danych ze [strony usługi wykaz danych,](https://azure.microsoft.com/services/data-catalog) wybierając pozycję **Wprowadzenie**.

   ![Usługa Azure Data Catalog — marketingowa strona docelowa](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Przejdź do strony **Ustawienia.**

    ![Usługa Azure Data Catalog — aprowizowanie wykazu danych](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozwiń **ceny** i zweryfikuj **wersję** usługi Azure Data Catalog (bezpłatna lub standardowa).

    ![Usługa Azure Data Catalog — wybieranie wersji](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Jeśli jako warstwę cenową wybierzesz wersję *Standardową,* możesz rozwinąć **grupy zabezpieczeń** i włączyć autoryzowanie grup zabezpieczeń usługi Active Directory w celu uzyskania dostępu do wykazu danych i włączenia automatycznego dostosowywania rozliczeń.

    ![Grupy zabezpieczeń wykazu danych platformy Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozwiń sekcję **Użytkownicy wykazu** i kliknij pozycję **Dodaj**, aby dodać użytkowników wykazu danych. Zostaniesz automatycznie dodany do tej grupy.

    ![Usługa Azure Data Catalog — użytkownicy](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Jeśli jako warstwę cenową wybierzesz wersję *Standardową,* możesz rozwinąć **pozycję Administratorzy glosariuszy** i kliknąć przycisk **Dodaj,** aby dodać użytkowników administratora glosariusza. Zostaniesz automatycznie dodany do tej grupy.

    ![Administratorzy słownika wykazu danych platformy Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozwiń sekcję **Administratorzy wykazu** i kliknij pozycję **Dodaj**, aby dodać administratorów wykazu danych. Zostaniesz automatycznie dodany do tej grupy.

    ![Usługa Azure Data Catalog — administratorzy](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozwiń tytuł **portalu** i dodaj dodatkowy tekst, który będzie wyświetlany w tytule portalu.

    ![Tytuł portalu wykazu danych platformy Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po zakończeniu strony **Ustawienia** przejdź następnie do strony **Publikowania.**

    ![Usługa Azure Data Catalog — utworzony wykaz](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Znajdowanie wykazu danych w portalu Azure

1. Na osobnej karcie lub w osobnym oknie przeglądarki sieci Web przejdź do [portalu Azure](https://portal.azure.com) i zaloguj się przy użyciu tego samego konta, którego użyto do utworzenia wykazu danych w poprzednim kroku.

2. Wybierz **pozycję Wszystkie usługi,** a następnie kliknij pozycję **Wykaz danych**.

    ![Usługa Azure Data Catalog — przeglądanie platformy Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Zobaczysz utworzony katalog danych.

    ![Usługa Azure Data Catalog — wykaz widoczny na liście](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kliknij utworzony wykaz. W portalu pojawi się blok **Wykaz danych**.

   ![Usługa Azure Data Catalog — blok w portalu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Właściwości wykazu danych można wyświetlać i aktualizować. Możesz na przykład kliknąć pozycję **Warstwa cenowa** i zmienić wersję.

    ![Usługa Azure Data Catalog — warstwa cenowa](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć wykaz danych platformy Azure dla swojej organizacji. Teraz można zarejestrować źródła danych w katalogu danych.

> [!div class="nextstepaction"]
> [Rejestrowanie źródeł danych w wykazie danych platformy Azure](data-catalog-how-to-register.md)
