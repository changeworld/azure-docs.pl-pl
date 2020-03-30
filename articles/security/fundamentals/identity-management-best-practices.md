---
title: Najważniejsze wskazówki dotyczące zabezpieczeń dotyczące tożsamości platformy Azure & dostępu | Dokumenty firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zarządzania tożsamościami i kontroli dostępu przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73053339"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management and access control security best practices (Zarządzanie tożsamościami na platformie Azure i najlepsze rozwiązania dotyczące kontroli dostępu)

W tym artykule omówimy kolekcję najlepszych rozwiązań w zakresie zabezpieczeń azure i kontroli dostępu. Te najlepsze rozwiązania pochodzą z naszego środowiska z [usługą Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) i środowiska klientów takich jak Ty.

Dla każdego najlepszego rozwiązania wyjaśniamy:

* Jakie są najlepsze praktyki
* Dlaczego chcesz włączyć tę najlepszą praktykę
* Co może być wynikiem, jeśli nie można włączyć najlepsze praktyki
* Możliwe alternatywy dla najlepszych praktyk
* Jak możesz nauczyć się włączać najlepsze praktyki

Ten artykuł dotyczący najlepszych rozwiązań dotyczących zarządzania tożsamościami platformy Azure i kontroli dostępu opiera się na opinii konsensusu oraz możliwościach i zestawach funkcji platformy Azure, ponieważ istnieją one w momencie pisania tego artykułu.

Zamiarem na piśmie w tym artykule jest zapewnienie ogólnego planu działania na bardziej solidną postawę zabezpieczeń po wdrożeniu, kierując się naszą["5 kroków do zabezpieczenia infrastruktury tożsamości"](steps-secure-identity.md)listy kontrolnej, która prowadzi cię przez niektóre z naszych podstawowych funkcji i usług.

Opinie i technologie zmieniają się w czasie, a ten artykuł będzie regularnie aktualizowany w celu odzwierciedlenia tych zmian.

Najważniejsze wskazówki dotyczące zarządzania tożsamościami platformy Azure i kontroli dostępu omówione w tym artykule obejmują:

* Traktuj tożsamość jako podstawowy obwód zabezpieczeń
* Scentralizowanie zarządzania tożsamościami
* Zarządzanie połączonymi dzierżawcami
* Włączanie logowania jednokrotnego
* Włączanie dostępu warunkowego
* Planowanie rutynowych ulepszeń zabezpieczeń
* Włączanie zarządzania hasłami
* Wymuszanie weryfikacji wieloskładnikowej dla użytkowników
* Korzystanie z kontroli dostępu opartej na rolach
* Mniejsza ekspozycja kont uprzywilejowanych
* Kontroluj lokalizacje, w których znajdują się zasoby
* Używanie usługi Azure AD do uwierzytelniania magazynu

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traktuj tożsamość jako podstawowy obwód zabezpieczeń

Wielu uważa tożsamość za podstawowy obwód zabezpieczeń. Jest to odejście od tradycyjnego skupienia się na bezpieczeństwie sieci. Obwody sieci są coraz bardziej porowate, a ochrona obwodowa nie może być tak skuteczna, jak przed wybuchem urządzeń [BYOD](https://aka.ms/byodcg) i aplikacji w chmurze.

[Usługa Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) to rozwiązanie platformy Azure do zarządzania tożsamościami i dostępem. Usługa Azure AD to wielodostępna usługa zarządzania katalogami i tożsamościami firmy Microsoft w oparciu o wiele zarządzania katalogami i tożsamościami. Łączy podstawowe usługi katalogowe z funkcjami zarządzania dostępem do aplikacji i ochrony tożsamości w jednym rozwiązaniu.

W poniższych sekcjach przedstawiono najważniejsze wskazówki dotyczące zabezpieczeń tożsamości i dostępu przy użyciu usługi Azure AD.

**Najlepsze rozwiązanie:** Center security controls and detections around user and service identitys.
**Szczegóły:** Użyj usługi Azure AD do kolokacji formantów i tożsamości.

## <a name="centralize-identity-management"></a>Scentralizowanie zarządzania tożsamościami

W scenariuszu [tożsamości hybrydowej](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) zaleca się zintegrowanie katalogów lokalnych i chmurowych. Integracja umożliwia zespołowi IT zarządzanie kontami z jednej lokalizacji, niezależnie od tego, gdzie jest tworzone konto. Integracja pomaga również użytkownikom zwiększyć produktywność, zapewniając wspólną tożsamość dostępu do zasobów w chmurze i lokalnie.

**Najlepsze rozwiązanie:** Ustanawianie pojedynczego wystąpienia usługi Azure AD. Spójność i pojedyncze autorytatywne źródła zwiększą przejrzystość i zmniejszą ryzyko związane z bezpieczeństwem wynikające z błędów ludzkich i złożoności konfiguracji.
**Szczegóły:** Wyznacz pojedynczy katalog usługi Azure AD jako autorytatywne źródło dla kont firmowych i organizacyjnych.

**Najlepsze rozwiązanie:** Integruj katalogi lokalne z usługą Azure AD.  
**Szczegóły:** Użyj [usługi Azure AD Connect,](/azure/active-directory/connect/active-directory-aadconnect) aby zsynchronizować katalog lokalny z katalogiem w chmurze.

> [!Note]
> Istnieją [czynniki wpływające na wydajność usługi Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Upewnij się, że usługa Azure AD Connect ma wystarczającą pojemność, aby uniemożliwić systemom o słabych wynikach utrudnianie bezpieczeństwa i produktywności. Duże lub złożone organizacje (organizacje inicjujące inicjowanie obsługi administracyjnej ponad 100 000 obiektów) powinny postępować zgodnie z [zaleceniami,](../../active-directory/hybrid/whatis-hybrid-identity.md) aby zoptymalizować ich implementację usługi Azure AD Connect.

**Najlepsze rozwiązanie:** Nie synchronizuj kont z usługą Azure AD, które mają wysokie uprawnienia w istniejącym wystąpieniu usługi Active Directory.
**Szczegóły:** Nie zmieniaj domyślnej [konfiguracji usługi Azure AD Connect,](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) która odfiltruje te konta. Ta konfiguracja zmniejsza ryzyko przestawiania się przeciwników z chmury do zasobów lokalnych (co może spowodować poważny incydent).

**Najlepsze rozwiązanie**: Włącz synchronizację skrótów haseł.  
**Szczegóły:** Synchronizacja skrótów haseł to funkcja służąca do synchronizowania skrótów haseł użytkownika z lokalnego wystąpienia usługi Active Directory do wystąpienia usługi Azure AD opartej na chmurze. Ta synchronizacja pomaga chronić przed wyciekiem poświadczeń odtwarzanych z poprzednich ataków.

Nawet jeśli zdecydujesz się używać federacji z usługami federacjowymi Active Directory (AD FS) lub innymi dostawcami tożsamości, opcjonalnie możesz skonfigurować synchronizację skrótów haseł jako kopię zapasową w przypadku awarii lub tymczasowej niedostępności serwerów lokalnych. Ta synchronizacja umożliwia użytkownikom zalogowanie się do usługi przy użyciu tego samego hasła, którego używają do logowania się do lokalnego wystąpienia usługi Active Directory. Umożliwia również ochronę tożsamości do wykrywania poświadczeń, które zostały naruszone, porównując zsynchronizowane skróty haseł z hasłami, o których wiadomo, że zostały naruszone, jeśli użytkownik użył tego samego adresu e-mail i hasła w innych usługach, które nie są połączone z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Najlepsze rozwiązanie:** W przypadku tworzenia nowych aplikacji użyj usługi Azure AD do uwierzytelniania.
**Szczegóły**: Obsługa uwierzytelniania umożliwia obsługę odpowiednich funkcji:

  - Usługa Azure AD dla pracowników
  - [Usługa Azure AD B2B](../../active-directory/b2b/index.yml) dla użytkowników-gości i partnerów zewnętrznych
  - [Usługa Azure AD B2C](../../active-directory-b2c/index.yml) do kontrolowania sposobu rejestrowania się, logowania się i zarządzania profilami podczas korzystania z aplikacji

Organizacje, które nie integrują swojej tożsamości lokalnej z tożsamością w chmurze, mogą mieć większe obciążenie związane z zarządzaniem kontami. To obciążenie zwiększa prawdopodobieństwo wystąpienia błędów i naruszeń zabezpieczeń.

> [!Note]
> Należy wybrać katalogi, w których będą przebywać konta krytyczne i czy używana stacja robocza administratora jest zarządzana przez nowe usługi w chmurze lub istniejące procesy. Korzystanie z istniejących procesów zarządzania i inicjowania obsługi tożsamości może zmniejszyć pewne ryzyko, ale może również stwarzać ryzyko naruszenia konta lokalnego przez osobę atakującą i przestawiania do chmury. Możesz użyć innej strategii dla różnych ról (na przykład administratorzy IT i administratorzy jednostek biznesowych). Dostępne są dwie opcje. Pierwszą opcją jest utworzenie kont usługi Azure AD, które nie są synchronizowane z lokalnym wystąpieniem usługi Active Directory. Dołącz do stacji roboczej administratora do usługi Azure AD, którą można zarządzać i łatać za pomocą usługi Microsoft Intune. Drugą opcją jest użycie istniejących kont administratora przez synchronizację z lokalnym wystąpieniem usługi Active Directory. Do zarządzania i zabezpieczeń używaj istniejących stacji roboczych w domenie usługi Active Directory.

## <a name="manage-connected-tenants"></a>Zarządzanie połączonymi dzierżawcami
Organizacja zabezpieczeń potrzebuje widoczności, aby ocenić ryzyko i określić, czy zasady organizacji i wszelkie wymagania prawne są przestrzegane. Należy upewnić się, że organizacja zabezpieczeń ma wgląd we wszystkie subskrypcje podłączone do środowiska produkcyjnego i sieci (za pośrednictwem [usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja lokacja).](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) [Administrator globalny/administrator firmy](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) w usłudze Azure AD może podnieść poziom dostępu do roli Administrator dostępu [użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) i wyświetlić wszystkie subskrypcje i grupy zarządzane połączone z twoim środowiskiem.

Zobacz [podnieść dostęp do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania,](../../role-based-access-control/elevate-access-global-admin.md) aby upewnić się, że Ty i Twoja grupa zabezpieczeń może wyświetlać wszystkie subskrypcje lub grupy zarządzania połączone z twoim środowiskiem. Po dokonaniu oceny ryzyka należy usunąć ten podwyższony poziom dostępu.

## <a name="enable-single-sign-on"></a>Włączanie logowania jednokrotnego

W świecie opartym na chmurze na urządzeniach, aplikacjach i usługach z dowolnego miejsca użytkownicy mogą pracować wydajnie w dowolnym miejscu i czasie. Jeśli masz wiele rozwiązań tożsamości do zarządzania, staje się to problemem administracyjnym nie tylko dla IT, ale także dla użytkowników, którzy muszą zapamiętać wiele haseł.

Korzystając z tego samego rozwiązania tożsamości dla wszystkich aplikacji i zasobów, można osiągnąć jedno i za pomocą funkcji SSO. Użytkownicy mogą używać tego samego zestawu poświadczeń do logowania się i uzyskiwania dostępu do potrzebnych zasobów, niezależnie od tego, czy zasoby znajdują się lokalnie, czy w chmurze.

**Najlepsze rozwiązanie**: Włącz opcję SSO.  
**Szczegóły:** usługa Azure AD [rozszerza lokalną usługę Active Directory na](/azure/active-directory/connect/active-directory-aadconnect) chmurę. Użytkownicy mogą korzystać z konta podstawowego lub szkolnego dla urządzeń, zasobów firmy oraz wszystkich aplikacji sieci Web i SaaS, których potrzebują do wykonania zadań. Użytkownicy nie muszą zapamiętywać wielu zestawów nazw użytkowników i haseł, a ich dostęp do aplikacji może być automatycznie aprowizyjny (lub anulowany) na podstawie członkostwa w grupach organizacji i ich statusu jako pracownika. Możesz również kontrolować dostęp do aplikacji galerii lub własnych aplikacji lokalnych opracowanych i opublikowanych za pośrednictwem [serwera proxy aplikacji usługi Azure AD](/azure/active-directory/active-directory-application-proxy-get-started).

Użyj funkcji jednośmiękowych, aby umożliwić użytkownikom dostęp do [aplikacji SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) na podstawie ich konta służbowego w usłudze Azure AD. Dotyczy to nie tylko aplikacji Microsoft SaaS, ale także innych aplikacji, takich jak [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) i [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). Aplikację można skonfigurować do używania usługi Azure AD jako dostawcy [tożsamości opartego na sieci SAML.](/azure/active-directory/fundamentals-identity) Jako formant zabezpieczeń usługa Azure AD nie wystawia tokenu, który umożliwia użytkownikom logowanie się do aplikacji, chyba że przyznano im dostęp za pośrednictwem usługi Azure AD. Można udzielić dostępu bezpośrednio lub za pośrednictwem grupy, której członkami są użytkownicy.

Organizacje, które nie tworzą wspólnej tożsamości w celu ustanowienia logowania jednokrotnego dla swoich użytkowników i aplikacji, są bardziej narażone na scenariusze, w których użytkownicy mają wiele haseł. Te scenariusze zwiększają prawdopodobieństwo ponownego użycia haseł przez użytkowników lub używania słabych haseł.

## <a name="turn-on-conditional-access"></a>Włączanie dostępu warunkowego

Użytkownicy mogą uzyskiwać dostęp do zasobów organizacji przy użyciu różnych urządzeń i aplikacji z dowolnego miejsca. Jako administrator IT chcesz upewnić się, że te urządzenia spełniają Twoje standardy bezpieczeństwa i zgodności. Samo skupienie się na tym, kto może uzyskać dostęp do zasobu, nie jest już wystarczające.

Aby zrównoważyć bezpieczeństwo i produktywność, należy zastanowić się, w jaki sposób dostęp do zasobu jest dostępny, zanim będzie można podjąć decyzję o kontroli dostępu. Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać to wymaganie. Dzięki dostępowi warunkowemu można podejmować decyzje dotyczące automatycznej kontroli dostępu na podstawie warunków uzyskiwania dostępu do aplikacji w chmurze.

**Najlepsze praktyki**: Zarządzanie i kontrolowanie dostępu do zasobów firmowych.  
**Szczegóły:** Konfigurowanie [dostępu warunkowego](/azure/active-directory/active-directory-conditional-access-azure-portal) usługi Azure AD na podstawie wrażliwości grupy, lokalizacji i aplikacji dla aplikacji SaaS i aplikacji połączonych z usługą Azure AD.

**Najlepsze rozwiązanie:** Blokowanie starszych protokołów uwierzytelniania.
**Szczegóły**: Atakujący wykorzystują słabości starszych protokołów każdego dnia, szczególnie w przypadku ataków natryskiwanie hasła. Skonfiguruj dostęp warunkowy, aby blokować starsze protokoły. Zobacz wideo [Usługi Azure AD: Do i nie robić, aby](https://www.youtube.com/watch?v=wGk0J4z90GI) uzyskać więcej informacji.

## <a name="plan-for-routine-security-improvements"></a>Planowanie rutynowych ulepszeń zabezpieczeń

Zabezpieczenia stale ewoluują i ważne jest, aby wbudować w platformie zarządzania chmurą i tożsamościami sposób regularnego pokazywania wzrostu i odkrywania nowych sposobów zabezpieczenia środowiska.

Identity Secure Score to zestaw zalecanych zabezpieczeń, które firma Microsoft publikuje, który działa w celu zapewnienia wyniku liczbowego, aby obiektywnie zmierzyć postawę zabezpieczeń i pomóc zaplanować przyszłe ulepszenia zabezpieczeń. Możesz również wyświetlić swój wynik w porównaniu z tymi w innych branżach, a także własne trendy w czasie.

**Najlepsze rozwiązanie:** Planowanie rutynowych przeglądów zabezpieczeń i ulepszeń w oparciu o najlepsze rozwiązania w twojej branży.
**Szczegóły:** Użyj funkcji Bezpieczny wynik tożsamości, aby uszeregować ulepszenia w czasie.

## <a name="enable-password-management"></a>Włączanie zarządzania hasłami

Jeśli masz wielu dzierżaw lub chcesz umożliwić użytkownikom [resetowanie własnych haseł,](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)ważne jest, aby użyć odpowiednich zasad zabezpieczeń, aby zapobiec nadużyciom.

**Najlepsze rozwiązanie:** Konfigurowanie samoobsługowego resetowania haseł (SSPR) dla użytkowników.  
**Szczegóły:** Użyj funkcji [samoobsługowego resetowania hasła](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) usługi Azure AD.

**Najlepsze rozwiązanie**: Monitoruj, jak i czy sspr jest naprawdę używany.  
**Szczegóły:** Monitorowanie użytkowników, którzy się rejestrują, korzystając z [raportu aktywności rejestracji resetowania hasła](/azure/active-directory/active-directory-passwords-get-insights)usługi Azure AD . Funkcja raportowania, którą udostępnia usługa Azure AD, ułatwia odpowiadanie na pytania przy użyciu wstępnie utworzonych raportów. Jeśli masz odpowiednią licencję, możesz również tworzyć zapytania niestandardowe.

**Najlepsze rozwiązanie:** Rozszerzenie zasad haseł opartych na chmurze na infrastrukturę lokalną.
**Szczegóły:** Udoskonal zasady haseł w organizacji, przeprowadzając te same kontrole lokalnych zmian haseł, co w przypadku zmian haseł w chmurze. Zainstaluj [ochronę hasłem usługi Azure AD](/azure/active-directory/authentication/concept-password-ban-bad) dla lokalnych agentów usługi Windows Server Active Directory, aby rozszerzyć listy zakazanych haseł na istniejącą infrastrukturę. Użytkownicy i administratorzy, którzy zmieniają, ustawiają lub resetują hasła lokalnie, muszą przestrzegać tych samych zasad haseł, co użytkownicy w chmurze.

## <a name="enforce-multi-factor-verification-for-users"></a>Wymuszanie weryfikacji wieloskładnikowej dla użytkowników

Zalecamy, aby wszyscy użytkownicy wymagali weryfikacji dwuetapowej. Dotyczy to również administratorów i innych osób w organizacji, które mogą mieć znaczący wpływ na ich konto (na przykład urzędników finansowych).

Istnieje wiele opcji wymagających weryfikacji dwuetapowej. Najlepszą opcją zależy od twoich celów, uruchomionej wersji usługi Azure AD i programu licencjonowania. Zobacz [Jak wymagać weryfikacji dwuetapowej,](/azure/active-directory/authentication/howto-mfa-userstates) aby użytkownik określił najlepszą opcję dla Ciebie. Więcej informacji na temat licencji i cennika można znaleźć na stronach cenowych [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) i Azure [Multi-Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Oto opcje i korzyści umożliwiające weryfikację dwuetapową:

**Opcja 1:** Włącz usługę MFA dla wszystkich użytkowników i metody logowania z **korzyścią**dla domyślnych zabezpieczeń usługi Azure AD: Ta opcja umożliwia łatwe i szybkie wymuszanie usługi MFA dla wszystkich użytkowników w środowisku za pomocą rygorystycznych zasad:

* Kwestionowanie rachunków administracyjnych i mechanizmów logowania administracyjnego
* Wymagaj wyzwania usługi MFA za pośrednictwem usługi Microsoft Authenticator dla wszystkich użytkowników
* Ogranicz starsze protokoły uwierzytelniania.

Ta metoda jest dostępna dla wszystkich warstw licencjonowania, ale nie można mieszać z istniejącymi zasadami dostępu warunkowego. Więcej informacji można znaleźć w ustawieniach domyślnych zabezpieczeń usługi Azure AD

**Opcja 2**: [Włącz uwierzytelnianie wieloskładnikowe, zmieniając stan użytkownika](../../active-directory/authentication/howto-mfa-userstates.md).   
**Korzyści**: Jest to tradycyjna metoda wymagająca weryfikacji dwuetapowej. Działa zarówno z [uwierzytelnianiem wieloskładnikowym platformy Azure w chmurze, jak i z serwerem uwierzytelniania wieloskładnikowego platformy Azure.](/azure/active-directory/authentication/concept-mfa-whichversion) Za pomocą tej metody wymaga od użytkowników do wykonywania weryfikacji dwuetapowej za każdym razem, gdy się logują i zastępuje zasady dostępu warunkowego.

Aby określić, gdzie należy włączyć uwierzytelnianie wieloskładnikowe, zobacz [Która wersja usługi Azure MFA jest odpowiednia dla mojej organizacji?](/azure/active-directory/authentication/concept-mfa-whichversion).

**Opcja 3**: [Włącz uwierzytelnianie wieloskładnikowe za pomocą zasad dostępu warunkowego](/azure/active-directory/authentication/howto-mfa-getstarted).
**Korzyści:** Ta opcja umożliwia monitowanie o weryfikację dwuetapową w określonych warunkach przy użyciu [dostępu warunkowego](/azure/active-directory/active-directory-conditional-access-azure-portal). Określone warunki mogą być logowania użytkownika z różnych lokalizacji, niezaufanych urządzeń lub aplikacji, które uważasz za ryzykowne. Definiowanie określonych warunków, w których wymagana jest weryfikacja dwuetapowa, pozwala uniknąć ciągłego monitowania użytkowników, co może być nieprzyjemnym doświadczeniem użytkownika.

Jest to najbardziej elastyczny sposób włączania weryfikacji dwuetapowej dla użytkowników. Włączenie zasad dostępu warunkowego działa tylko dla uwierzytelniania wieloskładnikowego platformy Azure w chmurze i jest funkcją premium usługi Azure AD. Więcej informacji na temat tej metody można znaleźć w obszarze [Wdrażanie opartego na chmurze uwierzytelniania wieloskładnikowego platformy Azure.](/azure/active-directory/authentication/howto-mfa-getstarted)

**Opcja 4**: Włącz uwierzytelnianie wieloskładnikowe za pomocą zasad dostępu warunkowego, oceniając ryzyko użytkownika i logowania związane z [ochroną tożsamości usługi Azure AD](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa).   
**Korzyści**: Ta opcja umożliwia:

* Wykrywaj potencjalne luki w zabezpieczeniach, które wpływają na tożsamość organizacji.
* Skonfiguruj automatyczne odpowiedzi do wykrytych podejrzanych akcji związanych z tożsamościami organizacji.
* Zbadaj podejrzane incydenty i podejmij odpowiednie działania, aby je rozwiązać.

Ta metoda używa oceny ryzyka usługi Azure AD Identity Protection, aby ustalić, czy weryfikacja dwuetapowa jest wymagana na podstawie ryzyka użytkownika i logowania dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencjonowania usługi Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w programie [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Opcja 1, włączanie uwierzytelniania wieloskładnikowego przez zmianę stanu użytkownika, zastępuje zasady dostępu warunkowego. Ponieważ w opcjach 2 i 3 są używane zasady dostępu warunkowego, nie można używać opcji 1 z nimi.

Organizacje, które nie dodają dodatkowych warstw ochrony tożsamości, takich jak weryfikacja dwuetapowa, są bardziej podatne na atak kradzieży poświadczeń. Atak kradzieży poświadczeń może prowadzić do naruszenia bezpieczeństwa danych.

## <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach

Zarządzanie dostępem do zasobów w chmurze ma kluczowe znaczenie dla każdej organizacji korzystającej z chmury. [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview)ułatwia zarządzanie tym, kto ma dostęp do zasobów platformy Azure, co mogą zrobić z tymi zasobami i do jakich obszarów mają dostęp.

Wyznaczanie grup lub poszczególnych ról odpowiedzialnych za określone funkcje na platformie Azure pomaga uniknąć nieporozumień, które mogą prowadzić do błędów ludzkich i automatyzacji, które tworzą zagrożenia bezpieczeństwa. Ograniczenie dostępu na podstawie [konieczności znać](https://en.wikipedia.org/wiki/Need_to_know) i [najmniej uprzywilejowanych](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zasad zabezpieczeń jest konieczne dla organizacji, które chcą wymuszać zasady zabezpieczeń dostępu do danych.

Zespół zabezpieczeń potrzebuje wglądu w zasoby platformy Azure w celu oceny i korygowanie ryzyka. Jeśli zespół zabezpieczeń ma obowiązki operacyjne, potrzebują dodatkowych uprawnień do wykonywania swoich zadań.

Za pomocą [funkcji RBAC](/azure/role-based-access-control/overview) można przypisywać uprawnienia użytkownikom, grupom i aplikacjom w określonym zakresie. Zakresem przypisania roli może być subskrypcja, grupa zasobów lub pojedynczy zasób.

**Najlepsze praktyki:** Segreguj obowiązki w swoim zespole i przyznaj tylko taką ilość dostępu do użytkowników, których potrzebują do wykonywania swoich zadań. Zamiast udzielać wszystkim nieograniczonych uprawnień w subskrypcji platformy Azure lub zasobów, zezwalaj tylko na określone akcje w określonym zakresie.
**Szczegóły:** Użyj [wbudowanych ról RBAC](/azure/role-based-access-control/built-in-roles) na platformie Azure, aby przypisać uprawnienia użytkownikom.

> [!Note]
> Określone uprawnienia tworzą niepotrzebną złożoność i zamieszanie, gromadząc się w "starszej" konfiguracji, którą trudno naprawić bez obawy o złamanie czegoś. Unikaj uprawnień specyficznych dla zasobów. Zamiast tego należy użyć grup zarządzania dla uprawnień dla całego przedsiębiorstwa i grup zasobów dla uprawnień w ramach subskrypcji. Unikaj uprawnień specyficznych dla użytkownika. Zamiast tego przypisz dostęp do grup w usłudze Azure AD.

**Najlepsze rozwiązanie:** Przyznaj zespołom zabezpieczeń dostęp do obowiązków platformy Azure, aby wyświetlić zasoby platformy Azure, aby mogły oceniać i korygować ryzyko.
**Szczegóły:** Przyznaj zespołom zabezpieczeń rolę [CZYTNIKA zabezpieczeń](/azure/role-based-access-control/built-in-roles#security-reader) RBAC. W zależności od zakresu obowiązków można użyć głównej grupy zarządzania lub grupy zarządzania segmentami:

* **Główna grupa zarządzania** dla zespołów odpowiedzialnych za wszystkie zasoby przedsiębiorstwa
* **Grupa zarządzania segmentami** dla zespołów o ograniczonym zakresie (często z powodu granic regulacyjnych lub innych organizacji)

**Najlepsze rozwiązanie:** Udziel odpowiednich uprawnień zespołom zabezpieczeń, które mają bezpośrednie obowiązki operacyjne.
**Szczegóły:** Przejrzyj wbudowane role RBAC dla odpowiedniego przypisania roli. Jeśli wbudowane role nie spełniają określonych potrzeb organizacji, można utworzyć [niestandardowe role dla zasobów platformy Azure.](/azure/role-based-access-control/custom-roles) Podobnie jak w odniesieniu do wbudowanych ról, można przypisać role niestandardowe do użytkowników, grup i podmiotów usługi w subskrypcji, grupie zasobów i zakresach zasobów.

**Najważniejsze wskazówki:** Przyznaj usłudze Azure Security Center dostęp do ról zabezpieczeń, które jej potrzebują. Usługa Security Center umożliwia zespołom zabezpieczeń szybkie identyfikowanie i korygowanie zagrożeń.
**Szczegóły:** Dodaj zespoły zabezpieczeń z tymi potrzebami do roli [administrator zabezpieczeń](/azure/role-based-access-control/built-in-roles#security-admin) RBAC, aby mogli wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia oraz odrzucać alerty i zalecenia. Można to zrobić za pomocą głównej grupy zarządzania lub grupy zarządzania segmentami, w zależności od zakresu obowiązków.

Organizacje, które nie wymuszają kontroli dostępu do danych przy użyciu funkcji, takich jak RBAC może dać więcej uprawnień niż to konieczne dla swoich użytkowników. Może to prowadzić do naruszenia bezpieczeństwa danych, umożliwiając użytkownikom dostęp do typów danych (na przykład o dużym wpływie na działalność firmy), których nie powinni mieć.

## <a name="lower-exposure-of-privileged-accounts"></a>Mniejsza ekspozycja kont uprzywilejowanych

Zabezpieczenie uprzywilejowanego dostępu jest pierwszym krokiem do ochrony zasobów biznesowych. Minimalizowanie liczby osób, które mają dostęp do bezpiecznych informacji lub zasobów zmniejsza prawdopodobieństwo uzyskania dostępu przez złośliwego użytkownika lub nieautoryzowanego użytkownika, który nieumyślnie wpływa na poufny zasób.

Konta uprzywilejowane to konta, które administrują systemami INFORMATYCZNI i nimi zarządzają. Cyberprzestępcy atakują te konta, aby uzyskać dostęp do danych i systemów organizacji. Aby zabezpieczyć uprzywilejowany dostęp, należy odizolować konta i systemy od ryzyka narażenia złośliwego użytkownika.

Zalecamy opracowanie i śledzenie planu działania w celu zabezpieczenia uprzywilejowanego dostępu przed cyberprzestępcami. Aby uzyskać informacje dotyczące tworzenia szczegółowego planu działania w celu zabezpieczenia tożsamości i dostępu, które są zarządzane lub zgłaszane w usłudze Azure AD, Microsoft Azure, usłudze Office 365 i innych usługach w chmurze, przejrzyj [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i chmurowych w usłudze Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

Poniżej podsumowano najlepsze rozwiązania znalezione w [zabezpieczaniu uprzywilejowanego dostępu dla wdrożeń hybrydowych i chmurowych w usłudze Azure AD:](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Najlepsze rozwiązanie:** Zarządzanie, kontrolowanie i monitorowanie dostępu do kont uprzywilejowanych.   
**Szczegóły:** Włącz [zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Po włączeniu zarządzania tożsamościami uprzywilejowanymi otrzymasz wiadomości e-mail z powiadomieniami o zmianach ról dostępu uprzywilejowanego. Te powiadomienia zapewniają wczesne ostrzeganie, gdy dodatkowi użytkownicy są dodawane do ról o wysokim uprzywilejowanich w katalogu.

**Najlepsze rozwiązanie:** Upewnij się, że wszystkie krytyczne konta administratora są zarządzane konta usługi Azure AD.
**Szczegóły:** Usuń wszystkie konta konsumentów z krytycznych ról administratora (na przykład kont Microsoft, takich jak hotmail.com, live.com i outlook.com).

**Najlepsze rozwiązanie:** Upewnij się, że wszystkie krytyczne role administratora mają oddzielne konto dla zadań administracyjnych, aby uniknąć wyłudzania informacji i innych ataków w celu naruszenia uprawnień administracyjnych.
**Szczegóły:** Utwórz osobne konto administratora, które ma przypisane uprawnienia potrzebne do wykonywania zadań administracyjnych. Blokowanie korzystania z tych kont administracyjnych dla narzędzi codziennej produktywności, takich jak poczta e-mail usługi Microsoft Office 365 lub dowolne przeglądanie sieci Web.

**Najlepsze rozwiązanie:** identyfikowanie i kategoryzowanie kont, które znajdują się w rolach wysoce uprzywilejowanych.   
**Szczegóły:** Po włączeniu usługi Azure AD Uprzywilejowane zarządzanie tożsamościami, wyświetl użytkowników, którzy są w administratorze globalnym, administrator ról uprzywilejowanych i innych ról o wysokim stopniu uprzywilejowania. Usuń wszystkie konta, które nie są już potrzebne w tych rolach, i kategoryzuj pozostałe konta przypisane do ról administratora:

* Indywidualnie przypisany do użytkowników administracyjnych i może być używany do celów nieadaucyjnych (na przykład osobistych wiadomości e-mail)
* Indywidualnie przypisane użytkownikom administracyjnym i wyznaczone wyłącznie do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* W przypadku scenariuszy dostępu awaryjnego
* Dla skryptów automatycznych
* Dla użytkowników zewnętrznych

**Najlepsze praktyki**: Zaimplementuj dostęp "just in time" (JIT), aby jeszcze bardziej obniżyć czas ekspozycji uprawnień i zwiększyć wgląd w korzystanie z uprzywilejowanych kont.   
**Szczegóły:** Zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD umożliwia:

* Ogranicz użytkowników tylko biorąc na ich uprawnienia JIT.
* Przypisz role na skrócony czas trwania zufnością, że uprawnienia są automatycznie odwoływane.

**Najlepsze rozwiązanie:** Zdefiniuj co najmniej dwa konta dostępu awaryjnego.   
**Szczegóły:** Konta dostępu awaryjnego pomagają organizacjom ograniczyć uprzywilejowany dostęp w istniejącym środowisku usługi Azure Active Directory. Konta te są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do scenariuszy, w których nie można używać zwykłych kont administracyjnych. Organizacje muszą ograniczyć użycie konta awaryjnego tylko do wymaganego czasu.

Oceń konta, które są przypisane lub kwalifikują się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w `*.onmicrosoft.com` chmurze przy użyciu domeny (przeznaczonej do dostępu awaryjnego), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Najlepsze praktyki**: W razie nagłego wypadku zasuń proces "break glass".
**Szczegóły:** Wykonaj kroki opisane w obszarze [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i chmurowych w usłudze Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Najlepsze rozwiązanie:** Wymagaj, aby wszystkie krytyczne konta administratorów były bez hasła (preferowane) lub wymagały uwierzytelniania wieloskładnikowego.
**Szczegóły:** Użyj [aplikacji Microsoft Authenticator,](/azure/active-directory/authentication/howto-authentication-phone-sign-in) aby zalogować się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak [Windows Hello dla firm,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)program Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby włączyć poświadczenia użytkownika powiązane z urządzeniem i używają uwierzytelniania biometrycznego lub numeru PIN.

Wymagaj uwierzytelniania wieloskładnikowego platformy Azure podczas logowania dla wszystkich indywidualnych użytkowników, którzy są trwale przypisani do jednej lub więcej ról administratora usługi Azure AD: administratora globalnego, administratora ról uprzywilejowanych, administratora usługi Exchange Online i usługi SharePoint Online Administratora. Włącz [uwierzytelnianie wieloskładnikowe dla kont administratora](/azure/active-directory/authentication/howto-mfa-userstates) i upewnij się, że użytkownicy kont administratora się zarejestrowali.

**Najlepsze rozwiązanie:** W przypadku krytycznych kont administratorów należy mieć stację roboczą administratora, na której zadania produkcyjne nie są dozwolone (na przykład przeglądanie i poczta e-mail). Pozwoli to chronić konta administratorów przed wektorami ataków, które używają przeglądania i poczty e-mail, i znacznie zmniejszy ryzyko poważnego incydentu.
**Szczegóły:** Użyj stacji roboczej administratora. Wybierz poziom zabezpieczeń stacji roboczej:

- Wysoce bezpieczne urządzenia zwiększające produktywność zapewniają zaawansowane zabezpieczenia podczas przeglądania i innych zadań związanych z produktywnością.
- [Stacje robocze dostępu uprzywilejowanego (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) zapewniają dedykowany system operacyjny, który jest chroniony przed atakami internetowymi i wektorami zagrożeń dla poufnych zadań.

**Najlepsze rozwiązanie:** Deprowizuj konta administratorów, gdy pracownicy opuszczają organizację.
**Szczegóły:** należy wprowadzić proces, który wyłącza lub usuwa konta administratorów, gdy pracownicy opuszczają organizację.

**Najlepsze rozwiązanie:** Regularnie testuj konta administratorów przy użyciu bieżących technik ataku.
**Szczegóły:** Użyj symulatora ataku usługi Office 365 lub oferty innej firmy, aby uruchomić realistyczne scenariusze ataków w organizacji. Może to pomóc w znalezieniu użytkowników podatnych na ataki przed wystąpieniem rzeczywistego ataku.

**Najlepsze rozwiązanie:** Podejmij kroki w celu złagodzenia najczęściej używanych zaatakowanych technik.  
**Szczegóły**: [Identyfikowanie kont Microsoft w rolach administracyjnych, które muszą zostać przeniesione na konta służbowe lub szkolne](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zapewnianie oddzielnych kont użytkowników i przekazywania poczty dla globalnych kont administratorów](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Upewnij się, że hasła kont administracyjnych zostały ostatnio zmienione](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Włączanie synchronizacji skrótów haseł](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników we wszystkich rolach uprzywilejowanych, a także dla użytkowników narażonych](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Uzyskaj bezpieczny wynik usługi Office 365 (w przypadku korzystania z usługi Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń i zgodności usługi Office 365 (w przypadku korzystania z usługi Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurowanie monitorowania aktywności usługi Office 365 (w przypadku korzystania z usługi Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Ustanowienie właścicieli planów reagowania na incydenty/sytuacje awaryjne](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Bezpieczne lokalne konta administracyjne uprzywilejowane](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Jeśli nie zabezpieczysz uprzywilejowanego dostępu, może się okazać, że masz zbyt wielu użytkowników w rolach o wysokim stopniu uprzywilejowanych i są bardziej narażone na ataki. Złośliwi aktorzy, w tym cyberprzestępcy, często kierują reklamy na konta administratorów i inne elementy uprzywilejowanego dostępu, aby uzyskać dostęp do poufnych danych i systemów przy użyciu kradzieży poświadczeń.

## <a name="control-locations-where-resources-are-created"></a>Kontroluj lokalizacje, w których tworzone są zasoby

Bardzo ważne jest umożliwienie operatorom chmury wykonywania zadań, a jednocześnie uniemożliwienie im łamania konwencji potrzebnych do zarządzania zasobami organizacji. Organizacje, które chcą kontrolować lokalizacje, w których są tworzone zasoby, powinny mieć twardy kod tych lokalizacji.

Usługi [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) można użyć do tworzenia zasad zabezpieczeń, których definicje opisują akcje lub zasoby, które są wyraźnie odrzucone. Te definicje zasad można przypisać w żądanym zakresie, takich jak subskrypcja, grupa zasobów lub pojedynczy zasób.

> [!NOTE]
> Zasady zabezpieczeń nie są takie same jak RBAC. Oni rzeczywiście używać RBAC wobec upoważnić użytkownik wobec tworzenie tych zasoby.
>
>

Organizacje, które nie kontrolują sposobu tworzenia zasobów, są bardziej podatne na użytkowników, którzy mogą nadużywać usługi, tworząc więcej zasobów, niż potrzebują. Zaostrzenie procesu tworzenia zasobów jest ważnym krokiem do zabezpieczenia scenariusza wielodostępnego.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktywne monitorowanie podejrzanych działań

Aktywny system monitorowania tożsamości może szybko wykryć podejrzane zachowanie i wyzwolić alert do dalszego badania. W poniższej tabeli wymieniono dwie funkcje usługi Azure AD, które mogą pomóc organizacjom monitorować ich tożsamości:

**Najlepsze rozwiązanie**: Mieć metodę identyfikacji:

- Próbuje się zalogować [bez śledzenia](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- [Brutalne ataki siłowe](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) na określone konto.
- Próbuje zalogować się z wielu lokalizacji.
- Logowania z [zainfekowanych urządzeń](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Podejrzane adresy IP.

**Szczegóły:** Użyj [raportów anomalii](/azure/active-directory/active-directory-view-access-usage-reports)usługi Azure AD Premium . Należy wprowadzić procesy i procedury, aby administratorzy IT mogli uruchamiać te raporty codziennie lub na żądanie (zwykle w scenariuszu reagowania na incydenty).

**Najlepsze rozwiązanie:** Posiadaj aktywny system monitorowania, który powiadamia o ryzyku i może dostosować poziom ryzyka (wysoki, średni lub niski) do wymagań biznesowych.   
**Szczegóły:** Użyj [usługi Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), która oznacza bieżące zagrożenia na własnym pulpicie nawigacyjnym i wysyła codzienne powiadomienia podsumowania za pośrednictwem poczty e-mail. Aby chronić tożsamości organizacji, można skonfigurować zasady oparte na ryzyku, które automatycznie reagują na wykryte problemy po osiągnięciu określonego poziomu ryzyka.

Organizacje, które nie monitorują aktywnie swoich systemów tożsamości, są narażone na ryzyko naruszenia poświadczeń użytkownika. Bez wiedzy, że podejrzane działania odbywają się za pośrednictwem tych poświadczeń, organizacje nie mogą złagodzić tego typu zagrożenia.

## <a name="use-azure-ad-for-storage-authentication"></a>Używanie usługi Azure AD do uwierzytelniania magazynu
[Usługa Azure Storage](/azure/storage/common/storage-auth-aad) obsługuje uwierzytelnianie i autoryzację za pomocą usługi Azure AD dla magazynu obiektów Blob i magazynu kolejek. Za pomocą uwierzytelniania usługi Azure AD można użyć kontroli dostępu opartej na rolach platformy Azure, aby udzielić określonych uprawnień użytkownikom, grupom i aplikacjom aż do zakresu pojedynczego kontenera obiektu blob lub kolejki.

Zaleca się używanie [usługi Azure AD do uwierzytelniania dostępu do magazynu.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Następny krok

Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.
