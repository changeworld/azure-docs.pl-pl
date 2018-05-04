---
title: Ochrona danych osobowych z Azure formanty tożsamościami i dostępem | Dokumentacja firmy Microsoft
description: Tożsamość platformy Azure i dostęp kontroluje pomocy ochrony danych osobowych i mogą być pomocne, zgodnych z ogólnych danych ochrony rozporządzenia (GDPR)
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: c0e7f2060f81812cd69ed1af0246287757985243
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory i uwierzytelniania wieloskładnikowego: ochrony danych osobowych z formantami tożsamościami i dostępem

Ten artykuł zawiera informacje i procedury, których można użyć do ochrony danych osobowych za pomocą usługi i funkcje zabezpieczeń uwierzytelniania usługi Azure Active Directory i usługi Multi-Factor. Informacje zawarte w tym artykule mogą być pomocne w przypadku wysiłków w celu zachowania zgodności z ogólnych danych ochrony rozporządzenia (GDPR).

## <a name="scenario"></a>Scenariusz

Firma rejs dużych, siedzibą w Stanach Zjednoczonych rozwija operacjach oferowanie trasy w DS, Adriatyku i Bałtyckiego mórz, jak również brytyjskich. Aby zapewnić obsługę tych działań, ustawił mniejszych rejs wiersze na podstawie we Włoszech, Niemczech, Dania i Zjednoczone Królestwo 

Przez firmę Microsoft Azure do przechowywania danych firmowych w chmurze. Dotyczy to również dane osobowe, takich jak nazwy, adresy, numery telefonów i informacje o karcie kredytowej z jej klientów globalnych. Zawiera także tradycyjnych informacje kadr, takie jak adresy, numery telefonów, numery identyfikacyjne podatku i inne informacje dotyczące pracowników firmy we wszystkich lokalizacjach. Wiersz rejs zachowuje również dużej bazy danych elementów członkowskich programu osób trzecich i lojalność zawierający informacje osobiste, aby śledzić relacje z bieżących i starszych klientów.

Pracownikom firmy dostępu do sieci w oddziałach firmy i podróży agentów na całym świecie mają dostęp do niektórych zasobów firmy.

## <a name="problem-statement"></a>Opis problemu

Firmy muszą chronić prywatność danych osobowych pracowników i klientów przed atakami chce używać złamany tożsamości w celu uzyskania dostępu. One również musi upewnij się, że dostęp do danych osobowych przez autoryzowanych użytkowników jest ograniczony tylko do tych wykonania swoich zadań potrzebują.

## <a name="company-goal"></a>Celem firmy

Firmy celem jest zapewnienie ścisłą kontrolę dostępu do danych osobowych. Istotne jest, że tożsamości użytkowników z dostępem do danych osobowych być chronione przez silnego uwierzytelniania. Zasady [najniższych uprawnień] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) musi zostać wymuszone tak, że istnieje tylko poziom dostępu potrzebują i nie więcej.

## <a name="solutions"></a>Rozwiązania

Microsoft Azure udostępnia narzędzia do zarządzania tożsamościami i dostępem ułatwiające kontrolę firmy, który ma dostęp do zasobów, które zawierają dane osobowe.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) zarządza tożsamości i kontroluje dostęp do platformy Azure, jak również innych lokalnych i innych zasobów w chmurze, danych i aplikacji. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) ułatwia administratorom Azure, aby zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji takich jak dane osobowe. Umożliwia im odnajdowanie, ograniczenia i monitorowanie tożsamości uprzywilejowanych oraz ich dostępu do zasobów i przypisz tymczasowy, kwalifikujących się użytkownikom prawa administracyjne just in Time (JIT). Zapewnia wgląd w tych, którzy mają uprawnienia administracyjne w usłudze AAD.

Działania związane ze stosowaniem AAD PIM obejmują:

- Włączanie katalogu Privileged Identity Management

- Przy użyciu pulpitu nawigacyjnego admin Privileged Identity Management, aby istotne informacje w skrócie

- Zarządzanie tożsamościami uprzywilejowanymi (Administratorzy) przez dodawanie lub usuwanie administratorów trwałych lub kwalifikujących się do każdej roli

- Konfigurowanie ustawień roli aktywacji

- Aktywacja ról

- Przeglądanie roli działania

#### <a name="how-do-i-enable-aad-pim"></a>Jak włączyć AAD PIM?

Aby rozpocząć korzystanie z usługi PIM dla katalogu, wykonaj następujące czynności:

1. Zaloguj się do portalu Azure jako administrator globalny katalogu.

2. Jeśli organizacja dysponuje więcej niż jednym katalogiem, wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure Portal. Wybierz katalog, w którym będziesz używać usługi Azure AD Privileged Identity Management.

3. Wybierz **więcej usług** i użyj **filtru** pole tekstowe do wyszukiwania Azure AD Privileged Identity Management.

4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

Po skonfigurowaniu usługi Azure AD Privileged Identity Management zobaczysz blok nawigacji pojawiający się przy każdym otwarciu aplikacji.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Aby uzyskać więcej informacji oraz instrukcje dotyczące wprowadzenie do korzystania z usługi PIM w usłudze AAD, zobacz [uruchomić przy użyciu usługi Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Kontrola dostępu oparta na rolach na platformie Azure

[Kontrola dostępu oparta na rolach Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) pomaga Azure administratorom zarządzanie dostępem do zasobów platformy Azure, należy włączyć udzielanie dostępu na podstawie przypisanej roli użytkownika. Możesz rozdzielenie obowiązków w zespole i udzielić tylko takiego dostępu użytkowników, grup i aplikacji, które są niezbędne do wykonywania swoich zadań.

Prawo dostępu oparte na rolach może zostać przydzielone użytkownikom za pomocą portalu Azure, narzędzia wiersza polecenia platformy Azure lub interfejsów API zarządzania platformy Azure.

Aby uzyskać więcej informacji dotyczących podstaw Azure RBAC, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure.](https://docs.microsoft.com/azure/role-based-access-control/overview)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Jak zarządzać Azure RBAC przy użyciu programu PowerShell?

Polecenia cmdlet programu PowerShell umożliwiają zarządzanie Azure RBAC, włączając następujące zadania zarządzania:

- Lista ról

- Zobacz, kto ma dostęp

- Udzielanie dostępu

- Usuń dostęp

- Tworzenie niestandardowej roli zabezpieczeń

- Pobierz akcji dla dostawcy zasobów

- Modyfikowanie niestandardowej roli zabezpieczeń

- Usunięcia niestandardowej roli zabezpieczeń

- Lista ról niestandardowych

Aby uzyskać instrukcje na temat zarządzania Azure RBAC przy użyciu programu PowerShell, zobacz [opartej na rolach Zarządzanie dostępu przy użyciu programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Usługa Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) to rozwiązanie weryfikacji dwuetapowej, która pomaga w zabezpieczaniu dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Oferuje ona silne uwierzytelnianie za pośrednictwem różnych metod weryfikacji, w tym weryfikacji w trakcie rozmowy telefonicznej albo przy użyciu wiadomości SMS lub aplikacji mobilnej.

Aby wdrożyć usługę MFA w chmurze Azure, należy najpierw go włączyć, a następnie Włącz weryfikację dwuetapową dla użytkowników.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Jak włączyć Azure, aby używać usługi MFA?

Jeśli użytkownicy mają licencje, które obejmują usługi Azure Multi-Factor Authentication, po prostu należy skonfigurować usługę Azure MFA na na podstawie grupy lub użytkownika. 

![Użytkownicy włączone uwierzytelnianie MFA](media/protect-personal-data-identity-access-controls/enable-mfa.png)

Jeśli obecnie nie masz licencji, które muszą przejść przez proces określania najodpowiedniejszy typ wdrożenia dla danego scenariusza. Można uruchomić, analizując artykuł [wybrać rozwiązania Azure Multi-Factor Autehntication](../active-directory/authentication/concept-mfa-whichversion.md). Jeśli zdecydujesz, należy utworzyć serwer Multi-Factor Authentication. Można uruchomić, wykonując następujące czynności:

1. Wybierz **usługi Active Directory** w portalu Azure (zalogowany jako administrator).

2. Wybierz **serwera usługi MFA**

3. Określ wartość limitu czasu. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. Kliknij pozycję **Zapisz**

W tym oknie również mieć możliwość pobrania serwera usługi MFA. Można uzyskać dodatkowe szczegóły dotyczące rozmiaru i Zaplanuj wdrożenie, przeglądając artykuł [wprowadzenie do korzystania z serwera usługi Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-deploy.md)

Aby uzyskać dodatkowe instrukcje na temat zarządzania dostawcy uwierzytelniania wieloskładnikowego, zobacz [wprowadzenie do korzystania z dostawcy usługi Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Jak włączyć weryfikację dwuetapową dla użytkowników?

Można wymusić weryfikacji dwuetapowej dla wszystkich logowania, lub można utworzyć zasady dostępu warunkowego, aby wymagać weryfikacji dwuetapowej tylko wtedy, gdy określone warunki są spełnione.

Włączanie usługi Azure MFA, zmieniając stanów użytkownika jest tradycyjne podejście, wymagająca weryfikacji dwuetapowej. Wszyscy użytkownicy, którzy włączeniu ma ten sam wymóg do przeprowadzenia weryfikacji dwuetapowej, za każdym razem zalogują się w. Włączenie użytkownik zastępuje wszystkie zasady dostępu warunkowego, które mogą mieć wpływ na tego użytkownika.

Włączanie usługi Azure MFA z zasad dostępu warunkowego jest bardziej elastyczne podejście wymagająca weryfikacji dwuetapowej. Można utworzyć zasady dostępu warunkowego, które są stosowane do grupy, a także poszczególnych użytkowników. O wysokim ryzyku grup można przydzielić więcej ograniczeń, niż niskiego ryzyka grup lub weryfikacji dwuetapowej mogą być wymagane tylko dla aplikacji w chmurze o wysokim ryzyku i pomijana dla nich niskiego ryzyka. Dostęp warunkowy jest jednak płatnych funkcji usługi Azure Active Directory.

Aby włączyć uwierzytelnianie wieloskładnikowe przez zmianę stanu użytkownika, wykonaj następujące czynności:

1. Zaloguj się do portalu Azure jako administrator.
2. Przejdź do **usługi Azure Active Directory \> użytkowników i grup \> wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. Znajdź użytkownika, którego chcesz włączyć dla usługi Azure MFA. Może być konieczna zmiana widoku w górnej części strony.
5. Zaznacz pole wyboru obok nazwy użytkownika.
6. Po prawej stronie w obszarze Szybkie kroki wybierz **włączyć**.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. Potwierdź wybór w otwartym oknie podręcznym.  Użytkownicy, dla których włączono usługę MFA, zostanie poproszony o zarejestrować przy następnym zalogowaniu.

Aby włączyć uwierzytelnianie wieloskładnikowe Azure za pomocą zasad dostępu warunkowego, wykonaj następujące czynności:

1. Zaloguj się do portalu Azure jako administrator.

2. Przejdź do **usługi Azure Active Directory \> dostępu warunkowego**.

3. Wybierz **nowe zasady**.

4. W obszarze **przypisania**, wybierz pozycję **użytkowników i grup**. Użyj **Include** i **wykluczyć** karty, aby określić, którzy użytkownicy i grupy będą zarządzane przez zasady.

5. W obszarze **przypisania**, wybierz pozycję **aplikacji w chmurze.** Wybierz **obejmują wszystkie aplikacje w chmurze**.
6.  W obszarze **dostęp do formantów**, wybierz pozycję **Grant**. Wybierz **wymusić uwierzytelnianie wieloskładnikowe**.
7.  Włącz **Włącz zasady** do **na** , a następnie wybierz **zapisać**.

Aby uzyskać informacje na temat konfigurowania ustawień usługi Azure MFA do konfigurowania alertów oszustwa, tworzenie jednorazowe obejście, użyj niestandardowe wiadomości głosowe, skonfigurować buforowanie, określ listę zaufanych adresów IP, tworzenie haseł aplikacji Włącz zapamiętywanie MFA dla urządzeń, które ufają użytkowników, a następnie wybierz metody weryfikacji, zobacz [Konfigurowanie ustawień uwierzytelnianie wieloskładnikowe Azure.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Kolejne kroki

- [Zabezpieczanie uprzywilejowanego dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Często zadawane pytania dotyczące usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Oparta na rolach kontrola dostępu do rozwiązywania problemów](https://docs.microsoft.com/azure/role-based-access-control/troubleshooting)

- [Ochronę tożsamości usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
