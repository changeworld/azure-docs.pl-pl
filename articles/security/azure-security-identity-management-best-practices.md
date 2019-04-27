---
title: Tożsamość i dostęp zabezpieczeń najlepsze rozwiązania platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań do zarządzania tożsamościami i dostępem do formantu za pomocą wbudowanych możliwości platformy Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f872c61ad0597d2307cd244668fdfc258f7a45cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611253"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management i kontrolę dostępu najlepsze rozwiązania dotyczące zabezpieczeń

Wiele należy wziąć pod uwagę tożsamości jako nową warstwę granicy zabezpieczeń, zajmuje ponad tę rolę z tradycyjnych multimediach sieci perspektywy. Tej ewolucji przestawnej podstawowego dla uwagi bezpieczeństwa i inwestycje pochodzą z faktu, że sieci co stają się coraz bardziej porowaty i obrony ten obwód nie może być tak skuteczne, jak one raz zostały przed rozbicie [BYOD ](https://aka.ms/byodcg) urządzeń i aplikacji w chmurze.

W tym artykule omawiane jest kolekcją usługi Azure identity management i najlepsze rozwiązania zabezpieczeń kontroli dostępu. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeniach z [usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md) i procesy, przez klientów, takich jak samodzielnie.

Dla każdego najlepszym rozwiązaniem jest Wyjaśnijmy:

* Co to jest najlepsze rozwiązanie
* Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
* W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze rozwiązanie polegające na
* Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

Ten artykuł został napisany w tym zarządzania tożsamościami platformy Azure i kontrolę dostępu, najlepsze rozwiązania dotyczące jest oparta na opinię konsensu i funkcji platformy Azure i zestawy funkcji występujących w czasie. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

Tożsamość platformy Azure zarządzania i dostęp do kontroli zabezpieczeń najlepsze rozwiązania omówione w tym artykule obejmują:

* Traktuj tożsamości jako głównych zabezpieczeń obwodowych
* Scentralizowanie zarządzania tożsamościami
* Włącz logowanie jednokrotne
* Włączanie dostępu warunkowego
* Włączanie zarządzania hasłami
* Wymuszaj weryfikację Multi-Factor Authentication dla użytkowników
* Korzystanie z kontroli dostępu opartej na rolach
* Niższe ryzyko narażenia kont uprzywilejowanych
* Kontrolowanie lokalizacji, gdzie znajdują się zasoby

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traktuj tożsamości jako głównych zabezpieczeń obwodowych

Wiele należy wziąć pod uwagę tożsamości jako podstawowy obwodu zabezpieczeń. Jest to przesunięcia od tradycyjnych skoncentrować się na zabezpieczenia sieci. Strefy sieci stają się coraz bardziej porowaty i obrony ten obwód nie może być co najmniej sprzed rozbicie [BYOD](https://aka.ms/byodcg) urządzeń i aplikacji w chmurze.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) to Azure rozwiązanie do zarządzania tożsamościami i dostępem. Usługa Azure AD to wielodostępna, oparta na chmurze zarządzania katalogami i tożsamościami Usługa zarządzania firmy Microsoft. Łączy ona podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochrony tożsamości w ramach jednego rozwiązania.

W poniższych sekcjach wymieniono najlepsze rozwiązania dotyczące zabezpieczeń tożsamości i dostępu za pomocą usługi Azure AD.

## <a name="centralize-identity-management"></a>Scentralizowanie zarządzania tożsamościami

W [tożsamości hybrydowej](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scenariusz, zaleca się zintegrować lokalne i katalogi w chmurze. Integracja umożliwia zespołowi IT do zarządzania kontami z jednej lokalizacji, niezależnie od tego, gdzie zostanie utworzone konto. Integracja pomaga również użytkownikom mu bardziej wydajnej pracy, zapewniając wspólną tożsamość do uzyskiwania dostępu do zasobów lokalnych i chmurze.


**Najlepsze rozwiązanie**: Integrowanie katalogów lokalnych z usługą Azure AD.  
**Szczegóły**: Użyj [program Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) do synchronizacji katalogu lokalnego z katalogiem w chmurze.

**Najlepsze rozwiązanie**: Włącz synchronizację skrótów haseł.  
**Szczegóły**: Synchronizacja skrótów haseł jest funkcją, używane do synchronizowania skrótów skrótów haseł użytkowników z lokalnym wystąpieniem usługi Active Directory na platformie Azure opartych na chmurze wystąpienia usługi AD.

Nawet jeśli użytkownik zdecyduje się za pomocą usług federacyjnych Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych w przypadku serwerów lokalnych się nie powieść lub stać się tymczasowo niedostępne. Umożliwia to użytkownikom na logowanie się do usługi przy użyciu tego samego hasła, używanego do logowania się w ich lokalnym wystąpieniem usługi Active Directory. Umożliwia również ochronę tożsamości do wykrywania przejęcie poświadczeń przez porównanie tych skrótów haseł z hasłami, znane złamać, jeśli użytkownik został użyty ich tego samego adresu e-mail i hasło w innych usługach, które nie są podłączone do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

Organizacje, które nie integrowanie tożsamości lokalnych za pomocą tożsamości usługi w chmurze mogą mieć większe obciążenie w zarządzaniu kontami. Ten narzut zwiększa prawdopodobieństwo popełnione błędy i naruszeń zabezpieczeń.

## <a name="enable-single-sign-on"></a>Włącz logowanie jednokrotne

W świecie urządzeń przenośnych i chmurze — chcesz włączyć logowanie jednokrotne (SSO) do urządzeń, aplikacji i usług z dowolnego miejsca, dzięki czemu użytkownicy mogą produktywności wszędzie tam, gdzie i kiedy. W przypadku wielu rozwiązań tożsamości do zarządzania staje się on administracyjne problem, nie tylko IT, ale także dla użytkowników, którzy musiał zapamiętywać wiele haseł.

Za pomocą tego samego rozwiązania tożsamości dla aplikacji i zasobów, można osiągnąć logowania jednokrotnego. I użytkowników przy użyciu tego samego zestawu poświadczeń do logowania i dostępu do zasobów, których potrzebują, czy zasoby są znajdujących się lokalnie lub w chmurze.

**Najlepsze rozwiązanie**: Włączanie logowania jednokrotnego.  
**Szczegóły**: Usługa Azure AD [rozszerza w lokalnej usłudze Active Directory](../active-directory/connect/active-directory-aadconnect.md) do chmury. Użytkownicy mogą używać ich podstawowego konta firmowego lub szkolnego dla urządzeń przyłączonych do domeny, zasobów firmy i wszystkie sieci web i aplikacji SaaS, które są im niezbędne do wykonywania ich zadań. Użytkownicy nie musieli pamiętać wiele zestawów nazwy użytkowników i hasła, a ich dostęp do aplikacji może być automatycznie aprowizowane (lub anulowanie aprowizacji) na podstawie ich członkostwa w grupach organizacji i ich stan jako pracownika. Możesz również kontrolować dostęp do aplikacji galerii lub własnych aplikacji lokalnych opracowanych i opublikowanych za pośrednictwem [serwera proxy aplikacji usługi Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Za pomocą logowania jednokrotnego, aby umożliwić użytkownikom dostęp do ich [aplikacji SaaS](../active-directory/active-directory-appssoaccess-whatis.md) oparte na swoje konto służbowe lub szkolne w usłudze Azure AD. Ma to zastosowanie nie tylko dla aplikacji SaaS firmy Microsoft, ale również innych aplikacji, takich jak [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) i [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Można skonfigurować aplikację do usługi Azure AD jako [tożsamości opartej na SAML](../active-directory/fundamentals-identity.md) dostawcy. Jako formant zabezpieczeń usługi Azure AD nie wystawia token, który pozwala użytkownikom na logowanie się do aplikacji, chyba że przyznano im dostęp za pośrednictwem usługi Azure AD. Możesz udzielić dostępu bezpośrednio lub za pośrednictwem grupy, użytkownicy są członkami.

Organizacje, które nie należy tworzyć wspólną tożsamość do ustalenia logowania jednokrotnego dla swoich użytkowników i aplikacji bardziej są widoczne dla scenariuszy, w której użytkownicy mają wiele haseł. Te scenariusze zwiększa prawdopodobieństwo użytkowników ponowne używanie haseł lub przy użyciu słabe hasła.

## <a name="turn-on-conditional-access"></a>Włączanie dostępu warunkowego

Użytkownicy mogą uzyskiwać dostęp do zasobów organizacji przy użyciu różnych urządzeń i aplikacji z dowolnego miejsca. Jako IT administrator chcesz upewnić się, że te urządzenia spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Po prostu koncentrujących się na kto ma dostęp do zasobu nie wystarcza już.

Równoważyć, zabezpieczenia i wydajność, należy wziąć pod uwagę sposób dostępu do zasobu przed może podjąć decyzję kontroli dostępu. Funkcja dostępu warunkowego w usłudze Azure AD pozwala sprostać temu wymaganiu. Przy użyciu dostępu warunkowego można wprowadzić decyzji dotyczących kontroli automatyczny dostęp do uzyskiwania dostępu do Twoich aplikacji w chmurze, które są oparte na warunkach.

**Najlepsze rozwiązanie**: Zarządzanie i kontrola dostępu do zasobów firmy.  
**Szczegóły**: Konfigurowanie usługi Azure AD [dostępu warunkowego](../active-directory/active-directory-conditional-access-azure-portal.md) bazujący na grupie, lokalizacji i ważności aplikacji dla aplikacji SaaS i aplikacje platformy Azure połączonej usługi AD.

## <a name="enable-password-management"></a>Włączanie zarządzania hasłami

Jeśli występuje wielu dzierżawców lub chcesz umożliwić użytkownikom [resetować swoje hasła](../active-directory/active-directory-passwords-update-your-own-password.md), jest ważne, użycie zasad zabezpieczeń odpowiednich aby zapobiec nadużyciu.

**Najlepsze rozwiązanie**: Konfigurowanie samoobsługowego resetowania haseł (SSPR) dla użytkowników.  
**Szczegóły**: Używaj usługi Azure AD [samoobsługowego resetowania haseł](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funkcji.

**Najlepsze rozwiązanie**: Monitor jak lub jeśli samoobsługowego resetowania HASEŁ jest rzeczywiście używane.  
**Szczegóły**: Monitorowanie użytkowników, którzy rejestrowania przy użyciu usługi Azure AD [raport aktywności rejestracji resetowania hasła](../active-directory/active-directory-passwords-get-insights.md). Funkcji raportowania przez usługę Azure AD pomaga w uzyskaniu odpowiedzi na pytania za pomocą wbudowanych raportów. Jeśli masz odpowiednio licencję, można również utworzyć niestandardowe zapytania.

## <a name="enforce-multi-factor-verification-for-users"></a>Wymuszaj weryfikację Multi-Factor Authentication dla użytkowników

Firma Microsoft zaleca żądania weryfikacji dwuetapowej dla wszystkich użytkowników. Obejmuje to administratorom oraz innym osobom w organizacji, którzy mogą mieć znaczący wpływ w przypadku naruszenia zabezpieczeń konta (na przykład dyrektorów finansowych).

Istnieje wiele opcji do wymagania weryfikacji dwuetapowej. Najlepszym rozwiązaniem dla Ciebie zależy od tego, cele, wersji usługi Azure AD, do którego jest uruchamiany i program licencjonowania. Zobacz [jak, które wymuszają weryfikację dwuetapową dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md) Aby określić najbardziej odpowiedni dla Ciebie. Zobacz [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) i [usługi Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ceny strony, aby uzyskać więcej informacji o licencjach i cenach.

Poniżej przedstawiono opcje oraz włączenie weryfikacji dwuetapowej korzyści:

**Opcja 1**: [Włączanie uwierzytelniania wieloskładnikowego przez zmianę stanu użytkownika](../active-directory/authentication/howto-mfa-userstates.md).   
**Korzyści z**: Jest to tradycyjne metody do wymagania weryfikacji dwuetapowej. Współdziała ona z obu [usługi Azure Multi-Factor Authentication w chmurze i serwera Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Za pomocą tej metody wymaga od użytkowników przeprowadzenia weryfikacji dwuetapowej, za każdym razem, gdy zalogują się w i zastąpień zasad dostępu warunkowego.

**Opcja 2**: [Włączanie uwierzytelniania wieloskładnikowego za pomocą zasad dostępu warunkowego](../active-directory/authentication/howto-mfa-getstarted.md).
**Korzyści z**: Ta opcja pozwala na monitowanie o weryfikacji dwuetapowej w określonych warunkach przy użyciu [dostępu warunkowego](../active-directory/active-directory-conditional-access-azure-portal.md). Określone warunki można logowania użytkowników z różnych lokalizacji, urządzeń niezaufanych lub aplikacje, które są uważane za ryzykowne. Definiowanie określone warunki, których wymagają weryfikacji dwuetapowej pozwala uniknąć, stałe monitowania dla użytkowników, które mogą być nieprzyjemnych komfortu.

Jest to najbardziej elastyczny sposób, aby włączyć weryfikację dwuetapową dla użytkowników. Włączenie zasad dostępu warunkowego działa tylko w przypadku usługi Azure Multi-Factor Authentication w chmurze i jest funkcją premium usługi Azure AD. Można znaleźć więcej informacji na temat tej metody w [wdrażanie oparte na chmurze usługi Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**Opcja 3**: Włączanie uwierzytelniania wieloskładnikowego za pomocą zasad dostępu warunkowego poprzez ocenę ryzyka użytkownika i logowania [usługi Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Korzyści z**: Ta opcja pozwala na:

- Wykrywanie potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji.
- Skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane działania, that are related to tożsamości w organizacji.
- Zbadaj podejrzanych zdarzeń i podejmij odpowiednią akcję, aby je rozwiązać.

Ta metoda używa usługi Azure AD Identity Protection ryzyka oceny w celu określenia, czy Weryfikacja dwuetapowa była wymagana na podstawie użytkownika i ryzyko logowania dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencji usługi Azure Active Directory P2. Można znaleźć więcej informacji na temat tej metody w [usługi Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Opcja 1, włączenie uwierzytelniania wieloskładnikowego przez zmianę stanu użytkownika, zastępuje zasady dostępu warunkowego. Ponieważ opcje 2 i 3, użyj zasad dostępu warunkowego, nie możesz użyć opcji 1 z nimi.

Organizacje, które nie dodają dodatkowe warstwy ochrony tożsamości, takie jak weryfikacja dwuetapowa są bardziej podatne na ataki kradzieży poświadczeń. Ataku kradzieży poświadczeń może prowadzić do naruszenia zabezpieczeń danych.

## <a name="use-role-based-access-control-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach (RBAC)

Ograniczanie dostępu na podstawie [trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. Możesz użyć [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) można przypisać uprawnienia dla użytkowników, grup i aplikacji w określonym zakresie. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób.

Możesz użyć [wbudowanej RBAC](../role-based-access-control/built-in-roles.md) ról na platformie Azure, aby przypisać uprawnienia do użytkowników. Organizacje, które nie wymusza kontrola dostępu do danych przy użyciu funkcji, takich jak RBAC może podając więcej uprawnień niż jest to konieczne do użytkowników. Może to prowadzić do naruszenia zabezpieczeń danych, umożliwiając użytkownikom dostęp do niektórych typów danych (na przykład duże znaczenie biznesowe), które nie mają.

## <a name="lower-exposure-of-privileged-accounts"></a>Niższe ryzyko narażenia kont uprzywilejowanych

Zabezpieczanie uprzywilejowanego dostępu jest pierwszym ważnym krokiem do ochrony zasobów biznesowych. Minimalizacja liczby osób, które mają dostęp do zabezpieczania informacji lub zasobów zmniejsza ryzyko złośliwy użytkownik uzyska dostęp lub autoryzowany użytkownik przypadkowo wpływających na poufnych zasobów.

Uprzywilejowane konta są kontami, służących do administrowania i zarządzania systemami IT. Ataki Cybernetyczne celem tych kont w celu uzyskania dostępu do danych i systemy w organizacji. Do zabezpieczania uprzywilejowanego dostępu, powinien izolowanie kont i systemami z całej ryzyko narażenia na złośliwy użytkownik.

Firma Microsoft zaleca tworzenie, a następnie postępuj zgodnie z planem w celu zabezpieczenia uprzywilejowanego dostępu przed atakami cybernetycznymi. Aby uzyskać informacje o tworzeniu szczegółowych planu przejścia do zabezpieczania tożsamości i dostępu, które są zarządzane lub nie zwróciło w usłudze Azure AD, Microsoft Azure, Office 365 i innych usług w chmurze, zapoznaj się [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w Usługa Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Poniżej znajduje się podsumowanie najlepszych rozwiązań w [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Najlepsze rozwiązanie**: Zarządzanie, sterowanie i monitorowanie dostępu do kont uprzywilejowanych.   
**Szczegóły**: Włącz [usługi Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Po włączeniu Privileged Identity Management otrzymasz powiadomienie e-mail wiadomości do uprzywilejowanego dostępu zmiany roli. Te powiadomienia stanowić wczesne ostrzeżenie, po dodaniu dodatkowych użytkowników do wysoce uprzywilejowanych ról w katalogu.

**Najlepsze rozwiązanie**: Identyfikowanie i klasyfikowanie kont, które znajdują się w wysoce uprzywilejowanych ról.   
**Szczegóły**: Po włączeniu usługi Azure AD Privileged Identity Management, należy wyświetlić użytkowników, którzy są w administratorem globalnym, administratorem ról uprzywilejowanych i inne role o wysokim poziomie uprawnień. Usuń wszelkie konta, które nie są już potrzebne w ramach tych ról, a skategoryzować pozostałe konta, które są przypisane do ról administratora:

- Indywidualnie przypisane do użytkowników administracyjnych i mogą być używane do celów innych niż administracyjne (na przykład osobistego adresu e-mail)
- Indywidualnie przypisane do użytkowników administracyjnych i przeznaczone wyłącznie do celów administracyjnych
- Współużytkowane przez wielu użytkowników
- Dla scenariuszy dostępu awaryjnego
- W przypadku zautomatyzowanych skryptów
- Dla użytkowników zewnętrznych

**Najlepsze rozwiązanie**: Implementowanie "just in time" (JIT) dostęp do dodatkowo zmniejszyć czas narażenia uprawnień i lepszy wgląd w używanie kont uprzywilejowanych.   
**Szczegóły**: Usługa Azure AD Privileged Identity Management zapewnia następujące możliwości:

- Ogranicz użytkowników do uwzględnienia tylko swoje uprawnienia JIT.
- Przypisz role skróconą czasu trwania z pewnością automatycznie odwołać uprawnienia.

**Najlepsze rozwiązanie**: Należy zdefiniować co najmniej dwóch kont dostępu awaryjnego.   
**Szczegóły**: Kont dostępu awaryjnego pomagają organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory. Te konta są wysoce uprzywilejowane i nie są przypisane do konkretnych osób. Kont dostępu awaryjnego są ograniczone do scenariuszy, w których nie można używać normalnego konta z uprawnieniami administracyjnymi. Organizacje muszą ograniczyć użycie konta dostępu awaryjnego na potrzeby ilość czasu.

Ocena kont, które są przypisane lub kwalifikuje się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze przy użyciu `*.onmicrosoft.com` domeny (przeznaczonych do dostępu awaryjnego), utwórz je. Aby uzyskać więcej informacji zobacz Zarządzanie kontami administracyjnymi z dostępem awaryjnym w usłudze Azure AD.

**Najlepsze rozwiązanie**: Włącz uwierzytelnianie wieloskładnikowe i Zarejestruj wszystkie inne konta o wysokim poziomie uprawnień administratora inne niż federacyjne pojedynczego użytkownika.  
**Szczegóły**: Wymaga usługi Azure Multi-Factor Authentication podczas logowania dla wszystkich poszczególnych użytkowników, którzy są trwale przypisane do przynajmniej jednej z ról administratora usługi Azure AD: administratorem globalnym, administratorem ról uprzywilejowanych, administrator usługi Exchange Online i SharePoint Online Administrator. Użyj przewodnika, aby włączyć [uwierzytelnianie wieloskładnikowe dla kont administratorów](../active-directory/authentication/howto-mfa-userstates.md) i upewnij się, że tych użytkowników [zarejestrowany](https://aka.ms/mfasetup).

**Najlepsze rozwiązanie**: Wykonaj kroki, aby można było zmniejszenie narażenia na najczęściej używane techniki zaatakowane.  
**Szczegóły**: [Zidentyfikować konta Microsoft w rolach administracyjnych, które muszą zostać przełączone do konta służbowego.](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Upewnij się, oddzielnych kont użytkowników i przekazywania dla konta administratora globalnego poczty](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Upewnij się, że niedawno zmieniono hasła konta z uprawnieniami administracyjnymi](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Włączanie synchronizacji skrótów haseł](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników pełniących uprzywilejowane wszystkie role, a także ujawnionych użytkowników](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Uzyskaj ocenę Secure Office 365 (w przypadku korzystania z usługi Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Przejrzyj wskazówki dotyczące zabezpieczeń i zgodności usługi Office 365 (w przypadku korzystania z usługi Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurowanie, monitorowanie aktywności usługi Office 365 (w przypadku korzystania z usługi Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Ustanowić właścicieli plan odpowiedzi na zdarzenia/awaryjnego](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Zabezpieczanie uprzywilejowanego lokalne konta z uprawnieniami administracyjnymi](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Jeśli nie bezpieczeństwa dostępu uprzywilejowanego, może się okazać ma zbyt wielu użytkowników w rolach o wysokim poziomie uprawnień i są bardziej narażone na ataki. Uczestników złośliwych działań, takich jak ataki cybernetyczne, często docelowe konta administratora i inne elementy uprzywilejowanego dostępu do uzyskania dostępu do poufnych danych i systemy przy użyciu kradzieży poświadczeń.

## <a name="control-locations-where-resources-are-created"></a>Kontrolowanie lokalizacji, w którym są tworzone zasoby

Bardzo ważne jest włączenie operatorom chmury do wykonywania zadań podczas uniemożliwia istotne konwencje, które są wymagane do zarządzania zasobami w Twojej organizacji. Organizacje, które ma być sterowana lokalizacje, w którym są tworzone zasoby powinny ciężko kodu te lokalizacje.

Możesz użyć [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) utworzenia zasad zabezpieczeń, których definicje opisu akcji lub zasobów, które w szczególności zostaną odrzucone. Możesz przypisać te definicje zasad w określonym zakresie, np. subskrypcji, grupy zasobów lub poszczególnych zasobów.

> [!NOTE]
> Zasady zabezpieczeń nie są takie same jak RBAC. Używają faktycznie RBAC, można zezwolić użytkownikom na tworzenie tych zasobów.
>
>

Organizacje, które nie są kontrolowania, jak zasoby są tworzone są bardziej podatne na użytkowników, którzy mogą gróźb usługi, tworząc więcej zasobów niż jest to wymagane. Wzmacnianie ochrony procesu tworzenia zasobu jest ważnym krokiem do zabezpieczania wielodostępne scenariusza.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktywne monitorowanie dla podejrzanych działań

Aktywną tożsamość systemu monitorowania może szybko wykrywać podejrzane zachowania i wyzwolić alert w celu bliższego zbadania problemu. W poniższej tabeli przedstawiono dwie możliwości usługi Azure AD, które mogą pomóc organizacjom monitorować ich tożsamości:

**Najlepsze rozwiązanie**: Posiada metody do identyfikacji:

- Spróbuje się zalogować [bez śledzone](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Atak siłowy](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataków na określone konto.
- Próbuje zalogować się w wielu lokalizacjach.
- Logowania z [zainfekowanych urządzeń](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Podejrzane adresy IP.

**Szczegóły**: Użyj usługi Azure AD Premium [raporty anomalii](../active-directory/active-directory-view-access-usage-reports.md). Dysponować procesów i procedur dla administratorów IT uruchomić te raporty, codziennie lub na żądanie (zwykle w scenariuszach reagowania na zdarzenia).

**Najlepsze rozwiązanie**: Mają aktywne system monitorowania, powiadamia o zagrożenia, który można dostosować poziom ryzyka (wysoki, średni lub niski) do potrzeb biznesowych.   
**Szczegóły**: Użyj [usługi Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), który oznacza bieżącą zagrożenie na swój własny pulpit nawigacyjny, a następnie wysyła codzienne podsumowanie powiadomienia pocztą e-mail. Aby lepiej chronić tożsamości w organizacji, można skonfigurować opartych na ryzykach zasady, które automatycznie reagują na wystąpienie wykryte problemy po osiągnięciu poziomu określonego ryzyka.

Organizacje, które nie aktywnie monitoruje swoje systemy tożsamości są na ryzyko, że poświadczenia użytkownika naruszenia zabezpieczeń. Bez wiedzy czy podejrzane działania odbywają się za pomocą tych poświadczeń organizacje nie można rozwiązać tego typu zagrożenia.

## <a name="next-step"></a>Następny krok

Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.
