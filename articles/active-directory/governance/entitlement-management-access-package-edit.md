---
title: Edytuj istniejący pakiet dostępu i Zarządzaj nim w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak edytować istniejący pakiet dostępu i zarządzać nim w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/26/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a79cf166025ced6cb08d2f9e24801ea498fdc1c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326378"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Edytuj istniejący pakiet dostępu i Zarządzaj nim w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pakiet dostępu umożliwia jednorazowe skonfigurowanie zasobów i zasad, które automatycznie zarządzają dostępem do czasu życia pakietu dostępu. Jako Menedżer pakietów programu Access można w dowolnym momencie zmienić zasoby w pakiecie dostępu bez konieczności aprowizacji dostępu użytkownika do nowych zasobów lub usuwania ich dostępu z poprzednich zasobów. Zasady można także aktualizować w dowolnym momencie, jednak zmiany zasad mają wpływ tylko na nowe dostępy.

W tym artykule opisano sposób edytowania istniejących pakietów dostępu i zarządzania nimi.

## <a name="add-resource-roles"></a>Dodaj role zasobów

Rola zasobu to kolekcja uprawnień skojarzonych z zasobem. Aby udostępnić zasoby użytkownikom do żądania, należy dodać role zasobów do pakietu dostępu. Można dodawać role zasobów dla grup, zespołów, aplikacji i witryn programu SharePoint.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **role zasobów**.

1. Kliknij pozycję **Dodaj role zasobów** , aby otworzyć stronę Dodawanie ról zasobów do pakietu dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. W zależności od tego, czy chcesz dodać witrynę grupy, zespołu, aplikacji lub programu SharePoint, wykonaj kroki z jednej z poniższych sekcji ról zasobów.

### <a name="add-a-group-or-team-resource-role"></a>Dodawanie grupy lub roli zasobu zespołu

Zarządzanie prawami może automatycznie dodawać użytkowników do grupy lub zespołu firmy Microsoft, gdy przypiszesz do nich pakiet dostępu. 

- Gdy grupa lub zespół jest częścią pakietu dostępu, a użytkownik jest przypisany do tego pakietu, użytkownik zostanie dodany do tej grupy lub zespołu, jeśli jeszcze nie istnieje.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika są one usuwane z grupy lub zespołu, chyba że są obecnie przypisane do innego pakietu dostępu, który zawiera tę samą grupę lub zespół.

Możesz wybrać dowolną [grupę zabezpieczeń usługi Azure AD lub grupę Office 365](../fundamentals/active-directory-groups-create-azure-portal.md).  Administratorzy mogą dodać dowolną grupę do wykazu; Właściciele wykazu mogą dodać dowolną grupę do wykazu, jeśli są właścicielami grupy. Podczas wybierania grupy należy pamiętać o następujących ograniczeniach usługi Azure AD:

- Gdy użytkownik, łącznie z gościem, jest dodawany jako członek do grupy lub zespołu, może zobaczyć wszystkich innych członków tej grupy lub zespołu.
- Usługa Azure AD nie może zmienić członkostwa w grupie, która została zsynchronizowana z systemem Windows Server Active Directory przy użyciu Azure AD Connect lub która została utworzona w usłudze Exchange Online jako grupa dystrybucyjna.  
- Członkostwa w grupach dynamicznych nie można zaktualizować przez dodanie lub usunięcie elementu członkowskiego, więc członkostwa w grupie dynamicznej nie są odpowiednie do użytku z zarządzaniem prawami.

Aby uzyskać więcej informacji, zobacz [porównywanie grup](/office365/admin/create-groups/compare-groups) i [grup pakietu Office 365 oraz Microsoft Teams](/microsoftteams/office-365-groups).

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **grupy i zespoły** , aby otworzyć okienko Wybieranie grup.

1. Wybierz grupy i zespoły, które chcesz uwzględnić w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie grup](./media/entitlement-management-access-package-edit/group-select.png)

1. Kliknij przycisk **wybierz**.

    Po wybraniu grupy lub zespołu w kolumnie **podtyp** zostanie wystawiona jedna z następujących podtypów:

    |  |  |
    | --- | --- |
    | Bezpieczeństwo | Służy do udzielania dostępu do zasobów. |
    | Dystrybucja | Służy do wysyłania powiadomień do grupy osób. |
    | O365 | Grupa pakietu Office 365, która nie jest włączona dla zespołów. Używany do współpracy między użytkownikami i spoza niej. |
    | Zespół | Grupa pakietu Office 365, dla której włączono zespoły. Używany do współpracy między użytkownikami i spoza niej. |

1. Na liście **rola** wybierz pozycję **właściciel** lub **członek**.

    Zwykle wybierasz rolę elementu członkowskiego. W przypadku wybrania roli właściciela, która umożliwi użytkownikom dodawanie lub usuwanie innych członków lub właścicieli.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla grupy lub zespołu](./media/entitlement-management-access-package-edit/group-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu automatycznie staną się członkami tej grupy lub zespołu po dodaniu.

### <a name="add-an-application-resource-role"></a>Dodawanie roli zasobu aplikacji

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do aplikacji usługi Azure AD Enterprise, w tym zarówno aplikacji SaaS, jak i aplikacji organizacji federacyjnych do usługi Azure AD, gdy użytkownik ma przypisany pakiet dostępu. W przypadku aplikacji, które integrują się z usługą Azure AD za pomocą federacyjnego logowania jednokrotnego, usługa Azure AD wystawia tokeny federacyjne dla użytkowników przypisanych do aplikacji.

Aplikacje mogą mieć wiele ról. W przypadku dodawania aplikacji do pakietu dostępu, jeśli aplikacja ma więcej niż jedną rolę, należy określić odpowiednią rolę dla tych użytkowników.  Jeśli tworzysz aplikacje, możesz dowiedzieć się więcej o tym, jak te role są udostępniane aplikacjom w artykule na temat [konfigurowania roszczeń ról wystawionych w tokenie SAML](../develop/active-directory-enterprise-app-role-management.md).

Gdy rola aplikacji jest częścią pakietu dostępu:

- Gdy użytkownik ma przypisany pakiet dostępu, użytkownik zostanie dodany do tej roli aplikacji, jeśli jeszcze nie istnieje.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika jego dostęp zostanie usunięty z aplikacji, chyba że ma przypisanie do innego pakietu dostępu zawierającego tę rolę aplikacji.

Oto kilka kwestii, które należy wziąć pod uwagę podczas wybierania aplikacji:

- Aplikacje mogą także mieć również grupy przypisane do ich ról.  Możesz wybrać opcję dodania grupy zamiast roli aplikacji w pakiecie dostępu, ale aplikacja nie będzie widoczna dla użytkownika jako część pakietu dostępu w portalu My Access.

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **aplikacje** , aby otworzyć okienko wybierz aplikacje.

1. Wybierz aplikacje, które mają zostać uwzględnione w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie aplikacji](./media/entitlement-management-access-package-edit/application-select.png)

1. Kliknij przycisk **wybierz**.

1. Z listy **rola** wybierz rolę aplikacji.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla aplikacji](./media/entitlement-management-access-package-edit/application-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu będą automatycznie mieli dostęp do tej aplikacji po dodaniu.

### <a name="add-a-sharepoint-site-resource-role"></a>Dodaj rolę zasobów witryny programu SharePoint

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do witryny usługi SharePoint Online lub kolekcji witryn usługi SharePoint Online po przypisaniu do nich pakietu dostępu.

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **witryny programu SharePoint** , aby otworzyć okienko Wybieranie witryn usługi SharePoint Online.

1. Wybierz witryny usługi SharePoint Online, które mają zostać uwzględnione w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie witryn usługi SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Kliknij przycisk **wybierz**.

1. Na liście **rola** wybierz rolę witryny usługi SharePoint Online.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla witryny usługi SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu będą automatycznie mieli dostęp do tej witryny usługi SharePoint Online, gdy zostanie ona dodana.

## <a name="remove-resource-roles"></a>Usuń role zasobów

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **role zasobów**.

1. Na liście ról zasobów Znajdź rolę zasobu, którą chcesz usunąć.

1. Kliknij przycisk wielokropka ( **...** ), a następnie kliknij pozycję **Usuń rolę zasobu**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu automatycznie otrzymają dostęp do tej roli zasobu po jej usunięciu.

## <a name="add-a-new-policy"></a>Dodaj nowe zasady

Aby określić, kto może zażądać pakietu dostępu, należy utworzyć zasady. Można utworzyć wiele zasad dla jednego pakietu dostępu, jeśli chcesz zezwolić różnym zestawom użytkowników na przyznawanie przypisań przy użyciu różnych ustawień zatwierdzenia i wygaśnięcia. Pojedynczej zasady nie można używać do przypisywania użytkowników wewnętrznych i zewnętrznych do tego samego pakietu dostępu. Można jednak utworzyć dwie zasady w tym samym pakiecie dostępu — jeden dla użytkowników wewnętrznych i jeden dla użytkowników zewnętrznych. Jeśli istnieje wiele zasad, które mają zastosowanie do użytkownika, zostanie wyświetlony monit o określenie zasad, do których mają być przypisane.

Na poniższym diagramie przedstawiono proces wysokiego poziomu służący do tworzenia zasad dla istniejącego pakietu dostępu.

![Tworzenie procesu zasad](./media/entitlement-management-access-package-edit/policy-process.png)

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie **Dodaj zasady**.

1. Wpisz nazwę i opis zasad.

    ![Tworzenie zasad o nazwie i opisie](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. W zależności od wyboru **użytkowników, którzy mogą zażądać dostępu**, wykonaj czynności opisane w jednej z poniższych sekcji zasad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Edytowanie istniejących zasad

Zasady można edytować w dowolnym momencie. Jeśli zmienisz datę wygaśnięcia zasad, Data wygaśnięcia żądań, które już oczekują na zatwierdzenie lub zatwierdzonego stanu, nie ulegnie zmianie.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie kliknij zasady, które chcesz edytować.

    Zostanie otwarte okienko **Szczegóły zasad** w dolnej części strony.

    ![Dostęp do programu Access — okienko Szczegóły zasad](./media/entitlement-management-access-package-edit/policy-details.png)

1. Kliknij przycisk **Edytuj** , aby edytować zasady.

    ![Dostęp do pakietu — edycja zasad](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Po zakończeniu kliknij przycisk **Aktualizuj**.

## <a name="directly-assign-a-user"></a>Bezpośrednie przypisanie użytkownika

W niektórych przypadkach może być konieczne bezpośrednie przypisanie określonych użytkowników do pakietu dostępu, dzięki czemu użytkownicy nie muszą przechodzić przez proces żądania pakietu dostępu. Aby bezpośrednio przypisywać użytkowników, pakiet dostępu musi mieć zasady umożliwiające bezpośrednie przypisania administratora.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania**.

1. Kliknij pozycję **nowe przypisanie** , aby otworzyć aplet Dodaj użytkownika do pakietu.

    ![Przypisania — Dodawanie użytkownika do pakietu dostępu](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Kliknij przycisk **Dodaj użytkowników** , aby wybrać użytkowników, do których chcesz przypisać pakiet dostępu.

1. Z listy **Wybierz zasady** wybierz zasadę z ustawieniem [Brak (tylko przypisania bezpośrednie administratora)](#policy-none-administrator-direct-assignments-only) .

    Jeśli ten pakiet dostępu nie ma tego typu zasad, możesz kliknąć przycisk **Utwórz nowe zasady** , aby dodać jeden.

1. Ustaw datę i godzinę, o której chcesz rozpocząć i zakończyć przypisanie wybranych użytkowników. Jeśli data zakończenia nie zostanie podana, zostaną użyte ustawienia wygasania zasad.

1. Opcjonalnie możesz podać uzasadnienie dla bezpośredniego przypisania do przechowywania rekordów.

1. Kliknij przycisk **Dodaj** , aby bezpośrednio przypisać wybranych użytkowników do pakietu dostępu.

    Po kilku chwilach kliknij pozycję **Odśwież** , aby wyświetlić użytkowników na liście przydziałów.

## <a name="view-who-has-an-assignment"></a>Wyświetl, kto ma przypisanie

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **przypisania** , aby wyświetlić listę aktywnych przypisań.

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Aby wyświetlić listę przypisań, dla których nie zainicjowano prawidłowo wszystkich ról zasobów, kliknij stan filtru i wybierz pozycję **dostarczanie**.

    Aby wyświetlić dodatkowe szczegóły dotyczące błędów dostarczania, można zlokalizować odpowiednie żądanie użytkownika na stronie **żądania** .

1. Aby wyświetlić wygasłe przypisania, kliknij stan filtru i wybierz pozycję **wygasłe**.

1. Aby pobrać plik CSV z filtrowanej listy, kliknij przycisk **Pobierz**.

## <a name="view-requests"></a>Wyświetl żądania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij konkretne żądanie, aby wyświetlić dodatkowe szczegóły.

## <a name="view-a-requests-delivery-errors"></a>Wyświetl błędy dostarczania żądania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Wybierz żądanie, które chcesz wyświetlić.

    Jeśli żądanie zawiera błędy dostarczania, stan żądania zostanie **wystawiony** , a podstan zostanie **częściowo dostarczony**.

    Jeśli występują błędy dostarczania, w okienku szczegółów żądania zostanie wyliczona liczba błędów dostarczania.

1. Kliknij liczbę, aby wyświetlić wszystkie błędy dostarczania żądania.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Można anulować tylko oczekujące żądanie, które nie zostało jeszcze dostarczone.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij żądanie, które chcesz anulować

1. W okienku Szczegóły żądania kliknij przycisk **Anuluj żądanie**.

## <a name="copy-my-access-portal-link"></a>Link Kopiuj mój Portal dostępu

Większość użytkowników w katalogu może zalogować się do portalu My Access i automatycznie wyświetlić listę pakietów dostępu, których mogą żądać. Jednak w przypadku użytkowników zewnętrznego partnera biznesowego, którzy nie znajdują się jeszcze w Twoim katalogu, należy wysłać im link umożliwiający zażądanie pakietu dostępu. 

Ważne jest, aby skopiować całe łącze portalu dostępu, gdy wyśle je do wewnętrznego partnera biznesowego. Gwarantuje to, że partner uzyska dostęp do portalu Twojego katalogu w celu zgłoszenia żądania. 

Link rozpocznie się z "dostępem do", zawiera wskazówkę katalogu i kończy się identyfikatorem pakietu dostępu. Upewnij się, że link zawiera wszystkie następujące elementy:

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

O ile pakiet dostępu jest włączony dla użytkowników zewnętrznych i masz zasady dla katalogu użytkownika zewnętrznego, użytkownik zewnętrzny może użyć linku Portal dostępu do aplikacji, aby zażądać pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd Skopiuj **łącze Mój dostęp do portalu**.

    ![Przegląd pakietu dostępu — mój link portalu dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

1. Wiadomość e-mail lub Wyślij link do zewnętrznego partnera biznesowego. Mogą oni udostępnić link swoim użytkownikom, aby zażądać pakietu dostępu.

## <a name="change-the-hidden-setting"></a>Zmień ustawienie ukrywane

Pakiety dostępu są domyślnie odnajdywane. Oznacza to, że jeśli zasady zezwalają użytkownikowi na żądanie pakietu dostępu, automatycznie zobaczą pakiet dostępu wymieniony w portalu mojego dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd kliknij przycisk **Edytuj**.

1. Ustaw ustawienie **ukryte** .

    Jeśli ustawisz wartość **nie**, pakiet dostępu zostanie wyświetlony w portalu dostępu użytkownika.

    W przypadku wybrania **opcji tak**pakiet dostępu nie będzie wyświetlany w portalu dostępu użytkownika. Jedynym sposobem wyświetlania pakietu dostępu przez użytkownika jest to, że użytkownik ma **link bezpośredniego dostępu Portal** do pakietu dostępu.

## <a name="delete"></a>Usuwanie

Pakiet dostępu można usunąć tylko wtedy, gdy nie ma aktywnych przypisań użytkownika.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania** i Usuń dostęp dla wszystkich użytkowników.

1. W menu po lewej stronie kliknij pozycję **Przegląd** , a następnie kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie Usuń kliknij przycisk **tak**.

## <a name="when-are-changes-applied"></a>Kiedy zmiany są stosowane

W obszarze Zarządzanie prawami usługa Azure AD będzie przetwarzać zmiany zbiorcze dotyczące przypisywania i zasobów w pakietach dostępu kilka razy dziennie. Dlatego w przypadku przypisania lub zmiany ról zasobów pakietu dostępu może upłynąć nawet 24 godziny, gdy zmiana zostanie wprowadzona w usłudze Azure AD, a także czas, w którym konieczne jest propagowanie tych zmian do innych usług online firmy Microsoft lub połączonej aplikacji SaaS wolumin. Jeśli zmiana ma wpływ tylko na kilka obiektów, zmiana będzie prawdopodobnie trwać tylko kilka minut w usłudze Azure AD, po której inne składniki usługi Azure AD będą wykrywać zmiany i aktualizować aplikacje SaaS. Jeśli zmiana wpłynie na tysiące obiektów, zmiana będzie trwać dłużej. Jeśli na przykład masz pakiet dostępu z 2 aplikacjami i przypisaniami użytkowników 100 i zdecydujesz się dodać rolę witryny programu SharePoint do pakietu dostępu, może się zdarzyć, że wszyscy użytkownicy będą częścią tej roli witryny programu SharePoint. Postęp można monitorować w dzienniku inspekcji usługi Azure AD, dzienniku aprowizacji usługi Azure AD oraz w dziennikach inspekcji witryny programu SharePoint.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie właściciela katalogu lub Menedżera pakietów programu Access](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
