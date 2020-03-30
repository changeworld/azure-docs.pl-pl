---
title: Zarządzanie dostępem dla użytkowników zewnętrznych w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się więcej o ustawieniach, które można określić, aby zarządzać dostępem dla użytkowników zewnętrznych w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128698"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Zarządzanie dostępem dla użytkowników zewnętrznych w zarządzaniu uprawnieniami usługi Azure AD

Zarządzanie uprawnieniami usługi Azure AD wykorzystuje [usługę Azure AD business(B2B) do](../b2b/what-is-b2b.md) współpracy z osobami spoza organizacji w innym katalogu. Dzięki usłudze Azure AD B2B użytkownicy zewnętrzni uwierzytelniają się w katalogu macierzystym, ale mają reprezentację w katalogu. Reprezentacja w katalogu umożliwia użytkownikowi przypisany dostęp do zasobów.

W tym artykule opisano ustawienia, które można określić, aby zarządzać dostępem dla użytkowników zewnętrznych.

## <a name="how-entitlement-management-can-help"></a>W jaki sposób zarządzanie uprawnieniami może pomóc

Korzystając z środowiska zapraszania [usługi Azure AD B2B,](../b2b/what-is-b2b.md) musisz już znać adresy e-mail zewnętrznych użytkowników-gości, których chcesz wprowadzić do katalogu zasobów i z którym pracować. To działa świetnie, gdy pracujesz nad mniejszym lub krótkoterminowym projektem i znasz już wszystkich uczestników, ale jest to trudniejsze do zarządzania, jeśli masz wielu użytkowników, z którymi chcesz pracować lub jeśli uczestnicy zmieniają się w czasie.  Na przykład możesz pracować z inną organizacją i mieć jeden punkt kontaktu z tą organizacją, ale z czasem dodatkowi użytkownicy z tej organizacji również będą potrzebować dostępu.

Za pomocą zarządzania uprawnieniami można zdefiniować zasadę, która umożliwia użytkownikom z organizacji, które określisz, możliwość samodzielnego żądania pakietu dostępu. Można określić, czy zatwierdzenie jest wymagane i datę wygaśnięcia dostępu. Jeśli zatwierdzenie jest wymagane, można również zaprosić jednego lub więcej użytkowników z organizacji zewnętrznej do katalogu i wyznaczyć ich jako osoby zatwierdzające — ponieważ prawdopodobnie wiedzą, którzy użytkownicy zewnętrzni z ich organizacji potrzebują dostępu. Po skonfigurowaniu pakietu dostępu można wysłać łącze pakietu dostępu do osoby kontaktowej (sponsora) w organizacji zewnętrznej. Ten kontakt może udostępniać innym użytkownikom w organizacji zewnętrznej i mogą użyć tego łącza, aby zażądać pakietu dostępu. Użytkownicy z tej organizacji, którzy zostali już zaproszeni do katalogu, mogą również użyć tego łącza.

Po zatwierdzeniu żądania zarządzanie uprawnieniami zapewni użytkownikowi niezbędny dostęp, który może obejmować zapraszanie użytkownika, jeśli nie znajduje się on jeszcze w katalogu. Usługa Azure AD automatycznie utworzy dla nich konto gościa B2B. Należy zauważyć, że administrator mógł wcześniej ograniczyć, które organizacje mogą współpracować, ustawiając [listę B2B zezwalają lub odmawiają](../b2b/allow-deny-list.md) zezwalanie lub blokowanie zaproszeń do innych organizacji.  Jeśli użytkownik nie jest dozwolony przez listę dozwolonych lub zablokowanych, nie zostanie zaproszony.

Ponieważ dostęp użytkownika zewnętrznego nie ma trwać wiecznie, w zasadach należy określić datę wygaśnięcia, na przykład 180 dni. Po 180 dniach, jeśli ich dostęp nie zostanie przedłużony, zarządzanie uprawnieniami spowoduje usunięcie całego dostępu skojarzonego z tym pakietem dostępu. Domyślnie, jeśli użytkownik, który został zaproszony za pośrednictwem zarządzania uprawnieniami, nie ma innych przypisań pakietów dostępu, to gdy utraci ostatnie przypisanie, jego konto gościa zostanie zablokowane przed zalogowaniem się na 30 dni, a następnie usunięte. Zapobiega to rozprzestrzenianiu się niepotrzebnych rachunków. Zgodnie z opisem w poniższych sekcjach te ustawienia są konfigurowalne.

## <a name="how-access-works-for-external-users"></a>Jak działa dostęp dla użytkowników zewnętrznych

Poniższy diagram i kroki zawierają omówienie sposobu, w jaki użytkownikom zewnętrznym uzyskuje się dostęp do pakietu dostępu.

![Diagram przedstawiający cykl życia użytkowników zewnętrznych](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Dodaj [połączoną organizację](entitlement-management-organization.md) dla katalogu lub domeny usługi Azure AD, z którą chcesz współpracować.

1. W katalogu utworzysz pakiet dostępu zawierający zasady [Dla użytkowników niew katalogu](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Wyślij [link portalu Mój dostęp](entitlement-management-access-package-settings.md) do kontaktu w organizacji zewnętrznej, który może udostępnić swoim użytkownikom, aby zażądać pakietu dostępu.

1. Użytkownik zewnętrzny **(Requestor A** w tym przykładzie) używa łącza Portal Mój dostęp, aby [zażądać dostępu](entitlement-management-request-access.md) do pakietu dostępu. Sposób logowania użytkownika zależy od typu uwierzytelniania katalogu lub domeny zdefiniowanej w połączonej organizacji.

1. Osoba zatwierdzająca [zatwierdza żądanie](entitlement-management-request-approve.md) (lub żądanie jest automatycznie zatwierdzane).

1. Żądanie przechodzi do [stanu dostawy](entitlement-management-process.md).

1. Za pomocą procesu zapraszania B2B w katalogu jest tworzone konto użytkownika-gościa **(Requestor A (Guest)** w tym przykładzie). Jeśli zostanie zdefiniowana [lista dozwolona lub lista odmowy,](../b2b/allow-deny-list.md) zostanie zastosowane ustawienie listy.

1. Użytkownik-gość ma przypisany dostęp do wszystkich zasobów w pakiecie dostępu. Może upłynąć trochę czasu, aby zmiany zostały wprowadzone w usłudze Azure AD i innych usługach Microsoft Online Services lub połączonych aplikacji SaaS. Aby uzyskać więcej informacji, zobacz [Kiedy zmiany są stosowane](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Użytkownik zewnętrzny otrzymuje wiadomość e-mail z informacją, że jego dostęp został [dostarczony](entitlement-management-process.md).

1. Aby uzyskać dostęp do zasobów, użytkownik zewnętrzny może kliknąć łącze w wiadomości e-mail lub spróbować uzyskać dostęp do dowolnego zasobu katalogu bezpośrednio, aby zakończyć proces zaproszenia.

1. W zależności od ustawień zasad w miarę upływu czasu przydział pakietu dostępu dla użytkownika zewnętrznego wygasa, a dostęp użytkownika zewnętrznego jest usuwany.

1. W zależności od cyklu życia ustawień użytkowników zewnętrznych, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, użytkownik zewnętrzny jest zablokowany przed logowaniem, a konto użytkownika gościa jest usuwane z katalogu.

## <a name="settings-for-external-users"></a>Ustawienia dla użytkowników zewnętrznych

Aby upewnić się, że osoby spoza organizacji mogą żądać pakietów dostępu i uzyskać dostęp do zasobów w tych pakietach dostępu, istnieją pewne ustawienia, które należy sprawdzić, czy są poprawnie skonfigurowane.

### <a name="enable-catalog-for-external-users"></a>Włączanie katalogu dla użytkowników zewnętrznych

- Domyślnie podczas tworzenia [nowego katalogu](entitlement-management-catalog-create.md)jest on włączony, aby umożliwić użytkownikom zewnętrznym żądanie pakietów dostępu w katalogu. Upewnij **się,** że opcja Włączone dla użytkowników zewnętrznych jest ustawiona na **Tak**.

    ![Edytowanie ustawień katalogu](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurowanie ustawień współpracy zewnętrznej usługi Azure AD B2B

- Zezwolenie gościom na zapraszanie innych gości do katalogu oznacza, że zaproszenia gości mogą wystąpić poza zarządzaniem uprawnieniami. Zalecamy ustawienie **Goście mogą zapraszać** na **nie,** aby zezwalać tylko na prawidłowo zarządzane zaproszenia.
- Jeśli używasz listy dozwolonych B2B, upewnij się, że do listy zostanie dodana dowolna domena, z którą chcesz współpracować przy użyciu zarządzania uprawnieniami. Alternatywnie, jeśli używasz listy odmowy B2B, należy upewnić się, że żadna domena, z którą chcesz współpracować, nie jest dodawana do listy.
- Jeśli utworzysz zasady zarządzania uprawnieniami dla **wszystkich użytkowników** (wszystkie połączone organizacje + nowi użytkownicy zewnętrzni), pierwszeństwo mają wszystkie ustawienia listy B2B zezwalają lub odrzucają. W związku z tym należy uwzględnić domeny, które zamierzasz uwzględnić w tej zasadie do listy dozwolonych, jeśli używasz, i wykluczyć je z listy odmów, jeśli używasz listy odmowy.
- Jeśli chcesz utworzyć zasadę zarządzania uprawnieniami, która obejmuje **wszystkich użytkowników** (wszystkie połączone organizacje + wszystkich nowych użytkowników zewnętrznych), należy najpierw włączyć uwierzytelnianie kodu dostępu poczty e-mail dla katalogu. Aby uzyskać więcej informacji, zobacz [Wysyłanie jednorazowego uwierzytelniania kodem dostępu (wersja zapoznawcza) pocztą e-mail](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Aby uzyskać więcej informacji na temat ustawień współpracy zewnętrznej usługi Azure AD B2B, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać gości.](../b2b/delegate-invitations.md)

    ![Ustawienia współpracy zewnętrznej usługi Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Przeglądanie zasad dostępu warunkowego

- Pamiętaj, aby wykluczyć gości z wszystkich zasad dostępu warunkowego, których nowi użytkownicy-goście nie będą mogli spełnić, ponieważ uniemożliwi im to zalogowanie się do katalogu. Na przykład goście prawdopodobnie nie mają zarejestrowanego urządzenia, nie znajdują się w znanej lokalizacji i nie chcą ponownie zarejestrować się w celu uwierzytelniania wieloskładnikowego (MFA), więc dodanie tych wymagań w zasadach dostępu warunkowego uniemożliwi gościom korzystanie z uprawnień Zarządzania. Aby uzyskać więcej informacji, zobacz [Jakie są warunki w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/concept-conditional-access-conditions.md).

    ![Ustawienia wykluczania zasad dostępu warunkowego usługi Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Przeglądanie ustawień udostępniania zewnętrznego usługi SharePoint Online

- Jeśli chcesz uwzględnić witryny usługi SharePoint Online w pakietach dostępu dla użytkowników zewnętrznych, upewnij się, że ustawienie udostępniania zewnętrznego na poziomie organizacji jest ustawione na **Anyone** (użytkownicy nie wymagają logowania) lub **Nowi i obecni goście** (goście muszą się zalogować lub podać kod weryfikacyjny). Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Jeśli chcesz ograniczyć udostępnianie zewnętrzne poza zarządzaniem uprawnieniami, możesz ustawić ustawienie udostępniania zewnętrznego na **Istniejący goście**. Następnie tylko nowi użytkownicy, którzy są zaproszeni poprzez zarządzanie uprawnieniami, będą mogli uzyskać dostęp do tych witryn. Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Upewnij się, że ustawienia na poziomie witryny umożliwiają dostęp gościa (te same opcje, które zostały wcześniej wymienione). Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego dla witryny](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Przeglądanie ustawień udostępniania grup usługi Office 365

- Jeśli chcesz uwzględnić grupy usługi Office 365 w pakietach dostępu dla użytkowników zewnętrznych, upewnij się, że **zezwalaj użytkownikom na dodawanie nowych gości do organizacji** jest ustawiona na **Włączone,** aby zezwolić na dostęp gościa. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem gościa do grup usługi Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Jeśli chcesz, aby użytkownicy zewnętrzni mieli dostęp do witryny usługi SharePoint Online i zasobów skojarzonych z grupą usługi Office 365, upewnij się, że włączysz udostępnianie zewnętrzne usługi SharePoint Online. Aby uzyskać więcej informacji, zobacz [Włączanie lub wyłączanie udostępniania zewnętrznego](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Aby uzyskać informacje dotyczące ustawiania zasad gościa dla grup usługi Office 365 na poziomie katalogu w programie PowerShell, zobacz [Przykład: Konfigurowanie zasad gościa dla grup na poziomie katalogu](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Przeglądanie ustawień udostępniania zespołów

- Jeśli chcesz dołączyć usługi Teams do pakietów dostępu dla użytkowników zewnętrznych, upewnij się, że **zezwalaj na dostęp gościa w usłudze Microsoft Teams** jest ustawiona **na Włączone,** aby zezwolić na dostęp gościa. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dostępu gościa w centrum administracyjnym usługi Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Zarządzanie cyklem życia użytkowników zewnętrznych

Można wybrać, co się dzieje, gdy użytkownik zewnętrzny, który został zaproszony do katalogu za pośrednictwem żądania pakietu dostępu jest zatwierdzony, nie ma już żadnych przypisań pakietów dostępu. Może się tak zdarzyć, jeśli użytkownik zrzeka się wszystkich przypisań pakietów dostępu lub wygaśnie ich ostatnie przypisanie pakietu dostępu. Domyślnie, gdy użytkownik zewnętrzny nie ma już żadnych przypisań pakietów dostępu, są one zablokowane przed logowaniem się do katalogu. Po 30 dniach ich konto użytkownika gościa zostanie usunięte z katalogu.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie uprawnieniami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do zarządzania cyklem życia użytkowników zewnętrznych](./media/entitlement-management-external-users/settings-external-users.png)

1. W sekcji **Zarządzanie cyklem życia użytkowników zewnętrznych** wybierz różne ustawienia dla użytkowników zewnętrznych.

1. Gdy użytkownik zewnętrzny utraci swoje ostatnie przypisanie do dowolnych pakietów dostępu, jeśli chcesz zablokować im logowanie się do tego katalogu, ustaw **opcję Blokuj użytkownika zewnętrznego przed logowaniem się do tego katalogu** na **Tak**.

    > [!NOTE]
    > Jeśli użytkownik jest zablokowany przed zalogowaniem się do tego katalogu, użytkownik nie będzie mógł ponownie zażądać pakietu dostępu ani zażądać dodatkowego dostępu w tym katalogu. Nie należy konfigurować blokowania ich przed logowaniem się, jeśli będą musieli następnie zażądać dostępu do innych pakietów dostępu.

1. Gdy użytkownik zewnętrzny utraci swoje ostatnie przypisanie do dowolnych pakietów dostępu, jeśli chcesz usunąć swoje konto użytkownika gościa w tym katalogu, ustaw **Usuń użytkownika zewnętrznego** na **Tak**.

    > [!NOTE]
    > Zarządzanie uprawnieniami usuwa tylko konta, które zostały zaproszone za pośrednictwem zarządzania uprawnieniami. Należy również pamiętać, że użytkownik zostanie zablokowany przed logowaniem się i usunięty z tego katalogu, nawet jeśli ten użytkownik został dodany do zasobów w tym katalogu, które nie były przypisania pakietów dostępu. Jeśli gość był obecny w tym katalogu przed odebraniem przypisań pakietów dostępu, pozostaną one. Jeśli jednak gość został zaproszony za pośrednictwem przypisania pakietu dostępu, a po zaproszeniu został również przypisany do witryny usługi OneDrive dla Firm lub usługi SharePoint Online, nadal będą one usuwane.

1. Jeśli chcesz usunąć konto użytkownika gościa w tym katalogu, możesz ustawić liczbę dni przed jego usunięciem. Jeśli chcesz usunąć konto użytkownika-gościa, gdy tylko utraci on swoje ostatnie przypisanie do dowolnych pakietów dostępu, ustaw **liczbę dni przed usunięciem użytkownika zewnętrznego z tego katalogu** na **0**.

1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie połączonej organizacji](entitlement-management-organization.md)
- [Dla użytkowników, którzy nie mają w katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Rozwiązywanie problemów](entitlement-management-troubleshoot.md)
