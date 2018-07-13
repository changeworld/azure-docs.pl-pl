---
title: Konfigurowanie usługi Azure AD Privileged Identity Management | Microsoft Docs
description: Temat, który wyjaśnia, czym jest usługa Azure AD Privileged Identity Management i jak używać usługi PIM w celu zwiększenia poziomu bezpieczeństwa w chmurze.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: protection
ms.topic: overview
ms.date: 03/07/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9e5039623b1f9b53c636ebaeb1e956d852185577
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952056"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Czym jest usługa Azure AD Privileged Identity Management?

Aplikacja Azure Active Directory (AD) Privileged Identity Management umożliwia kontrolę i monitorowanie dostępu, a także zarządzanie nim w ramach danej organizacji. Dotyczy to również dostępu do zasobów w usłudze Azure AD, zasobów platformy Azure (wersja zapoznawcza) i innych usługach Microsoft Online Services, takich jak Office 365 lub Microsoft Intune.

> [!NOTE]
> W przypadku włączenia usługi Privileged Identity Management dla dzierżawy licencja płatna lub licencja wersji próbnej Azure AD Premium P2 lub Enterprise Mobility + Security E5 jest wymagana dla każdego użytkownika, który wchodzi w interakcję z usługą lub otrzymuje korzyść z tej usługi. Przykłady obejmują użytkowników/użytkowników w grupie, którzy:
>
>- Są przypisani do roli Administrator ról uprzywilejowanych 
>- Są przypisani jako uprawnieni do innych ról katalogu, którymi można zarządzać za pośrednictwem usługi PIM 
>- Mogą zatwierdzać/odrzucać żądania w usłudze PIM 
>- Są przypisani do roli zasobów platformy Azure za pomocą przypisań dostępu dokładnie na czas (just in time) lub bezpośrednich (na podstawie czasu)  
>- Są przypisani do przeglądu dostępu
>
>Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](fundamentals/active-directory-whatis.md).

Organizacje chcą ograniczyć liczbę osób, które mają dostęp do zabezpieczonych informacji lub zasobów, ponieważ zmniejsza to prawdopodobieństwo uzyskania dostępu do nich przez złośliwego użytkownika lub tego, że autoryzowany użytkownik niechcący zmodyfikuje poufne zasoby.  Jednak użytkownicy nadal muszą wykonywać operacje uprzywilejowane w aplikacjach usługi Azure AD, platformy Azure, usługi Office 365 i SaaS. Organizacje mogą przyznać użytkownikom dostęp uprzywilejowany do zasobów platformy Azure, takich jak subskrypcje i usługa Azure AD. Istnieje potrzeba nadzorowania sposobu, w jaki Ci użytkownicy korzystają z uprawnień administratora. Usługa Azure AD Privileged Identity Management pomaga zmniejszyć ryzyko związane z nadmiernymi, niepotrzebnymi lub niewłaściwie używanymi prawami dostępu.

Usługa Azure AD Privileged Identity Management ułatwia organizacji:

- Sprawdzanie, którzy użytkownicy mają przypisane role uprzywilejowane do zarządzania zasobami platformy Azure (wersja zapoznawcza), a także którzy użytkownicy mają przypisane role administracyjne w usłudze Azure AD
- Włączanie dostępu administracyjnego na żądanie („just in time”) do usług Microsoft Online Services, takich jak usługa Office 365 i usługa Intune, oraz do zasobów platformy Azure (wersja zapoznawcza) dla subskrypcji, grup zasobów i poszczególnych zasobów, na przykład maszyn wirtualnych 
-   Wyświetlanie historii aktywności administratorów, w tym zmian wprowadzanych przez administratorów w zasobach platformy Azure (wersja zapoznawcza)
- Otrzymywanie alertów o zmianach wprowadzonych w przypisaniach administratorów
- Wymaganie zatwierdzenia w celu aktywacji uprzywilejowanych ról administratora usługi Azure AD (wersja zapoznawcza) 
- Przeglądanie członkostwa w rolach administracyjnych i wymaganie od użytkowników uzasadnienia ciągłego członkostwa

W usłudze Azure AD usługa Azure AD Privileged Identity Management może zarządzać użytkownikami przypisanymi do wbudowanych ról usługi Azure AD w organizacji, takich jak Administrator globalny. Na platformie Azure usługa Azure AD Privileged Identity Management może zarządzać użytkownikami i grupami przypisanymi za pośrednictwem ról RBAC platformy Azure, w tym ról Właściciel lub Współautor.

## <a name="just-in-time-administrator-access"></a>Dostęp just in time administratora

W przeszłości można było przypisać użytkownika do roli administratora za pomocą witryny Azure Portal, innych portali usług Microsoft Online Services lub poleceń cmdlet usługi Azure AD w programie Windows PowerShell. W efekcie użytkownik ten stawał się **administratorem trwałym**, zawsze aktywnym w przypisanej roli. Usługa Azure AD Privileged Identity Management wprowadza koncepcję **administratora uprawnionego**. Administratorami uprawnionymi powinni być użytkownicy, którzy potrzebują dostępu uprzywilejowanego co rusz, ale nie przez cały dzień, każdego dnia. Rola jest nieaktywna, dopóki użytkownik nie potrzebuje dostępu, a wtedy wykonuje proces aktywacji i staje się aktywnym administratorem na wstępnie określony czas. Coraz więcej organizacji decyduje się na to podejście w celu ograniczenia lub wyeliminowania „stałego dostępu administratora” do ról uprzywilejowanych.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Włączanie usługi Privileged Identity Management dla katalogu

Możesz rozpocząć używanie usługi Azure AD Privileged Identity Management w witrynie [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Aby włączyć usługi Azure AD Privileged Identity Management dla katalogu, musisz być administratorem globalnym z kontem organizacji (na przykład @yourdomain.com), a nie z kontem firmy Microsoft (na przykład @outlook.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny katalogu.
2. Jeśli organizacja dysponuje więcej niż jednym katalogiem, wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure Portal. Wybierz katalog, w którym będziesz używać usługi Azure AD Privileged Identity Management.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

Jeśli jesteś pierwszą osobą, która będzie używać aplikacji Azure AD Privileged Identity Management w danym katalogu, i przejdziesz do ról katalogu usługi Azure AD, [kreator zabezpieczeń](active-directory-privileged-identity-management-security-wizard.md) przeprowadzi Cię przez początkowy etap przypisania. Następnie automatycznie zostaniesz pierwszym **administratorem zabezpieczeń** i **administratorem ról uprzywilejowanych** katalogu.

W przypadku ról usługi Azure AD tylko użytkownik należący do roli Administrator ról uprzywilejowanych może zarządzać przypisaniami innych administratorów w usłudze Azure AD PIM. Możesz [dać innym użytkownikom możliwość zarządzania rolami katalogu w usłudze PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Administratorzy globalni, administratorzy zabezpieczeń i czytelnicy zabezpieczeń mogą wyświetlać przypisania do ról usługi Azure AD w usłudze Azure AD PIM.
W przypadku ról RBAC platformy Azure tylko administrator subskrypcji, właściciel zasobu lub administrator dostępu użytkownika zasobu może zarządzać przypisaniami innych administratorów w usłudze Azure AD PIM.  Użytkownicy będący administratorami ról uprzywilejowanych, administratorami zabezpieczeń lub czytelnikami zabezpieczeń nie mają domyślnie uprawnień do wyświetlania przypisań do ról RBAC platformy Azure w usłudze Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Przegląd usługi Privileged Identity Management (punkt wejścia)

Usługa Azure AD Privileged Identity Management obsługuje administrowanie rolami katalogu usługi Azure AD i rolami dla zasobów platformy Azure (wersja zapoznawcza). Funkcja ról dla zasobów platformy Azure różni się od ról administracyjnych w usłudze Azure AD. Role zasobów platformy Azure zapewniają szczegółowe uprawnienia do zasobu, w którym są przypisywane, oraz wszystkich zasobów podrzędnych w hierarchii zasobów (dziedziczenie). [Dowiedz się więcej na temat kontroli dostępu opartej na rolach (RBAC), hierarchii zasobów i dziedziczenia](../role-based-access-control/role-assignments-portal.md). Usługą PIM dla ról katalogu usługi Azure AD i zasobów platformy Azure (wersja zapoznawcza) można administrować, uzyskując dostęp do odpowiedniego linku w sekcji Zarządzanie w lewym menu nawigacji punktu wejścia Przegląd usługi PIM.

Usługa PIM zapewnia wygodny dostęp do aktywowania ról, wyświetlania oczekujących aktywacji/żądań, oczekujących zatwierdzeń (dla ról katalogu usługi Azure AD) i przeglądów oczekujących na odpowiedź za pomocą sekcji Zadania w menu nawigacji po lewej stronie.

W przypadku uzyskiwania dostępu do elementów menu Zadania z punktu wejścia Przegląd wynikowy widok zawiera wyniki dla ról katalogu usługi Azure AD i ról zasobów platformy Azure (wersja zapoznawcza).

![Szybki start](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Lista Moje role zawiera listę aktywnych i kwalifikujących się przypisań ról dla ról katalogu usługi Azure AD i ról zasobów platformy Azure (wersja zapoznawcza). [Dowiedz się więcej na temat aktywowania kwalifikujących się przypisań ról](active-directory-privileged-identity-management-how-to-activate-role.md).

Aktywacja ról dla zasobów platformy Azure (wersja zapoznawcza) wprowadza nową funkcjonalność, która umożliwia kwalifikującym się członkom roli zaplanowanie aktywacji na przyszłą datę/godzinę i wybranie czasu trwania konkretnej aktywacji w ramach maksymalnego czasu dozwolonego przez administratorów.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

W przypadku gdy zaplanowana aktywacja nie jest już wymagana, użytkownicy mogą anulować swoje oczekujące żądanie, przechodząc do oczekujących żądań za pomocą menu nawigacji po lewej stronie, a następnie klikając polecenie Anuluj obok tego żądania.

![Oczekujące żądania](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Pulpit nawigacyjny administratora usługi Privileged Identity Management

Usługa Azure AD Privileged Identity Manager udostępnia pulpit nawigacyjny administratora zapewniający ważne informacje, takie jak:

* Alerty, które wskazują możliwości poprawy bezpieczeństwa
* Liczba użytkowników przypisanych do poszczególnych ról uprzywilejowanych  
* Liczba administratorów uprawnionych i trwałych
* Wykres aktywacji ról uprzywilejowanych w katalogu
*   Liczba przypisań just in time, ograniczonych czasowo i trwałych dla ról zasobów platformy Azure (wersja zapoznawcza)
*   Użytkownicy i grupy z nowymi przypisaniami roli w ciągu ostatnich 30 dni (role zasobów platformy Azure)


![Pulpit nawigacyjny usługi PIM — zrzut ekranu](./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png)

## <a name="privileged-role-management"></a>Zarządzanie rolami uprzywilejowanymi

Za pomocą usługi Azure AD Privileged Identity Management można zarządzać administratorami, dodając lub usuwając administratorów trwałych i uprawnionych dla poszczególnych ról dla ról katalogu usługi Azure AD. Za pomocą usługi PIM dla zasobów platformy Azure (wersja zapoznawcza) właściciele, administratorzy dostępu użytkowników i administratorzy globalni, którzy włączają zarządzanie subskrypcjami w ramach ich dzierżawy, mogą przypisywać użytkowników lub grupy do ról zasobów platformy Azure jako uprawnionych (dostęp just in time), z dostępem ograniczonym czasowo (nie jest wymagana aktywacja) przy użyciu daty/godziny rozpoczęcia i zakończenia lub trwałych (jeśli włączono w ustawieniach roli).

![Dodawanie/usuwanie administratorów w usłudze PIM — zrzut ekranu](./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png)

## <a name="configure-the-role-activation-settings"></a>Konfigurowanie ustawień aktywacji roli

Za pomocą [ustawień roli](active-directory-privileged-identity-management-how-to-change-default-settings.md) można skonfigurować właściwości aktywacji kwalifikujących się ról dla ról katalogu usługi Azure AD, w tym:

* Czas trwania okresu aktywacji roli
* Powiadomienie dotyczące aktywacji roli
* Informacje, które użytkownik musi podać w procesie aktywacji roli
* Numer biletu lub zdarzenia usługi
* [Wymagania przepływu pracy zatwierdzania — wersja zapoznawcza](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Ustawienia usługi PIM — aktywacja administratora — zrzut ekranu](./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png)

Zauważ, że na obrazie przyciski usługi **Multi-Factor Authentication** są wyłączone. W przypadku niektórych, wysoko uprzywilejowanych ról wymagamy usługi MFA w celu zapewnienia podwyższonej ochrony.

Ustawienia roli dla ról zasobów platformy Azure (wersja zapoznawcza) umożliwiają administratorom konfigurowanie ustawień przypisań just in time i bezpośrednich, w tym:

- Możliwość przypisywania użytkowników lub grup do ról bez daty/godziny zakończenia (przypisanie trwałe)
- Domyślny czas trwania przypisania (jeśli nie jest trwałe)
- Maksymalny czas trwania aktywacji (w przypadku aktywacji kwalifikującego się członka roli)
- Informacje, które użytkownik musi podać podczas aktywacji roli (w przypadku przypisań just in time) lub procesu przypisania (w przypadku przypisań bezpośrednich)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Aktywacja roli

Aby [aktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md), administrator uprawniony żąda aktywacji ograniczonej czasowo dla roli. Aktywacji można zażądać za pomocą opcji **Aktywuj moją rolę** w usłudze Azure AD Privileged Identity Management.

Administrator, który chce aktywować rolę, musi zainicjować usługę Azure AD Privileged Identity Management w witrynie Azure Portal.

Aktywację roli można dostosować. W ustawieniach usługi PIM można określić długość aktywacji i informacje, które administrator musi podać w celu uaktywnienia roli.

![Administrator żąda aktywacji roli w usłudze PIM — zrzut ekranu](./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png)

## <a name="review-role-activity"></a>Przegląd działań roli

Istnieją dwa sposoby, aby śledzić, jak pracownicy i administratorzy korzystają z ról uprzywilejowanych. Pierwszą opcją jest korzystanie z [historii inspekcji ról katalogu](active-directory-privileged-identity-management-how-to-use-audit-log.md). Dzienniki historii inspekcji śledzą zmiany w przypisaniach ról uprzywilejowanych, historię aktywacji ról i zmiany ustawień dla ról zasobów platformy Azure (wersja zapoznawcza). 

![Historia aktywacji w usłudze PIM — zrzut ekranu](./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png)

Drugą opcją jest skonfigurowanie regularnych [przeglądów dostępu](active-directory-privileged-identity-management-how-to-start-security-review.md). Te przeglądy dostępu mogą być wykonywane przez przypisanego recenzenta (na przykład kierownika zespołu) lub przez samych pracowników. To jest najlepszy sposób monitorowania, kto nadal potrzebuje dostępu, a kto nie.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Usługa Azure AD PIM w momencie wygaśnięcia subskrypcji

Przed rozpoczęciem korzystania z usługi Azure AD PIM dzierżawca musi mieć subskrypcję usługi Azure AD Premium P2 (lub EMS E5) w wersji próbnej lub płatnej w ramach swojej dzierżawy.  Ponadto licencje muszą być przypisane do administratorów dzierżawy.  Ściślej mówiąc, licencje muszą być przypisane do administratorów w rolach usługi Azure AD zarządzanych za pomocą usługi Azure AD PIM, administratorów w rolach RBAC platformy Azure zarządzanych za pomocą usługi Azure AD PIM oraz wszystkich użytkowników bez uprawnień administratora, którzy wykonują przeglądy dostępu.
Jeśli Twoja organizacja nie odnowi licencji usługi Azure AD Premium P2 lub wersja próbna wygaśnie, funkcje usługi Azure AD PIM nie będą już dostępne w Twojej dzierżawie, kwalifikujące się przypisania do roli zostaną usunięte i użytkownicy nie będą już mogli aktywować ról. Więcej informacji zawiera artykuł na temat [wymagań subskrypcji usługi Azure AD PIM](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
