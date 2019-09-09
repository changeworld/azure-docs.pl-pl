---
title: Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3984d3276590357866c824d01ea8c51cf5b28fd7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804392"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generuje alerty w przypadku podejrzanych lub niebezpiecznych działań w środowisku. Po wyzwoleniu alertu zostanie on wyświetlony na pulpicie nawigacyjnym PIM. Wybierz Alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliły alert.

![Role usługi Azure AD — okienko alertów z listą alertów i ważnością](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

W tej sekcji znajduje się lista wszystkich alertów zabezpieczeń dla ról usługi Azure AD, a także sposób naprawy i zapobiegania. Ważność ma następujące znaczenie:

* **Wysoka**: Wymaga natychmiastowej akcji z powodu naruszenia zasad.
* **Średni**: Nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: Nie wymaga natychmiastowego działania, ale sugeruje zmianę zasad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratorzy nie korzystają z ról uprzywilejowanych

| | |
| --- | --- |
| **Obrażeń** | Małe |
| **Dlaczego otrzymuję ten alert?** | Użytkownicy, którym przypisano role uprzywilejowane, których nie potrzebują, zwiększają prawdopodobieństwo ataku. Łatwiejsze jest również, aby osoby atakujące pozostały niezauważalny w przypadku kont, które nie są aktywnie używane. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usuń je z uprzywilejowanych ról, których nie potrzebują. |
| **Zapobiegania** | Tylko role uprzywilejowane mogą być przypisywane do użytkowników, którzy mają uzasadnienie biznesowe. </br>Zaplanuj regularne [przeglądy dostępu](pim-how-to-start-security-review.md) , aby upewnić się, że użytkownicy nadal potrzebują dostępu. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, gdy użytkownik przejdzie określoną ilość czasu bez aktywowania roli. |
| **Liczba dni** | To ustawienie określa liczbę dni, od 0 do 100, które użytkownik może przejść bez aktywowania roli.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nie wymagają uwierzytelniania wieloskładnikowego na potrzeby aktywacji

| | |
| --- | --- |
| **Obrażeń** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bez usługi MFA użytkownicy z naruszonymi zabezpieczeniami mogą aktywować role uprzywilejowane. |
| **Jak naprawić?** | Przejrzyj listę ról i [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli. |
| **Zapobiegania** | [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Akcja łagodzenia w portalu** | Powoduje, że uwierzytelnianie wieloskładnikowe jest wymagane do aktywacji roli uprzywilejowanej. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Dzierżawca nie ma Azure AD — wersja Premium P2

| | |
| --- | --- |
| **Obrażeń** | Małe |
| **Dlaczego otrzymuję ten alert?** | Bieżąca dzierżawa nie ma Azure AD — wersja Premium P2. |
| **Jak naprawić?** | Przejrzyj informacje o [wersjach usługi Azure AD](../fundamentals/active-directory-whatis.md). Uaktualnij do Azure AD — wersja Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne przestarzałe konta w roli uprzywilejowanej

| | |
| --- | --- |
| **Obrażeń** | Średni |
| **Dlaczego otrzymuję ten alert?** | Konta w roli uprzywilejowanej, które nie zmieniły hasła w ciągu ostatnich 90 dni. Te konta mogą być kontami usług lub udostępnionymi, które nie są utrzymywane i są narażone na ataki. |
| **Jak naprawić?** | Przejrzyj konta na liście. Jeśli nie potrzebują już dostępu, usuń je z ról uprzywilejowanych. |
| **Zapobiegania** | Upewnij się, że konta, które są udostępnione, obracają silne hasła w przypadku zmiany użytkowników, którzy znają hasło. </br>Regularnie Przeglądaj konta z rolami uprzywilejowanymi przy użyciu [przeglądów dostępu](pim-how-to-start-security-review.md) i usuwaj przypisania ról, które nie są już potrzebne. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Najlepsze praktyki** | Konta dostępu współdzielonego, usługi i trybu awaryjnego, które uwierzytelniają się przy użyciu hasła i są przypisane do uprzywilejowanych ról administracyjnych, takich jak Administrator globalny lub administrator zabezpieczeń, powinny być obracane w następujących przypadkach:<ul><li>Po wystąpieniu zdarzenia zabezpieczeń związanego z nieprawidłowym lub naruszeniem praw dostępu administracyjnego</li><li>Po zmianie uprawnień użytkownika, tak aby nie były one już administratorami (na przykład po odejściu pracownika przez administratora lub opuszczenia organizacji)</li><li>W regularnych odstępach czasu (na przykład co kwartał lub co rok) nawet w przypadku braku znanego naruszenia lub zmiany działu IT</li></ul>Ponieważ wielu osób ma dostęp do tych kont, należy je obrócić, aby upewnić się, że osoby, które opuściły swoje role, nie będą miały dostępu do kont. [Dowiedz się więcej](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Role są przypisywane poza usługą PIM

| | |
| --- | --- |
| **Obrażeń** | Wysoka |
| **Dlaczego otrzymuję ten alert?** | Przypisania ról uprzywilejowanych wprowadzone poza usługą PIM nie są prawidłowo monitorowane i mogą wskazywać na aktywny atak. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń ich z uprzywilejowanych ról przypisanych poza programem PIM. |
| **Zapobiegania** | Zbadaj, gdzie użytkownicy mają przypisane role uprzywilejowane poza usługą PIM, i Zabroń w niej przyszłych przypisań. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |

### <a name="there-are-too-many-global-administrators"></a>Zbyt wielu administratorów globalnych

| | |
| --- | --- |
| **Obrażeń** | Małe |
| **Dlaczego otrzymuję ten alert?** | Administrator globalny jest najwyższą rolą uprzywilejowaną. W przypadku naruszenia zabezpieczeń administratora globalnego osoba atakująca uzyskuje dostęp do wszystkich swoich uprawnień, co stanowi zagrożenie dla całego systemu. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń wszystkie, które nie potrzebują absolutnie roli administratora globalnego. </br>Przypisz tych użytkowników poniżej niższych ról uprzywilejowanych. |
| **Zapobiegania** | Przypisywanie użytkownikom najniższych ról, których potrzebują. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli spełnione są dwa różne kryteria, i można skonfigurować obie. Najpierw należy uzyskać określony próg dla administratorów globalnych. Sekunda, pewna część całkowitych przypisań ról musi być administratorami globalnymi. Jeśli spełniasz tylko jeden z tych pomiarów, alert nie zostanie wyświetlony. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę administratorów globalnych z przedziału od 2 do 100, który jest uważany za niebezpieczną kwotę. |
| **Procent administratorów globalnych** | To ustawienie określa minimalną odsetek administratorów, którzy są administratorami globalnymi, od 0% do 100%, które nie są niebezpieczne w danym środowisku. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są uaktywniane zbyt często

| | |
| --- | --- |
| **Obrażeń** | Małe |
| **Dlaczego otrzymuję ten alert?** | Wielokrotne aktywacje z tą samą uprzywilejowaną rolą przez tego samego użytkownika jest znakiem ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla roli uprzywilejowanej jest ustawiony wystarczająco długo, aby wykonywał zadania. |
| **Zapobiegania** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych jest ustawiony na wystarczająco długi, aby użytkownicy mogli wykonywać swoje zadania.</br>[Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych, które mają konta współużytkowane przez wielu administratorów. |
| **Akcja łagodzenia w portalu** | ND |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik aktywuje tę samą uprzywilejowaną rolę wiele razy w określonym przedziale czasu. Można skonfigurować zarówno okres, jak i liczbę aktywacji. |
| **Przedział czasu odnawiania aktywacji** | To ustawienie określa liczbę dni, godzin, minut i sekund okresu, który ma być używany do śledzenia podejrzanych odnowień. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji z przedziału od 2 do 100, które należy wziąć pod uwagę zaufanego alertu w wybranym okresie. To ustawienie można zmienić, przesuwając suwak lub wpisując liczbę w polu tekstowym. |

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Niektóre alerty zabezpieczeń w programie PIM można dostosować do pracy ze środowiskiem i celami zabezpieczeń. Wykonaj następujące kroki, aby otworzyć ustawienia alertu zabezpieczeń:

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij przycisk **Ustawienia** , a następnie pozycję **alerty**.

    ![Role usługi Azure AD — ustawienia z alertami wybrane](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Kliknij nazwę alertu, aby skonfigurować ustawienie dla tego alertu.

    ![Dla wybranego alertu okienko ustawień alertów zabezpieczeń](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
