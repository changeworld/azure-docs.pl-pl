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
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2b57ec7727e8f5b648bcb97e5fae26c63724411c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127206"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management i kontrolę dostępu najlepsze rozwiązania dotyczące zabezpieczeń
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
* Zarządzanie dzierżawami połączonych
* Włącz logowanie jednokrotne
* Włączanie dostępu warunkowego
* Włączanie zarządzania hasłami
* Wymuszaj weryfikację Multi-Factor Authentication dla użytkowników
* Korzystanie z kontroli dostępu opartej na rolach
* Niższe ryzyko narażenia kont uprzywilejowanych
* Kontrolowanie lokalizacji, gdzie znajdują się zasoby
* Używaj usługi Azure AD do uwierzytelniania magazynu

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traktuj tożsamości jako głównych zabezpieczeń obwodowych

Wiele należy wziąć pod uwagę tożsamości jako podstawowy obwodu zabezpieczeń. Jest to przesunięcia od tradycyjnych skoncentrować się na zabezpieczenia sieci. Strefy sieci stają się coraz bardziej porowaty i obrony ten obwód nie może być co najmniej sprzed rozbicie [BYOD](https://aka.ms/byodcg) urządzeń i aplikacji w chmurze.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) to Azure rozwiązanie do zarządzania tożsamościami i dostępem. Usługa Azure AD to wielodostępna, oparta na chmurze zarządzania katalogami i tożsamościami Usługa zarządzania firmy Microsoft. Łączy ona podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochrony tożsamości w ramach jednego rozwiązania.

W poniższych sekcjach wymieniono najlepsze rozwiązania dotyczące zabezpieczeń tożsamości i dostępu za pomocą usługi Azure AD.

## <a name="centralize-identity-management"></a>Scentralizowanie zarządzania tożsamościami

W [tożsamości hybrydowej](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scenariusz, zaleca się zintegrować lokalne i katalogi w chmurze. Integracja umożliwia zespołowi IT do zarządzania kontami z jednej lokalizacji, niezależnie od tego, gdzie zostanie utworzone konto. Integracja pomaga również użytkownikom mu bardziej wydajnej pracy, zapewniając wspólną tożsamość do uzyskiwania dostępu do zasobów lokalnych i chmurze.

**Najlepsze rozwiązanie**: Ustanowić pojedynczego wystąpienia usługi Azure AD. Spójność i pojedynczego źródła autorytatywnego spowoduje zwiększenie przejrzystości i ograniczyć zagrożenie bezpieczeństwa, z błędami ludzkimi i złożoność konfiguracji.
**Szczegóły**: Wyznacz pojedynczej usłudze Azure AD katalogu jako autorytatywne źródło dla kont firmowych, jak i organizacji.

**Najlepsze rozwiązanie**: Integrowanie katalogów lokalnych z usługą Azure AD.  
**Szczegóły**: Użyj [program Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) do synchronizacji katalogu lokalnego z katalogiem w chmurze.

> [!Note]
> Istnieją [czynniki mające wpływ na wydajność programu Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Upewnij się, że program Azure AD Connect ma wystarczające moce przerobowe, aby zachować gorszych wynikach w przypadku systemów z referują zabezpieczenia i wydajność. Należy przestrzegać dużych lub złożonych organizacji (organizacji inicjowania obsługi więcej niż 100 000 obiektów) [zalecenia](../active-directory/hybrid/whatis-hybrid-identity.md) do optymalizacji ich implementacji usługi Azure AD Connect.

**Najlepsze rozwiązanie**: Nie Synchronizuj kont do usługi Azure AD, które mają wysokim poziomem uprawnień w ramach istniejącego wystąpienia usługi Active Directory.
**Szczegóły**: Nie należy zmieniać domyślne [konfiguracji programu Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) który odfiltrowuje tych kont. Ta konfiguracja zmniejsza ryzyko przeciwników przestawianie z chmury do zasobów w środowisku lokalnym, (które można utworzyć zdarzenia główne).

**Najlepsze rozwiązanie**: Włącz synchronizację skrótów haseł.  
**Szczegóły**: Synchronizacja skrótów haseł jest funkcją do synchronizacji skrótów haseł użytkowników z lokalnym wystąpieniem usługi Active Directory na platformie Azure opartych na chmurze wystąpienia usługi AD. Pomaga chronić przed ujawnionymi poświadczeniami jest odtwarzany atakami poprzedniego tej synchronizacji.

Nawet jeśli użytkownik zdecyduje się za pomocą usług federacyjnych Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych w przypadku serwerów lokalnych się nie powieść lub stać się tymczasowo niedostępne. Tej synchronizacji umożliwia użytkownikom na logowanie się do usługi przy użyciu tego samego hasła, używanego do logowania się w ich lokalnym wystąpieniem usługi Active Directory. Umożliwia również ochronę tożsamości do wykrywania przejęciem poświadczeń, porównując skrótów synchronizowanych haseł za pomocą haseł znane złamać, jeśli użytkownik został użyty w innych usługach, które nie są podłączone do usługi Azure AD tego samego adresu e-mail i hasło.

Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Najlepsze rozwiązanie**: W przypadku nowych wdrożeń aplikacji użyj usługi Azure AD do uwierzytelniania.
**Szczegóły**: Aby zapewnić obsługę uwierzytelniania, należy użyć poprawne możliwości:

  - Usługa Azure AD dla pracowników
  - [Usługa Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) dla użytkowników-gości i partnerom zewnętrznym
  - [Usługa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) do kontrolowania, jak klienci tworzą konta, zaloguj się i zarządzają swoimi profilami podczas korzystania z usługi aplikacji

Organizacje, które nie integrowanie tożsamości lokalnych za pomocą tożsamości usługi w chmurze mogą mieć większe obciążenie w zarządzaniu kontami. Ten narzut zwiększa prawdopodobieństwo popełnione błędy i naruszeń zabezpieczeń.

> [!Note]
> Musisz wybrać katalogi, które konta będą znajdować się w i czy używana administracyjnej stacji roboczej jest zarządzane przez nowych usług w chmurze lub istniejące procesy. Użycie istniejącej zarządzania i tożsamości inicjowania obsługi procesów umożliwia zmniejszenie zagrożenia związane, ale można również utworzyć ryzyko osobie atakującej naruszenie konta w środowisku lokalnym i przestawianie w chmurze. Można użyć różnych strategii dla różnych ról (na przykład Administratorzy i administratorzy jednostek biznesowych). Dostępne są dwie opcje. Pierwsza opcja jest do utworzenia konta platformy Azure AD, które nie są synchronizowane z wystąpieniem usługi Active Directory w środowisku lokalnym. Dołącz do stacji roboczej administratora do usługi Azure AD, w którym możesz zarządzać i stosowanie poprawek dzięki usłudze Microsoft Intune. Drugą opcją jest synchronizowanie z wystąpieniem usługi Active Directory w środowisku lokalnym za pomocą istniejących kont administratora. Użyj istniejącego stacje robocze w domenie usługi Active Directory do zarządzania i zabezpieczeń.

## <a name="manage-connected-tenants"></a>Zarządzanie dzierżawami połączonych
Twoja organizacja zabezpieczeń wymaga widoczność w celu dokonania oceny ryzyka i określić, czy przestrzeganie zasad firmy i wszelkie wymagania prawne. Należy upewnić się, że Twoja organizacja security ma wgląd we wszystkie subskrypcje podłączone do środowiska produkcyjnego i sieci (za pośrednictwem [usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). A [globalnego administratora/Administrator firmy](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) w usłudze Azure AD podnieść poziom konta dostępu do [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) roli i wyświetlić wszystkie subskrypcje i grup zarządzanych przez podłączone do danego środowiska.

Zobacz [podniesienie poziomu dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure i grup zarządzania](../role-based-access-control/elevate-access-global-admin.md) aby upewnić się, że możesz i swojej grupy zabezpieczeń mogą wyświetlać wszystkich subskrypcji lub grupy zarządzania podłączone do danego środowiska. Po po ocenie ryzyka, należy usunąć ten podwyższonego poziomu dostępu.

## <a name="enable-single-sign-on"></a>Włącz logowanie jednokrotne

W świecie urządzeń przenośnych i chmurze — chcesz włączyć logowanie jednokrotne (SSO) do urządzeń, aplikacji i usług z dowolnego miejsca, dzięki czemu użytkownicy mogą produktywności wszędzie tam, gdzie i kiedy. W przypadku wielu rozwiązań tożsamości do zarządzania staje się on administracyjne problem, nie tylko IT, ale także dla użytkowników, którzy musiał zapamiętywać wiele haseł.

Za pomocą tego samego rozwiązania tożsamości dla aplikacji i zasobów, można osiągnąć logowania jednokrotnego. I użytkowników przy użyciu tego samego zestawu poświadczeń do logowania i dostępu do zasobów, których potrzebują, czy zasoby są znajdujących się lokalnie lub w chmurze.

**Najlepsze rozwiązanie**: Włączanie logowania jednokrotnego.  
**Szczegóły**: Usługa Azure AD [rozszerza w lokalnej usłudze Active Directory](../active-directory/connect/active-directory-aadconnect.md) do chmury. Użytkownicy mogą używać ich podstawowego konta firmowego lub szkolnego dla urządzeń przyłączonych do domeny, zasobów firmy i wszystkie sieci web i aplikacji SaaS, które są im niezbędne do wykonywania ich zadań. Użytkownicy nie musieli pamiętać wiele zestawów nazwy użytkowników i hasła, a ich dostęp do aplikacji może być automatycznie aprowizowane (lub anulowanie aprowizacji) na podstawie ich członkostwa w grupach organizacji i ich stan jako pracownika. Możesz również kontrolować dostęp do aplikacji galerii lub własnych aplikacji lokalnych opracowanych i opublikowanych za pośrednictwem [serwera proxy aplikacji usługi Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Za pomocą logowania jednokrotnego, aby umożliwić użytkownikom dostęp do ich [aplikacji SaaS](../active-directory/active-directory-appssoaccess-whatis.md) oparte na swoje konto służbowe lub szkolne w usłudze Azure AD. Ma to zastosowanie nie tylko dla aplikacji SaaS firmy Microsoft, ale również innych aplikacji, takich jak [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) i [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Można skonfigurować aplikację do usługi Azure AD jako [tożsamości opartej na SAML](../active-directory/fundamentals-identity.md) dostawcy. Jako formant zabezpieczeń usługi Azure AD nie wystawia token, który pozwala użytkownikom na logowanie się do aplikacji, chyba że przyznano im dostęp za pośrednictwem usługi Azure AD. Możesz udzielić dostępu bezpośrednio lub za pośrednictwem grupy, użytkownicy są członkami.

Organizacje, które nie należy tworzyć wspólną tożsamość do ustalenia logowania jednokrotnego dla swoich użytkowników i aplikacji bardziej są widoczne dla scenariuszy, w której użytkownicy mają wiele haseł. Te scenariusze zwiększa prawdopodobieństwo użytkowników ponowne używanie haseł lub przy użyciu słabe hasła.

## <a name="turn-on-conditional-access"></a>Włączanie dostępu warunkowego

Użytkownicy mogą uzyskiwać dostęp do zasobów organizacji przy użyciu różnych urządzeń i aplikacji z dowolnego miejsca. Jako administrator IT chcesz upewnić się, że te urządzenia spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Po prostu koncentrujących się na kto ma dostęp do zasobu nie wystarcza już.

Równoważyć, zabezpieczenia i wydajność, należy wziąć pod uwagę sposób dostępu do zasobu przed dokonaniem decyzji dotyczących kontroli dostępu. Przy użyciu dostępu warunkowego usługi Azure AD można rozwiązać tego wymagania. Przy użyciu dostępu warunkowego można wprowadzać automatyczny dostęp decyzji dotyczących kontroli na podstawie warunków do uzyskiwania dostępu do aplikacji w chmurze.

**Najlepsze rozwiązanie**: Zarządzanie i kontrola dostępu do zasobów firmy.  
**Szczegóły**: Konfigurowanie usługi Azure AD [dostępu warunkowego](../active-directory/active-directory-conditional-access-azure-portal.md) bazujący na grupie, lokalizacji i ważności aplikacji dla aplikacji SaaS i aplikacje platformy Azure połączonej usługi AD.

**Najlepsze rozwiązanie**: Zablokować starsze protokoły.
**Szczegóły**: Osoby atakujące luki w starszych protokołów codziennie, szczególnie w przypadku złamania osłony hasła. Konfigurowanie dostępu warunkowego, aby zablokować starsze protokoły. Zobacz wideo [usługi Azure AD: Porady](https://www.youtube.com/watch?v=wGk0J4z90GI) Aby uzyskać więcej informacji.

## <a name="enable-password-management"></a>Włączanie zarządzania hasłami

Jeśli występuje wielu dzierżawców lub chcesz umożliwić użytkownikom [resetować swoje hasła](../active-directory/active-directory-passwords-update-your-own-password.md), jest ważne, użycie zasad zabezpieczeń odpowiednich aby zapobiec nadużyciu.

**Najlepsze rozwiązanie**: Konfigurowanie samoobsługowego resetowania haseł (SSPR) dla użytkowników.  
**Szczegóły**: Używaj usługi Azure AD [samoobsługowego resetowania haseł](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funkcji.

**Najlepsze rozwiązanie**: Monitor jak lub jeśli samoobsługowego resetowania HASEŁ jest rzeczywiście używane.  
**Szczegóły**: Monitorowanie użytkowników, którzy rejestrowania przy użyciu usługi Azure AD [raport aktywności rejestracji resetowania hasła](../active-directory/active-directory-passwords-get-insights.md). Funkcji raportowania przez usługę Azure AD pomaga w uzyskaniu odpowiedzi na pytania za pomocą wbudowanych raportów. Jeśli masz odpowiednio licencję, można również utworzyć niestandardowe zapytania.

**Najlepsze rozwiązanie**: Rozszerz zasady haseł oparte na chmurze do infrastruktury lokalnej.
**Szczegóły**: Rozszerz zasady haseł w organizacji, wykonując tych samych kontroli zmian haseł w środowisku lokalnym, podobnie jak w przypadku zmiany haseł oparte na chmurze. Zainstaluj [ochrona za pomocą hasła usługi Azure AD](../active-directory/authentication/concept-password-ban-bad.md) Windows Server Active Directory agentów lokalnych można rozszerzyć listy zakazanych haseł w istniejącej infrastrukturze. Użytkownicy i Administratorzy, którzy zmienić, ustawiania lub resetowania hasła, które są wymagane do wykonania tych samych zasad haseł jako użytkowników tylko w chmurze, w środowisku lokalnym.

## <a name="enforce-multi-factor-verification-for-users"></a>Wymuszaj weryfikację Multi-Factor Authentication dla użytkowników

Firma Microsoft zaleca żądania weryfikacji dwuetapowej dla wszystkich użytkowników. Obejmuje to administratorom oraz innym osobom w organizacji, którzy mogą mieć znaczący wpływ w przypadku naruszenia zabezpieczeń konta (na przykład dyrektorów finansowych).

Istnieje wiele opcji do wymagania weryfikacji dwuetapowej. Najlepszym rozwiązaniem dla Ciebie zależy od tego, cele, wersji usługi Azure AD, do którego jest uruchamiany i program licencjonowania. Zobacz [jak, które wymuszają weryfikację dwuetapową dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md) Aby określić najbardziej odpowiedni dla Ciebie. Zobacz [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) i [usługi Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ceny strony, aby uzyskać więcej informacji o licencjach i cenach.

Poniżej przedstawiono opcje oraz włączenie weryfikacji dwuetapowej korzyści:

**Opcja 1**: [Włączanie uwierzytelniania wieloskładnikowego przez zmianę stanu użytkownika](../active-directory/authentication/howto-mfa-userstates.md).   
**Korzyści z**: Jest to tradycyjne metody do wymagania weryfikacji dwuetapowej. Współdziała ona z obu [usługi Azure Multi-Factor Authentication w chmurze i serwera Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Za pomocą tej metody wymaga od użytkowników przeprowadzenia weryfikacji dwuetapowej, za każdym razem, gdy zalogują się w i zastąpień zasad dostępu warunkowego.

Aby określić, gdzie ma być włączone uwierzytelnianie wieloskładnikowe, zobacz [wersję usługi Azure MFA jest odpowiednia dla mojej organizacji?](../active-directory/authentication/concept-mfa-whichversion.md).

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

## <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach
Zarządzanie dostępem do zasobów w chmurze jest krytyczny dla każdej organizacji, która korzysta z chmury. [Kontrola dostępu oparta na rolach (RBAC)](../role-based-access-control/overview.md) pomaga w zarządzaniu, kto ma dostęp do zasobów platformy Azure, co może zrobić przy użyciu tych zasobów i jakie obszary, których mają dostęp.

Wyznaczanie grup lub poszczególnych ról odpowiedzialny za określonych funkcji na platformie Azure pomaga uniknąć nieporozumień, który może prowadzić do człowieka i błędów usługi automation, które tworzenie zagrożenia dla bezpieczeństwa. Ograniczanie dostępu na podstawie [trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych.

Zespół zabezpieczeń musi mieć wgląd w zasoby platformy Azure w celu oceny i skorygowania ryzyka. Jeśli zespół ds. zabezpieczeń ma obowiązków operacyjnych, potrzebują dodatkowych uprawnień do wykonywania ich zadań.

Możesz użyć [RBAC](../role-based-access-control/overview.md) można przypisać uprawnienia dla użytkowników, grup i aplikacji w określonym zakresie. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób.

**Najlepsze rozwiązanie**: Segregowanie zadań w ramach zespołu i udzielać uprawnień dostępu do użytkowników, które są im niezbędne do wykonywania swoich zadań. Zamiast dzięki czemu wszyscy nieograniczonych uprawnień w ramach subskrypcji platformy Azure lub zasobów, można zezwolić tylko określone akcje w określonym zakresie.
**Szczegóły**: Użyj [wbudowane role kontroli RBAC](../role-based-access-control/built-in-roles.md) na platformie Azure, aby przypisać uprawnienia do użytkowników.

> [!Note]
> Określone uprawnienia Tworzenie niepotrzebną złożoność i pomyłek, grupowania w konfiguracji "starszych", która jest trudne rozwiązać problem bez obawy przed czegoś nie zepsuć. Należy unikać uprawnienia specyficzne dla zasobu. Zamiast tego należy użyć grup zarządzania dla całego przedsiębiorstwa, uprawnień i grup zasobów dla uprawnień w ramach subskrypcji. Należy unikać uprawnienia specyficzne dla użytkownika. Przypisz dostęp do grup w usłudze Azure AD.

**Najlepsze rozwiązanie**: Przyznaj zespoły zabezpieczeń z dostępem Azure obowiązki wyświetlić zasobów platformy Azure, dzięki czemu mogą ocenić i skorygować zagrożenia.
**Szczegóły**: Udzielić zespołom zabezpieczeń RBAC [Czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader) roli. W zależności od zakresu obowiązków, można użyć głównej grupy zarządzania lub grupie zarządzania segmencie:

- **Grupa zarządzania głównym** dla zespołów odpowiedzialnych za wszystkie zasoby przedsiębiorstwa
- **Grupa zarządzania segmentów** dla zespołów o ograniczonym zakresie (zwykle z powodu przepisami lub innymi granicami organizacji)

**Najlepsze rozwiązanie**: Przyznaj odpowiednie uprawnienia do zespołów zabezpieczeń, które mają bezpośredni obowiązki operacyjnej.
**Szczegóły**: Przejrzyj wbudowane role RBAC do przypisywania odpowiednią rolę. Jeśli wbudowane role nie spełnienia specyficznych potrzeb swojej organizacji, możesz utworzyć [ról niestandardowych dla zasobów platformy Azure](../role-based-access-control/custom-roles.md). Jak za pomocą wbudowanych ról, role niestandardowe można przypisać do użytkowników, grup i nazwy główne usług w subskrypcji, grupy zasobów i zakresy zasobów.

**Najlepsze praktyki**: Przydział usługi Azure Security Center dostęp do ról zabezpieczeń, które go potrzebują. Usługa Security Center umożliwia zespołom zabezpieczeń szybkie identyfikowanie i korygowanie zagrożeń.
**Szczegóły**: Dodaj zespoły zabezpieczeń za pomocą tych potrzeb opartej na ROLACH [Administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) roli, dzięki czemu mogą wyświetlić zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń i zamknąć alerty i zalecenia. Można to zrobić za pomocą głównej grupy zarządzania lub z grupą zarządzania segment, w zależności od zakresu obowiązków.

Organizacje, które nie wymuszają kontrola dostępu do danych przy użyciu funkcji, takich jak RBAC może podając więcej uprawnień niż jest to konieczne do użytkowników. Może to prowadzić do naruszenia zabezpieczeń danych, pozwalając użytkownikom można uzyskiwać dostęp do typów danych (na przykład duże znaczenie biznesowe), które nie mają.

## <a name="lower-exposure-of-privileged-accounts"></a>Niższe ryzyko narażenia kont uprzywilejowanych

Zabezpieczanie uprzywilejowanego dostępu jest pierwszym ważnym krokiem do ochrony zasobów biznesowych. Minimalizacja liczby osób, które mają dostęp do zabezpieczania informacji lub zasobów zmniejsza ryzyko złośliwy użytkownik uzyska dostęp lub autoryzowany użytkownik przypadkowo wpływających na poufnych zasobów.

Uprzywilejowane konta są kontami, służących do administrowania i zarządzania systemami IT. Ataki Cybernetyczne celem tych kont w celu uzyskania dostępu do danych i systemy w organizacji. Do zabezpieczania uprzywilejowanego dostępu, powinien izolowanie kont i systemami z całej ryzyko narażenia na złośliwy użytkownik.

Firma Microsoft zaleca tworzenie, a następnie postępuj zgodnie z planem w celu zabezpieczenia uprzywilejowanego dostępu przed atakami cybernetycznymi. Aby uzyskać informacje o tworzeniu szczegółowych planu przejścia do zabezpieczania tożsamości i dostępu, które są zarządzane lub nie zwróciło w usłudze Azure AD, Microsoft Azure, Office 365 i innych usług w chmurze, zapoznaj się [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w Usługa Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Poniżej znajduje się podsumowanie najlepszych rozwiązań w [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Najlepsze rozwiązanie**: Zarządzanie, sterowanie i monitorowanie dostępu do kont uprzywilejowanych.   
**Szczegóły**: Włącz [usługi Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Po włączeniu Privileged Identity Management otrzymasz powiadomienie e-mail wiadomości do uprzywilejowanego dostępu zmiany roli. Te powiadomienia stanowić wczesne ostrzeżenie, po dodaniu dodatkowych użytkowników do wysoce uprzywilejowanych ról w katalogu.

**Najlepsze rozwiązanie**: Upewnij się, wszystkich kont administratora krytycznych są zarządzane konta usługi Azure AD.
**Szczegóły**: Usunąć wszystkie konta klienta z rolami administratora krytyczne (na przykład kont Microsoft, takich jak hotmail.com, live.com i outlook.com).

**Najlepsze rozwiązanie**: Upewnij się, że wszystkie role administratora krytyczne osobnego konta do wykonywania zadań administracyjnych w celu uniknięcia wyłudzania informacji oraz innych ataków złamanie uprawnień administracyjnych.
**Szczegóły**: Utwórz oddzielne konto administratora, który ma przypisane uprawnienia wymagane do wykonywania zadań administracyjnych. Blokuj użycie tych kont z uprawnieniami administracyjnymi do codziennego narzędzi zwiększających produktywność, takich jak wiadomości e-mail usługi Microsoft Office 365 lub przeglądania sieci web dowolnego.

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

Ocena kont, które są przypisane lub kwalifikuje się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze przy użyciu `*.onmicrosoft.com` domeny (przeznaczonych do dostępu awaryjnego), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi z dostępem awaryjnym w usłudze Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Najlepsze rozwiązanie**: Istnieje proces "break szkła" w miejscu, w przypadku zagrożenia.
**Szczegóły**: Postępuj zgodnie z instrukcjami w [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze w usłudze Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Najlepsze rozwiązanie**: Wymagaj wszystkich kont administratora krytyczne jako hasło (preferowany) lub Wymagaj uwierzytelniania wieloskładnikowego.
**Szczegóły**: Użyj [aplikacji Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) zalogować się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak [Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczeń użytkownika, który jest powiązany z urządzeniem i używa uwierzytelnianie biometryczne lub numer PIN.

Wymaga usługi Azure Multi-Factor Authentication podczas logowania dla wszystkich poszczególnych użytkowników, którzy są trwale przypisana do co najmniej jednej z ról administratora usługi Azure AD: Administrator globalny, Administrator ról uprzywilejowanych, Online Administrator programu Exchange i SharePoint Online administratora. Włącz [uwierzytelnianie wieloskładnikowe dla kont administratorów](../active-directory/authentication/howto-mfa-userstates.md) i sprawdź, czy zarejestrowano użytkowników kont administratora.

**Najlepsze rozwiązanie**: Dla kont administratora krytycznych ma administracyjnej stacji roboczej, których zadania w środowisku produkcyjnym nie są dozwolone (na przykład, przeglądanie i wiadomości e-mail). Kont administratorów to chroni przed atakami, przeglądanie i wiadomości e-mail, które znacząco obniżyć ryzyko dużego incydentu.
**Szczegóły**: Za pomocą stacji roboczej administratora. Wybierz poziom zabezpieczeń stacji roboczej:

- Wysoce bezpieczna produktywności urządzeń zapewniają zaawansowane zabezpieczenia do przeglądania i innych zadań produktywności.
- [Privileged Access Workstations (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) zapewniają dedykowany system operacyjny, który jest chroniony przed atakami internetowymi i wektorami zagrożenia dla poufnych zadań.

**Najlepsze rozwiązanie**: Anulowanie aprowizacji kont administratorów, gdy pracownik odejdzie z organizacji.
**Szczegóły**: Istnieje proces w miejscu, która wyłącza lub usuwa konta administratora, gdy pracownik odejdzie z organizacji.

**Najlepsze rozwiązanie**: Regularnie Testuj kont administratora przy użyciu bieżącego technik ataku.
**Szczegóły**: Użyj symulatora ataku programu Office 365 lub innych firm, oferty do uruchomienia ataku realistycznej scenariuszy w Twojej organizacji. Może to pomóc w Znajdź użytkowników na ataki, zanim wystąpi prawdziwego ataku.

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

## <a name="use-azure-ad-for-storage-authentication"></a>Używaj usługi Azure AD do uwierzytelniania magazynu
[Usługa Azure Storage](../storage/common/storage-auth-aad.md) obsługuje uwierzytelnianie i autoryzacja w usłudze Azure AD dla usługi Blob storage i Queue storage. Przy użyciu uwierzytelniania usługi Azure AD można użyć kontroli dostępu opartej na rolach na platformie Azure można udzielić określonych uprawnień użytkownikom, grupom i aplikacjom do zakresu, w kontenerze poszczególnych obiektów blob lub kolejki.

Firma Microsoft zaleca użycie [usługi Azure AD do uwierzytelniania dostępu do magazynu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Następny krok

Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.
