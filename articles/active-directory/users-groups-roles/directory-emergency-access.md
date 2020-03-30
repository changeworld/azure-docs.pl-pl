---
title: Zarządzanie kontami administratorów dostępu awaryjnego — Usługa Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać kont dostępu awaryjnego, aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028125"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD

Ważne jest, aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure Active Directory (Azure AD), ponieważ nie można zalogować się ani aktywować konta innego użytkownika jako administratora. Można złagodzić wpływ przypadkowego braku dostępu administracyjnego, tworząc co najmniej dwa *konta dostępu awaryjnego* w organizacji.

Konta dostępu awaryjnego są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do scenariuszy awaryjnych lub "break glass", w których nie można używać zwykłych kont administracyjnych. Zalecamy utrzymanie celu ograniczenia używania konta awaryjnego tylko do czasów, gdy jest to absolutnie konieczne.

Ten artykuł zawiera wskazówki dotyczące zarządzania kontami dostępu awaryjnego w usłudze Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Dlaczego warto korzystać z konta dostępu awaryjnego

Organizacja może być konieczne użycie konta dostępu awaryjnego w następujących sytuacjach:

- Konta użytkowników są sfederowane, a federacja jest obecnie niedostępna z powodu przerwy w sieci komórkowej lub awarii dostawcy tożsamości. Na przykład jeśli host dostawcy tożsamości w twoim środowisku został uł osłabł, użytkownicy mogą nie być w stanie zalogować się, gdy usługa Azure AD przekierowuje do dostawcy tożsamości.
- Administratorzy są zarejestrowani za pośrednictwem uwierzytelniania wieloskładnikowego platformy Azure, a wszystkie ich poszczególne urządzenia są niedostępne lub usługa jest niedostępna. Użytkownicy mogą nie być w stanie ukończyć uwierzytelniania wieloskładnikowego, aby aktywować rolę. Na przykład awaria sieci komórkowej uniemożliwia im odbieranie połączeń telefonicznych lub odbieranie wiadomości tekstowych, tylko dwa mechanizmy uwierzytelniania, które zarejestrowali na swoim urządzeniu.
- Osoba z najnowszym dostępem administratora globalnego opuściła organizację. Usługa Azure AD zapobiega usunięciu ostatniego konta administratora globalnego, ale nie uniemożliwia usunięcia lub wyłączenia konta lokalnie. Każda z tych sytuacji może sprawić, że organizacja nie będzie mogła odzyskać konta.
- Nieprzewidziane okoliczności, takie jak klęska żywiołowa, podczas której telefon komórkowy lub inne sieci mogą być niedostępne. 

## <a name="create-emergency-access-accounts"></a>Tworzenie kont dostępu awaryjnego

Utwórz co najmniej dwa konta dostępu awaryjnego. Te konta powinny być kontami \*tylko w chmurze, które używają domeny .onmicrosoft.com i które nie są sfederowane lub synchronizowane ze środowiska lokalnego.

Podczas konfigurowania tych kont muszą być spełnione następujące wymagania:

- Konta dostępu awaryjnego nie powinny być skojarzone z żadnym użytkownikiem w organizacji. Upewnij się, że konta nie są połączone z telefonami komórkowymi dostarczonymi przez pracowników, tokenami sprzętowymi, które podróżują z poszczególnymi pracownikami, ani z innymi poświadczeniami specyficznymi dla pracownika. Ten środek ostrożności obejmuje przypadki, w których pojedynczy pracownik jest nieosiągalny, gdy potrzebne jest poświadczenie. Ważne jest, aby upewnić się, że wszystkie zarejestrowane urządzenia są przechowywane w znanej, bezpiecznej lokalizacji, która ma wiele sposobów komunikowania się z usługą Azure AD.
- Mechanizm uwierzytelniania używany dla konta dostępu awaryjnego powinien być inny niż mechanizm używany przez inne konta administracyjne, w tym inne konta dostępu awaryjnego.  Na przykład jeśli normalne logowanie administratora odbywa się za pośrednictwem lokalnego usługi MFA, usługa Azure MFA będzie inny mechanizm.  Jeśli jednak usługa Azure MFA jest podstawową częścią uwierzytelniania dla kont administracyjnych, należy rozważyć inne podejście do nich, takie jak przy użyciu dostępu warunkowego z dostawcą usługi MFA innej firmy.
- Urządzenie lub poświadczenie nie może wygasnąć lub być w zakresie automatycznego oczyszczania z powodu braku użycia.  
- Przypisanie roli administratora globalnego powinno być stałe dla kont dostępu awaryjnego. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Wykluczanie co najmniej jednego konta z uwierzytelniania wieloskładnikowego opartego na telefonie

Aby zmniejszyć ryzyko ataku wynikającego z zagrożonego hasła, usługa Azure AD zaleca, aby wymagać uwierzytelniania wieloskładnikowego dla wszystkich indywidualnych użytkowników. Ta grupa obejmuje administratorów i wszystkich innych (na przykład urzędników finansowych), których konto, którego konto zostało naruszone, miałoby znaczący wpływ.

Jednak co najmniej jedno z kont dostępu awaryjnego nie powinno mieć tego samego mechanizmu uwierzytelniania wieloskładnikowego, co inne konta inne niż awaryjne. Obejmuje to rozwiązania uwierzytelniania wieloskładnikowego innych firm. Jeśli masz zasady dostępu warunkowego, aby wymagać [uwierzytelniania wieloskładnikowego dla każdego administratora](../authentication/howto-mfa-userstates.md) dla usługi Azure AD i innego podłączonego oprogramowania jako usługi (SaaS) aplikacji, należy wykluczyć konta dostępu awaryjnego z tego wymagania i zamiast tego skonfigurować inny mechanizm. Ponadto należy upewnić się, że konta nie mają zasad uwierzytelniania wieloskładnikowego dla użytkownika.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Wykluczanie co najmniej jednego konta z zasad dostępu warunkowego

W sytuacji awaryjnej nie chcesz, aby zasady potencjalnie zablokować dostęp, aby rozwiązać problem. Co najmniej jedno konto dostępu awaryjnego powinno być wyłączone ze wszystkich zasad dostępu warunkowego. Jeśli włączono [zasady bazowe,](../conditional-access/baseline-protection.md)należy wykluczyć konta dostępu awaryjnego.

## <a name="federation-guidance"></a>Wskazówki dotyczące federacji

Dodatkową opcją dla organizacji korzystających z usług domenowych usług AD i usługi ADFS lub podobnego dostawcy tożsamości w celu połączenia z usługą Azure AD jest skonfigurowanie konta dostępu awaryjnego, którego oświadczenie usługi MFA może być dostarczone przez tego dostawcę tożsamości.  Na przykład konto dostępu awaryjnego może być wspierane przez certyfikat i parę kluczy, takie jak przechowywane na karcie inteligentnej.  Gdy ten użytkownik jest uwierzytelniony do usługi AD, usługa ADFS może dostarczyć oświadczenie usługi Azure AD wskazujące, że użytkownik spełnił wymagania usługi MFA.  Nawet przy takim podejściu organizacje muszą nadal mieć konta dostępu awaryjnego oparte na chmurze w przypadku, gdy nie można ustanowić federacji. 

## <a name="store-account-credentials-safely"></a>Bezpieczne przechowywanie poświadczeń konta

Organizacje muszą upewnić się, że poświadczenia dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do korzystania z nich. Niektórzy klienci używają karty inteligentnej, a inni używają haseł. Hasło do konta dostępu awaryjnego jest zwykle podzielone na dwie lub trzy części, napisane na oddzielnych kawałkach papieru i przechowywane w bezpiecznych, ognioodpornych sejfach, które znajdują się w bezpiecznych, oddzielnych miejscach.

Jeśli używasz haseł, upewnij się, że konta mają silne hasła, które nie wygasają hasła. W idealnym przypadku hasła powinny mieć co najmniej 16 znaków i generować losowo.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorowanie dzienników logowania i inspekcji

Organizacje powinny monitorować aktywność dziennika logowania i inspekcji z kont awaryjnych i wyzwalać powiadomienia do innych administratorów. Podczas monitorowania aktywności na kontach break glass, można sprawdzić, te konta są używane tylko do testowania lub rzeczywistych sytuacji awaryjnych. Usługi Azure Log Analytics można używać do monitorowania dzienników logowania i wyzwalania alertów e-mail i SMS do administratorów za każdym razem, gdy logujesz się na kontach typu break glass.

### <a name="prerequisites"></a>Wymagania wstępne

1. [Wysyłanie dzienników logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) do usługi Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Uzyskiwanie identyfikatorów obiektów kont szkła łamanego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta przypisanego do roli administratora użytkownika.
1. Wybierz**pozycję Użytkownicy** **usługi Azure Active Directory** > .
1. Wyszukaj konto typu break-glass i wybierz nazwę użytkownika.
1. Skopiuj i zapisz atrybut Identyfikator obiektu, aby można było go później użyć.
1. Powtórz poprzednie kroki dla drugiego konta break-glass.

### <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy za pomocą konta przypisanego do roli współautora monitorowania w usłudze Azure Monitor.
1. Wybierz **pozycję Wszystkie usługi**", wprowadź "analitykę dziennika" w obszarze Wyszukiwanie, a następnie wybierz pozycję Obszary **robocze usługi Log Analytics**.
1. Wybierz obszar roboczy.
1. W obszarze roboczym wybierz pozycję **Alerty** > **Nowa reguła alertu**.
    1. W obszarze **Zasób**sprawdź, czy subskrypcja jest tą, z którą chcesz skojarzyć regułę alertu.
    1. W **obszarze Warunek**wybierz pozycję **Dodaj**.
    1. Wybierz **pozycję Niestandardowe wyszukiwanie dzienników** w obszarze **Nazwa sygnału**.
    1. W obszarze **Kwerenda wyszukiwania**wprowadź następującą kwerendę, wstawiając identyfikatory obiektów dwóch kont ze szkła łamanego.
        > [!NOTE]
        > Dla każdego dodatkowego konta break glass, które chcesz dołączyć, dodaj do kwerendy kolejne "lub UserId == "ObjectGuid".For each additional break glass account you want to include, add another "or UserId == "ObjectGuid"" do kwerendy.

        ![Dodawanie identyfikatorów obiektów kont szkła przerwania do reguły alertu](./media/directory-emergency-access/query-image1.png)

    1. W obszarze **Logika alertu**wprowadź następujące informacje:

        - Na podstawie: Liczba wyników
        - Operator: Większy niż
        - Wartość progowa: 0

    1. W obszarze **Oceniane na podstawie**wybierz okres **(w minutach),** jak długo kwerenda ma być uruchamiana, oraz **częstotliwość (w minutach)** dla częstotliwości uruchamiania kwerendy. Częstotliwość powinna być mniejsza lub równa okresowi.

        ![logika alertu](./media/directory-emergency-access/alert-image2.png)

    1. Wybierz pozycję **Done** (Gotowe). Możesz teraz wyświetlić szacowany miesięczny koszt tego alertu.
1. Wybierz grupę akcji użytkowników, którzy mają być powiadamiani przez alert. Jeśli chcesz go utworzyć, zobacz [Tworzenie grupy akcji](#create-an-action-group).
1. Aby dostosować powiadomienie e-mail wysyłane do członków grupy akcji, wybierz akcje w obszarze **Dostosowywanie akcji**.
1. W obszarze **Szczegóły alertu**określ nazwę reguły alertu i dodaj opis opcjonalny.
1. Ustaw **poziom ważności** zdarzenia. Zaleca się ustawienie go na **Krytyczny(Sev 0)**.
1. W obszarze **Włącz regułę przy tworzeniu**pozostaw ją ustawioną jako **tak**.
1. Aby wyłączyć alerty na chwilę, zaznacz pole wyboru **Pomiń alerty** i wprowadź czas oczekiwania przed ponownym alertem, a następnie wybierz pozycję **Zapisz**.
1. Kliknij **pozycję Utwórz regułę alertu**.

### <a name="create-an-action-group"></a>Tworzenie grupy akcji

1. Wybierz **pozycję Utwórz grupę akcji**.

    ![tworzenie grupy akcji dla akcji powiadomień](./media/directory-emergency-access/action-group-image3.png)

1. Wprowadź nazwę grupy akcji i krótką nazwę.
1. Sprawdź subskrypcję i grupę zasobów.
1. W obszarze Typ akcji wybierz **pozycję E-mail/SMS/Push/Voice**.
1. Wprowadź nazwę akcji, taką jak **Powiadom administratora globalnego**.
1. Wybierz **typ akcji** jako **wiadomość e-mail/SMS/Wypychanie/głos**.
1. Wybierz **pozycję Edytuj szczegóły,** aby wybrać metody powiadamiania, które chcesz skonfigurować, i wprowadź wymagane informacje kontaktowe, a następnie wybierz **przycisk Ok,** aby zapisać szczegóły.
1. Dodaj dodatkowe akcje, które chcesz wyzwolić.
1. Kliknij przycisk **OK**.

## <a name="validate-accounts-regularly"></a>Regularnie sprawdzaj konta

Podczas szkolenia pracowników w celu korzystania z kont dostępu awaryjnego i sprawdzania poprawności kont dostępu awaryjnego, co najmniej wykonaj następujące czynności w regularnych odstępach czasu:

- Upewnij się, że pracownicy monitorujący zabezpieczenia są świadomi, że działanie sprawdzania konta jest w toku.
- Upewnij się, że proces awaryjnego złamania szkła do korzystania z tych kont jest udokumentowany i aktualny.
- Upewnij się, że administratorzy i funkcjonariusze ochrony, którzy mogą być zmuszeni do wykonania tych czynności w nagłych wypadkach, są szkoleni w tym procesie.
- Zaktualizuj poświadczenia konta, w szczególności hasła, dla kont dostępu awaryjnego, a następnie sprawdź, czy konta dostępu awaryjnego mogą logować się i wykonywać zadania administracyjne.
- Upewnij się, że użytkownicy nie zarejestrowali uwierzytelniania wieloskładnikowego ani samoobsługowego resetowania hasła (SSPR) na urządzeniu lub danych osobowych poszczególnych użytkowników. 
- Jeśli konta są zarejestrowane dla uwierzytelniania wieloskładnikowego na urządzeniu, do użycia podczas logowania lub aktywacji roli, upewnij się, że urządzenie jest dostępne dla wszystkich administratorów, którzy mogą potrzebować go używać w nagłych wypadkach. Sprawdź również, czy urządzenie może komunikować się za pośrednictwem co najmniej dwóch ścieżek sieciowych, które nie współużytkują wspólny tryb awarii. Na przykład urządzenie może komunikować się z Internetem zarówno za pośrednictwem sieci bezprzewodowej obiektu, jak i sieci dostawcy komórek.

Kroki te powinny być wykonywane w regularnych odstępach czasu i w przypadku kluczowych zmian:

- Co najmniej co 90 dni
- W przypadku ostatniej zmiany personelu IT, takiej jak zmiana pracy, odejście lub
- Po zmianie subskrypcji usługi Azure AD w organizacji

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](directory-admin-roles-secure.md)
- [Dodawanie użytkowników przy użyciu usługi Azure AD](../fundamentals/add-users-azure-active-directory.md) i [przypisywanie nowego użytkownika do roli Administratora globalnego](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Zarejestruj się w usłudze Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), jeśli jeszcze się nie zarejestrowałeś
- [Jak wymagać weryfikacji dwuetapowej dla użytkownika](../authentication/howto-mfa-userstates.md)
- [Konfigurowanie dodatkowych zabezpieczeń dla administratorów globalnych w usłudze Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), jeśli korzystasz z usługi Office 365
- [Rozpocznij przegląd dostępu administratorów globalnych](../privileged-identity-management/pim-how-to-start-security-review.md) i [przesuń istniejących administratorów globalnych do bardziej szczegółowych ról administratora](directory-assign-admin-roles.md)
