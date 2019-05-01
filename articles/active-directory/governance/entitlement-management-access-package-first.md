---
title: Samouczek — Tworzenie pierwszego pakietu dostępu w zarządzanie uprawnieniami usługi Azure AD (wersja zapoznawcza) — usługi Azure Active Directory
description: Samouczek krok po kroku dotyczące sposobu tworzenia pierwszego pakietu dostępu w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873542"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Samouczek: Tworzenie pierwszego pakietu dostępu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zarządzanie dostępem do wszystkich zasobów pracownikom potrzebę, takich jak grupy, aplikacji i witryn, jest funkcją ważne dla organizacji. Chcesz udzielić pracownikom odpowiedni poziom dostępu do potrzebnych im wydajnej i usuń ich dostęp, jeśli nie jest już potrzebny.

W tym samouczku możesz pracować dla banku Woodgrove Bank jako IT administrator. Po prośby o ich utworzenie pakietu zasobów dla projektu sieci web, że użytkownicy wewnętrzni mogą Samoobsługowe żądania. Żądania wymagają zatwierdzenia i dostęp użytkownika wygasa po upływie 30 dni. W tym samouczku zasoby projektu sieci web są po prostu członkostwa w jednej grupie, ale może to być kolekcję grup, aplikacji lub witryn usługi SharePoint Online.

![Omówienie scenariusza](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie pakietu dostępu za pomocą grupy jako zasób
> * Wyznaczanie osoby zatwierdzającej
> * Pokazują, jak użytkownik wewnętrzny mogą żądać dostępu do pakietu
> * Zatwierdź żądanie dostępu

Jeśli nie masz usługi Azure AD Premium P2 lub Enterprise Mobility + Security E5 licencji Tworzenie bezpłatnego [pakietu Enterprise Mobility + Security E5 w wersji próbnej](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z usługi Azure AD Zarządzanie uprawnieniami (wersja zapoznawcza), musi mieć jeden z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5 license

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Konfigurowanie użytkowników i grup

Katalog zasobów ma co najmniej jeden zasób do udostępniania. W tym kroku utworzysz grupę o nazwie **grupy Engineering** w katalogu banku Woodgrove Bank, który jest zasobu docelowego Zarządzanie uprawnieniami. Możesz też skonfigurować wewnętrzny obiekt żądający.

**Rola wymagań wstępnych:** Administrator globalny lub administrator użytkowników

![Tworzenie użytkowników i grup](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub administrator użytkowników.  

1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

1. Utwórz lub skonfiguruj następujących dwóch użytkowników. Można użyć tych nazw lub innych nazw. **Admin1** może być użytkownikiem jest aktualnie zalogowany jako.

    | Name (Nazwa) | Rola katalogu | Opis |
    | --- | --- | --- |
    | **admin1** | Administrator globalny<br/>— lub —<br/>Ograniczony administrator (administrator użytkowników) | Administrator, a osoby zatwierdzającej |
    | **Requestor1** | Użytkownik | Wewnętrzny obiekt żądający |

    W tym samouczku administrator, a osoba zatwierdzająca to ta sama osoba, ale zazwyczaj wyznaczyć co najmniej jedną osobę jako osób zatwierdzających.

1. Tworzenie zabezpieczeń usługi Azure AD grupy o nazwie **grupy Engineering** z typem członkostwa **przypisane**.

    Ta grupa będzie zasobu docelowego Zarządzanie uprawnieniami. Grupa powinna być pusta elementów członkowskich, aby rozpocząć.

## <a name="step-2-create-an-access-package"></a>Krok 2: Tworzenie pakietu dostępu

*Dostępu do pakietu* to pakiet wszystkie zasoby, które użytkownik chce pracować nad projektem lub wykonywać swoje zadania. Dostęp do pakietów są zdefiniowane w kontenery wywoływane *katalogi*. W tym kroku opisano tworzenie **pakietu dostępu do projektu sieci Web** w **ogólne** katalogu.

**Rola wymagań wstępnych:** Administrator globalny lub administrator użytkowników

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-first/elm-access-package.png)

1. W witrynie Azure portal w obszarze nawigacji po lewej stronie, kliknij przycisk **usługi Azure Active Directory**.

1. W menu po lewej stronie kliknij **funkcje zarządzania tożsamościami**

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów**.  Jeśli widzisz **odmowa dostępu**, upewnij się, że licencji usługi Azure AD Premium P2 znajduje się w tym katalogu.

1. Kliknij przycisk **nowy pakiet dostępu**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na **podstawy** karty, wpisz nazwę **pakietu dostępu do projektu sieci Web** i opis **dostępu pakietu dla projektu sieci web zespołu inżynieryjnego**.

1. Pozostaw **katalogu** listy rozwijanej równa **ogólne**.

    ![Nowy pakiet dostępu — podstawowe informacje dotyczące karty](./media/entitlement-management-access-package-first/basics.png)

1. Kliknij przycisk **dalej** otworzyć **role zasobów** kartę.

    Na tej karcie Wybierz uprawnienia, aby uwzględnić w pakiecie dostępu.

1. Kliknij przycisk **grup**.

1. W okienku wybierz grupy, należy znaleźć i wybrać **grupy Engineering** wcześniej utworzoną grupę.

    Domyślnie wyświetlane są grupy wewnątrz i na zewnątrz **ogólne** katalogu. Po wybraniu grupy poza **ogólne** katalogu, jego zostanie dodany do **ogólne** katalogu.

    ![Nowy pakiet dostępu — kartę role zasobów](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kliknij przycisk **wybierz** Aby dodać grupę do listy.

1. W **roli** listy rozwijanej wybierz **elementu członkowskiego**.

    ![Nowy pakiet dostępu — kartę role zasobów](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kliknij przycisk **dalej** otworzyć **zasad** kartę.

1. Ustaw **Utwórz pierwszą zasadę** Przełącz, aby **później**.

    Utworzysz zasady w następnej sekcji.

    ![Nowy pakiet dostępu — karta Zasady](./media/entitlement-management-access-package-first/policy.png)

1. Kliknij przycisk **dalej** otworzyć **przeglądu + Utwórz** kartę.

    ![Nowy pakiet dostępu — przeglądanie + Tworzenie karty](./media/entitlement-management-access-package-first/review-create.png)

1. Przejrzyj ustawienia dostępu do pakietu, a następnie kliknij przycisk **Utwórz**.

    Użytkownik może pojawić się komunikat, że pakiet dostępu nie będą widoczne dla użytkowników, ponieważ katalog nie jest jeszcze włączona.

    ![Nowy pakiet dostępu — nie jest widoczny komunikat](./media/entitlement-management-access-package-first/not-visible.png)

1. Kliknij przycisk **OK**.

    Po kilku chwilach powinno zostać wyświetlone powiadomienie, w pakiet dostępu został pomyślnie utworzony.

## <a name="step-3-create-a-policy"></a>Krok 3: Utwórz zasady

A *zasad* definiuje reguły lub guardrails do dostępu do pakietu dostępu. W tym kroku utworzysz zasady, które zezwalają określonego użytkownika w katalogu zasobów, aby zażądać dostępu do pakietu. Należy również określić, czy muszę zatwierdzić żądania, i kto będzie osoby zatwierdzającej.

![Tworzenie zasad dostępu pakietu](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Rola wymagań wstępnych:** Administrator globalny lub administrator użytkowników

1. W **pakietu dostępu do projektu sieci Web**, w menu po lewej stronie kliknij **zasady**.

    ![Lista zasad pakietów dostępu](./media/entitlement-management-access-package-first/policies-list.png)

1. Kliknij przycisk **Dodaj zasady** można otworzyć tworzenia zasad.

1. Wpisz nazwę **zasad wewnętrznego obiektu żądającego** i opis **umożliwia użytkownikom w tym katalogu, aby poprosić o dostęp do zasobów projektu sieci web**.

1. W **użytkowników, którzy mogą zażądać dostępu** kliknij **dla użytkowników w katalogu**.

    ![Tworzenie zasad](./media/entitlement-management-access-package-first/policy-create.png)

1. Przewiń w dół do **Wybieranie użytkowników i grup** sekcji, a następnie kliknij przycisk **dodanie użytkowników i grup**.

1. Wybierz użytkowników i grup w okienku, wybierz **Requestor1** użytkownika została utworzona wcześniej, a następnie kliknij przycisk **wybierz**.

1. W **żądania** sekcji, ustaw **wymagają zatwierdzenia** do **tak**.

1. W **wybierz osoby zatwierdzające** kliknij **Dodaj zatwierdzających**.

1. W okienku wybierz osoby zatwierdzające wybierz **Admin1** utworzony wcześniej, a następnie kliknij przycisk **wybierz**.

    W tym samouczku administrator, a osoba zatwierdzająca to ta sama osoba, ale można określić inną osobę jako osoba zatwierdzająca.

1. W **wygaśnięcia** sekcji, ustaw **pakiet dostępu wygasa** do **liczbę dni**.

1. Ustaw **dostępu wygasa po upływie** do **30** dni.

1. Aby uzyskać **Włącz zasady**, kliknij przycisk **tak**.

    ![Utwórz ustawienia zasad](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Kliknij przycisk **Utwórz** utworzyć **zasad wewnętrznego obiektu żądającego**.

1. W menu po lewej stronie pakietu dostępu do projektu sieci Web, kliknij przycisk **Przegląd**.

1. Kopiuj **link do portalu Moje dostępu**.

    Użyjesz tego łącza do kolejnego kroku.

    ![Omówienie pakietu dostępu — link do portalu Moje dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Krok 4: Żądaj dostępu

W tym kroku należy wykonać kroki jako **wewnętrznego obiektu żądającego** i żądać dostępu do pakietu dostępu. Obiekty żądające przesyłanie ich żądania przy użyciu lokacji o nazwie portalu Moje dostępu. Portalu Moje dostępu umożliwia dostępu komputerom zgłaszającym żądania do przesyłania żądań dostępu do pakietów, zobacz pakietów dostępu one już mieć dostęp do i wyświetlanie ich historii żądań.

**Rola wymagań wstępnych:** Wewnętrzny obiekt żądający

1. Wyloguj się z witryny Azure portal.

1. W nowym oknie przeglądarki przejdź do moich link dostępu do portalu skopiowanym w poprzednim kroku.

1. Zaloguj się do portalu Moje dostępu jako **Requestor1**.

    Powinien zostać wyświetlony **pakietu dostępu do projektu sieci Web**.

1. W razie potrzeby w **opis** kolumny, kliknij strzałkę, aby zobaczyć szczegółowe informacje o pakiecie dostępu.

    ![Moje portalu dostępu — dostęp do pakietów](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet.

1. Kliknij przycisk **żądania dostępu** aby otworzyć okienko żądania dostępu.

1. W **uzasadnienie biznesowe** wpisz uzasadnienie **Praca nad projektem sieci web**.

1. Ustaw **żądanie dla określonego okresu** Przełącz, aby **tak**.

1. Ustaw **Data rozpoczęcia** do dzisiaj i **Data zakończenia** do jutra.

    ![Moje portalu dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Kliknij przycisk **Prześlij**.

1. W menu po lewej stronie kliknij **Historia żądań** Aby sprawdzić, czy żądanie zostało przesłane.

## <a name="step-5-approve-access-request"></a>Krok 5. Zatwierdź żądanie dostępu

W tym kroku należy Zaloguj się jako **osoba zatwierdzająca** użytkownika i zatwierdzić żądanie dostępu do wewnętrznego obiektu żądającego. Osoby zatwierdzające za pomocą tego samego portalu Moje dostępu dostępu komputerom zgłaszającym żądania do używania w celu przesłania żądania. Za pomocą portalu Moje dostępu, osoby zatwierdzające mogą przeglądać oczekujące zatwierdzenia i zatwierdzenie lub odrzucenie żądań.

**Rola wymagań wstępnych:** Osoba zatwierdzająca

1. Wyloguj się z portalu Moje dostępu.

1. Zaloguj się do [portalu Moje dostępu](https://myaccess.microsoft.com) jako **Admin1**.

1. W menu po lewej stronie kliknij **zatwierdzenia**.

1. Na **oczekujące** kartę, Znajdź **Requestor1**.

    Jeśli nie widzisz żądania z Requestor1, poczekaj kilka minut i spróbuj ponownie.

1. Kliknij przycisk **widoku** link, aby otworzyć okienko żądania dostępu.

1. Kliknij przycisk **zatwierdzić**.

1. W **Przyczyna** wpisz przyczynę **zatwierdzone dostęp dla projektu sieci web**.

    ![Moje portalu dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kliknij przycisk **przesyłania** przesłać Twoją decyzję.

    Powinien zostać wyświetlony komunikat został pomyślnie zatwierdzony.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Krok 6: Zweryfikuj, że dostęp został przypisany

Teraz, gdy zostały zatwierdzone żądania dostępu, w tym kroku należy potwierdzić, że **wewnętrznego obiektu żądającego** został przypisany dostęp pakietu i że są one teraz członkiem **grupy Engineering** grupy.

**Rola wymagań wstępnych:** Administrator globalny lub administrator użytkowników

1. Wyloguj się z portalu Moje dostępu.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako **Admin1**.

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów**.

1. Znajdź i kliknij pozycję **pakietu dostępu do projektu sieci Web**.

1. W menu po lewej stronie kliknij **żądań**.

    Powinien zostać wyświetlony Requestor1 i zasady wewnętrznego obiektu żądającego ze stanem **dostarczone**.

1. Kliknij przycisk żądania, aby wyświetlić jego szczegóły.

    ![Pakiet dostępu — szczegóły żądania](./media/entitlement-management-access-package-first/request-details.png)

1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

1. Kliknij przycisk **grup** , a następnie otwórz **grupy Engineering** grupy.

1. Kliknij przycisk **członków**.

    Powinien zostać wyświetlony **Requestor1** wymieniony jako członek.

    ![Członkowie grupy Engineering](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Krok 7: Oczyszczanie zasobów

W tym kroku możesz usunąć wprowadzone zmiany i usuwać **pakietu dostępu do projektu sieci Web** dostępu do pakietu.

**Rola wymagań wstępnych:**  Administrator globalny lub administrator użytkowników

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. Otwórz **pakietu dostępu do projektu sieci Web**.

1. Kliknij przycisk **przypisania**.

1. Aby uzyskać **Requestor1**, kliknij przycisk wielokropka (**...** ) a następnie kliknij przycisk **spowodować usunięcie dostępu**.

    Stan zmieni się z dostarczane na wygasł.

1. Kliknij przycisk **zasady**.

1. Aby uzyskać **zasad wewnętrznego obiektu żądającego**, kliknij przycisk wielokropka (**...** ) a następnie kliknij przycisk **Usuń**.

1. Kliknij przycisk **role zasobów**.

1. Aby uzyskać **grupy Engineering**, kliknij przycisk wielokropka (**...** ) a następnie kliknij przycisk **Usuń zasób roli**.

1. Otwórz listę pakietów dostępu.

1. Aby uzyskać **projekt dostępu do projektu sieci Web**, kliknij przycisk wielokropka (**...** ) a następnie kliknij przycisk **Usuń**.

1. W usłudze Azure Active Directory, należy usunąć wszystkich użytkowników, takie jak utworzono **Requestor1** i **Admin1**.

1. Usuń **grupy Engineering** grupy.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wspólne kroki w scenariuszu zarządzania uprawnienie.
> [!div class="nextstepaction"]
> [Typowe scenariusze](entitlement-management-scenarios.md)
