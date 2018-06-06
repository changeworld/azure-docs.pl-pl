---
title: Konfigurowanie usługi Azure AD Privileged Identity Management | Dokumentacja firmy Microsoft
description: Temat, który objaśnia, co to jest Azure AD Privileged Identity Management i sposobu użycia usługi PIM, aby zwiększyć bezpieczeństwo chmury.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/07/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b8ae9c91882c91263b6bcbcb640bc6cf27756548
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698530"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co to jest Azure AD Privileged Identity Management?

Aplikacja Azure Active Directory (AD) Privileged Identity Management umożliwia kontrolę i monitorowanie dostępu, a także zarządzanie nim w ramach danej organizacji. Obejmuje to dostęp do zasobów w usłudze Azure AD, zasobów Azure (wersja zapoznawcza) i innych usług Online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.

> [!NOTE]
> Po włączeniu Privileged Identity Management dla swojej dzierżawy, prawidłowy Azure AD Premium P2 lub Enterprise Mobility + Security E5 płatnej lub dla każdego użytkownika, który współdziała z lub odbiera korzyści z usługi wymagana jest licencja próbna. Przykłady obejmują użytkowników/użytkowników w > grupy, które są:
>
>- Przypisane do roli Administrator ról uprzywilejowanych 
>- Przypisany jako kwalifikujący się do innych ról katalogu można zarządzać za pomocą usługi PIM 
>- Możliwość zatwierdzanie odrzucanie żądań w PIM 
>- Przypisane do roli zasobów platformy Azure z tylko w czasie lub bezpośredniego przypisania (na podstawie czasu)  
>- Przypisane do dostępu do przeglądu
>
>Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](active-directory-whatis.md).

Organizacje chcesz ograniczyć liczbę użytkowników, którzy mają dostęp do bezpiecznego informacji lub zasobów, ponieważ, która ogranicza ryzyko złośliwemu użytkownikowi uzyskiwanie dostępu lub autoryzowanym użytkownikiem przypadkowo wpływające na poufnych zasobów.  Jednak użytkownicy nadal muszą wykonywać operacje uprzywilejowane w usłudze Azure AD, Azure, Office 365 lub SaaS aplikacji. Organizacje zapewnić użytkownikom uprzywilejowanego dostępu do zasobów platformy Azure, takich jak subskrypcje i Azure AD. Istnieje potrzeba nadzoru dla tych użytkowników, robią z ich uprawnieniami administratora. Azure AD Privileged Identity Management pomaga ograniczyć ryzyko nadmiernego, zbędne lub niewłaściwego użycia praw dostępu.

Azure AD Privileged Identity Management pomaga organizacji:

- Zobacz użytkowników, którzy są przypisane role uprzywilejowane do zarządzania zasobami Azure (wersja zapoznawcza), a także użytkowników, którzy są przypisane role administracyjne w usłudze Azure AD
- Włącz na żądanie, "just in time" dostęp administracyjny do usługi Online firmy Microsoft, takich jak usługi Office 365 i Intune oraz do zasobów platformy Azure (wersja zapoznawcza) w subskrypcji, grupy zasobów i poszczególnych zasobów, takich jak maszyny wirtualne 
-   Zobacz historię aktywacji administratora, w tym administratorzy zmiany wprowadzone do zasobów platformy Azure (wersja zapoznawcza)
- Alerty o zmianach wprowadzonych w przypisaniach administratora
- Wymagaj zatwierdzenia aktywacji usługi Azure AD uprzywilejowany ról administratora (wersja zapoznawcza) 
- Przejrzyj członkostwo ról administracyjnych, a użytkownicy musieli podać uzasadnienie dla dalszego członkostwa

W usłudze Azure AD, Azure AD Privileged Identity Management można zarządzać użytkowników przypisanych do wbudowanych ról usługi Azure AD w organizacji, takich jak Administrator globalny. Na platformie Azure Azure AD Privileged Identity Management można zarządzać użytkownicy i grupy przypisane przez role Azure RBAC, w tym właściciela lub współautora.

## <a name="just-in-time-administrator-access"></a>Tylko w czasie dostępu administratora

W przeszłości można przypisać użytkownika do roli administratora za pośrednictwem portalu Azure, portali innych usług Microsoft Online Services lub poleceń cmdlet środowiska Windows PowerShell dla usługi Azure AD. W związku z tym staje się **administrator trwały**, zawsze aktywny w przypisanej roli. Azure AD Privileged Identity Management pojęcia związane z **kwalifikujących się administrator**. Kwalifikujące się Administratorzy powinni mieć użytkowników, które wymagają uprzywilejowanego dostępu do, ale nie całodzienne każdego dnia. Rola jest nieaktywny, dopóki użytkownik będzie potrzebował dostępu, a następnie zakończyć proces aktywacji i stają się aktywne administratora dla wstępnie określoną ilość czasu. Organizacje coraz więcej są wybór posłuż się tą metodą ograniczenie lub wyeliminowanie "stały dostęp administratora" do ról uprzywilejowanych.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Włącz zarządzanie tożsamościami uprzywilejowanymi dla katalogu

Możesz rozpocząć korzystanie z usługi Azure AD Privileged Identity Management w [portalu Azure](https://portal.azure.com/).

> [!NOTE]
> Musi być administratorem globalnym, za pomocą konta organizacyjnego (na przykład @yourdomain.com), nie jest konto Microsoft (na przykład @outlook.com), aby włączyć usługi Azure AD Privileged Identity Management dla katalogu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny katalogu.
2. Jeśli organizacja dysponuje więcej niż jednym katalogiem, wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure Portal. Wybierz katalog, w którym będziesz używać usługi Azure AD Privileged Identity Management.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

Jeśli jesteś pierwszą osobą, aby użyć usługi Azure AD Privileged Identity Management w katalogu, przejdź do usługi Azure AD directory ról, a następnie przejdź do ról katalogu usługi Azure AD, [Kreator zabezpieczeń](active-directory-privileged-identity-management-security-wizard.md) przeprowadzi Cię przez początkowy etap przypisania. Po wykonaniu tej automatycznie staje się pierwszym **administrator zabezpieczeń** i **administrator ról uprzywilejowanych** katalogu.

Dla ról usługi Azure AD tylko użytkownik, który należy do roli Administrator ról uprzywilejowanych można zarządzać przydziały dla innych administratorów w usłudze Azure AD PIM. Możesz [przekazać innym użytkownikom możliwość zarządzania rolami katalogu w PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Globalne administratorów, administratorów zabezpieczeń i czytników zabezpieczeń można wyświetlić przypisania do ról usługi Azure AD w usłudze Azure AD PIM.
Dla ról Azure RBAC administratorem subskrypcji, właściciela zasobów lub zasobów administrator dostępu użytkowników można zarządzać przydziały dla innych administratorów w usłudze Azure AD PIM.  Użytkownicy, którzy są uprzywilejowani Administratorzy roli, administratorów zabezpieczeń lub zabezpieczeń czytelnicy nie domyślnie mają dostęp do wyświetlania przypisań ról Azure RBAC w usłudze Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Omówienie zarządzania tożsamościami uprzywilejowanymi (punkt wejścia)

Azure AD Privileged Identity Management obsługuje administracyjną katalogu usługi Azure AD, oraz ról zasobów Azure (wersja zapoznawcza). Funkcja ról dla zasobów Azure różnią się od ról administracyjnych w usłudze Azure AD. Role zasobów platformy Azure zapewniają szczegółowe uprawnienia dla zasobów, w którym są przypisane i wszystkie zasoby podrzędne w hierarchii zasobów (nazywane dziedziczenia). [Dowiedz się więcej o RBAC, hierarchii zasobów i dziedziczenie](../role-based-access-control/role-assignments-portal.md). PIM dla obu ról katalogu usługi Azure AD i zasobów Azure (wersja zapoznawcza) można podawać uzyskując dostęp do odpowiednie łącze w sekcji Zarządzanie menu nawigacji po lewej stronie punktu wejścia omówienie PIM.

PIM umożliwia wygodne dostęp do aktywowania usługi ról, wyświetlanie oczekujących aktywacji /, oczekujących żądań zatwierdzenia (dla ról katalogu usługi Azure AD) i monitoruje oczekuje na Twoją odpowiedź z sekcji zadań menu nawigacji po lewej stronie.

Podczas uzyskiwania dostępu do żadnego z elementów menu zadania z punktu wejścia Przegląd, wynikowy widok zawiera wyniki zarówno usługi Azure AD directory ról i zasobów Azure (wersja zapoznawcza).

![Szybki start](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Moje role zawierają listę przypisań ról aktywne i kwalifikujących się do ról katalogu usługi Azure AD i role zasobów Azure (wersja zapoznawcza). [Dowiedz się więcej na temat aktywowania przypisań ról kwalifikujących się](active-directory-privileged-identity-management-how-to-activate-role.md).

Aktywacja ról zasobów Azure (wersja zapoznawcza) wprowadza nową funkcjonalność, która umożliwia kwalifikujących się członkami roli do planowania aktywacji dla przyszłych daty/godziny i wybierz czas trwania aktywacji określonych w ramach maksymalną dozwoloną przez administratorów.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

W przypadku zaplanowanego aktywacji nie jest już potrzebne, użytkowników można anulować ich oczekujące żądania przechodzenia do żądań oczekujących w menu nawigacji po lewej stronie, a następnie klikając polecenie Anuluj przycisk śródwierszowej z tym żądaniem.

![Żądania oczekujące](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Uprzywilejowane pulpitu nawigacyjnego administratora zarządzania tożsamościami

Azure AD Privileged Identity Manager udostępnia pulpit nawigacyjny administratora, zapewniająca ważne informacje, takie jak:

* Alerty, które wskazać możliwości zwiększające bezpieczeństwo
* Liczba użytkowników przypisanych do poszczególnych ról uprzywilejowanych  
* Liczba administratorów kwalifikujących się i stałe
* Wykres aktywacji ról uprzywilejowanych w katalogu
*   Liczba Just In Time, powiązane z czasu i stałych przypisań ról zasobów Azure (wersja zapoznawcza)
*   Użytkownicy i grupy z nowych przypisań ról w ciągu ostatnich 30 dni (role zasobów Azure)


![Pulpit nawigacyjny PIM — zrzut ekranu][2]

## <a name="privileged-role-management"></a>Zarządzanie ról uprzywilejowanych

Z usługi Azure AD Privileged Identity Management można zarządzać administratorów, przez dodawanie lub usuwanie administratorów trwałych lub kwalifikujących się do każdej roli dla ról katalogu usługi Azure AD. Za pomocą usługi PIM zasobów Azure (wersja zapoznawcza), właścicieli, Administratorzy dostępu użytkowników i administratorów globalnych, które umożliwiają zarządzanie subskrypcjami w swojej dzierżawy można przypisać użytkowników lub grup do ról zasobów platformy Azure jako uprawniających (dostępu Just In Time), lub (czas wiązaniem dostęp nie jest wymagana aktywacja) z rozpoczęcia i zakończenia daty/godziny lub stałego (jeśli jest włączone w ustawieniach roli).

![Administratorzy dodawania i usuwania PIM — zrzut ekranu][3]

## <a name="configure-the-role-activation-settings"></a>Skonfiguruj ustawienia aktywacji roli

Przy użyciu [ustawienia roli](active-directory-privileged-identity-management-how-to-change-default-settings.md) można skonfigurować właściwości aktywacji roli kwalifikujących się do usługi Azure AD directory ról w tym:

* Okres aktywacji roli
* Powiadomienie dotyczące uaktywnienia roli
* Informacje, które użytkownik musi podać w procesie aktywacji w roli
* Numer biletu lub zdarzenia usługi
* [Wymagania w zakresie przepływu pracy zatwierdzenia - Preview](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Zrzut ekranu — Aktywacja administratora — ustawienia usługi PIM][4]

Należy pamiętać, że w obrazie przyciski **uwierzytelnianie wieloskładnikowe** są wyłączone. Dla niektórych, wysoko uprzywilejowane ról, wymagamy MFA podwyższonym ochrony.

Ustawienia roli dla ról zasobów Azure (wersja zapoznawcza) umożliwiają administratorom konfigurowanie Just In Time i ustawienia bezpośrednio przypisywać w tym:

- Możliwość przypisania grup użytkowników lub do ról bez Data/Godzina zakończenia (przypisanie stałe)
- Domyślny czas trwania przypisania (gdy jest trwałe nie)
- Czas trwania aktywacji maksymalną (gdy członek roli kwalifikujących się aktywuje)
- Informacje, użytkownik musi podać podczas uaktywniania roli (Just-In-Time przydziałów) lub procesu przypisania (przypisania bezpośredniego)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Aktywacja roli

Aby [aktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md), administrator uprawnionych żądań czasu wiązaniem "Aktywacja" dla roli. Aktywacja może zażądać przy użyciu **Uaktywnij rolę Mój** opcji w usłudze Azure AD Privileged Identity Management.

Administrator, który chce aktywować rolę musi zainicjować usługi Azure AD Privileged Identity Management w portalu Azure.

Aktywacja roli jest można dostosowywać. W ustawieniach usługi PIM można określić długości aktywacji i informacje, administrator musi podać aktywowania roli.

![Aktywacja roli PIM administratora żądania — zrzut ekranu][5]

## <a name="review-role-activity"></a>Czynność przeglądu roli

Istnieją dwa sposoby śledzić pracowników i Administratorzy korzystania z ról uprzywilejowanych. Pierwsza opcja używa [Historia inspekcji ról katalogu](active-directory-privileged-identity-management-how-to-use-audit-log.md). Historia inspekcji dzienniki śledzenia zmian w przypisaniach ról uprzywilejowanych, Historia aktywacji dla roli, a i zmiany ustawień dla ról zasobów Azure (wersja zapoznawcza). 

![Historia aktywacji PIM — zrzut ekranu][6]

Drugą opcją jest skonfigurowanie regular [dostępu przeglądami](active-directory-privileged-identity-management-how-to-start-security-review.md). Te przeglądami dostępu mogą być wykonywane przez i przypisane recenzenta (takie jak Menedżer zespołu) lub pracowników można przejrzeć samodzielnie. Jest to najlepszy sposób, aby monitorować kto nadal wymaga dostępu i który już nie występuje.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM na wygaśnięcia subskrypcji

Dzierżawca musi mieć Azure AD Premium P2 (lub EMS E5) subskrypcji próbnej lub płatnej w swojej dzierżawy, przed rozpoczęciem korzystania z usługi Azure AD PIM.  Ponadto należy przypisać licencje do administratorów dzierżawy.  W szczególności należy przypisać licencje do administratorów w usłudze Azure AD ról zarządzanych za pomocą usługi Azure AD PIM, Administratorzy w rolach Azure RBAC zarządzanych za pomocą usługi Azure AD PIM i użytkowników bez uprawnień administratora, które wykonują przeglądami dostępu.
Jeśli Twoja organizacja nie odnowić Azure AD Premium P2 lub wersja próbna wygaśnie, funkcje usługi Azure AD PIM nie będzie już dostępna w dzierżawie, przypisań ról kwalifikujących się zostaną usunięte i użytkownicy nie będą mogli aktywować role. Możesz przeczytać więcej informacji, zobacz [wymagań dotyczących subskrypcji usługi Azure AD PIM](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
