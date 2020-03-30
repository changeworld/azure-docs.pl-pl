---
title: Alerty zabezpieczeń dla ról usługi Azure AD w usłudze PIM — usługa Azure AD | Dokumenty firmy Microsoft
description: Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD Zarządzanie tożsamościami uprzywilejowanych w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253314"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Zarządzanie tożsamościami uprzywilejowanymi (PIM) generuje alerty, gdy w organizacji usługi Azure Active Directory (Azure AD) występuje podejrzana lub niebezpieczna aktywność. Po wyzwoleniu alertu pojawia się na pulpicie nawigacyjnym zarządzania tożsamościami uprzywilejowanymi. Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub role, które wyzwoliły alert.

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która odpowiada środowiskom ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zbadać alerty zabezpieczeń dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

![Role usługi Azure AD — wyświetlanie alertów z listą alertów i ważności](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

W tej sekcji wymieniono wszystkie alerty zabezpieczeń dla ról usługi Azure AD, wraz z instrukcjami naprawy i jak zapobiegać. Ważność ma następujące znaczenie:

- **Wysoki:** Wymaga natychmiastowego działania z powodu naruszenia zasad.
- **Medium:** Nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niski:** Nie wymaga natychmiastowych działań, ale sugeruje preferowaną zmianę polityki.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratorzy nie używają ról uprzywilejowanych

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Użytkownicy, którzy otrzymali uprzywilejowane role, których nie potrzebują, zwiększają ryzyko ataku. Osoby atakujące mogą również pozostać niezauważone na kontach, które nie są aktywnie używane. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń ich z ról uprzywilejowanych, których nie potrzebują. |
| **Zapobieganie** | Przypisywanie ról uprzywilejowanych tylko użytkownikom, którzy mają uzasadnienie biznesowe. </br>Zaplanuj regularne [przeglądy dostępu,](pim-how-to-start-security-review.md) aby sprawdzić, czy użytkownicy nadal potrzebują dostępu. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik przechodzi przez określoną liczbę dni bez aktywowania roli. |
| **Liczba dni** | To ustawienie określa maksymalną liczbę dni od 0 do 100, przez które użytkownik może przejść bez aktywowania roli.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nie wymagają uwierzytelniania wieloskładnikowego do aktywacji

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bez uwierzytelniania wieloskładnikowego użytkownicy, których bezpieczeństwo zostało naruszone, mogą aktywować role uprzywilejowane. |
| **Jak naprawić?** | Przejrzyj listę ról i [wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli. |
| **Zapobieganie** | [Wymagaj usługi MFA](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Działania łagodzące w portalu** | Powoduje, że uwierzytelnianie wieloskładnikowe jest wymagane do aktywacji roli uprzywilejowanej. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizacja nie ma usługi Azure AD Premium P2

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bieżąca organizacja usługi Azure AD nie ma usługi Azure AD Premium P2. |
| **Jak naprawić?** | Przejrzyj informacje o [wersjach usługi Azure AD](../fundamentals/active-directory-whatis.md). Uaktualnienie do usługi Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne przestarzałe konta w roli uprzywilejowanej

| | |
| --- | --- |
| **Ważność** | Medium |
| **Dlaczego otrzymuję ten alert?** | Konta w roli uprzywilejowanej nie zmieniły hasła w ciągu ostatnich 90 dni. Te konta mogą być kontami usługowymi lub udostępnionymi, które nie są obsługiwane i są narażone na ataki. |
| **Jak naprawić?** | Przejrzyj konta na liście. Jeśli nie potrzebują już dostępu, usuń je ze swoich ról uprzywilejowanych. |
| **Zapobieganie** | Upewnij się, że konta, które są współużytkowane są obracanie silne hasła, gdy nastąpiła zmiana w użytkowników, którzy znają hasło. </br>Regularnie przeglądaj konta z uprzywilejowanymi rolami przy użyciu [przeglądów dostępu](pim-how-to-start-security-review.md) i usuwaj przypisania ról, które nie są już potrzebne. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Najważniejsze wskazówki** | Konta dostępu współdzielonego, usługi i dostępu awaryjnego uwierzytelniane przy użyciu hasła i przypisane do wysoce uprzywilejowanych ról administracyjnych, takich jak administrator globalny lub administrator zabezpieczeń, powinny mieć swoje hasła obrócone w następujących przypadkach:<ul><li>Po incydencie związanym z bezpieczeństwem, w wyniku niego, polegającym na niewłaściwym wykorzystaniu lub naruszenia administracyjnych praw dostępu</li><li>Po zmianie uprawnień dowolnego użytkownika, tak aby nie był już administratorem (na przykład po tym, jak pracownik, który był administratorem, opuści it lub opuści organizację)</li><li>W regularnych odstępach czasu (na przykład kwartalnie lub co roku), nawet jeśli nie było znanego naruszenia lub zmiany personelu IT</li></ul>Ponieważ wiele osób ma dostęp do poświadczeń tych kont, poświadczenia powinny zostać obrócene, aby zapewnić, że osoby, które opuściły swoje role, nie będą już mogły uzyskiwać dostępu do kont. [Dowiedz się więcej o zabezpieczaniu kont](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role są przypisywane poza zarządzaniem tożsamościami uprzywilejowanymi

| | |
| --- | --- |
| **Ważność** | Wysoka |
| **Dlaczego otrzymuję ten alert?** | Przypisania ról uprzywilejowanych wykonane poza zarządzaniem tożsamościami uprzywilejowanymi nie są właściwie monitorowane i mogą wskazywać na aktywny atak. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń ich z ról uprzywilejowanych przypisanych poza zarządzanie tożsamościami uprzywilejowanymi. |
| **Zapobieganie** | Sprawdź, gdzie użytkownicy są przypisywani uprzywilejowanych ról poza Zarządzanie tożsamością uprzywilejowanych i zabronić przyszłych przypisań z tego miejsca. |
| **Działania łagodzące w portalu** | Usuwa użytkownika z roli uprzywilejowanej. |

### <a name="there-are-too-many-global-administrators"></a>Zbyt wielu administratorów globalnych

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Administrator globalny jest najwyższą uprzywilejowaną rolą. Jeśli administrator globalny zostanie naruszony, osoba atakująca uzyska dostęp do wszystkich swoich uprawnień, co zagraża całemu systemowi. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń wszystkie, które nie są absolutnie potrzebne roli administratora globalnego. </br>Zamiast tego przypisz do tych użytkowników niższe role uprzywilejowane. |
| **Zapobieganie** | Przypisz użytkownikom najmniej uprzywilejowaną rolę, jakiej potrzebują. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Wyzwalacz** | Wyzwalane, jeśli spełnione są dwa różne kryteria i można skonfigurować oba z nich. Najpierw musisz osiągnąć pewien próg administratorów globalnych. Po drugie, pewien procent całkowitych przypisań ról musi być administratorami globalnymi. Jeśli spełniasz tylko jeden z tych pomiarów, alert nie jest wyświetlany. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę administratorów globalnych od 2 do 100, które uważasz za zbyt mało dla organizacji usługi Azure AD. |
| **Odsetek administratorów globalnych** | To ustawienie określa minimalny odsetek administratorów będących administratorami globalnymi od 0% do 100%, poniżej którego nie chcesz, aby twoja organizacja usługi Azure AD była niższa. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są aktywowane zbyt często

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Wielokrotne aktywacje do tej samej roli uprzywilejowanej przez tego samego użytkownika jest oznaką ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ich uprzywilejowanej roli jest ustawiony wystarczająco długo, aby mogli wykonywać swoje zadania. |
| **Zapobieganie** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) ról uprzywilejowanych jest ustawiony wystarczająco długo, aby użytkownicy mogli wykonywać swoje zadania.</br>[Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych, które mają konta współużytkowane przez wielu administratorów. |
| **Działania łagodzące w portalu** | Nie dotyczy |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik aktywuje tę samą rolę uprzywilejowaną wiele razy w określonym czasie. Można skonfigurować zarówno okres czasu, jak i liczbę aktywacji. |
| **Ramy czasowe odnowienia aktywacji** | To ustawienie określa w dniach, godzinach, minutach i sekundach okres, którego chcesz użyć do śledzenia podejrzanych odnowień. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji od 2 do 100, przy których chcesz otrzymywać powiadomienia, w wybranym przedziale czasowym. To ustawienie można zmienić, przesuwając suwak lub wpisując numer w polu tekstowym. |

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Na stronie Alerty przejdź do **pozycji Ustawienia**.

![Strona Alerty z wyróżnionymi ustawieniami](media/pim-how-to-configure-security-alerts/alert-settings.png)

Dostosuj ustawienia w różnych alertach, aby pracować z celami środowiska i zabezpieczeń.

![Ustawianie strony alertu w celu włączania i konfigurowania ustawień](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

![Role usługi Azure AD — wyświetlanie alertów z listą alertów i ważności](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

W tej sekcji wymieniono wszystkie alerty zabezpieczeń dla ról usługi Azure AD, wraz z instrukcjami naprawy i jak zapobiegać. Ważność ma następujące znaczenie:

- **Wysoki:** Wymaga natychmiastowego działania z powodu naruszenia zasad.
- **Medium:** Nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niski:** Nie wymaga natychmiastowych działań, ale sugeruje preferowaną zmianę polityki.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratorzy nie używają ról uprzywilejowanych

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Użytkownicy, którzy otrzymali uprzywilejowane role, których nie potrzebują, zwiększają ryzyko ataku. Osoby atakujące mogą również pozostać niezauważone na kontach, które nie są aktywnie używane. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń ich z ról uprzywilejowanych, których nie potrzebują. |
| **Zapobieganie** | Przypisywanie ról uprzywilejowanych tylko użytkownikom, którzy mają uzasadnienie biznesowe. </br>Zaplanuj regularne [przeglądy dostępu,](pim-how-to-start-security-review.md) aby sprawdzić, czy użytkownicy nadal potrzebują dostępu. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik przechodzi przez określoną liczbę dni bez aktywowania roli. |
| **Liczba dni** | To ustawienie określa maksymalną liczbę dni od 0 do 100, przez które użytkownik może przejść bez aktywowania roli.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nie wymagają uwierzytelniania wieloskładnikowego do aktywacji

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bez uwierzytelniania wieloskładnikowego użytkownicy, których bezpieczeństwo zostało naruszone, mogą aktywować role uprzywilejowane. |
| **Jak naprawić?** | Przejrzyj listę ról i [wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli. |
| **Zapobieganie** | [Wymagaj usługi MFA](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Działania łagodzące w portalu** | Powoduje, że uwierzytelnianie wieloskładnikowe jest wymagane do aktywacji roli uprzywilejowanej. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizacja nie ma usługi Azure AD Premium P2

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bieżąca organizacja usługi Azure AD nie ma usługi Azure AD Premium P2. |
| **Jak naprawić?** | Przejrzyj informacje o [wersjach usługi Azure AD](../fundamentals/active-directory-whatis.md). Uaktualnienie do usługi Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne przestarzałe konta w roli uprzywilejowanej

| | |
| --- | --- |
| **Ważność** | Medium |
| **Dlaczego otrzymuję ten alert?** | Konta w roli uprzywilejowanej nie zmieniły hasła w ciągu ostatnich 90 dni. Te konta mogą być kontami usługowymi lub udostępnionymi, które nie są obsługiwane i są narażone na ataki. |
| **Jak naprawić?** | Przejrzyj konta na liście. Jeśli nie potrzebują już dostępu, usuń je ze swoich ról uprzywilejowanych. |
| **Zapobieganie** | Upewnij się, że konta, które są współużytkowane są obracanie silne hasła, gdy nastąpiła zmiana w użytkowników, którzy znają hasło. </br>Regularnie przeglądaj konta z uprzywilejowanymi rolami przy użyciu [przeglądów dostępu](pim-how-to-start-security-review.md) i usuwaj przypisania ról, które nie są już potrzebne. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Najważniejsze wskazówki** | Konta dostępu współdzielonego, usługi i dostępu awaryjnego uwierzytelniane przy użyciu hasła i przypisane do wysoce uprzywilejowanych ról administracyjnych, takich jak administrator globalny lub administrator zabezpieczeń, powinny mieć swoje hasła obrócone w następujących przypadkach:<ul><li>Po incydencie związanym z bezpieczeństwem, w wyniku niego, polegającym na niewłaściwym wykorzystaniu lub naruszenia administracyjnych praw dostępu</li><li>Po zmianie uprawnień dowolnego użytkownika, tak aby nie był już administratorem (na przykład po tym, jak pracownik, który był administratorem, opuści it lub opuści organizację)</li><li>W regularnych odstępach czasu (na przykład kwartalnie lub co roku), nawet jeśli nie było znanego naruszenia lub zmiany personelu IT</li></ul>Ponieważ wiele osób ma dostęp do poświadczeń tych kont, poświadczenia powinny zostać obrócene, aby zapewnić, że osoby, które opuściły swoje role, nie będą już mogły uzyskiwać dostępu do kont. [Dowiedz się więcej](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role są przypisywane poza zarządzaniem tożsamościami uprzywilejowanymi

| | |
| --- | --- |
| **Ważność** | Wysoka |
| **Dlaczego otrzymuję ten alert?** | Przypisania ról uprzywilejowanych wykonane poza zarządzaniem tożsamościami uprzywilejowanymi nie są właściwie monitorowane i mogą wskazywać na aktywny atak. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń ich z ról uprzywilejowanych przypisanych poza zarządzanie tożsamościami uprzywilejowanymi. |
| **Zapobieganie** | Sprawdź, gdzie użytkownicy są przypisywani uprzywilejowanych ról poza Zarządzanie tożsamością uprzywilejowanych i zabronić przyszłych przypisań z tego miejsca. |
| **Działania łagodzące w portalu** | Usuwa użytkownika z roli uprzywilejowanej. |

### <a name="there-are-too-many-global-administrators"></a>Zbyt wielu administratorów globalnych

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Administrator globalny jest najwyższą uprzywilejowaną rolą. Jeśli administrator globalny zostanie naruszony, osoba atakująca uzyska dostęp do wszystkich swoich uprawnień, co zagraża całemu systemowi. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń wszystkie, które nie są absolutnie potrzebne roli administratora globalnego. </br>Zamiast tego przypisz do tych użytkowników niższe role uprzywilejowane. |
| **Zapobieganie** | Przypisz użytkownikom najmniej uprzywilejowaną rolę, jakiej potrzebują. |
| **Działania łagodzące w portalu** | Usuwa konto z ich uprzywilejowanej roli. |
| **Wyzwalacz** | Wyzwalane, jeśli spełnione są dwa różne kryteria i można skonfigurować oba z nich. Najpierw musisz osiągnąć pewien próg administratorów globalnych. Po drugie, pewien procent całkowitych przypisań ról musi być administratorami globalnymi. Jeśli spełniasz tylko jeden z tych pomiarów, alert nie jest wyświetlany. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę administratorów globalnych od 2 do 100, które uważasz za zbyt mało dla organizacji usługi Azure AD. |
| **Odsetek administratorów globalnych** | To ustawienie określa minimalny odsetek administratorów będących administratorami globalnymi od 0% do 100%, poniżej którego nie chcesz, aby twoja organizacja usługi Azure AD była niższa. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są aktywowane zbyt często

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego otrzymuję ten alert?** | Wielokrotne aktywacje do tej samej roli uprzywilejowanej przez tego samego użytkownika jest oznaką ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ich uprzywilejowanej roli jest ustawiony wystarczająco długo, aby mogli wykonywać swoje zadania. |
| **Zapobieganie** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) ról uprzywilejowanych jest ustawiony wystarczająco długo, aby użytkownicy mogli wykonywać swoje zadania.</br>[Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych, które mają konta współużytkowane przez wielu administratorów. |
| **Działania łagodzące w portalu** | Nie dotyczy |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik aktywuje tę samą rolę uprzywilejowaną wiele razy w określonym czasie. Można skonfigurować zarówno okres czasu, jak i liczbę aktywacji. |
| **Ramy czasowe odnowienia aktywacji** | To ustawienie określa w dniach, godzinach, minutach i sekundach okres, którego chcesz użyć do śledzenia podejrzanych odnowień. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji od 2 do 100, przy których chcesz otrzymywać powiadomienia, w wybranym przedziale czasowym. To ustawienie można zmienić, przesuwając suwak lub wpisując numer w polu tekstowym. |

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Niektóre alerty zabezpieczeń w zarządzania tożsamościami uprzywilejowanymi można dostosować do potrzeb i celów zabezpieczeń organizacji. Aby otworzyć ustawienia alertów zabezpieczeń, wykonaj następujące czynności:

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi** w usłudze Azure AD.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Ustawienia,** a następnie **pozycję Alerty**.

    ![Role usługi Azure AD — ustawienia z zaznaczonymi alertami](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Wybierz nazwę alertu, aby skonfigurować ustawienie dla tego alertu.

    ![W przypadku wybranego alertu okienko ustawień alertu zabezpieczeń](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-change-default-settings.md)
