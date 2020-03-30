---
title: Cztery kroki do silnego fundamentu tożsamości — usługa Azure AD
description: W tym temacie opisano cztery kroki tożsamości hybrydowej klienci mogą podjąć w celu utworzenia podstawy silnej tożsamości.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206781"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Cztery kroki do silnego fundamentu tożsamości dzięki usłudze Azure Active Directory

Zarządzanie dostępem do aplikacji i danych nie może już polegać na tradycyjnych strategiach granic zabezpieczeń sieci, takich jak sieci obwodowe i zapory z powodu szybkiego przenoszenia aplikacji do chmury. Teraz organizacje muszą ufać swojemu rozwiązaniu tożsamości, aby kontrolować, kto i co ma dostęp do aplikacji i danych organizacji. Coraz więcej organizacji pozwala pracownikom na przynosze własne urządzenia do pracy i korzystanie z urządzeń z dowolnego miejsca, z którego mogą łączyć się z Internetem. Zapewnienie zgodności i bezpieczeństwa tych urządzeń stało się ważnym czynnikiem w rozwiązaniu tożsamości, które organizacja zdecyduje się zaimplementować. W dzisiejszym cyfrowym miejscu pracy [tożsamość jest główną płaszczyzną sterowania](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) każdej organizacji przechodzącej do chmury.

Przyjmując rozwiązanie do tożsamości hybrydowej usługi Azure Active Directory (Azure AD), organizacje uzyskują dostęp do funkcji premium, które odblokowują produktywność dzięki funkcjom automatyzacji, delegowania, samoobsługi i logowania jednokrotnego. Umożliwia pracownikom dostęp do zasobów firmy z dowolnego miejsca, w jakiej są im potrzebne, a jednocześnie umożliwia zespołowi IT regulowanie tego dostępu, zapewniając właściwym ludziom odpowiedni dostęp do odpowiednich zasobów w celu zapewnienia bezpiecznej wydajności.

Na podstawie naszych doświadczeń ta lista kontrolna najlepszych praktyk pomoże Ci szybko wdrożyć zalecane działania w celu zbudowania *silnego* fundamentu tożsamości w organizacji:

* Łatwe łączenie się z aplikacjami
* Automatyczne ustanawianie jednej tożsamości dla każdego użytkownika
* Zapewnij użytkownikom bezpieczeństwo
* Operacjonalizacja swoich spostrzeżeń

## <a name="step-1---connect-to-apps-easily"></a>Krok 1 - Łatwe łączenie się z aplikacjami

Łącząc aplikacje z usługą Azure AD, można zwiększyć produktywność i bezpieczeństwo użytkowników końcowych, włączając logowanie jednokrotne (Logowanie jednokrotne) i inicjowanie obsługi administracyjnej użytkowników. Zarządzając aplikacjami w jednym miejscu, usługi Azure AD, można zminimalizować obciążenie administracyjne i osiągnąć jeden punkt kontroli dla zasad zabezpieczeń i zgodności.

W tej sekcji opisano opcje zarządzania dostępem użytkowników do aplikacji, włączanie bezpiecznego dostępu zdalnego do aplikacji wewnętrznych oraz korzyści z migracji aplikacji do usługi Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Bezproblemowe udostępnianie aplikacji użytkownikom

Usługa Azure AD umożliwia administratorom [dodawanie aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) do galerii aplikacji przedsiębiorstwa w [witrynie Azure portal.](https://portal.azure.com/) Dodawanie aplikacji do galerii aplikacji enterprise ułatwia konfigurowanie aplikacji do używania usługi Azure AD jako dostawcy tożsamości. Umożliwia również zarządzanie dostępem użytkowników do aplikacji za pomocą zasad dostępu warunkowego i konfigurowanie logowania jednokrotnego (SSO) do aplikacji, dzięki czemu użytkownicy nie muszą wielokrotnie wprowadzać swoich haseł i są automatycznie logowane zarówno lokalnie, jak i aplikacji opartych na chmurze.

Po dodaniu aplikacji do galerii usługi Azure AD użytkownicy mogą wyświetlać aplikacje, które są do nich przypisane, a w razie potrzeby wyszukiwać i żądać innych aplikacji. Usługa Azure AD udostępnia [użytkownikom kilka metod](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) uzyskiwania dostępu do aplikacji:

* Panel Dostępu/Moje aplikacje
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Bezpośrednie łącza logowania

Aby dowiedzieć się więcej o dostępie użytkowników do aplikacji, zobacz **Krok 3 — Wzmocnienie pozycji użytkowników** w tym artykule.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrowanie aplikacji z usług federacyjnych Active Directory do usługi Azure AD

Migrowanie konfiguracji logowania jednokrotnego z usług federacyjnych Active Directory (ADFS) do usługi Azure AD umożliwia dodatkowe możliwości zabezpieczeń, bardziej spójne zarządzanie i współpracę. Aby uzyskać optymalne wyniki, zaleca się migrację aplikacji z usług AD FS do usługi Azure AD. Przeniesienie uwierzytelniania i autoryzacji aplikacji do usługi Azure AD zapewnia następujące korzyści:

* Zarządzanie kosztami
* Zarządzanie ryzykiem
* Zwiększenie wydajności
* Zajęcie się zgodnością i zarządzaniem

Aby dowiedzieć się więcej, zobacz oficjalny dokument [migrowanie aplikacji do usługi Azure Active Directory.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>Włącz bezpieczny zdalny dostęp do aplikacji

[Usługa Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) zapewnia proste rozwiązanie dla organizacji do publikowania aplikacji lokalnych w chmurze dla użytkowników zdalnych, którzy potrzebują dostępu do aplikacji wewnętrznych w bezpieczny sposób. Po pojedynczym logowaniu do usługi Azure AD użytkownicy mogą uzyskiwać dostęp zarówno do aplikacji w chmurze, jak i lokalnych za pośrednictwem zewnętrznych adresów URL lub wewnętrznego portalu aplikacji.

Usługa Azure AD Application Proxy oferuje następujące korzyści:

* Rozszerzanie usługi Azure AD na zasoby lokalne
  * Bezpieczeństwo i ochrona w skali chmury
  * Funkcje, takie jak dostęp warunkowy i uwierzytelnianie wieloskładnikowe, które można łatwo włączyć
* Brak składników w sieci obwodowej, takich jak VPN i tradycyjne rozwiązania odwrotnego serwera proxy
* Nie są wymagane połączenia przychodzące
* Logowanie jednokrotne (Logowanie jednokrotne) na różnych urządzeniach, zasobach i aplikacjach w chmurze i lokalnie
* Umożliwia użytkownikom końcowym produktywność w dowolnym miejscu i czasie

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Odkryj oprogramowanie Shadow IT dzięki usłudze Microsoft Cloud App Security

W nowoczesnych przedsiębiorstwach działy IT często nie są świadome wszystkich aplikacji w chmurze, które są używane przez użytkowników do wykonywania ich pracy. Gdy administratorzy IT są pytani, ile aplikacji w chmurze ich zdaniem korzystają ich pracownicy, średnio mówią 30 lub 40. W rzeczywistości średnia wynosi ponad 1000 oddzielnych aplikacji używanych przez pracowników w organizacji. 80% pracowników korzysta z aplikacji niesakonserwowanych, których nikt nie przeglądał i które mogą nie być zgodne z zasadami zabezpieczeń i zgodności.

[Usługa Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) może pomóc w zidentyfikowaniu przydatnych aplikacji, które są popularne wśród użytkowników, które mogą sankcjonować i dodawać do galerii aplikacji enterprise, dzięki czemu użytkownicy korzystają z funkcji, takich jak samouszczeniesz/r. i dostęp warunkowy.

<em>"Cloud**App Security** pomaga nam zapewnić, że nasi pracownicy właściwie korzystają z naszych aplikacji w chmurze i SaaS w sposób, który wspiera podstawowe zasady bezpieczeństwa, które pomagają chronić Accenture."</em> --- [John Blasi, dyrektor zarządzający, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oprócz wykrywania zaplecza informatycznego w tle, usługa MCAS może również określić poziom ryzyka aplikacji, zapobiegać nieautoryzowanemu dostępowi do danych firmowych, możliwemu wyciekowi danych i innym zagrożeniom bezpieczeństwa związanym z aplikacjami.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2 - Automatyczne ustanawianie jednej tożsamości dla każdego użytkownika

Połączenie katalogów lokalnych i opartych na chmurze w rozwiązaniu tożsamości hybrydowej usługi Azure AD umożliwi ponowne użycie istniejącej lokalnej inwestycji usługi Active Directory przez inicjowanie obsługi administracyjnej istniejących tożsamości w chmurze. Rozwiązanie synchronizuje tożsamości lokalne z usługą Azure AD, podczas gdy program IT utrzymuje lokalną usługę Active Directory z istniejącymi rozwiązaniami ładu jako podstawowe źródło prawdy dla tożsamości. Rozwiązanie tożsamości hybrydowej usługi Azure AD firmy Microsoft obejmuje funkcje lokalne i oparte na chmurze, tworząc wspólną tożsamość użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od ich lokalizacji.

Integracja katalogów lokalnych z usługą Azure AD sprawia, że użytkownicy są bardziej produktywni i uniemożliwiają użytkownikom korzystanie z wielu kont w aplikacjach i usługach, zapewniając wspólną tożsamość w celu uzyskania dostępu zarówno do zasobów w chmurze, jak i lokalnych. Korzystanie z wielu kont jest punktem bólu zarówno dla użytkowników końcowych, jak i it. Z punktu widzenia użytkownika końcowego posiadanie wielu kont oznacza konieczność zapamiętywania wielu haseł. Aby tego uniknąć, wielu użytkowników ponownie używać tego samego hasła dla każdego konta, co jest złe z punktu widzenia zabezpieczeń. Z punktu widzenia działu IT ponowne użycie często prowadzi do większej liczby resetowania haseł i kosztów pomocy technicznej wraz ze skargami użytkowników końcowych.

Usługa Azure AD Connect to narzędzie używane do synchronizowania tożsamości lokalnych z usługą Azure AD, które następnie może służyć do uzyskiwania dostępu do aplikacji w chmurze. Gdy tożsamości są w usłudze Azure AD, mogą aprowizować aplikacje SaaS, takie jak Salesforce lub Concur.

W tej sekcji wymienimy zalecenia dotyczące zapewnienia wysokiej dostępności, nowoczesnego uwierzytelniania dla chmury i zmniejszenia lokalnego śladu.

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o usłudze Azure AD Connect, zobacz [Co to jest usługa Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Konfigurowanie serwera przemieszczania usługi Azure AD Connect i utrzymywanie go na bieżąco

Usługa Azure AD Connect odgrywa kluczową rolę w procesie inicjowania obsługi administracyjnej. Jeśli serwer synchronizacji przejdzie w tryb offline z jakiegokolwiek powodu, zmiany w środowisku lokalnym nie zostaną zaktualizowane w chmurze i powodują problemy z dostępem dla użytkowników. Ważne jest zdefiniowanie strategii pracy awaryjnej, która umożliwia administratorom szybkie wznowienie synchronizacji po przejściu serwera synchronizacji w tryb offline.

Aby zapewnić wysoką dostępność w przypadku, gdy podstawowy serwer usługi Azure AD Connect przejdzie w tryb offline, zaleca się wdrożenie oddzielnego [serwera przemieszczania](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) dla usługi Azure AD Connect. Wdrożenie serwera umożliwia administratorowi "promowanie" serwera przejściowego do środowiska produkcyjnego za pomocą prostego przełącznika konfiguracji. Posiadanie serwera rezerwowego skonfigurowanego w trybie przejściowym umożliwia również testowanie i wdrażanie nowych zmian konfiguracji i wprowadzenie nowego serwera w przypadku likwidacji starego.

> [!TIP]
> Usługa Azure AD Connect jest aktualizowana regularnie. W związku z tym zdecydowanie zaleca się, aby zachować bieżący serwer przejściowy w celu skorzystania z ulepszeń wydajności, poprawek błędów i nowych możliwości, które zapewnia każda nowa wersja.

### <a name="enable-cloud-authentication"></a>Włączanie uwierzytelniania w chmurze

Organizacje z lokalną usługą Active Directory powinny rozszerzyć swój katalog na usługę Azure AD przy użyciu usługi Azure AD Connect i skonfigurować odpowiednią metodę uwierzytelniania. [Wybranie poprawnej metody uwierzytelniania](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) dla organizacji jest pierwszym krokiem w trakcie przenoszenia aplikacji do chmury. Jest to składnik krytyczny, ponieważ kontroluje dostęp do wszystkich danych i zasobów w chmurze.

Najprostszą i zalecaną metodą włączania uwierzytelniania w chmurze dla lokalnych obiektów katalogów w usłudze Azure AD jest włączenie [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Alternatywnie niektóre organizacje mogą rozważyć włączenie [uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Niezależnie od tego, czy wybierzesz PHS, czy PTA, nie zapomnij włączyć [bezproblemowego logowania jednokrotnego,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) aby umożliwić użytkownikom dostęp do aplikacji w chmurze bez ciągłego wprowadzania nazwy użytkownika i hasła w aplikacji podczas korzystania z urządzeń z systemem Windows 7 i 8 w sieci firmowej. Bez logowania jednokrotnego użytkownicy muszą pamiętać hasła specyficzne dla aplikacji i zalogować się do każdej aplikacji. Podobnie pracownicy działu IT muszą tworzyć i aktualizować konta użytkowników dla każdej aplikacji, takiej jak Office 365, Box i Salesforce. Użytkownicy muszą zapamiętać swoje hasła, a także poświęcić czas na zalogowanie się do każdej aplikacji. Zapewnienie ustandaryzowanego mechanizmu logowania jednokrotnego dla całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego doświadczenia użytkownika, zmniejszenia ryzyka, możliwości raportowania i zarządzania.

W przypadku organizacji, które już korzystają z usług AD FS lub innego lokalnego dostawcy uwierzytelniania, przejście na usługę Azure AD jako dostawcy tożsamości może zmniejszyć złożoność i zwiększyć dostępność. Jeśli nie masz szczególnych przypadków użycia do korzystania z federacji, zalecamy migrację z uwierzytelnienia federacyjnego do PHS i bezproblemowego logowania jednokrotnego lub PTA i bezproblemowego logowania jednokrotnego, aby cieszyć się korzyściami związanymi z mniejszym śladem lokalnym i elastycznością, jaką oferuje chmura lepsze doświadczenia użytkownika. Aby uzyskać więcej informacji, zobacz [Migrowanie z federacji do synchronizacji skrótów haseł dla usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Włączanie automatycznego anulowania obsługi administracyjnej kont

Włączenie automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji jest najlepszą strategią zarządzania cyklem życia tożsamości w wielu systemach. Usługa Azure AD obsługuje [automatyczne inicjowanie obsługi administracyjnej oparte na zasadach i usuwanie obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) kont użytkowników do różnych popularnych aplikacji SaaS, takich jak ServiceNow i Salesforce, oraz innych, które implementują [protokół SCIM 2.0](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). W przeciwieństwie do tradycyjnych rozwiązań inicjowania obsługi administracyjnej, które wymagają niestandardowego kodu lub ręcznego przekazywania plików CSV, usługa inicjowania obsługi administracyjnej jest hostowana w chmurze i zawiera wstępnie zintegrowane łączniki, które można skonfigurować i zarządzać za pomocą witryny Azure portal. Kluczową zaletą automatycznego anulowania obsługi administracyjnej jest to, że pomaga zabezpieczyć organizację, natychmiast usuwając tożsamości użytkowników z kluczowych aplikacji SaaS po opuszczeniu organizacji.

Aby dowiedzieć się więcej na temat automatycznego inicjowania obsługi administracyjnej konta użytkownika i jego działania, zobacz [Automatyzacja inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Krok 3 - Zapewnij użytkownikom bezpieczeństwo

W dzisiejszym cyfrowym miejscu pracy ważne jest, aby równoważyć bezpieczeństwo z wydajnością. Jednak użytkownicy końcowi często przesuwają środki bezpieczeństwa, które spowalniają ich produktywność i dostęp do aplikacji w chmurze. Aby rozwiązać ten problem, usługa Azure AD zapewnia możliwości samoobsługi, które umożliwiają użytkownikom zachowanie wydajności przy jednoczesnym zminimalizowaniu nakładu administracyjnego.

W tej sekcji wymieniono zalecenia dotyczące usuwania tarć z organizacji przez wzmocnienie pozycji użytkowników przy zachowaniu czujności.

### <a name="enable-self-service-password-reset-for-all-users"></a>Włącz samoobsługowe resetowanie hasła dla wszystkich użytkowników

[Samoobsługowe resetowanie haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) platformy Azure oferuje administratorom IT prosty sposób na umożliwienie użytkownikom resetowania i odblokowywania haseł lub kont bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami.

Domyślnie usługa Azure AD odblokowuje konta podczas resetowania hasła. Jednak po włączeniu integracji usługi Azure AD Connect [lokalnie,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)masz również możliwość oddzielenia tych dwóch operacji, które umożliwiają użytkownikom odblokowanie konta bez konieczności resetowania hasła.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Upewnij się, że wszyscy użytkownicy są zarejestrowani w uinakładanie mfa i sspr

Platforma Azure udostępnia raporty, które mogą być używane przez Ciebie i Twoją organizację, aby upewnić się, że użytkownicy są zarejestrowani dla uwierzytelniania WIELOWŁADNIKOWEJ i sspr. Użytkownicy, którzy nie zarejestrowali się, mogą wymagać wykształcenia w tym procesie.

[Raport logowania](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) usługi MFA zawiera informacje o użyciu usługi MFA i zawiera szczegółowe informacje na temat pracy usługi MFA w organizacji. Posiadanie dostępu do aktywności logowania (i inspekcji i wykrywania ryzyka) dla usługi Azure AD ma kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i dochodzeń kryminalistycznych.

Podobnie [samoobsługowego raportu zarządzania hasłami](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) można użyć do określenia, kto ma (lub nie) zarejestrowany dla samoobsługowego resetowania haseł.

### <a name="self-service-app-management"></a>Samoobsługowe zarządzanie aplikacjami

Zanim użytkownicy będą mogli samodzielnie wykrywać aplikacje z panelu dostępu, należy włączyć [samoobsługowy dostęp do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) do wszystkich aplikacji, do których chcesz zezwolić użytkownikom na samodzielne wykrywanie i żądanie dostępu. Dostęp do samoobsługi aplikacji to świetny sposób, aby umożliwić użytkownikom samodzielne wykrywanie aplikacji i opcjonalnie zezwolić grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Można zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników dla [aplikacji logowania jednokrotnego hasła](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) bezpośrednio z ich paneli dostępu.

### <a name="self-service-group-management"></a>Samoobsługowe zarządzanie grupami

Przypisywanie użytkowników do aplikacji najlepiej jest mapowane podczas korzystania z grup, ponieważ umożliwiają dużą elastyczność i możliwość zarządzania na dużą skalę:

* Oparte na atrybutach przy użyciu członkostwa w grupach dynamicznych
* Delegowanie do właścicieli aplikacji

Usługa Azure AD umożliwia zarządzanie dostępem do zasobów przy użyciu grup zabezpieczeń i grup usługi Office 365. Te grupy mogą być zarządzane przez właściciela grupy, który może zatwierdzać lub odrzucać żądania członkostwa i delegować kontrolę nad członkostwem w grupie. Ta funkcja, znana jako [samoobsługowe zarządzanie grupami,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)pozwala właścicielom grup, którym nie przypisano roli administracyjnej, tworzenie grup i zarządzanie nimi bez konieczności polegania na administratorach w celu obsługi żądań.

## <a name="step-4---operationalize-your-insights"></a>Krok 4 - Operationalize swoje spostrzeżenia

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów są podstawowymi składnikami wydajnej strategii, aby zapewnić, że użytkownicy pozostaną produktywni, a twoja organizacja jest bezpieczna. Dzienniki zabezpieczeń i raporty mogą pomóc w udzieleniu odpowiedzi na pytania, takie jak:

* Czy korzystasz z tego, za co płacisz?
* Czy w dzierżawie dzieje się coś podejrzanego lub złośliwego?
* Kto został poszkodowany podczas incydentu bezpieczeństwa?

Dzienniki zabezpieczeń i raporty zapewniają elektroniczny rejestr podejrzanych działań i pomagają wykrywać wzorce, które mogą wskazywać na próbę lub pomyślną penetrację zewnętrzną sieci oraz ataki wewnętrzne. Inspekcję można używać do monitorowania aktywności użytkowników, dokumentowania zgodności z przepisami, przeprowadzania analiz kryminalistycznych i innych czynności. Alerty zapewniają powiadomienia o zdarzeniach zabezpieczeń.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Przypisywanie najmniej uprzywilejowanych ról administratora dla operacji

Jak myślisz o swoim podejściu do operacji, istnieje kilka poziomów administracji do rozważenia. Pierwszy poziom nakłada ciężar administrowania na administratorów globalnych. Zawsze przy użyciu roli administratora globalnego, może być odpowiedni dla mniejszych firm. Jednak w przypadku większych organizacji z personelem pomocy technicznej i administratorami odpowiedzialnymi za określone zadania przypisanie roli administratora globalnego może stanowić zagrożenie dla bezpieczeństwa, ponieważ zapewnia tym osobom możliwość zarządzania zadaniami, które wykraczają poza zakres i nie tylko co powinni być w stanie zrobić.

W takim przypadku należy wziąć pod uwagę następny poziom administracji. Za pomocą usługi Azure AD można wyznaczyć użytkowników końcowych jako "ograniczonych administratorów", którzy mogą zarządzać zadaniami w rolach o mniej uprzywilejowanych. Na przykład można przypisać personelowi działu pomocy technicznej rolę [czytnika zabezpieczeń,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) aby zapewnić im możliwość zarządzania funkcjami związanymi z zabezpieczeniami z dostępem tylko do odczytu. A może warto przypisać rolę [administratora uwierzytelniania](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) do osób fizycznych, aby dać im możliwość resetowania poświadczeń innych niż hasło lub odczytu i konfigurowania usługi Azure Service Health.

Aby dowiedzieć się więcej, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorowanie składników hybrydowych (synchronizacja usługi Azure AD Connect, usługi AD FS) przy użyciu usługi Azure AD Connect Health

Usługi Azure AD Connect i usługi AD FS są krytycznymi składnikami, które mogą potencjalnie przerwać zarządzanie cyklem życia i uwierzytelnianie, a ostatecznie doprowadzić do awarii. W związku z tym należy wdrożyć usługi Azure AD Connect Health do monitorowania i raportowania tych składników.

Aby dowiedzieć się więcej, przeczytaj [artykuł Monitoruj usługi AD FS przy użyciu usługi Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Zbieranie dzienników danych do celów analitycznych za pomocą usługi Azure Monitor

[Usługa Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) to ujednolicony portal monitorowania dla wszystkich dzienników usługi Azure AD, który zapewnia szczegółowe informacje, zaawansowane analizy i inteligentne uczenie maszynowe. Za pomocą usługi Azure Monitor można korzystać z metryk i dzienników w portalu i za pośrednictwem interfejsów API, aby uzyskać lepszy wgląd w stan i wydajność zasobów. Umożliwia pojedynczą szybę środowiska w portalu, umożliwiając jednocześnie szeroką gamę integracji produktów za pośrednictwem interfejsów API i opcji eksportu danych, które obsługują tradycyjne systemy SIEM innych firm. Usługa Azure Monitor umożliwia również konfigurowanie reguł alertów w celu uzyskania powiadomień lub podejmowania zautomatyzowanych akcji dotyczących problemów mających wpływ na zasoby.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Twórz niestandardowe pulpity nawigacyjne dla swojego przywództwa i codziennego

Organizacje, które nie mają rozwiązania SIEM, mogą pobrać dodatek [Power BI Content Pack](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) for Azure AD. Pakiet zawartości usługi Power BI zawiera wstępnie utworzone raporty ułatwiające zrozumienie, w jaki sposób użytkownicy przyjmują i korzystają z funkcji usługi Azure AD, co pozwala uzyskać wgląd we wszystkie działania w katalogu. Możesz również utworzyć własny [niestandardowy pulpit nawigacyjny](https://docs.microsoft.com/power-bi/service-dashboards) i udostępnić je zespołowi kierowniczemu, aby raportować codzienne działania. Pulpity nawigacyjne to świetny sposób na monitorowanie firmy i szybki wgląd we wszystkie najważniejsze dane. Wizualizacje na pulpicie nawigacyjnym mogą pochodzić z jednego bazowego zestawu danych lub raportu albo wielu zestawów danych lub raportów. Pulpit nawigacyjny łączy dane lokalne i przechowywane w chmurze, zapewniają skonsolidowany widok bez względu na to, gdzie znajdują się dane.

![Niestandardowy pulpit nawigacyjny usługi Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Poznaj sterowniki połączeń pomocy technicznej

Po zaimplementowanie rozwiązania tożsamości hybrydowej, jak opisano w tym artykule, należy ostatecznie zauważyć zmniejszenie wywołań pomocy technicznej. Typowe problemy, takie jak zapomniane hasła i blokady kont, są łagodzone przez wdrożenie samoobsługowego resetowania hasła platformy Azure, a włączenie dostępu do aplikacji samoobsługowych umożliwia użytkownikom samodzielne wykrywanie i żądanie dostępu do aplikacji bez polegania pracowników IT.

Jeśli nie obserwujesz zmniejszenia liczby połączeń z obsługą, zalecamy przeanalizowanie sterowników połączeń pomocy technicznej w celu potwierdzenia, czy samoobsługowy dostęp do sspru lub samoobsługowego dostępu do aplikacji został poprawnie skonfigurowany lub jeśli istnieją inne nowe problemy, które można systematycznie Skierowana.

*"W naszej podróży do transformacji cyfrowej potrzebowaliśmy niezawodnego dostawcy zarządzania tożsamością i dostępem, aby ułatwić bezproblemową, ale bezpieczną integrację między nami, partnerami i dostawcami usług w chmurze, dla skutecznego ekosystemu; Usługa Azure AD była najlepszą opcją oferującą nam potrzebne możliwości i widoczność, które umożliwiły nam wykrywanie zagrożeń i reagowanie na nie".* --- [Yazan Almasri, globalny dyrektor ds.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorowanie korzystania z aplikacji w celu zwiększenia szczegółowych informacji

Oprócz odnajdowania funkcji Shadow IT monitorowanie użycia aplikacji w całej organizacji przy użyciu usługi [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) może pomóc twojej organizacji w przeglądarce w pełni wykorzystać obietnicę dotyczącą aplikacji w chmurze. Dzięki lepszemu wglądowi w aktywność i zwiększeniu ochrony krytycznych danych w aplikacjach w chmurze może pomóc w utrzymaniu kontroli nad zasobami. Monitorowanie użycia aplikacji w organizacji przy użyciu usługi MCAS może pomóc w udzieleniu odpowiedzi na następujące pytania:

* W jakich nieusankcjonowanych aplikacjach pracownicy używają danych?
* Gdzie i kiedy poufne dane są przechowywane w chmurze?
* Kto uzyskuje dostęp do poufnych danych w chmurze?

*"Dzięki usłudze Cloud App Security możemy szybko wykrywać anomalie i podejmować działania".* --- [Eric LePenske, starszy menedżer, bezpieczeństwo informacji, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów implementacji hybrydowego rozwiązania tożsamości, ale ta czteroetapowa lista kontrolna pomoże Ci szybko osiągnąć infrastrukturę tożsamości, która umożliwi użytkownikom większą produktywność i bezpieczeństwo.

* Łatwe łączenie się z aplikacjami
* Automatyczne ustanawianie jednej tożsamości dla każdego użytkownika
* Zapewnij użytkownikom bezpieczeństwo
* Operacjonalizacja swoich spostrzeżeń

Mamy nadzieję, że ten dokument jest użytecznym planem tworzenia silnej podstawy tożsamości dla Twojej organizacji.

## <a name="identity-checklist"></a>Lista kontrolna tożsamości

Zaleca się wydrukowanie poniższej listy kontrolnej w celach informacyjnych podczas rozpoczynania podróży do solidniejszego fundamentu tożsamości w organizacji.

### <a name="today"></a>Today

|Gotowe?|Element|
|:-|:-|
||Pilotażowe samoobsługowe resetowanie hasła (SSPR) dla grupy|
||Monitorowanie składników hybrydowych przy użyciu usługi Azure AD Connect Health|
||Przypisywanie najmniej uprzywilejowanych ról administratora do operacji|
||Odkryj oprogramowanie Shadow IT dzięki usłudze Microsoft Cloud App Security|
||Zbieranie dzienników danych do analizy za pomocą usługi Azure Monitor|

### <a name="next-two-weeks"></a>Kolejne dwa tygodnie

|Gotowe?|Element|
|:-|:-|
||Udostępnianie aplikacji użytkownikom|
||Pilotażowe inicjowanie obsługi administracyjnej usługi Azure AD dla wybranej aplikacji SaaS|
||Konfigurowanie serwera przemieszczania usługi Azure AD Connect i utrzymywanie go na bieżąco|
||Rozpoczynanie migracji aplikacji z usługi ADFS do usługi Azure AD|
||Twórz niestandardowe pulpity nawigacyjne dla swojego przywództwa i codziennego|

### <a name="next-month"></a>W przyszłym miesiącu

|Gotowe?|Element|
|:-|:-|
||Monitorowanie korzystania z aplikacji w celu zwiększenia szczegółowych informacji|
||Pilotuj bezpieczny zdalny dostęp do aplikacji|
||Upewnij się, że wszyscy użytkownicy są zarejestrowani w uinakładanie mfa i sspr|
||Włączanie uwierzytelniania w chmurze|

### <a name="next-three-months"></a>Kolejne trzy miesiące

|Gotowe?|Element|
|:-|:-|
||Włączanie samodzielnego zarządzania aplikacjami|
||Włączanie zarządzania grupami samoobsługowymi|
||Monitorowanie korzystania z aplikacji w celu zwiększenia szczegółowych informacji|
||Poznaj sterowniki połączeń pomocy technicznej|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zwiększyć swoją bezpieczną postawę przy użyciu możliwości usługi Azure Active Directory i tej pięcioetapowej listy kontrolnej — [pięć kroków do zabezpieczenia infrastruktury tożsamości.](https://aka.ms/securitysteps)

Dowiedz się, jak funkcje tożsamości w usłudze Azure AD mogą pomóc w przyspieszeniu przejścia do zarządzania podlegaego chmurze, udostępniając rozwiązania i możliwości, które umożliwiają organizacjom szybkie przyjęcie i przeniesienie większej liczby zarządzania tożsamościami z tradycyjnych systemów lokalnych do usługi Azure AD — [jak usługa Azure AD zapewnia zarządzanie w chmurze dla obciążeń lokalnych.](https://aka.ms/cloudgoverned)
