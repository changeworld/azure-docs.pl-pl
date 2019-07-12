---
title: Cztery kroki, które foundation silna tożsamość w usłudze Azure Active Directory
description: W tym temacie opisano cztery kroki, które klienci tożsamości hybrydowej mogą korzystać umożliwia tworzenie fundamentów silna tożsamość.
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
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655941"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Cztery kroki, które foundation silna tożsamość w usłudze Azure Active Directory

Zarządzanie dostępem do aplikacji i danych nie jest już polegać na strategii granic zabezpieczeń tradycyjnej sieci, takich jak sieci obwodowej i zapór ze względu na szybkie przenoszenie aplikacji do chmury. Teraz organizacje muszą ufać swoje rozwiązanie tożsamości do kontrolowania, kto i jakie ma dostęp do danych i aplikacji w organizacji. Więcej organizacji umożliwia pracownikom przynoszą własne urządzenia do pracy i korzystanie ze swoich urządzeń z dowolnego miejsca, z którym się łączą się z Internetem. Zapewnienie, że te urządzenia są zgodne i bezpieczne stał się ważną kwestią w rozwiązaniu tożsamości, które organizacja chce zaimplementować. W obszarze roboczym cyfrowego dzisiejsza [tożsamość jest na płaszczyźnie kontroli głównej](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) każdej organizacji, przejście do chmury.

W przyjmowania rozwiązania tożsamości hybrydowej usługi Azure Active Directory (Azure AD), w organizacji uzyskują dostęp do funkcji premium, które uwalniają produktywność dzięki automatyzacji, delegowanie, samoobsługi i jednej funkcji logowania jednokrotnego. Umożliwia pracownikom dostęp do zasobów firmy z wszędzie tam, gdzie potrzebują do wykonania swojej pracy, podczas gdy Twój zespół IT dotyczące tego dostępu, zapewniając, że odpowiednie osoby mają prawo dostępu do odpowiednich zasobów można ustanowić bezpiecznego produktywności.

Oparte na nauce naszych, ta lista kontrolna najlepszych rozwiązań pomoże Ci szybko wdrożyć zalecane akcje umożliwiające tworzenie *silne* identity foundation w Twojej organizacji:

* Łatwe podłączanie do aplikacji
* Automatycznie ustanowienia jedną tożsamość, dla każdego użytkownika
* Bezpiecznie zwiększać możliwości użytkowników
* Operacjonalizowanie szczegółowe dane

## <a name="step-1---connect-to-apps-easily"></a>Krok 1 — łatwe podłączanie do aplikacji

Połączenie aplikacji z usługą Azure AD, możesz zwiększyć produktywność użytkowników końcowych i zabezpieczeń, należy włączyć logowanie jednokrotne (SSO) i czy Inicjowanie obsługi użytkowników. Przez Zarządzanie aplikacjami w jednym miejscu, usługa Azure AD, możesz zminimalizować obciążenie administracyjne i osiągnięcia jednego punktu kontroli zasad zabezpieczeń i zgodności.

Ta sekcja obejmuje opcje Zarządzanie dostępem użytkowników do aplikacji, które umożliwiają bezpieczny dostęp zdalny do aplikacji wewnętrznych oraz zalety migracji aplikacji do usługi Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Twórz aplikacje dostępne dla użytkowników bezproblemowo

Usługa Azure AD umożliwia administratorom [dodać aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) w galerii aplikacji przedsiębiorstwa w [witryny Azure portal](https://portal.azure.com/). Dodawanie aplikacji w galerii aplikacji Enterprise ułatwia konfigurowanie aplikacji do korzystania z usługi Azure AD jako dostawcy tożsamości. Umożliwia także zarządzanie dostępem użytkowników do aplikacji z zasadami dostępu warunkowego i konfigurowanie logowania jednokrotnego (SSO) do aplikacji, dzięki czemu użytkownicy nie muszą wielokrotnie wprowadzać haseł i automatyczne logowanie lokalnie i aplikacje oparte na chmurze.

Gdy aplikacje są dodawane do galerii usługi Azure AD, użytkownicy mogą zobaczyć w aplikacji, które są do nich przypisane, wyszukiwania i żądania innych aplikacji, zgodnie z potrzebami. Usługa Azure AD zapewnia [kilka metod](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) użytkownikom dostęp do swoich aplikacji:

* Panel dostępu / Moje aplikacje
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Łącza bezpośrednie logowanie jednokrotne

Aby dowiedzieć się więcej na temat dostępu użytkowników do aplikacji, zobacz **krok 3 — zwiększenie możliwości dostępnych dla użytkowników** w tym artykule.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrowanie aplikacji z usług federacyjnych Active Directory do usługi Azure AD

Migrowanie jednej konfiguracji logowania jednokrotnego z Active Directory Federation Services (ADFS) do usługi Azure AD umożliwia dodatkowe funkcje zabezpieczeń, możliwości bardziej spójny i współpracy. Aby uzyskać optymalne wyniki zaleca się migrować swoje aplikacje z usług AD FS do usługi Azure AD. Zapewnieniem Twojej aplikacji uwierzytelnianie i autoryzacja w usłudze Azure AD zapewnia następujące korzyści:

* Zarządzanie kosztami
* Zarządzanie ryzykiem
* Zwiększenie produktywności
* Addressing zgodności i nadzoru

Aby dowiedzieć się więcej, zobacz [migracji Twojej aplikacji do usługi Azure Active Directory](https://aka.ms/migrateapps/whitepaper) oficjalny dokument.

### <a name="enable-secure-remote-access-to-apps"></a>Włączyć bezpieczny dostęp zdalny do aplikacji

[Serwer Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) zapewnia proste rozwiązanie dla organizacji opublikować aplikacje lokalne do chmury na potrzeby użytkowników zdalnych, którzy potrzebują dostępu do aplikacji wewnętrznych w bezpieczny sposób. Po logowanie jednokrotne do usługi Azure AD użytkownicy mogą korzystać aplikacje w chmurze i lokalnych za pośrednictwem zewnętrzne adresy URL lub portalu wewnętrznych aplikacji.

Serwer Proxy aplikacji usługi Azure AD oferuje następujące korzyści:

* Rozszerzenie usługi Azure AD do zasobów lokalnych
  * Zabezpieczenia w skali chmury i ochrona
  * Funkcje, takie jak dostęp warunkowy i uwierzytelniania wieloskładnikowego, które są łatwe do włączenia
* Brak składników w sieci obwodowej, takich jak sieci VPN i rozwiązania tradycyjnych zwrotny serwer proxy
* Nie połączeń przychodzących, wymagane
* Logowanie jednokrotne (SSO) między urządzeniami, zasoby i aplikacje w chmurze i lokalnych
* Umożliwia użytkownikom końcowym produktywności dowolnym czasie i z dowolnego miejsca

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Odkrywanie działań IT z usługą Microsoft Cloud App Security

W nowoczesnych przedsiębiorstwach działom IT często nie rozpoznają wszystkich aplikacji, które są używane przez użytkowników do wykonania swojej pracy w chmurze. Gdy administratorzy IT zostaną poproszeni o ile w chmurze uważają pracownikom korzystają, średnio one powiedzmy 30 lub 40. W rzeczywistości średnia jest ponad 1000 oddzielenie aplikacji, które są używane przez pracowników w Twojej organizacji. 80% pracowników Użyj — zaakceptowane oficjalnie aplikacje, które nie zostało przejrzane i może nie być zgodne z zasadami dotyczącymi zabezpieczeń i zgodności.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) może pomóc w zidentyfikowaniu przydatne aplikacje, które są popularne wśród użytkowników jej może oficjalne akceptowanie i dodać do galerii aplikacji przedsiębiorstwa, aby użytkownicy korzystają z możliwości, takie jak logowanie Jednokrotne i dostęp warunkowy.

*"** Cloud App Security** pomaga upewnić się, że nasi pracownicy są prawidłowo przy użyciu naszej chmury i aplikacji SaaS, w sposób, który obsługuje zasady zabezpieczeń podstawowe, które pomagają w ochronie Accenture." *--- [John Blasi, zarządzanie Accenture Dyrektor ds. bezpieczeństwa informacji](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oprócz wykrywania działań IT, MCAS można również określić poziom ryzyka aplikacji, należy zapobiegać nieautoryzowanemu dostępowi do danych firmowych, wyciekom danych i innych zabezpieczeń ryzyka związanego z używaniem w aplikacjach.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2 — automatycznie ustanowienie jedna tożsamość dla każdego użytkownika

Łącząc lokalne i katalogi oparte na chmurze rozwiązaniu tożsamości hybrydowej usługi Azure AD będzie można ponownie użyć istniejącą inwestycję usługi Active Directory w środowisku lokalnym, aprowizując istniejące tożsamości w chmurze. Rozwiązanie synchronizuje tożsamości lokalnych z usługą Azure AD podczas przechowuje IT, usługi Active Directory w środowisku lokalnym, uruchomiony z wszelkie istniejące rozwiązania nadzoru jako podstawowe źródło prawdziwych tożsamości. Rozwiązania tożsamości hybrydowej firmy Microsoft w usłudze Azure AD obejmuje lokalnych i opartych na chmurze możliwości tworzenia wspólną tożsamość użytkowników na potrzeby uwierzytelniania i autoryzacji do wszystkich zasobów niezależnie od ich lokalizacji.

Integrowanie katalogów lokalnych z usługą Azure AD pozwala użytkownikom osiągać wyższą produktywność i uniemożliwia użytkownikom przy użyciu wielu kont w aplikacjach i usługach, zapewniając wspólną tożsamość do uzyskiwania dostępu do obu chmury i zasobów lokalnych. Używanie wielu kont jest punktem ból dla użytkowników końcowych i podobne IT. Z perspektywy użytkownika końcowego o wiele oznacza kont konieczności zapamiętywać wiele haseł. Aby tego uniknąć, wielu użytkowników, należy ponownie użyć tego samego hasła dla każdego konta, które jest nieprawidłowa z punktu widzenia zabezpieczeń. Z punktu widzenia działu IT ponowne użycie często prowadzi do większej liczby operacji resetowania hasła i koszty pomocy technicznej oraz skarg przez użytkownika końcowego.

Program Azure AD Connect to narzędzie, które jest używane do synchronizacji tożsamości lokalnych z usługą Azure AD, która następnie umożliwia dostęp do aplikacji w chmurze. Po tożsamości w usłudze Azure AD, można zainicjować obsługę do aplikacji SaaS, takich jak Salesforce i Concur.

W tej sekcji listę zaleceń dotyczących wysokiej dostępności, nowoczesnego uwierzytelniania w chmurze i zmniejszenie zużycia Twojego środowiska lokalnego.

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o usłudze Azure AD Connect, zobacz [co to jest Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Skonfiguruj serwer przejściowy programu Azure AD Connect i ich aktualizowania

Program Azure AD Connect odgrywa kluczową rolę w procesie inicjowania obsługi administracyjnej. Jeśli z jakiegokolwiek powodu serwer synchronizacji przejdzie do trybu offline, zmiany w środowisku lokalnym nie zostanie zaktualizowana w chmurze i spowodować problemy dotyczące dostępu do użytkowników. Należy zdefiniować strategię pracy awaryjnej, która pozwala administratorom na wznowienie synchronizacji po serwer synchronizacji przejdzie do trybu offline.

Aby zapewnić wysoką dostępność w przypadku podstawowej platformy Azure serwer AD Connect przechodzi do trybu offline, zalecane jest wdrożenie oddzielnego [przemieszczania serwera](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) programu Azure AD Connect. Wdrażanie serwera umożliwia administratorowi "Podwyższanie poziomu" serwer przejściowy do środowiska produkcyjnego przez przełącznik prostej konfiguracji. Posiadanie serwera rezerwy skonfigurowane w trybie przejściowym również umożliwia testowanie i wdrażanie nowych zmian konfiguracji i wprowadzenie nowego serwera, jeśli likwidowanie starego.

> [!TIP]
> Program Azure AD Connect jest aktualizowana w regularnych odstępach czasu. W związku z tym zdecydowanie zalecane jest, zachowanie serwer przejściowy bieżącej, aby wykorzystać ulepszenia wydajności, poprawki i nowe możliwości, które zawiera każda nowa wersja.

### <a name="enable-cloud-authentication"></a>Włączanie uwierzytelniania w chmurze

Organizacje z usługą Active Directory w środowisku lokalnym powinien dotyczyć swojego katalogu usługi Azure AD za pomocą usługi Azure AD Connect i skonfiguruj odpowiednią metodę uwierzytelniania. [Wybieranie metody uwierzytelniania poprawne](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) dla Twojej organizacji jest pierwszym etapem swoją podróż po przeniesieniu aplikacji w chmurze. Ponieważ kontroluje dostęp do wszystkich danych w chmurze i zasobów, jest kluczowym.

Najprostszy i zalecane metody umożliwiające uwierzytelnianie w chmurze dla obiektów katalogu lokalnego w usłudze Azure AD jest umożliwienie [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (wersji). Alternatywnie, w niektórych organizacjach może należy rozważyć włączenie [uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Czy należy wybierać wersji PTA, nie zapomnij włączyć [bezproblemowego logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) aby umożliwić użytkownikom uzyskiwanie dostępu do aplikacji w chmurze bez stale wprowadzania nazwy użytkownika i hasła w aplikacji podczas korzystania z Windows 7 i 8 urządzeń w sieci firmowej sieć. Bez logowania jednokrotnego użytkownicy muszą należy pamiętać, hasła specyficzne dla aplikacji i logowanie do każdej aplikacji. Podobnie personel IT potrzebuje do tworzenia i aktualizowania konta użytkowników dla każdej aplikacji, takich jak usługi Office 365, Box i Salesforce. Użytkownicy potrzebują do haseł oraz poświęcać czasu, aby zalogować się do poszczególnych aplikacji. Dostarczanie standardowych pojedynczego logowania jednokrotnego mechanizm całe przedsiębiorstwo ma kluczowe znaczenie podczas najlepsze środowisko użytkownika, zmniejszenia ryzyka, możliwość raportu i nadzoru.

W przypadku organizacji, które już za pomocą usług AD FS lub innego dostawcy uwierzytelniania lokalnego przejście do usługi Azure AD jako dostawcy tożsamości można ograniczyć złożoność i zwiększyć dostępność. Jeśli nie masz wybrane przypadki użycia dla stosowania mechanizmu Federacji, zalecamy przeprowadzenie migracji z federacyjnego uwierzytelniania do wersji i bezproblemowe logowanie Jednokrotne lub PTA i bezproblemowe logowanie Jednokrotne korzyści zużycie zmniejszenie w środowisku lokalnym i zapewnia elastyczność, oferowanych w chmurze za pomocą Ulepszone funkcje i możliwości. Aby uzyskać więcej informacji, zobacz [migracji z Federacji na synchronizację skrótów haseł usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Włącz automatyczne anulowanie aprowizacji kont

Włączenie automatycznego i cofania aprowizacji aplikacji jest najlepszą strategię dla dotyczące cyklu życia tożsamości w wielu systemach. Usługa Azure AD obsługuje [automatycznego, oparte na zasadach i cofania aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) kont użytkowników z szeroką gamą takich jak usługi ServiceNow i Salesforce oraz innych popularnych aplikacji SaaS, które implementują [2.0 Standard SCIM Protokół](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). W przeciwieństwie do tradycyjnych rozwiązań inicjowania obsługi, które wymagają kodu niestandardowego lub ręcznego przekazywania plików CSV, usługa aprowizowania jest hostowana w chmurze, a funkcje wstępnie zintegrowane z łączników, które można skonfigurować pod kątem i zarządzane przy użyciu witryny Azure portal. Najważniejszą korzyścią z automatycznego anulowania zastrzeżenia jest pomaga zabezpieczyć Twoją organizację, natychmiastowe usuwanie tożsamości użytkowników z kluczowych aplikacjach SaaS, gdy opuszczają organizację.

Aby dowiedzieć się więcej na temat inicjowania obsługi administracyjnej konta użytkowników i sposób jej działania, zobacz [Automatyzowanie aprowizacji użytkowników oraz anulowania zastrzeżenia do aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Krok 3 — bezpiecznie zwiększać możliwości użytkowników

W obszarze roboczym cyfrowego dzisiejsza warto zabezpieczeń saldo produktywne. Jednak użytkownicy końcowi często wypchnięcia z powrotem na środki bezpieczeństwa, które spowalniają ich wydajność i dostęp do aplikacji w chmurze. Aby rozwiązać ten, usługa Azure AD zapewnia możliwości samoobsługi, które umożliwiają użytkownikom produktywności, jednocześnie minimalizując koszty administracyjne.

W tej sekcji przedstawiono zalecenia dotyczące usuwania płynnie z Twojej organizacji, umożliwiając użytkownikom pozostając czujność.

### <a name="enable-self-service-password-reset-for-all-users"></a>Włączanie funkcji samoobsługowego resetowania hasła dla wszystkich użytkowników

Azure [samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) oferuje proste oznacza, że dla administratorów IT umożliwić użytkownikom Resetowanie i odblokowywania ich haseł lub kont bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami.

Domyślnie usługa Azure AD umożliwia odblokowanie konta podczas resetowania hasła. Jednak po włączeniu usługi Azure AD Connect [integracji środowiska lokalnego](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), istnieje również możliwość oddzielenia te dwie operacje, które umożliwiają użytkownikom na odblokowywanie kont bez konieczności, zresetować hasło.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Upewnij się, że wszyscy użytkownicy są zarejestrowane dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ

Platforma Azure udostępnia raporty używane przez Ciebie i Twojej organizacji w celu zapewnienia, że użytkownicy są zarejestrowani do uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Użytkownicy, którzy jeszcze nie dokonano rejestracji może być konieczne będzie edukowanie procesu.

MFA [raporcie logowań](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) zawiera informacje na temat użycia usługi MFA i zapewnia wgląd w sposób działania usługi MFA w Twojej organizacji. Dostęp do logowania działania (i inspekcji i zdarzeń o podwyższonym ryzyku) dla usługi Azure AD ma kluczowe znaczenie podczas rozwiązywania problemów, analizy użycia i odpytywanie danych dowodowych.

Podobnie [raportu samoobsługowego zarządzania hasłami](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) może służyć do określenia, kto ma (lub nie) zarejestrowany do samoobsługowego resetowania HASEŁ.

### <a name="self-service-app-management"></a>Zarządzanie aplikacjami samoobsługi

Zanim użytkownicy mogą odnajdować samodzielnie aplikacje z panelu dostępu, musisz włączyć [samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) do dowolnych aplikacji, które użytkownicy mogą samodzielnie odnajdywanie i żądania dostępu do. Dostęp do aplikacji samoobsługowej to doskonały sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, a następnie opcjonalnie zezwolić grupie biznesowej zatwierdzać dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników w celu [hasło logowania jednokrotnego w aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) bezpośrednio w swoich panelach dostępu.

### <a name="self-service-group-management"></a>Samoobsługowe zarządzanie grupami

Przypisywanie użytkowników do aplikacji jest najlepiej mapowany podczas korzystania z grup, ponieważ umożliwiają one elastyczność i możliwość zarządzania w skali:

* Oparte na atrybutach przy użyciu dynamiczne członkostwo w grupie
* Delegowanie do właścicieli aplikacji

Usługa Azure AD zapewnia możliwość zarządzania dostępem do zasobów przy użyciu grup zabezpieczeń i grup usługi Office 365. Te grupy mogą zarządzane przez właściciela grupy, kto może zatwierdzać lub odrzucać żądania członkostwa i delegowanie kontroli nad członkostwa w grupie. Znane jako [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), funkcja ta pozwala zaoszczędzić czas, dzięki czemu właściciele grupy, którym nie przypisano rolę administracyjną, tworzenie grupy i zarządzać nimi bez konieczności polegać na administratorach w sprawie ich żądania obsługi.

## <a name="step-4---operationalize-your-insights"></a>Krok 4 — Operacjonalizować szczegółowe dane

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów są niezbędne składniki wydajne strategii, aby upewnić się, że produktywności użytkowników, a Twoja organizacja jest bezpieczne. Dzienniki zabezpieczeń i raporty mogą pomóc w odpowiedzi na pytanie takich jak:

* Czy używasz, co w przypadku płacenia za?
* Jest coś podejrzanego lub złośliwych działań wykonywanych w mojej dzierżawy?
* Kto dotyczyło to również w przypadku wystąpienia zdarzenia zabezpieczeń?

Dzienniki zabezpieczeń i raporty umożliwiają rekord elektronicznych podejrzanych działań i wykrywać wzorce, które mogą wskazywać próbę lub niepowodzenia zewnętrznych penetracji sieci i atakami wewnętrznymi pomocy. Inspekcja służy do monitorowania aktywności użytkownika, zgodność z przepisami dokument, czy analizy śledczej i nie tylko. Alerty zapewniają powiadomienia zdarzeń zabezpieczeń.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Przypisywanie najniższych uprawnieniach ról administratora dla operacji

Myśląc o swoje podejście do operacji istnieje kilka poziomów administracji wziąć pod uwagę. Pierwszy poziom obciążenia administracyjnej są umieszczane na globalne administratorom. Zawsze przy użyciu roli administratora globalnego, może być odpowiednie dla małych przedsiębiorstw. Jednak w przypadku większych organizacji z personelem pomocy technicznej i administratorów odpowiedzialnych za wykonywanie określonych zadań, przypisywania roli administratora globalnego może stanowić zagrożenie bezpieczeństwa od osób, zapewnia możliwość zarządzania zadaniami, które są powyżej i poza co powinny one być może zrobić.

W takim przypadku należy rozważyć następnego poziomu administracji. Używanie programu Azure AD, można wyznaczyć użytkowników końcowych jako "ograniczony Administrator", którzy mogą zarządzać zadaniami w mniej uprzywilejowane role. Na przykład można przypisać Twojej pomocy technicznej [Czytelnik zabezpieczeń](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) roli, aby zapewnić możliwość zarządzania funkcje zabezpieczeń z dostępem tylko do odczytu. Lub może być dobrym pomysłem będzie przypisać [uwierzytelniania administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) roli użytkowników indywidualnych, aby dać im możliwość zresetowania poświadczeń innych niż hasła lub odczytu i skonfigurować usługi Azure Service Health.

Aby dowiedzieć się więcej, zobacz [uprawnienia roli administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorowanie składników hybrydowych (program Azure AD Connect sync, usług AD FS) przy użyciu programu Azure AD Connect Health

Program Azure AD Connect i usług AD FS są krytycznych składników, które mogą potencjalnie uszkodzić zarządzania cyklem życia i uwierzytelniania i w końcu doprowadzić do awarii. W związku z tym należy wdrożyć program Azure AD Connect Health monitorowania i raportowania tych składników.

Aby dowiedzieć się więcej, przejdź odczytu [monitora usługi AD FS przy użyciu programu Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Zbieranie dzienników danych do analizy przy użyciu usługi Azure Monitor

[Usługa Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) to ujednolicone monitorowania portal dla wszystkich dzienników usługi Azure AD, co zapewnia szczegółowe informacje, zaawansowane analizy i uczenia maszynowego inteligentne. Dzięki usłudze Azure Monitor może zużywać metryk i dzienników w portalu, a także za pośrednictwem interfejsów API, aby uzyskać więcej wgląd w stan i wydajność zasobów. Umożliwia ona jedną taflę szkła środowisko, w portalu podczas włączania szeroką gamę integracji produktu za pośrednictwem interfejsów API i dane opcji eksportu, które obsługują tradycyjnych systemów SIEM innych firm. Usługa Azure Monitor zapewnia również możliwość konfigurowania reguł alertów do otrzymywania powiadomień, lub wykonywanie zautomatyzowanych akcji na problemy wpływające na Twoje zasoby.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Tworzenie niestandardowych pulpitów nawigacyjnych usługi przywództwo i dnia na dzień

Organizacje, które nie mają rozwiązania SIEM można pobrać [pakiet zawartości usługi Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) dla usługi Azure AD. Usługi Power BI content pack zawiera wstępnie utworzone raporty ułatwiające zrozumienie, jak użytkownicy przyjmują i korzystać z funkcji usługi Azure AD, co pozwala uzyskać wgląd w wszystkim działaniom w katalogu. Można także tworzyć własne [niestandardowego pulpitu nawigacyjnego](https://docs.microsoft.com/power-bi/service-dashboards) i udostępnij ją zespołowi kierownicze do zgłaszania codziennej działalności. Pulpity nawigacyjne to świetny sposób Monitoruj działanie swojej firmy i wyświetlenie wszystkich najważniejszych metryk w skrócie. Wizualizacje na pulpicie nawigacyjnym mogą pochodzić z jednego bazowego zestawu danych lub wiele, a z raportu lub wiele. Pulpit nawigacyjny łączy w środowisku lokalnym i danymi, zapewniają skonsolidowany widok niezależnie od tego, gdzie dane są przechowywane w chmurze.

![Niestandardowy pulpit nawigacyjny usługi Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Zrozumienie sterowniki wywołania pomocy technicznej

Podczas implementowania rozwiązania z tożsamością hybrydową zgodnie z opisem w tym artykule można ostatecznie zauważyć zmniejszenie w wywołania pomocy technicznej. Typowe problemy, takie jak zapomniane hasła i blokady konta zostaną skorygowane przez zaimplementowanie Azure samoobsługowego resetowania haseł, podczas włączania samoobsługowego dostępu do aplikacji pozwala użytkownikom na własnym odnajdywania i poprosić o dostęp do aplikacji bez konieczności korzystania z na działu IT.

Jeśli zauważysz nie zmniejszenie interwencji obsługi, zaleca się analizowanie sterowniki wywołania pomocy technicznej w celu podjęcia próby upewnij się, jeśli samoobsługowego resetowania HASEŁ lub samoobsługowego dostępu do aplikacji został skonfigurowany poprawnie lub czy wszelkich nowych zagadnień, które mogą być systematycznie Problem rozwiązany.

*"Na drodze transformacji cyfrowej potrzebowaliśmy niezawodne tożsamości i dostępu do dostawcy zarządzania ułatwiają bezproblemowe jeszcze bezpiecznego integracji między USA, partnerów i dostawców usług w chmurze dla ekosystemu skuteczne; Usługi Azure AD jest najlepszym rozwiązaniem, oferując nam potrzebne możliwości i widoczność, która pomogła nam do wykrywania i reagowania na zagrożenia".* --- [Aramex Yazan Almasri, globalne informacje zabezpieczeń Dyrektor ds.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorowanie użycia aplikacji, aby uzyskiwaniu wglądu w dane

Oprócz wyszukiwania niezatwierdzonych przez dział IT, monitorowanie użycia aplikacji w organizacji za pomocą [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) może pomóc Twojej organizacji, po przeniesieniu do w pełni korzystać z prawdziwą aplikacji w chmurze. Może to pomóc zachowaniu kontroli dzięki ulepszonemu wglądowi w działanie zasobów i zwiększyć ochronę kluczowych danych w aplikacjach w chmurze. Monitorowanie użycia aplikacji w organizacji za pomocą MCAS może pomóc w uzyskaniu odpowiedzi na następujące pytania:

* Jakie aplikacje niezaakceptowane oficjalnie pracownicy korzystają z do przechowywania danych?
* Gdzie i kiedy poufne dane są przechowywane w chmurze?
* Kto ma dostęp do poufnych danych w chmurze?

*"Dzięki usłudze Cloud App Security, firma Microsoft może szybko wykrywaniu anomalii i podejmij akcję."* --- [Eric LePenske, starszy Menedżer, bezpieczeństwo informacji Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów, które wdrażanie rozwiązania tożsamości hybrydowej, ale ta lista kontrolna czterech kroków pomoże szybko wykonać umożliwiające użytkownikom bardziej wydajnej i bezpiecznej infrastruktury tożsamości.

* Łatwe podłączanie do aplikacji
* Automatycznie ustanowienia jedną tożsamość, dla każdego użytkownika
* Bezpiecznie zwiększać możliwości użytkowników
* Operacjonalizowanie szczegółowe dane

Mamy nadzieję, że ten dokument jest przydatne plan do ustanowienia podstawę silna tożsamość dla Twojej organizacji.

## <a name="identity-checklist"></a>Lista kontrolna tożsamości

Firma Microsoft zaleca drukowania z poniższej listy kontrolnej odwołania, po rozpoczęciu swoją drogę do bardziej solidną podstawę tożsamości w Twojej organizacji.

### <a name="today"></a>Dzisiaj

|Gotowe?|Element|
|:-|:-|
||Pilotażowe Self - Service hasło resetowania (SSPR) dla grupy|
||Monitorowanie składników hybrydowych przy użyciu programu Azure AD Connect Health|
||Przypisywanie najniższych uprawnieniach ról administratora dla operacji|
||Odkrywanie działań IT z usługą Microsoft Cloud App Security|
||Zbieranie dzienników danych do analizy przy użyciu usługi Azure Monitor|

### <a name="next-two-weeks"></a>Następne 2 tygodnie

|Gotowe?|Element|
|:-|:-|
||Udostępnij aplikację dla użytkowników|
||Pilotażu, inicjowanie obsługi administracyjnej aplikacji SaaS w wybranej usługi Azure AD|
||Skonfigurować serwer przejściowy programu Azure AD Connect, a następnie aktualny|
||Rozpoczynanie migracji aplikacji z usług AD FS do usługi Azure AD|
||Tworzenie niestandardowych pulpitów nawigacyjnych usługi przywództwo i dnia na dzień|

### <a name="next-month"></a>Następny miesiąc

|Gotowe?|Element|
|:-|:-|
||Monitorowanie użycia aplikacji, aby uzyskiwaniu wglądu w dane|
||Pilotażowe bezpieczny dostęp zdalny do aplikacji|
||Upewnij się, że wszyscy użytkownicy są zarejestrowane dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ|
||Włączanie uwierzytelniania w chmurze|

### <a name="next-three-months"></a>Następnych trzech miesięcy

|Gotowe?|Element|
|:-|:-|
||Włącz zarządzanie aplikacjami samoobsługi|
||Włącz samoobsługowe zarządzanie grupami|
||Monitorowanie użycia aplikacji, aby uzyskiwaniu wglądu w dane|
||Zrozumienie sterowniki wywołania pomocy technicznej|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak można zwiększyć poziomu bezpiecznego przy użyciu funkcji usługi Azure Active Directory i ta lista kontrolna krok 5 — [pięć kroków do zabezpieczania infrastruktury tożsamości](https://aka.ms/securitysteps).

Dowiedz się, jak funkcje obsługi tożsamości w usłudze Azure AD może pomóc Ci w przyspieszeniu przejście do zarządzanych zarządzania w chmurze, zapewniając rozwiązań i możliwości, które pozwalają organizacjom szybko przyjmuje i przenieść jeden z jego Zarządzanie tożsamościami z tradycyjnych lokalnych systemów do usługi Azure AD — [jak Azure AD zapewnia podlegają zarządzania chmurą dla obciążeń lokalnych](https://aka.ms/cloudgoverned).
