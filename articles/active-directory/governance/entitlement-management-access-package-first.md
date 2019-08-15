---
title: Samouczek — Tworzenie pierwszego pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Samouczek krok po kroku dotyczący sposobu tworzenia pierwszego pakietu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76ba284ec1a30322a24c762a1829b399f2583c6c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032929"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Samouczek: Tworzenie pierwszego pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zarządzanie dostępem do wszystkich zasobów wymaganych przez pracowników, takich jak grupy, aplikacje i lokacje, jest ważną funkcją dla organizacji. Aby udzielić pracownikom odpowiedniego poziomu dostępu, muszą one być produktywne i usuwać ich dostęp, gdy nie są już potrzebne.

W tym samouczku będziesz używać banku Woodgrove jako administrator IT. Zażądano utworzenia pakietu zasobów dla projektu sieci Web, które użytkownicy wewnętrzni mogą zażądać samoobsługi. Żądania wymagają zatwierdzenia i dostęp użytkownika wygaśnie po upływie 30 dni. W tym samouczku Zasoby projektu sieci Web są tylko członkostwem w pojedynczej grupie, ale może to być Kolekcja grup, aplikacji lub witryn usługi SharePoint Online.

![Omówienie scenariusza](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie pakietu dostępu z grupą jako zasobem
> * Wyznaczanie osoby zatwierdzającej
> * Pokazuje, w jaki sposób użytkownik wewnętrzny może zażądać pakietu dostępu
> * Zatwierdź żądanie dostępu

Aby zapoznać się z krok po kroku procesu wdrażania Azure Active Directory Zarządzanie prawami, w tym tworzenia pierwszego pakietu dostępu, zobacz następujące wideo:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z usługi Azure AD do zarządzania prawami (wersja zapoznawcza), musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Licencja na usługę Enterprise Mobility + Security (EMS) E5

Jeśli nie masz licencji na Azure AD — wersja Premium P2 lub Enterprise Mobility + Security E5, Utwórz bezpłatną [wersję próbną Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Konfigurowanie użytkowników i grup

Katalog zasobów zawiera co najmniej jeden zasób do udostępnienia. W tym kroku utworzysz grupę o nazwie **Grupa inżynieryjna** w katalogu banku Woodgrove Bank, który jest zasobem docelowym do zarządzania prawami. Należy również skonfigurować wewnętrzny Obiekt żądający.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

![Tworzenie użytkowników i grup](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny lub administrator użytkowników.  

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. Utwórz lub skonfiguruj następujących dwóch użytkowników. Możesz użyć tych nazw lub różnych nazw. **Admin1** może być zalogowany jako użytkownik.

    | Name | Rola katalogu | Opis |
    | --- | --- | --- |
    | **Admin1** | Administrator globalny<br/>—lub—<br/>Administrator z ograniczeniami (administrator użytkowników) | Administrator i osoba zatwierdzająca |
    | **Requestor1** | Użytkownik | Wewnętrzny Obiekt żądający |

    W tym samouczku administrator i osoba zatwierdzająca to ta sama osoba, ale zazwyczaj wyznaczasz co najmniej jedną osobę, która ma osoby zatwierdzające.

1. Utwórz grupę zabezpieczeń usługi Azure AD o nazwie **Grupa inżynierów** z przypisanymtypem członkostwa.

    Ta grupa będzie zasobem docelowym dla zarządzania uprawnieniami. Grupa powinna być pusta elementów członkowskich do uruchomienia.

## <a name="step-2-create-an-access-package"></a>Krok 2: Tworzenie pakietu dostępu

*Pakiet dostępu* to pakiet wszystkich zasobów, które użytkownik musi pracować nad projektem lub wykonywać zadania. Pakiety dostępu są definiowane w konteneracho nazwie wykazów. W tym kroku utworzysz **pakiet dostępu do projektu sieci Web** w katalogu **ogólnym** .

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-first/elm-access-package.png)

1. W Azure Portal w lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. W menu po lewej stronie kliknij pozycję **Zarządzanie tożsamościami** .

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.  Jeśli zostanie wyświetlony komunikat **odmowa dostępu**, upewnij się, że w tym katalogu znajduje się licencja na Azure AD — wersja Premium P2.

1. Kliknij pozycję **nowy pakiet dostępu**.

    ![Zarządzanie prawami w Azure Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na karcie **podstawowe** wpisz nazwę pakietu dostęp do **projektu sieci Web** i pakiet dostępu do opisu **dla projektu sieci Web dla inżynierów**.

1. Pozostaw listę rozwijaną **wykazu** ustawioną na **Ogólne**.

    ![Nowy pakiet dostępu — karta podstawowe](./media/entitlement-management-access-package-first/basics.png)

1. Kliknij przycisk **dalej** , aby otworzyć kartę **role zasobów** .

    Na tej karcie należy wybrać uprawnienia do uwzględnienia w pakiecie dostępu.

1. Kliknij pozycję **grupy**.

1. W okienku wybierz grupy Znajdź i wybierz grupę **grup inżynieryjnych** utworzoną wcześniej.

    Domyślnie widoczne są grupy wewnątrz i na zewnątrz wykazu **ogólnego** . Po wybraniu grupy poza katalogiem **ogólnym** zostanie ona dodana do wykazu **ogólnego** .

    ![Nowy pakiet dostępu — karta role zasobów](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kliknij pozycję **Wybierz** , aby dodać grupę do listy.

1. Z listy rozwijanej **rola** wybierz **element członkowski**.

    ![Nowy pakiet dostępu — karta role zasobów](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kliknij przycisk **dalej** , aby otworzyć kartę **zasady** .

1. Ustaw przełącznik **Utwórz pierwsze zasady** na **później**.

    Zasady zostaną utworzone w następnej sekcji.

    ![Nowy pakiet dostępu — karta zasady](./media/entitlement-management-access-package-first/policy.png)

1. Kliknij przycisk **dalej** , aby otworzyć kartę **Recenzja + tworzenie** .

    ![Nowy pakiet dostępu — karta przegląd + tworzenie](./media/entitlement-management-access-package-first/review-create.png)

1. Przejrzyj ustawienia pakietu dostępu, a następnie kliknij przycisk **Utwórz**.

    Może zostać wyświetlony komunikat informujący o tym, że pakiet dostępu nie będzie widoczny dla użytkowników, ponieważ katalog nie jest jeszcze włączony.

    ![Nowy pakiet dostępu — niewidoczny komunikat](./media/entitlement-management-access-package-first/not-visible.png)

1. Kliknij przycisk **OK**.

    Po kilku chwilach powinien pojawić się powiadomienie, że pakiet dostępu został pomyślnie utworzony.

## <a name="step-3-create-a-policy"></a>Krok 3: Tworzenie zasad

*Zasady* definiują reguły lub guardrails w celu uzyskania dostępu do pakietu dostępu. W tym kroku utworzysz zasady, które umożliwią określonemu użytkownikowi w katalogu zasobów zażądanie pakietu dostępu. Należy również określić, że żądania muszą być zatwierdzone i osoba zatwierdzająca.

![Tworzenie zasad pakietu dostępu](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W **pakiecie dostępnym w projekcie sieci Web**w menu po lewej stronie kliknij pozycję **zasady**.

    ![Lista zasad dostępu do pakietów](./media/entitlement-management-access-package-first/policies-list.png)

1. Kliknij pozycję **Dodaj zasady** , aby otworzyć przystawkę Utwórz zasady.

1. Wpisz nazwę **wewnętrznej zasady żądającej** i opis **umożliwia użytkownikom w tym katalogu żądanie dostępu do zasobów projektu sieci Web**.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników w katalogu**.

    ![Utwórz zasady](./media/entitlement-management-access-package-first/policy-create.png)

1. Przewiń w dół do sekcji **Wybieranie użytkowników i grup** , a następnie kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku wybierz użytkowników i grupy wybierz utworzonego wcześniej użytkownika **Requestor1** , a następnie kliknij przycisk **Wybierz**.

1. W sekcji **żądanie** ustaw opcję **Wymagaj zatwierdzenia** na **wartość tak**.

1. W sekcji **Wybierz osoby zatwierdzające** kliknij przycisk **Dodaj osoby zatwierdzające**.

1. W okienku wybierz osoby zatwierdzające wybierz utworzony wcześniej **admin1** , a następnie kliknij przycisk **Wybierz**.

    W tym samouczku administrator i osoba zatwierdzająca to ta sama osoba, ale można wyznaczyć inną osobę jako osobę zatwierdzającą.

1. W sekcji **wygaśnięcie** ustaw pozycję **pakiet dostępu** na **wartość Liczba dni**.

1. Wartość ustawienia **dostęp wygasa po upływie** **30** dni.

1. W obszarze **Włączanie zasad**kliknij pozycję **tak**.

    ![Tworzenie ustawień zasad](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć **zasady wewnętrznej osoby żądającej**.

1. W menu po lewej stronie pakietu dostępu do projektu sieci Web kliknij pozycję **Przegląd**.

1. Skopiuj **link portalu My Access**.

    Ten link zostanie użyty do następnego kroku.

    ![Przegląd pakietu dostępu — mój link portalu dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Krok 4: Żądaj dostępu

W tym kroku wykonasz kroki jako **wewnętrzny Obiekt żądający** i zażądasz dostępu do pakietu dostępu. Osoby żądające przesyłają żądania przy użyciu witryny o nazwie Portal dostępu. Portal My Access umożliwia żądającym przesyłanie żądań dotyczących pakietów dostępu, zapoznaj się z pakietami dostępu, do których mają dostęp, i Wyświetl ich historię żądań.

**Rola wymagana wstępnie:** Wewnętrzny Obiekt żądający

1. Wyloguj się z Azure Portal.

1. W nowym oknie przeglądarki przejdź do linku do portalu My Access, który został skopiowany w poprzednim kroku.

1. Zaloguj się do portalu My Access jako **Requestor1**.

    Powinien zostać wyświetlony **pakiet dostępu do projektu sieci Web**.

1. W razie potrzeby w kolumnie **Opis** kliknij strzałkę, aby wyświetlić szczegółowe informacje o pakiecie dostępu.

    ![Portal dostępu — dostęp — pakiety](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet.

1. Kliknij pozycję Zażądaj **dostępu** , aby otworzyć okienko żądania dostępu.

1. W polu **uzasadnienie biznesowe** wpisz uzasadnienie **pracy nad projektem sieci Web**.

1. Dla opcji przełączenia **dla określonego okresu** Ustaw **wartość tak**.

1. Ustaw **datę początkową** na dzisiaj i **datę końcową** na jutro.

    ![Portal dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Kliknij przycisk **Submit** (Prześlij).

1. W menu po lewej stronie kliknij pozycję **historia żądań** , aby sprawdzić, czy Twoje żądanie zostało przesłane.

## <a name="step-5-approve-access-request"></a>Krok 5. Zatwierdź żądanie dostępu

W tym kroku należy zalogować się jako użytkownik **osoby zatwierdzającej** i zatwierdzić żądanie dostępu dla wewnętrznego obiektu żądającego. Osoby zatwierdzające używają tego samego portalu dostępu, który jest używany do przesyłania żądań. Za pomocą portalu My Access osoby zatwierdzające mogą wyświetlać oczekujące zatwierdzenia i zatwierdzać lub odrzucać żądania.

**Rola wymagana wstępnie:** Osoba zatwierdzająca

1. Wyloguj się z portalu My Access.

1. Zaloguj się do [portalu My Access](https://myaccess.microsoft.com) jako **admin1**.

1. W menu po lewej stronie kliknij pozycję **zatwierdzenia**.

1. Na karcie **oczekiwanie** Znajdź **Requestor1**.

    Jeśli nie widzisz żądania z Requestor1, odczekaj kilka minut i spróbuj ponownie.

1. Kliknij link **Wyświetl** , aby otworzyć okienko żądanie dostępu.

1. Kliknijprzycisk Zatwierdź.

1. W polu **Przyczyna** wpisz przyczynę zatwierdzonego **dostępu do projektu sieci Web**.

    ![Portal dostępu moje dostęp — żądanie dostępu](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kliknij pozycję **Prześlij** , aby przesłać swoją decyzję.

    Powinien zostać wyświetlony komunikat informujący o tym, że został pomyślnie zatwierdzony.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Krok 6: Sprawdzanie, czy dostęp został przypisany

Po zaakceptowaniu żądania dostępu w tym kroku potwierdzasz, że **wewnętrzny Obiekt żądający** został przypisany do pakietu, a teraz jest członkiem grupy **grup inżynieryjnych** .

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. Wyloguj się z portalu My Access.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako **admin1**.

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.

1. Znajdź i kliknij pozycję **pakiet dostępu do projektu sieci Web**.

1. W menu po lewej stronie kliknij pozycję **żądania**.

    Powinna zostać wyświetlona Requestor1 i wewnętrzna zasada żądająca ze stanem **dostarczone**.

1. Kliknij żądanie, aby wyświetlić szczegóły żądania.

    ![Dostęp do pakietu — szczegóły żądania](./media/entitlement-management-access-package-first/request-details.png)

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.

1. Kliknij pozycję **grupy** , a następnie otwórz grupę **grup inżynieryjnych** .

1. Kliknij pozycję **Członkowie**.

    Powinna zostać wyświetlona lista **Requestor1** jako element członkowski.

    ![Członkowie grupy inżynieryjnej](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Krok 7: Oczyszczanie zasobów

W tym kroku usuniesz wprowadzone zmiany i usuniesz pakiet dostępu do **pakietu dostępu do projektu sieci Web** .

**Rola wymagana wstępnie:**  Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. Otwórz **pakiet dostępu do projektu sieci Web**.

1. Kliknij pozycję **przypisania**.

1. W przypadku **Requestor1**kliknij przycisk wielokropka ( **...** ), a następnie kliknij przycisk **Usuń dostęp**.

    Stan zmieni się z dostarczone na wygasłe.

1. Kliknij pozycję **zasady**.

1. W przypadku **wewnętrznych zasad**programu żądającego kliknij przycisk wielokropka ( **...** ), a następnie kliknij przycisk **Usuń**.

1. Kliknij pozycję **role zasobów**.

1. W obszarze **Grupa inżynierów**kliknij przycisk wielokropka ( **...** ), a następnie kliknij pozycję **Usuń rolę zasobu**.

1. Otwórz listę pakietów dostępu.

1. W przypadku **projektu dostępu do projektu sieci Web**kliknij wielokropek ( **...** ), a następnie kliknij przycisk **Usuń**.

1. W Azure Active Directory Usuń wszystkich utworzonych użytkowników, takich jak **Requestor1** i **admin1**.

1. Usuń grupę **grup inżynieryjnych** .

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej o typowych krokach związanych z scenariuszem zarządzania prawami.
> [!div class="nextstepaction"]
> [Typowe scenariusze](entitlement-management-scenarios.md)
