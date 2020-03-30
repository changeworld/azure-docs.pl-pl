---
title: Przewodnik po operacjach zarządzania uwierzytelnianiem usługi Azure Active Directory
description: W tym przewodniku po operacjach opisano kontrole i akcje, które należy podjąć w celu zabezpieczenia zarządzania uwierzytelnianiem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: bc5824fcb62477d4e6dc6c2b7390b1bfa916094f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368046"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Przewodnik po operacjach zarządzania uwierzytelnianiem usługi Azure Active Directory

W tej sekcji [przewodnika odnośniku operacji usługi Azure AD](active-directory-ops-guide-intro.md) opisano kontrole i akcje, które należy podjąć w celu zabezpieczenia i zarządzania poświadczeniami, definiowania środowiska uwierzytelniania, przypisywania delegowania, mierzenia użycia i definiowania zasad dostępu na podstawie postawy zabezpieczeń przedsiębiorstwa.

> [!NOTE]
> Zalecenia te są aktualne na dzień publikacji, ale mogą się zmieniać w czasie. Organizacje powinny stale oceniać swoje praktyki tożsamości w miarę rozwoju produktów i usług firmy Microsoft w miarę rozwoju w czasie.

## <a name="key-operational-processes"></a>Kluczowe procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie usługą Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu optymalizacji środowiska. Do kluczowych zadań i ich zalecanych właścicieli należą:

| Zadanie | Właściciel |
| :- | :- |
| Zarządzanie cyklem życia konfiguracji logowania jednokrotnego w usłudze Azure AD | Zespół operacyjny IAM |
| Projektowanie zasad dostępu warunkowego dla aplikacji usługi Azure AD | Zespół architektury InfoSec |
| Archiwizowanie aktywności logowania w systemie SIEM | Zespół operacyjny InfoSec |
| Archiwizowanie zdarzeń o podwyższonym ryzyku w systemie SIEM | Zespół operacyjny InfoSec |
| Klasyfikowanie i badanie raportów dotyczących zabezpieczeń | Zespół operacyjny InfoSec |
| Triage i zbadać zdarzenia ryzyka | Zespół operacyjny InfoSec |
| Klasyfikowanie i badanie użytkowników oznaczonych raportami o ryzyku i lukach w zabezpieczeniach z usługi Azure AD Identity Protection | Zespół operacyjny InfoSec |

> [!NOTE]
> Usługa Azure AD Identity Protection wymaga licencji usługi Azure AD Premium P2. Aby znaleźć odpowiednią licencję dla swoich wymagań, zobacz [Porównywanie ogólnie dostępnych funkcji wersji Azure AD Free i Azure AD Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Podczas przeglądania listy może okazać się konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosowanie własności do zadań z właścicielami, które nie są zgodne z powyższymi zaleceniami.

#### <a name="owner-recommended-reading"></a>Właściciel zaleca czytanie

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Nadzór na platformie Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Zarządzanie poświadczeniami

### <a name="password-policies"></a>Zasady dotyczące haseł

Bezpieczne zarządzanie hasłami jest jedną z najważniejszych części zarządzania tożsamościami i dostępem i często największym celem ataków. Usługa Azure AD obsługuje kilka funkcji, które mogą zapobiec pomyślnemu atakowi.

Skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie problemu, który należy rozwiązać:

| Problem | Zalecenie |
| :- | :- |
| Brak mechanizmu ochrony przed słabymi hasłami | Włączanie [samoobsługowego resetowania haseł usługi](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD i [ochrony hasłem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Brak mechanizmu wykrywania wyciekających haseł | Włącz [synchronizację skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) w celu uzyskania szczegółowych informacji |
| Korzystanie z usług AD FS i nie można przejść do uwierzytelniania zarządzanego | Włącz [inteligentną blokadę dodatku AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) i/lub [blokadę inteligentną usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Zasady haseł używają reguł opartych na złożoności, takich jak długość, wiele zestawów znaków lub wygaśnięcie | Rozważ ponownie na rzecz [zalecanych praktyk firmy Microsoft](https://aka.ms/passwordguidance) i przełącz swoje podejście do zarządzania hasłami i wdrażaj [ochronę hasłem usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) |
| Użytkownicy nie są zarejestrowani do korzystania z uwierzytelniania wieloskładnikowego (MFA) | [Zarejestruj wszystkie informacje zabezpieczające użytkownika,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) aby mogły być używane jako mechanizm weryfikacji tożsamości użytkownika wraz z hasłem |
| Nie ma odwołania haseł ze względu na ryzyko użytkownika | Wdrażanie [zasad ryzyka użytkownika usługi](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) Azure AD Identity Protection w celu wymuszenia zmian haseł w przypadku wyciekanych poświadczeń przy użyciu funkcji SSPR |
| Nie ma mechanizmu inteligentnej blokady, który chronił złośliwe uwierzytelnianie przed złymi podmiotami pochodzącymi ze zidentyfikowanych adresów IP | Wdrażanie uwierzytelniania zarządzanego w chmurze za pomocą synchronizacji skrótów haseł lub [uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Zasady haseł zalecane do czytania

- [Najważniejsze wskazówki dotyczące usługi Azure AD i AD FS: Ochrona przed atakami z szyfrowaniem haseł — Mobilność w przedsiębiorstwie + zabezpieczenia](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Włączanie samoobsługowego resetowania hasła i ochrony hasłem

Użytkownicy, którzy muszą zmienić lub zresetować swoje hasła, są jednym z największych źródeł głośności i kosztów połączeń pomocy technicznej. Oprócz kosztów zmiana hasła jako narzędzia w celu ograniczenia ryzyka użytkownika jest podstawowym krokiem w kierunku poprawy postawy zabezpieczeń organizacji.

Co najmniej zaleca się wdrożenie [samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) usługi Azure AD (SSPR) i lokalnej [ochrony hasłem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) w celu wykonania:

- Odchylanie połączeń z pomocą techniczną.
- Wymień użycie haseł tymczasowych.
- Zastąp wszystkie istniejące samoobsługowe rozwiązanie do zarządzania hasłami, które opiera się na rozwiązaniu lokalnym.
- [Wyeliminuj słabe hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) w organizacji.

> [!NOTE]
> W przypadku organizacji z subskrypcją usługi Azure AD Premium P2 zaleca się wdrożenie samowolu i używanie go w ramach [zasad dotyczących ryzyka użytkownika ochrony tożsamości.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="strong-credential-management"></a>Silne zarządzanie poświadczeniami

Hasła same w sobie nie są wystarczająco bezpieczne, aby uniemożliwić złym podmiotom uzyskanie dostępu do środowiska. Co najmniej każdy użytkownik z kontem uprzywilejowanym musi być włączony dla uwierzytelniania wieloskładnikowego (MFA). Najlepiej, jeśli należy włączyć [rejestrację połączoną](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) i wymagać od wszystkich użytkowników rejestracji w urzędzie obsługi klienta i wiele innych osób przy użyciu [połączonego doświadczenia rejestracji.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Ostatecznie zalecamy przyjęcie strategii [zapewniającej odporność](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) w celu zmniejszenia ryzyka blokady z powodu nieprzewidzianych okoliczności.

![Połączony przepływ doświadczenia użytkownika](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Odporność uwierzytelniania awarii lokalnej

Oprócz korzyści płynących z prostoty i umożliwienia wykrywania wycieków poświadczeń, synchronizacja hash (PHS) i usługa Azure MFA umożliwiają użytkownikom dostęp do aplikacji SaaS i usługi Office 365 pomimo awarii lokalnych spowodowanych cyberatakami, takimi jak [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Możliwe jest również włączenie PHS w połączeniu z federacją. Włączenie phs umożliwia rezerwowe uwierzytelniania, gdy usługi federacyjne nie są dostępne.

Jeśli w organizacji lokalnej brakuje strategii odporności awarii lub ma strategię, która nie jest zintegrowana z usługą Azure AD, należy wdrożyć usługi Azure AD PHS i zdefiniować plan odzyskiwania po awarii, który zawiera phs. Włączenie usługi Azure AD PHS umożliwi użytkownikom uwierzytelnianie się w usłudze Azure AD, jeśli lokalna usługa Active Directory będzie niedostępna.

![przepływ synchronizacji skrótu hasła](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Aby lepiej zrozumieć opcje uwierzytelniania, zobacz [Wybieranie właściwej metody uwierzytelniania dla rozwiązania do tożsamości hybrydowej usługi Azure Active Directory.](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)

### <a name="programmatic-usage-of-credentials"></a>Programowe użycie poświadczeń

Skrypty usługi Azure AD przy użyciu programu PowerShell lub aplikacji korzystających z interfejsu API programu Microsoft Graph wymagają bezpiecznego uwierzytelniania. Słabe zarządzanie poświadczeniami wykonujące te skrypty i narzędzia zwiększa ryzyko kradzieży poświadczeń. Jeśli używasz skryptów lub aplikacji, które opierają się na hasłach zakodowanych na stałe lub monitach o hasło, należy najpierw przejrzeć hasła w plikach konfiguracyjnych lub kodzie źródłowym, a następnie zastąpić te zależności i używać zarządzanych tożsamości platformy Azure, uwierzytelniania zintegrowanego systemu Windows lub [certyfikatów,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) gdy tylko jest to możliwe. W przypadku aplikacji, w których poprzednie rozwiązania nie są możliwe, należy rozważyć użycie [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Jeśli okaże się, że istnieją jednostki usługi z poświadczeniami hasła i nie masz pewności, jak te poświadczenia hasła są zabezpieczone przez skrypty lub aplikacje, skontaktuj się z właścicielem aplikacji, aby lepiej zrozumieć wzorce użycia.

Firma Microsoft zaleca również kontakt z właścicielami aplikacji, aby zrozumieć wzorce użycia, jeśli istnieją podmioty usługi z poświadczeniami hasła.

## <a name="authentication-experience"></a>Doświadczenie uwierzytelniania

### <a name="on-premises-authentication"></a>Uwierzytelnianie lokalne

Uwierzytelnianie federacyjne ze zintegrowanym uwierzytelnianiem systemu Windows (IWA) lub bezproblemowym logowaniem jednokrotnym (SSO) zarządzane z synchronizacją hash hasła lub uwierzytelnianiem pass-through jest najlepszym doświadczeniem użytkownika w sieci firmowej do lokalnych kontrolerów domeny. Minimalizuje zmęczenie poświadczeń i zmniejsza ryzyko, że użytkownicy padają ofiarą ataków phishingowych. Jeśli uwierzytelnianie zarządzane w chmurze jest już używane za pomocą phs lub PTA, ale użytkownicy nadal muszą wpisywać swoje hasło podczas uwierzytelniania lokalnego, należy natychmiast [wdrożyć bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Z drugiej strony, jeśli są obecnie federowane z planami, aby ostatecznie przeprowadzić migrację do uwierzytelniania zarządzanego w chmurze, należy zaimplementować bezproblemowe logowanie jednokrotne w ramach projektu migracji.

### <a name="device-trust-access-policies"></a>Zasady dostępu zaufania do urządzeń

Podobnie jak użytkownik w organizacji, urządzenie jest podstawową tożsamością, którą chcesz chronić. Możesz użyć tożsamości urządzenia, aby chronić swoje zasoby w dowolnym czasie i z dowolnego miejsca.Uwierzytelnianie urządzenia i rozliczanie jego typu zaufania poprawia postawę bezpieczeństwa i użyteczność poprzez:

- Unikanie tarcia, na przykład w przypadku usługi MFA, gdy urządzenie jest zaufane
- Blokowanie dostępu z niezaufanych urządzeń
- W przypadku urządzeń z systemem Windows 10 [bezproblemowo udostępniaj logowanie jednokrotne do zasobów lokalnych.](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)

Ten cel można osiągnąć, wprowadzając tożsamości urządzeń i zarządzając nimi w usłudze Azure AD przy użyciu jednej z następujących metod:

- Organizacje mogą używać [usługi Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) do zarządzania urządzeniem i wymuszania zasad zgodności, testowania kondycji urządzenia i ustawiania zasad dostępu warunkowego na podstawie tego, czy urządzenie jest zgodne. Usługa Microsoft Intune może zarządzać urządzeniami z systemem iOS, komputerami mac (integracja z JAMF), komputerami stacjonarnymi z systemem Windows (natywnie korzystającymi z zarządzania urządzeniami przenośnymi dla systemu Windows 10 oraz współzarządzaniem z programem Microsoft Endpoint Configuration Manager) i urządzeniami przenośnymi z systemem Android.
- [Hybrydowe dołączanie do usługi AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) zapewnia zarządzanie za pomocą zasad grupy lub programu Microsoft Endpoint Configuration Manager w środowisku z urządzeniami komputerów przyłączonych do domeny usługi Active Directory. Organizacje mogą wdrażać środowisko zarządzane za pośrednictwem phs lub PTA z bezproblemowym logowaniem jednokrotnym. Przeniesienie urządzeń do usługi Azure AD maksymalizuje produktywność użytkowników za pośrednictwem jednokrotnego przysłowania jednocześnie w chmurze i w zasobach lokalnych, umożliwiając jednocześnie bezpieczny dostęp do zasobów w chmurze i lokalnie za pomocą [dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) warunkowego.

Jeśli masz urządzenia z systemem Windows przyłączone do domeny, które nie są zarejestrowane w chmurze lub urządzenia z systemem Windows przyłączone do domeny, które są zarejestrowane w chmurze, ale bez zasad dostępu warunkowego, należy zarejestrować niezarejestrowane urządzenia i w obu przypadkach [użyć hybrydowego sprzężenia usługi Azure AD jako formantu](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) w zasadach dostępu warunkowego.

![Zrzut ekranu przedstawiający przyznanie w zasadach dostępu warunkowego wymagających urządzenia hybrydowego](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Jeśli zarządzasz urządzeniami z mdm lub microsoft intune, ale nie używasz formantów urządzeń w zasadach dostępu warunkowego, zalecamy użycie [funkcji Wymagaj urządzenia, aby oznaczyć](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) je jako zgodne jako formant w tych zasadach.

![Zrzut ekranu przedstawiający przyznanie w zasadach dostępu warunkowego wymagających zgodności z urządzeniami](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Zalecane jest czytanie zasad dostępu zaufania urządzenia

- [Jak: Planowanie implementacji dołączania do hybrydowej usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Konfiguracje obsługi tożsamości i uzyskiwania dostępu do urządzeń](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello dla firm

W systemie Windows 10 [funkcja Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) zastępuje hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach. Funkcja Windows Hello dla firm umożliwia użytkownikom usprawnienie korzystania z usługi MFA i zmniejsza zależność od haseł. Jeśli nie rozpoczęto wdrażania urządzeń z systemem Windows 10 lub tylko częściowo je wdrożyłeś, zalecamy uaktualnienie do systemu Windows 10 i [włączenie funkcji Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) na wszystkich urządzeniach.

Jeśli chcesz dowiedzieć się więcej o uwierzytelnianiu bez użycia hasła, zobacz [Świat bez haseł w usłudze Azure Active Directory](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Uwierzytelnianie i przypisywanie aplikacji

### <a name="single-sign-on-for-apps"></a>Logowanie jednokrotne dla aplikacji

Zapewnienie ustandaryzowanego mechanizmu logowania jednokrotnego dla całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego doświadczenia użytkownika, zmniejszenia ryzyka, możliwości raportowania i zarządzania. Jeśli używasz aplikacji, które obsługują sytuowane usługi SSO z usługą Azure AD, ale są obecnie skonfigurowane do używania kont lokalnych, należy ponownie skonfigurować te aplikacje do używania usługi SSO z usługą Azure AD. Podobnie jeśli używasz żadnych aplikacji, które obsługują jedno i drugie z usługą Azure AD, ale są przy użyciu innego dostawcy tożsamości, należy ponownie skonfigurować te aplikacje do używania usługi SSO z usługą Azure AD, jak również. W przypadku aplikacji, które nie obsługują protokołów federacyjnych, ale obsługują uwierzytelnianie oparte na formularzach, zaleca się skonfigurowanie aplikacji do [używania przechowywania haseł](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) za pomocą serwera proxy aplikacji usługi Azure AD.

![Logowanie oparte na hasłach AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Jeśli nie masz mechanizmu wykrywania niezarządzanych aplikacji w organizacji, zalecamy wdrożenie procesu odnajdywania przy użyciu rozwiązania brokera zabezpieczeń dostępu do chmury (CASB), takiego jak [Microsoft Cloud App Security .](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)

Na koniec, jeśli masz galerię aplikacji usługi Azure AD i używasz aplikacji obsługujących dane sytuacyjne z usługą Azure AD, zalecamy [umieszczenie aplikacji na liście w galerii aplikacji.](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)

#### <a name="single-sign-on-recommended-reading"></a>Zalecane czytanie logowania jednokrotnego

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migracja aplikacji usług AD FS do usługi Azure AD

[Migrowanie aplikacji z usług AD FS do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) umożliwia dodatkowe możliwości w zakresie zabezpieczeń, bardziej spójne zarządzanie i lepsze środowisko współpracy. Jeśli masz aplikacje skonfigurowane w usługach AD FS, które obsługują sytą usługę Azure AD, należy ponownie skonfigurować te aplikacje do używania usługi SSO z usługą Azure AD. Jeśli masz aplikacje skonfigurowane w usługach AD FS z nietypowych konfiguracji nieobsługiwał przez usługę Azure AD, należy skontaktować się z właścicielami aplikacji, aby zrozumieć, czy specjalna konfiguracja jest bezwzględnym wymaganiem aplikacji. Jeśli nie jest to wymagane, należy ponownie skonfigurować aplikację do używania usługi SSO z usługą Azure AD.

![Usługa Azure AD jako podstawowy dostawca tożsamości](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Usługa Azure AD Connect Health dla usługi ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) może służyć do zbierania szczegółów konfiguracji dotyczących każdej aplikacji, która może zostać zmigrowana do usługi Azure AD.

### <a name="assign-users-to-applications"></a>Przypisywanie użytkowników do aplikacji

[Przypisywanie użytkowników do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) najlepiej jest mapowane przy użyciu grup, ponieważ umożliwiają one większą elastyczność i możliwość zarządzania na dużą skalę. Korzyści płynące z używania grup obejmują [dynamiczne członkostwo w grupach oparte na atrybutach](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) i [delegowanie do właścicieli aplikacji.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners) W związku z tym jeśli już używasz grup i zarządzasz nimi, zalecamy podjęcie następujących działań w celu poprawy zarządzania na dużą skalę:

- Delegować zarządzanie grupami i zarządzanie do właścicieli aplikacji.
- Zezwalaj na samoobsługowy dostęp do aplikacji.
- Zdefiniuj grupy dynamiczne, jeśli atrybuty użytkownika mogą konsekwentnie określać dostęp do aplikacji.
- Zaimplementuj zaświadczanie do grup używanych do dostępu do aplikacji przy użyciu [przeglądów dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Z drugiej strony, jeśli znajdziesz aplikacje, które mają przypisanie do poszczególnych użytkowników, należy zaimplementować [zarządzania](https://docs.microsoft.com/azure/active-directory/governance/index) wokół tych aplikacji.

#### <a name="assign-users-to-applications-recommended-reading"></a>Przypisywanie użytkowników do aplikacji zalecanych do odczytu

- [Przypisywanie użytkowników i grup do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegowanie uprawnień rejestracji aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Dynamiczne reguły członkostwa dla grup w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Zasady dostępu

### <a name="named-locations"></a>Nazwane lokalizacje

W usługach Azure AD [można](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) oznaczyć nazwy zakresów adresów IP w organizacji. Usługa Azure AD używa nazwanych lokalizacji w następujących celach:

- Zapobiegaj fałszywym alarmom w zdarzeniach o podwyższonym ryzyku. Logowanie się z zaufanej lokalizacji sieciowej zmniejsza ryzyko logowania użytkownika.
- Skonfiguruj [dostęp warunkowy oparty na lokalizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Nazwana lokalizacja](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Na podstawie priorytetu skorzystaj z poniższej tabeli, aby znaleźć zalecane rozwiązanie, które najlepiej odpowiada potrzebom Twojej organizacji:

| **Priorytet** | **Scenariusz** | **Zalecenie** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Jeśli używasz PHS lub PTA i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu poprawy wykrywania zdarzeń o podwyższonym ryzyku |
| 2 | Jeśli jesteś sfederowany i nie używaj oświadczenia "insideCorporateNetwork" i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu poprawy wykrywania zdarzeń o podwyższonym ryzyku |
| 3 | Jeśli nie używasz nazwanych lokalizacji w zasadach dostępu warunkowego i nie ma żadnych kontroli ryzyka ani kontroli urządzeń w zasadach dostępu warunkowego | Konfigurowanie zasad dostępu warunkowego w celu uwzględnienia nazwanych lokalizacji |
| 4 | Jeśli jesteś sfederowany i użyj "insideCorporateNetwork" oświadczenie i nazwane lokalizacje nie zostały zdefiniowane | Definiowanie nazwanych lokalizacji w celu poprawy wykrywania zdarzeń o podwyższonym ryzyku |
| 5 | Jeśli używasz zaufanych adresów IP z uwierzytelnianiem USŁUGI MFA, a nie z nazwanymi lokalizacjami i oznaczasz je jako zaufane | Definiowanie nazwanych lokalizacji i oznaczanie ich jako zaufanych w celu poprawy wykrywania zdarzeń o podwyższonym ryzyku |

### <a name="risk-based-access-policies"></a>Zasady dostępu oparte na ryzyku

Usługa Azure AD może obliczyć ryzyko dla każdego logowania i każdego użytkownika. Używanie ryzyka jako kryterium w zasadach dostępu może zapewnić lepsze środowisko użytkownika, na przykład mniejszą liczbę monitów o uwierzytelnianie i lepsze zabezpieczenia, na przykład, tylko monitować użytkowników, gdy są potrzebne, i zautomatyzować odpowiedzi i korygowania.

![Zasady dotyczące ryzyka logowania](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Jeśli posiadasz już licencje usługi Azure AD Premium P2, które obsługują ryzyko w zasadach dostępu, ale nie są używane, zdecydowanie zalecamy dodanie ryzyka do postawy zabezpieczeń.

#### <a name="risk-based-access-policies-recommended-reading"></a>Zasady dostępu oparte na ryzyku zalecane

- [Jak: Konfigurowanie zasad ryzyka logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Jak: Konfigurowanie zasad dotyczących ryzyka użytkownika](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Zasady dostępu do aplikacji klienckich

Usługa Microsoft Intune Application Management (MAM) umożliwia wypychanie kontroli ochrony danych, takich jak szyfrowanie pamięci masowej, numer PIN, oczyszczanie magazynu zdalnego itp., do zgodnych aplikacji klienckich, takich jak Outlook Mobile. Ponadto można utworzyć zasady dostępu warunkowego, aby [ograniczyć dostęp](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) do usług w chmurze, takich jak Usługa Exchange Online, z zatwierdzonych lub zgodnych aplikacji.

Jeśli pracownicy instalują aplikacje obsługujące usługę MAM, takie jak aplikacje mobilne pakietu Office, aby uzyskać dostęp do zasobów firmowych, takich jak Exchange Online lub SharePoint Online, a także obsługujesz byod (przynieś własne urządzenie), zalecamy wdrożenie zasad aplikacji MAM w celu zarządzania konfiguracja aplikacji w urządzeniach będących własnością osobistą bez rejestracji mdm, a następnie zaktualizować zasady dostępu warunkowego, aby zezwolić tylko na dostęp od klientów obsługujących usługę MAM.

![Kontrola dotacji dostępu warunkowego](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Jeśli pracownicy instalują aplikacje obsługujące usługę MAM w stosunku do zasobów firmowych, a dostęp jest ograniczony na zarządzanych urządzeniach usługi Intune, należy rozważyć wdrożenie zasad mam aplikacji w celu zarządzania konfiguracją aplikacji dla urządzeń osobistych, oraz aktualizowanie zasad dostępu warunkowego, aby zezwolić tylko na dostęp od klientów obsługujących usługę MAM.

### <a name="conditional-access-implementation"></a>Implementacja dostępu warunkowego

Dostęp warunkowy jest podstawowym narzędziem służącym do poprawy postawy bezpieczeństwa organizacji. W związku z tym ważne jest, aby postępować zgodnie z tymi najlepszymi praktykami:

- Upewnij się, że wszystkie aplikacje SaaS mają co najmniej jedną zasadę
- Unikaj łączenia filtru **Wszystkie aplikacje** z kontrolką **bloku,** aby uniknąć ryzyka blokady
- Unikaj używania **wszystkich użytkowników** jako filtru i nieumyślnego **dodawania**
- **Migrowanie wszystkich "starszych" zasad do witryny Azure portal**
- Złap wszystkie kryteria dla użytkowników, urządzeń i aplikacji
- Użyj zasad dostępu warunkowego do [zaimplementowania usługi MFA,](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)a nie przy użyciu **usługi MFA dla użytkownika**
- Mieć mały zestaw podstawowych zasad, które mogą mieć zastosowanie do wielu aplikacji
- Definiowanie pustych grup wyjątków i dodawanie ich do zasad w celu strategii wyjątków
- Planowanie rachunków [ze szkła łamania](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) bez kontroli usługi MFA
- Zapewnij spójne środowisko w aplikacjach klienckich usługi Office 365, na przykład w zespołach, usłudze OneDrive dla Firm, w programie Outlook itp.) poprzez wdrożenie tego samego zestawu formantów dla usług takich jak Exchange Online i Sharepoint Online
- Przypisywanie do polityk powinno być realizowane za pośrednictwem grup, a nie osób
- Czy regularne przeglądy grup wyjątków używanych w zasadach, aby ograniczyć czas użytkownicy są poza postawy zabezpieczeń. Jeśli jesteś właścicielem usługi Azure AD P2, możesz użyć recenzji dostępu, aby zautomatyzować proces

#### <a name="conditional-access-recommended-reading"></a>Zalecany odczyt dostępu warunkowego

- [Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Konfiguracje obsługi tożsamości i uzyskiwania dostępu do urządzeń](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Odwołanie do ustawień dostępu warunkowego usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Typowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Powierzchnia dostępu

### <a name="legacy-authentication"></a>Uwierzytelnianie starsze

Silne poświadczenia, takie jak usługa MFA, nie mogą chronić aplikacji przy użyciu starszych protokołów uwierzytelniania, co czyni go preferowanym wektorem ataku przez złośliwe podmioty. Blokowanie uwierzytelniania starszego ma kluczowe znaczenie dla poprawy postawy zabezpieczeń dostępu.

Uwierzytelnianie starsze to termin, który odnosi się do protokołów uwierzytelniania używanych przez aplikacje, takie jak:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
- Klienci korzystający z protokołów poczty, takich jak IMAP/SMTP/POP

Atakujący zdecydowanie preferują te protokoły - w rzeczywistości prawie [100% ataków na spray hasłem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) używa starszych protokołów uwierzytelniania! Hakerzy używają starszych protokołów uwierzytelniania, ponieważ nie obsługują interaktywnego logowania, które jest potrzebne do dodatkowych wyzwań związanych z zabezpieczeniami, takich jak uwierzytelnianie wieloskładnikowe i uwierzytelnianie urządzeń.

Jeśli uwierzytelnianie starsze jest szeroko stosowane w twoim środowisku, należy zaplanować migrację starszych klientów do klientów [obsługujących nowoczesne uwierzytelnianie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) tak szybko, jak to możliwe. W tym samym tokenie, jeśli niektórzy użytkownicy już używają nowoczesnego uwierzytelniania, ale inni, którzy nadal używają starszego uwierzytelniania, należy wykonać następujące kroki, aby zablokować starszych klientów uwierzytelniania:

1. Raporty [aktywności logowania służą](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) do identyfikowania użytkowników, którzy nadal korzystają ze starszego uwierzytelniania i korygowania planu:

   a. Uaktualnij do nowoczesnych klientów obsługujących uwierzytelnianie do użytkowników, których dotyczy problem.
   
   b. Zaplanuj skrócony harmonogram, aby zablokować na kolejne kroki.
   
   d. Określ, jakie starsze aplikacje mają twardą zależność od starszego uwierzytelniania. Patrz krok 3 poniżej.

2. Wyłącz starsze protokoły u źródła (na przykład skrzynka pocztowa programu Exchange) dla użytkowników, którzy nie używają starszej akcesji, aby uniknąć większej ekspozycji.
3. W przypadku pozostałych kont (najlepiej tożsamości innych niż ludzkie, takie jak konta usług), należy użyć [dostępu warunkowego, aby ograniczyć starsze protokoły](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) po uwierzytelnieniu.

#### <a name="legacy-authentication-recommended-reading"></a>Zalecane czytanie uwierzytelniania starszego

- [Włączanie lub wyłączanie dostępu pop3 lub IMAP4 do skrzynek pocztowych w programie Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Dotacje na zgodę

W przypadku ataku z niedozwoloną zgodą osoba atakująca tworzy aplikację zarejestrowaną w usłudze Azure AD, która żąda dostępu do danych, takich jak informacje kontaktowe, adres e-mail lub dokumenty. Użytkownicy mogą udzielać zgody na złośliwe aplikacje za pośrednictwem ataków phishingowych podczas lądowania na złośliwych stronach internetowych.

Poniżej znajduje się lista aplikacji z uprawnieniami, które można zbadać w odniesieniu do usług w chmurze firmy Microsoft:

- Aplikacje z aplikacją lub delegowane \*. Uprawnienia readwrite
- Aplikacje z uprawnieniami delegowanymi mogą odczytywać, wysyłać lub zarządzać wiadomościami e-mail w imieniu użytkownika
- Aplikacje, którym udzielono następujących uprawnień:

| Zasób | Uprawnienie |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| Interfejs API programu Microsoft Graph | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite (Wyślij do wiadomości) adres Mail.ReadWrite |

- Aplikacje przyznały pełne podszywanie się pod zalogowanego użytkownika. Przykład:

|Zasób | Uprawnienie |
| :- | :- |
| Interfejs API programu Microsoft Graph| Directory.AccessAsUser.All |
| Interfejs API REST platformy Azure | user_impersonation |

Aby uniknąć tego scenariusza, należy odwołać się do [wykrywania i korygowania niedozwolenia dotacji w usłudze Office 365,](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) aby zidentyfikować i naprawić wszystkie aplikacje z nielegalnych dotacji lub aplikacji, które mają więcej dotacji niż są to konieczne. Następnie [należy całkowicie usunąć samoobsługę](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) i [ustanowić procedury zarządzania](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Na koniec zaplanuj regularne przeglądy uprawnień aplikacji i usuń je, gdy nie są potrzebne.

#### <a name="consent-grants-recommended-reading"></a>Zgoda udziela zalecanej lektury

- [Uprawnienia interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Ustawienia użytkownika i grupy

Poniżej znajdują się ustawienia użytkownika i grupy, które można zablokować, jeśli nie ma wyraźnej potrzeby biznesowej:

#### <a name="user-settings"></a>Ustawienia użytkownika

- **Użytkownicy zewnętrzni** — współpraca zewnętrzna może odbywać się organicznie w przedsiębiorstwie dzięki usługom, takim jak Teams, Power BI, Sharepoint Online i Azure Information Protection. Jeśli masz jawne ograniczenia do kontrolowania współpracy zewnętrznej inicjowane przez użytkownika, zaleca się włączenie użytkowników zewnętrznych przy użyciu [zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) lub kontrolowanej operacji, takich jak za pośrednictwem działu pomocy technicznej. Jeśli nie chcesz zezwalać na organiczną współpracę zewnętrzną usług, możesz [całkowicie zablokować członkom możliwość zapraszania użytkowników zewnętrznych.](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations) Alternatywnie można również [zezwolić lub zablokować określone domeny](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) w zaproszeniach użytkowników zewnętrznych.
- **Rejestracje aplikacji** — gdy rejestracje aplikacji są włączone, użytkownicy końcowi mogą samodzielnie dołączać aplikacje i udzielą dostępu do swoich danych. Typowym przykładem rejestracji aplikacji są użytkownicy, którzy włączają wtyczki programu Outlook lub asystentów głosowych, takich jak Alexa i Siri, aby odczytywać wiadomości e-mail i kalendarz lub wysyłać wiadomości e-mail w ich imieniu. Jeśli klient zdecyduje się wyłączyć rejestrację aplikacji, zespoły InfoSec i IAM muszą być zaangażowane w zarządzanie wyjątkami (rejestracje aplikacji, które są potrzebne na podstawie wymagań biznesowych), ponieważ muszą zarejestrować aplikacje za pomocą konta administratora, i najprawdopodobniej wymagają projektowania procesu w celu operacjonalizacji procesu.
- **Portal administracyjny** — organizacje mogą zablokować obiekt Azure AD w witrynie Azure portal, dzięki czemu osoby niebędące administratorami nie mogą uzyskać dostępu do zarządzania usługą Azure AD w witrynie Azure portal i pomylić się. Przejdź do ustawień użytkownika w portalu zarządzania usługi Azure AD, aby ograniczyć dostęp:

![Dostęp z ograniczeniami portalu administracyjnego](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Nie-adminstrators nadal można uzyskać dostęp do interfejsów zarządzania usługi Azure AD za pośrednictwem wiersza polecenia i innych interfejsów programowych.

#### <a name="group-settings"></a>Ustawienia grupy

**Samoobsługowe zarządzanie grupami / Użytkownicy mogą tworzyć grupy zabezpieczeń / grupy O365.** Jeśli nie ma bieżącej inicjatywy samoobsługi dla grup w chmurze, klienci mogą zdecydować się wyłączyć ją, dopóki nie będą gotowi do korzystania z tej funkcji.

#### <a name="groups-recommended-reading"></a>Grupy zalecane czytanie

- [Co to jest współpraca w usłudze Azure Active Directory B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Aplikacje, uprawnienia i zgoda w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Zarządzanie dostępem do zasobów w usłudze Azure Active Directory za pomocą grup](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Konfigurowanie samoobsługowego zarządzania dostępem do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Ruch z nieoczekiwanych lokalizacji

Napastnicy pochodzą z różnych części świata. Zarządzaj tym ryzykiem przy użyciu zasad dostępu warunkowego z lokalizacją jako warunkiem. [Warunek lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) zasad dostępu warunkowego umożliwia blokowanie dostępu do lokalizacji, z których nie ma powodu biznesowego, aby się zalogować.

![Tworzenie nowej nazwanej lokalizacji](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Jeśli jest dostępna, użyj rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), aby analizować i znajdować wzorce dostępu w różnych regionach. Jeśli nie używasz produktu SIEM lub nie jest pozyskiwania informacji uwierzytelniania z usługi Azure AD, zaleca się użycie [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) do identyfikowania wzorców dostępu w różnych regionach.

## <a name="access-usage"></a>Dostęp do użycia

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Dzienniki usługi Azure AD zarchiwizowane i zintegrowane z planami reagowania na incydenty

Posiadanie dostępu do działania logowania, inspekcji i zdarzeń ryzyka dla usługi Azure AD ma kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i dochodzeń kryminalistycznych. Usługa Azure AD zapewnia dostęp do tych źródeł za pośrednictwem interfejsów API REST, które mają ograniczony okres przechowywania. System zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) lub równoważna technologia archiwizacji ma kluczowe znaczenie dla długoterminowego przechowywania audytów i możliwości wsparcia. Aby włączyć długoterminowe przechowywanie dzienników usługi Azure AD, należy dodać je do istniejącego rozwiązania SIEM lub użyć [usługi Azure Monitor.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Archiwizuj dzienniki, które mogą być używane jako część planów reagowania na incydenty i dochodzeń.

#### <a name="logs-recommended-reading"></a>Logi zalecane czytanie

- [Odwołanie do interfejsu API inspekcji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Odwołanie do interfejsu API raportu logowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph for Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Odwołanie do interfejsu API działania zarządzania usługą Office 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Podsumowanie

Istnieje 12 aspektów bezpiecznej infrastruktury tożsamości. Ta lista pomoże Ci dodatkowo zabezpieczyć poświadczenia i zarządzać nimi, definiować środowisko uwierzytelniania, delegować przypisanie, mierzyć użycie i definiować zasady dostępu na podstawie postawy zabezpieczeń przedsiębiorstwa.

- Przypisz właścicieli do kluczowych zadań.
- Wdrażanie rozwiązań do wykrywania słabych lub wyciekających haseł, poprawy zarządzania hasłami i ochrony oraz dalszego bezpiecznego dostępu użytkowników do zasobów.
- Zarządzaj tożsamością urządzeń, aby chronić swoje zasoby w dowolnym czasie i z dowolnego miejsca.
- Zaimplementuj uwierzytelnianie bez hasła.
- Podaj znormalizowany mechanizm logowania jednokrotnego w całej organizacji.
- Migruj aplikacje z usług AD FS do usługi Azure AD, aby umożliwić lepsze zabezpieczenia i bardziej spójne zarządzanie.
- Przypisz użytkowników do aplikacji przy użyciu grup, aby umożliwić większą elastyczność i możliwość zarządzania na dużą skalę.
- Skonfiguruj zasady dostępu oparte na ryzyku.
- Blokowanie starszych protokołów uwierzytelniania.
- Wykrywanie i korygowanie nielegalnych dotacji zgody.
- Blokowanie ustawień użytkownika i grupy.
- Włącz długoterminowe przechowywanie dzienników usługi Azure AD w celu rozwiązywania problemów, analizy użycia i dochodzeń kryminalistycznych.

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [kontroli operacyjnych i akcji zarządzania tożsamościami](active-directory-ops-guide-govern.md).
