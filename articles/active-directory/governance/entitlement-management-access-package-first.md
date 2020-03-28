---
title: Samouczek — tworzenie pakietu dostępu — zarządzanie uprawnieniami usługi Azure AD
description: Samouczek krok po kroku, jak utworzyć pierwszy pakiet dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4feeb83acc3842874e7a2e4bbd32dacabcc00d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75422654"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Samouczek: Tworzenie pierwszego pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Zarządzanie dostępem do wszystkich zasobów potrzebnych pracownikom, takich jak grupy, aplikacje i lokacje, jest ważną funkcją dla organizacji. Chcesz przyznać pracownikom odpowiedni poziom dostępu, który musi być produktywny i usunąć ich dostęp, gdy nie jest już potrzebny.

W tym samouczku pracujesz dla Woodgrove Bank jako administrator IT. Zostałeś poproszony o utworzenie pakietu zasobów dla kampanii marketingowej, który użytkownicy wewnętrzni mogą żądać samoobsługi. Żądania nie wymagają zatwierdzenia, a dostęp użytkownika wygasa po 30 dniach. W tym samouczku zasoby kampanii marketingowej są tylko członkostwem w jednej grupie, ale mogą to być zbiory grup, aplikacji lub witryn usługi SharePoint Online.

![Omówienie scenariusza](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie pakietu dostępu z grupą jako zasobem
> * Zezwalanie użytkownikowi w katalogu na żądanie dostępu
> * Zademonstruj, jak użytkownik wewnętrzny może zażądać pakietu dostępu

Aby zapoznać się krok po kroku z procesem wdrażania zarządzania uprawnieniami usługi Azure Active Directory, w tym tworzenie pierwszego pakietu dostępu, wyświetl następujący klip wideo:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z zarządzania uprawnieniami usługi Azure AD, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Licencja Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Konfigurowanie użytkowników i grupowanie

Katalog zasobów ma jeden lub więcej zasobów do udostępnienia. W tym kroku utworzysz grupę o nazwie **Zasoby marketingowe** w katalogu Banku Woodgrove, która jest zasobem docelowym do zarządzania uprawnieniami. Można również skonfigurować wewnętrznego żądacza.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

![Tworzenie użytkowników i grup](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub administrator użytkownika.  

1. W lewej nawigacji kliknij pozycję **Usługa Azure Active Directory**.

1. Utwórz lub skonfiguruj następujących dwóch użytkowników. Można użyć tych nazw lub różnych nazw. **Admin1** może być użytkownikiem, który jest aktualnie zalogowany jako.

    | Nazwa | Rola katalogu |
    | --- | --- |
    | **Administracja1** | Administrator globalny<br/>— lub —<br/>Administrator użytkownika |
    | **Wniosek1** | Użytkownik |

1. Utwórz grupę zabezpieczeń usługi Azure AD o nazwie **Zasoby marketingowe** z typem członkostwa **Przypisany**.

    Ta grupa będzie zasobem docelowym do zarządzania uprawnieniami. Grupa powinna być pusta z członków, aby rozpocząć.

## <a name="step-2-create-an-access-package"></a>Krok 2: Tworzenie pakietu dostępu

*Pakiet dostępu* to pakiet zasobów, które zespół lub projekt potrzebuje i jest regulowany za pomocą zasad. Pakiety dostępu są definiowane w kontenerach *nazywanych katalogami*. W tym kroku utworzysz pakiet dostępu **kampanii marketingowej** w katalogu **ogólne.**

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-first/elm-access-package.png)

1. W witrynie Azure portal w lewej nawigacji kliknij pozycję **Usługa Azure Active Directory**.

1. W menu po lewej stronie kliknij pozycję **Zarządzanie tożsamościami**

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access**.  Jeśli widzisz **odmowy programu Access**, upewnij się, że licencja usługi Azure AD Premium P2 jest obecny w katalogu.

1. Kliknij **pozycję Nowy pakiet dostępu**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](./media/entitlement-management-shared/access-packages-list.png)

1. Na karcie **Podstawy** wpisz nazwę pakiet dostępu **kampanii marketingowej** i opis **Dostęp do zasobów kampanii**.

1. Pozostaw listę rozwijaną **Katalog** ustawioną na **Ogólne**.

    ![Nowy pakiet dostępu — karta Podstawy](./media/entitlement-management-access-package-first/basics.png)

1. Kliknij **przycisk Dalej,** aby otworzyć kartę **Role zasobów.**

    Na tej karcie należy wybrać zasoby i rolę zasobu do uwzględnienia w pakiecie dostępu.

1. Kliknij pozycję **Grupy i zespoły**.

1. W okienku Wybierz grupy znajdź i wybierz wcześniej utworzoną grupę **Zasoby marketingowe.**

    Domyślnie są widoczne grupy wewnątrz i na zewnątrz katalogu **ogólnego.** Po wybraniu grupy poza **katalogiem ogólnym** zostanie on dodany do katalogu **ogólnego.**

    ![Nowy pakiet dostępu — karta Role zasobów](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kliknij **przycisk Wybierz,** aby dodać grupę do listy.

1. Z listy rozwijanej **Rola** wybierz pozycję **Członek**.

    ![Nowy pakiet dostępu — karta Role zasobów](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kliknij **przycisk Dalej,** aby otworzyć kartę **Żądania.**

    Na tej karcie należy utworzyć zasady żądania. *Zasady* definiuje reguły lub barierki dostępu do pakietu dostępu. Tworzenie zasad, które umożliwiają określonego użytkownika w katalogu zasobów, aby zażądać tego pakietu dostępu.

1. W sekcji **Użytkownicy, którzy mogą żądać dostępu,** kliknij pozycję **Dla użytkowników w katalogu,** a następnie kliknij pozycję **Konkretne użytkownicy i grupy**.

    ![Nowy pakiet dostępu — karta Żądania](./media/entitlement-management-access-package-first/requests.png)

1. Kliknij **pozycję Dodaj użytkowników i grupy**.

1. W okienku Wybierz użytkowników i grupy wybierz wcześniej utworzonego użytkownika **Requestor1.**

    ![Nowy pakiet dostępu — karta Żądania — wybieranie użytkowników i grup](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Kliknij **pozycję Wybierz**.

1. Przewiń w dół do sekcji **Zatwierdzanie** i **Włączanie żądań.**

1. Zostaw **wymagaj zatwierdzenia** ustawionego na **Nie**.

1. W przypadku **opcji Włącz żądania**kliknij przycisk **Tak,** aby włączyć żądanie tego pakietu dostępu zaraz po jego utworzeniu.

    ![Nowy pakiet dostępu — karta Żądania — zatwierdzanie i włączanie żądań](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Kliknij **przycisk Dalej,** aby otworzyć kartę **Cykl życia.**

1. W sekcji **Wygasanie** ustaw **przypisania pakietów programu Access wygaśnie** na **Liczbę dni**.

1. Ustaw **przydziały wygasają po** **30** dniach.

    ![Nowy pakiet dostępu — karta Cykl życia](./media/entitlement-management-access-package-first/lifecycle.png)

1. Kliknij **przycisk Dalej,** aby otworzyć kartę **Recenzja + Utwórz.**

    ![Nowy pakiet dostępu — recenzja + karta Utwórz](./media/entitlement-management-access-package-first/review-create.png)

    Po kilku chwilach powinno zostać wyświetlone powiadomienie, że pakiet dostępu został pomyślnie utworzony.

1. W menu po lewej stronie pakietu dostępu kampanii marketingowej kliknij pozycję **Przegląd**.

1. Skopiuj **łącze Mój portal programu Access**.

    Użyjesz tego linku do następnego kroku.

    ![Omówienie pakietu dostępu — łącze z portalem Mój dostęp](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Krok 3: Żądanie dostępu

W tym kroku należy wykonać kroki jako **wewnętrzny żądacz** i zażądać dostępu do pakietu dostępu. Żądacze przesyłają swoje żądania za pomocą witryny o nazwie Portal Mój dostęp. Portal Mój dostęp umożliwia żądało przesyłanie żądań dla pakietów dostępu, wyświetlanie pakietów dostępu, do których mają już dostęp, i wyświetlanie historii żądań.

**Rola wstępna:** Wewnętrzny żądacz

1. Wyloguj się z witryny Azure portal.

1. W nowym oknie przeglądarki przejdź do łącza Mój portal programu Dostępu skopiowany w poprzednim kroku.

1. Zaloguj się do portalu Mój dostęp jako **Requestor1**.

    Powinien zostać wyświetlony pakiet dostępu do **kampanii marketingowej.**

1. W razie potrzeby w kolumnie **Opis** kliknij strzałkę, aby wyświetlić szczegóły dotyczące pakietu dostępu.

    ![Mój portal dostępu — pakiety dostępu](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet.

1. Kliknij **pozycję Poproś o dostęp,** aby otworzyć okienko Dostęp żądania.

    ![Mój portal dostępu — przycisk Poproś o dostęp](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. W polu **Uzasadnienie biznesowe** wpisz uzasadnienie, **nad jakim pracuję nad nową kampanią marketingową**.

    ![Mój portal dostępu — prośba o dostęp](./media/entitlement-management-shared/my-access-request-access.png)

1. Kliknij **przycisk Prześlij**.

1. W menu po lewej stronie kliknij pozycję **Historia żądań,** aby sprawdzić, czy twoje żądanie zostało przesłane.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Krok 4: Sprawdź, czy przypisano dostęp

W tym kroku upewnij się, że **wewnętrzny żądający** został przypisany pakiet dostępu i że są one teraz członkiem grupy **zasobów marketingowych.**

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. Wyloguj się z portalu Mój dostęp.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako **Administrator1**.

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję Zarządzanie **tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access**.

1. Znajdź i kliknij pakiet dostępu kampanii **marketingowej.**

1. W menu po lewej stronie kliknij pozycję **Żądania**.

    Powinieneś zobaczyć Requestor1 i zasady początkowe o stanie **Dostarczone**.

1. Kliknij żądanie, aby wyświetlić szczegóły żądania.

    ![Pakiet dostępu — szczegóły żądania](./media/entitlement-management-access-package-first/request-details.png)

1. W lewej nawigacji kliknij pozycję **Usługa Azure Active Directory**.

1. Kliknij **pozycję Grupy** i otwórz grupę Zasoby **marketingowe.**

1. Kliknij pozycję **Członkowie**.

    Powinieneś zobaczyć **Requestor1** wymienione jako element członkowski.

    ![Członkowie zasobów marketingowych](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Krok 5: Oczyszczanie zasobów

W tym kroku usuniesz wprowadzone zmiany i usuniesz pakiet dostępu do **kampanii marketingowej.**

**Rola wstępna:**  Administrator globalny lub administrator użytkownika

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. Otwórz pakiet dostępu do **kampanii marketingowej.**

1. Kliknij **pozycję Przydziały**.

1. W przypadku **requestora1**kliknij wielokropek (**...**), a następnie kliknij przycisk **Usuń dostęp**. W wyświetlonym komunikacie kliknij przycisk **Tak**.

    Po kilku chwilach stan zmieni się z Dostarczone na Wygasłe.

1. Kliknij **pozycję Role zasobów**.

1. W przypadku **zasobów marketingowych**kliknij wielokropek (**...**), a następnie kliknij pozycję Usuń **rolę zasobu**. W wyświetlonym komunikacie kliknij przycisk **Tak**.

1. Otwórz listę pakietów dostępu.

1. W przypadku **kampanii marketingowej**kliknij wielokropek (**...**), a następnie kliknij przycisk **Usuń**. W wyświetlonym komunikacie kliknij przycisk **Tak**.

1. W usłudze Azure Active Directory usuń wszystkich utworzonych użytkowników, takich jak **Requestor1** i **Admin1**.

1. Usuń grupę **Zasoby marketingowe.**

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej o typowych krokach scenariusza w zarządzaniu uprawnieniami.
> [!div class="nextstepaction"]
> [Typowe scenariusze](entitlement-management-scenarios.md)
