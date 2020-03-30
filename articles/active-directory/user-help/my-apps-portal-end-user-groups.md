---
title: Aktualizowanie informacji o grupach w portalu Moje aplikacje — Usługa Azure AD
description: Dowiedz się, jak wyświetlać i aktualizować informacje dotyczące grup, w tym wyświetlanie posiadanych grup, tworzenie nowych grup, wyświetlanie grup, do których jesteś już członkiem, oraz dołączanie do grup, do których nie jesteś jeszcze częścią.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022300"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aktualizowanie informacji o grupach w portalu Moje aplikacje

Za pomocą konta służbowego można korzystać w portalu **Moje aplikacje** w sieci Web, wyświetlać i uruchamiać wiele aplikacji chmurowych organizacji, aktualizować niektóre informacje o profilu i koncie, wyświetlać informacje o **grupach** oraz przeprowadzać **przeglądy dostępu** do aplikacji i grup. Jeśli nie masz dostępu do portalu **Moje aplikacje,** skontaktuj się z działem pomocy technicznej w celu uzyskania zgody.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, więcej informacji na temat konfigurowania aplikacji opartych na chmurze i zarządzania nimi można znaleźć w [dokumentacji zarządzania aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Wyświetlanie informacji o grupach

Jeśli administrator udzielił Ci uprawnień do wyświetlania kafelka **Grupy,** możesz:

- **Jako członek grupy.** Wyświetl szczegóły lub pozostaw dowolną grupę.

- **Jako właściciel grupy.** Wyświetlanie szczegółów, tworzenie nowej grupy, dodawanie lub usuwanie członków lub usuwanie grupy.

### <a name="to-view-your-groups-information"></a>Aby wyświetlić informacje o grupach

1. Zaloguj się na swoje konto służbowe.

2. Otwórz przeglądarkę internetową https://myapps.microsoft.comi przejdź do strony , lub użyj łącza dostarczonego przez organizację. Na przykład może być przekierowany do dostosowanej strony https://myapps.microsoft.com/contoso.comdla organizacji, takich jak .

    Zostanie wyświetlona strona **Aplikacje** z wyświetlonymi wszystkimi aplikacjami w chmurze należącymi do organizacji i dostępnymi do użycia.

    ![Strona Aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Wybierz kafelek **Grupy,** aby wyświetlić informacje związane z grupą.

    ![Strona Grupy z grupami będącymi własnością i członkami](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Na podstawie twoich uprawnień można użyć strony **Grupy** do:

    - **Przejrzyj posiadane grupy.** Wyświetlanie informacji o grupach posiadane w organizacji z obszaru **Grupy, których jestem właścicielem.** Wybranie określonej nazwy grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczbę członków, zasady dołączania i listę aktywnych członków.

    - **Utwórz nową grupę.** Utwórz nową grupę z tobą jako właścicielem z **obszaru Grupy, których jestem właścicielem.** Aby uzyskać szczegółowe kroki, zobacz [sekcję Tworzenie nowej grupy](#create-a-new-group) tego artykułu.

    - **Edytuj istniejącą grupę.** Edytuj szczegóły dla dowolnej z twoich własnych grup. Aby zapoznać się z określonymi krokami, zobacz [sekcję Edytowanie istniejącej grupy](#edit-an-existing-group) tego artykułu.

    - **Dodawanie lub usuwanie członków.** Dodawanie lub usuwanie członków dla grup, które posiadasz. Aby uzyskać szczegółowe informacje, zobacz [sekcję Dodawanie lub usuwanie członka](#add-or-remove-a-member) tego artykułu.

    - **Odnawianie grupy usługi Office 365.** Jeśli twoja organizacja na to zezwala, możesz odnowić grupy usługi Office 365. Aby uzyskać szczegółowe kroki, zobacz [sekcję grupy Odnawianie usługi Office 365](#renew-an-office-365-group) w tym artykule. 

    - **Usuwanie grupy.** Usuń wszystkie grupy, które posiadasz. Aby uzyskać szczegółowe kroki, zobacz [sekcję Usuwanie grupy](#delete-a-group) tego artykułu.

    - **Przejrzyj grupy, do których należysz.** Wyświetl nazwy wszystkich grup, do których należysz z **grup, w** których jestem. Wybranie określonej nazwy grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczbę członków, zasady dołączania i listę aktywnych członków.

    - **Dołącz do grupy.** Dołącz do istniejącej grupy, której nie jesteś jeszcze członkiem, z **obszaru Grupy, w których jestem.** Aby uzyskać szczegółowe kroki, zobacz [Dołącz do istniejącej grupy](#join-an-existing-group).

## <a name="create-a-new-group"></a>Tworzenie nowej grupy

1. Na stronie **Grupy** wybierz pozycję **Utwórz grupę** z własnego obszaru **Grupy.**

    Pojawi się pole **Utwórz grupę.**

    ![Utwórz pole grupy](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Wprowadź wymagane informacje:

    - **Typ grupy:**

        - **Zabezpieczeń.** Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno.

        - **Usługa Office 365.** Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia także udzielenie dostępu do grupy osobom spoza organizacji.

    - **Nazwa grupy.** Dodaj nazwę grupy, która ma znaczenie i którą łatwo zapamiętać.

    - **Opis grupy (opcjonalnie).** Do grupy możesz dodać opcjonalny opis.

    - **Zasady grupy.** Wybierz opcję, aby umożliwić wszystkim dołączenie do grupy lub zezwolić tylko właścicielowi grupy na dodawanie członków.

3. Wybierz **pozycję Utwórz**.

    Nowa grupa jest tworzona z Tobą jako właścicielem i pojawia się na liście **grupy, które posiadam.** Ponieważ jesteś właścicielem, ta grupa pojawia się również na liście **Grupy, w których jestem.**

## <a name="edit-an-existing-group"></a>Edytowanie istniejącej grupy

Po utworzeniu grupy można edytować jej szczegóły, w tym aktualizować wszystkie istniejące informacje.

1. Zaznacz grupę, którą chcesz edytować na stronie **Grupy,** a następnie wybierz pozycję **Edytuj szczegóły** na * &lt;stronie group_name.&gt; *

    Zostanie **wyświetlenie** pola Edytuj szczegóły i można zaktualizować informacje dodane podczas początkowego tworzenia grupy.

2. Wykonuj wszystkie zmiany, a następnie wybierz pozycję **Aktualizuj**.

## <a name="add-or-remove-a-member"></a>Dodawanie lub usuwanie członka

Możesz dodawać lub usuwać członków dla wszystkich grup, które posiadasz.

1. Zaznacz grupę, do której chcesz dodać **+** członków, a następnie wybierz na * &lt;stronie group_name.&gt; *

    ![Dodawanie członka grupy z wyróżnionym znakiem +](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Wyszukaj członka, którego chcesz dodać, w polu **Dodaj członków,** a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj członków z nowym członkiem do dodania](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Zaproszenie jest wysyłane do nowego członka, aby rozpocząć dostęp do aplikacji organizacji.

3. Jeśli członek został dodany przez pomyłkę lub członek opuścił organizację, możesz usunąć członka, wybierając **pozycję Usuń członka** obok nazwy członka na * &lt;stronie group_name.&gt; *

    ![Usuwanie członka z wyróżnionym łączem usuwania](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Odnawianie grupy usługi Office 365

Jeśli twoja organizacja na to zezwala, możesz odnowić grupę usługi Office 365, przedłużając datę wygaśnięcia.

1. Wybierz grupę usługi Office 365, którą chcesz odnowić, a następnie wybierz pozycję **Odnów grupę**.

    ![Odnawianie grupy usługi Office 365, przedłużenie daty wygaśnięcia](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Kliknij **przycisk OK,** aby zamknąć wiadomość potwierdzającą.

    Po odświeżeniu strony zobaczysz zaktualizowane daty **ostatniego odnowienia** i **wygaśnięcia grupy.**

## <a name="delete-a-group"></a>Usuwanie grupy

W każdej chwili możesz usunąć dowolną z własnych grup. Jeśli jednak usuniesz grupę przez pomyłkę, musisz ją utworzyć i dodać członków ponownie.

1. Zaznacz grupę, którą chcesz trwale usunąć, a następnie wybierz pozycję **Usuń grupę** * &lt;&gt; * na stronie group_name.

    ![<Group_name> stronie z wyróżnionym łączem Usuń grupę](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Wybierz **pozycję Tak** w wiadomości z potwierdzeniem.

    Grupa zostanie trwale usunięta.

## <a name="join-an-existing-group"></a>Dołączanie do istniejącej grupy

Możesz dołączyć lub opuścić już istniejącą grupę na stronie **Grupy.**

1. Na stronie **Grupy** wybierz pozycję **Dołącz do grupy** z obszaru Grupy, w którym się **znajdujem.**

    Zostanie wyświetlona strona **Dołącz grupy.**

    ![Strona Dołączanie do grup z wyróżnionym przyciskiem Dołącz do grupy](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na stronie **Dołączanie do grup** wybierz nazwę grupy, do której chcesz dołączyć, wyświetl szczegóły skojarzonej grupy, a następnie, jeśli grupa jest dostępna, wybierz pozycję **Dołącz do grupy**.

    Jeśli grupa wymaga od właściciela grupy zatwierdzenia członkostwa, zostaniesz poproszony o wprowadzenie uzasadnienia biznesowego, dlaczego musisz dołączyć do grupy, a następnie wybierz pozycję **Poproś**. Jeśli grupa nie wymaga zatwierdzenia, zostaniesz natychmiast dodany jako członek, a grupa pojawi się na liście **Grupy, w** których jestem na liście.

3. Jeśli grupa została przyłączona przez pomyłkę lub nie musisz już być jej częścią, możesz wybrać jej nazwę na stronie Dołączanie do **grup,** a następnie wybrać pozycję **Opuść grupę**.

    ![Strona Dołączanie do grup z wyróżnionym przyciskiem Opłać grupę](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Następne kroki

- [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](my-apps-portal-end-user-access.md).

- [Zmień informacje profilowe](my-apps-portal-end-user-update-profile.md).

- [Wykonywanie własnych recenzji dostępu](my-apps-portal-end-user-access-reviews.md).
