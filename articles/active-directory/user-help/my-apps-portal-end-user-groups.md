---
title: Zaktualizuj swoje informacje grup z portalu Moje aplikacje — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić i zaktualizować swoje informacje związane z grup, tym grup jesteś właścicielem, tworzenie nowych grup, wyświetlanie grup, do których użytkownik jest już członkiem i dołączenie do dowolnej grupy nie są już częścią.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578ca5fa1de338a92280e2dc7cc252fb616e2111
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60483411"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Zaktualizować swoje informacje grup z portalu Moje aplikacje
Za pomocą swojego konta firmowego lub szkolnego oparta na sieci web **Moje aplikacje** portalu, aby wyświetlić i uruchomić wiele organizacji aplikacji w chmurze, aby zaktualizować niektóre z Twoich informacji profilu i konta, aby wyświetlić swoje **grup** informacji i wykonywać **przeglądów dostępu** dla aplikacji i grup. Jeśli nie masz dostępu do **Moje aplikacje** portal, należy skontaktować się z pomocą techniczną o uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania nimi aplikacji opartych na chmurze w [dokumentacja dotycząca zarządzania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Wyświetlanie informacji o Twoim grup
Jeśli administrator przyznał Ci uprawnienia do wyświetlania **grup** kafelka, możesz:

- **Jako członka grupy.** Wyświetl szczegóły, lub pozostaw żadnej grupy.

- **Jako właściciela grupy.** Szczegółowe informacje, Utwórz nową grupę, dodać lub usunąć członków lub Usuń grupę.

### <a name="to-view-your-groups-information"></a>Aby wyświetlić informacje o grupach

1.  Zaloguj się do swojego konta firmowego lub szkolnego.

2.  Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.com, albo użyj linku udostępnioną przez Twoją organizację. Na przykład możesz mogą być kierowane do dostosowanej strony dla całej organizacji, takich jak https://myapps.microsoft.com/contoso.com.

    **Aplikacje** zostanie wyświetlona strona, przedstawiający wszystkie aplikacje oparte na chmurze własnością Twojej organizacji i dostępna do użycia.

    ![Strona aplikacji w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Wybierz **grup** Kafelek, aby wyświetlić dane powiązane z grupą.

    ![Strony zarówno właścicielem i grupy członków grup](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Oparte na swoje uprawnienia, możesz użyć **grup** stronie:

    - **Przejrzyj grupy, których jesteś właścicielem.** Wyświetlanie informacji na temat grupy własnej organizacji z **grup własnym** obszaru. Wybieranie nazwy określonej grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczba elementów członkowskich, zasady dołączania i listy aktywnych członków.

    - **Utwórz nową grupę.** Utwórz nową grupę Tobie jako właściciela z **grup własnym** obszaru. Aby poznać konkretne kroki, zobacz [Utwórz nową grupę](#create-a-new-group) dalszej części tego artykułu.

    - **Edytuj istniejącą grupę.** Edytuj szczegóły dla każdego własne grupy. Aby poznać konkretne kroki, zobacz [edytować istniejącą grupę](#edit-an-existing-group) dalszej części tego artykułu.

    - **Dodawanie lub usuwanie elementów członkowskich.** Dodaj lub usuń elementy członkowskie grup, których jesteś właścicielem. Aby poznać konkretne kroki, zobacz [Dodawanie lub usuwanie członka](#add-or-remove-a-member) dalszej części tego artykułu.

    - **Odnawianie grupy usługi Office 365.** Jeśli Twoja organizacja pozwala na to, można odnowić grup usługi Office 365. Aby poznać konkretne kroki, zobacz [odnowić grupy usługi Office 365](#renew-an-office-365-group) dalszej części tego artykułu. 

    - **Usuwanie grupy.** Usuń wszystkie grupy, których jesteś właścicielem. Aby poznać konkretne kroki, zobacz [usunąć grupę](#delete-a-group) dalszej części tego artykułu.

    - **Przegląd grup, do których należysz.** Wyświetlanie nazw grup, do której należysz z **grup jestem w** obszaru. Wybieranie nazwy określonej grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczba elementów członkowskich, zasady dołączania i listy aktywnych członków.

    - **Dołącz do grupy.** Dołącz do istniejącej grupy, dla której nie jesteś już członkiem, z **grup jestem w** obszaru. Aby poznać konkretne kroki, zobacz [Dołącz do istniejącej grupy](#join-an-existing-group).

## <a name="create-a-new-group"></a>Tworzenie nowej grupy
1. Na **grup** wybierz opcję **Utwórz grupę** z **grup własnym** obszaru.

    **Utwórz grupę** pojawi się okno.

    ![Utwórz pole grupy](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Wprowadź wymagane informacje:

    - **Typ grupy:**
        
        - **Zabezpieczenia.** Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno.

        - **Office 365.** Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia także udzielenie dostępu do grupy osobom spoza organizacji.

    - **Nazwa grupy.** Dodaj nazwę grupy, która ma znaczenie i którą łatwo zapamiętać.

    - **Opis grupy (opcjonalnie).** Do grupy możesz dodać opcjonalny opis.

    - **Zasady grupy.** Wybierz opcję Zezwalaj wszystkim na dołączenie do grupy lub aby zezwalać tylko na właściciela grupy dodać elementy członkowskie.

3. Wybierz pozycję **Utwórz**.

    Zostanie utworzona nowa grupa, Tobie jako właściciela, ale pojawia się Twoja **jestem właścicielem grupy** listy. Ponieważ jesteś właścicielem, ta grupa pojawia się również w **grup jestem w** listy.

## <a name="edit-an-existing-group"></a>Edytuj istniejącą grupę
Po utworzeniu grupy możesz edytować jego szczegóły, w tym aktualizowanie wszystkich istniejących informacji.

### <a name="to-edit-your-details"></a>Aby edytować szczegóły
1. Wybierz grupę, którą chcesz edytować z **grup** strony, a następnie wybierz pozycję **Edytuj szczegóły** na *&lt;nazwa_grupy&gt;* strony.

    **Edytuj szczegóły** pojawi się okno i zaktualizować informacje dodane podczas początkowego tworzenia grupy.

2. Upewnij się, wszystkie zmiany, a następnie wybierz **aktualizacji**.

## <a name="add-or-remove-a-member"></a>Dodawanie lub usuwanie członka
Można dodać lub usunąć członków dla każdej własne grupy.

### <a name="to-add-or-remove-a-member"></a>Aby dodać lub usunąć element członkowski
1. Wybierz grupę, którą chcesz dodać członków do, a następnie wybierz **+** na *&lt;nazwa_grupy&gt;* strony.

    ![Dodaj członka grupy z + logowania wyróżniony](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Wyszukiwanie elementu członkowskiego, które chcesz dodać, z **dodawać członków** , a następnie wybierz **Dodaj**.

    ![Dodaj pola elementów członkowskich z nowego członka do dodania](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Zaproszenia są wysyłane do nowego członka, aby rozpocząć uzyskiwanie dostępu do aplikacji w organizacji.

3. Jeśli przez pomyłkę dodany członek lub członek opuścił organizacji, możesz usunąć element członkowski, wybierając **usuwanie elementu członkowskiego** obok nazwy elementu członkowskiego *&lt;nazwa_grupy&gt;* strony.

    ![Usuń członka z wyróżnionym linkiem usuwania](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Odnawianie grupy usługi Office 365
Jeśli Twoja organizacja pozwala na to, można odnowić grupy usługi Office 365, rozszerzając daty wygaśnięcia.

### <a name="to-renew-a-group"></a>Aby odnowić grupy
1. Wybierz grupę usługi Office 365, którą chcesz odnowić, a następnie wybierz **Odnów grupę**.

    ![Odnawianie grupy usługi Office 365, opóźniając datę wygaśnięcia](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Kliknij przycisk **OK** aby zamknąć komunikat potwierdzenia.

    Po odświeżeniu strony zobaczysz zaktualizowana **ostatniego odnowienia** i **grupie wygaśnięcia** daty.

## <a name="delete-a-group"></a>Usuwanie grupy
W dowolnym momencie można usuwać wszystkie własne grupy. Jednakże jeśli przez pomyłkę usunąć grupę musisz utworzyć je, a następnie ponownie dodać elementy członkowskie.

### <a name="to-delete-the-group"></a>Aby usunąć grupę
1. Wybierz grupę, którą chcesz trwale usunąć, a następnie wybierz **Usuń grupę** na *&lt;nazwa_grupy&gt;* strony.

    ![Strona < nazwa_grupy > z wyróżnionym linkiem grupy Delete](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Wybierz **tak** w komunikacie potwierdzenia.

    Grupy zostaną trwale usunięte. 
    
## <a name="join-an-existing-group"></a>Dołącz do istniejącej grupy
Możesz dołączyć już istniejącą grupę z **grup** strony.

### <a name="to-join-or-leave-a-group"></a>Aby przyłączyć do grupy lub opuszczenia

1. Na **grup** wybierz opcję **dołączanie do grupy** z **grup jestem w** obszaru.

    **Dołączenia do grup** zostanie wyświetlona strona.

    ![Dołącz do strony grupy z wyróżnionym przyciskiem grupy sprzężenia](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na **dołączenia do grup** wybierz nazwę grupy, aby dołączyć, wyświetlanie szczegółów skojarzonej grupy, a następnie wybierz Jeśli grupa jest dostępna, **dołączanie do grupy**.

    Jeśli grupa wymaga właściciela grupy zatwierdzić członkostwo, zostanie poproszony o wprowadzenie biznesowego wyjaśnienia dla Dlaczego należy przyłączyć do grupy, a następnie wybierz **żądania**. Jeśli grupa nie wymaga zatwierdzania, natychmiast dodawane jako członek i grupa pojawia się w Twojej **grup jestem w** listy.

3. Jeśli zostanie przyłączona do grupy przez pomyłkę lub jeśli nie potrzebujesz już jako część, możesz wybrać nazwę grupy z **dołączenia do grup** strony, a następnie wybierz pozycję **Opuść grupę**.

    ![Dołącz do strony grupy z wyróżnionym przyciskiem grupy pozostaw](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Kolejne kroki

- [Dostęp i korzystać z aplikacji w portalu Moje aplikacje](my-apps-portal-end-user-access.md).

- [Zmiana informacji o Twoim profilu](my-apps-portal-end-user-update-profile.md).

- [Wykonywać własne przeglądów dostępu](my-apps-portal-end-user-access-reviews.md).