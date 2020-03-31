---
title: Publikowanie aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD
description: Dowiedz się, dlaczego za pomocą serwera proxy aplikacji można publikować lokalne aplikacje sieci web zewnętrznie użytkownikom zdalnym. Dowiedz się więcej o architekturze serwera proxy aplikacji, łącznikach, metodach uwierzytelniania i korzyściach z zabezpieczeń.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7047dfd0f02ffe95dcacfdf4ddc014047a338513
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481198"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Używanie serwera proxy aplikacji usługi Azure AD do publikowania aplikacji lokalnych dla użytkowników zdalnych

Usługa Azure Active Directory (Azure AD) oferuje wiele funkcji ochrony użytkowników, aplikacji i danych w chmurze i lokalnie. W szczególności funkcja serwera proxy aplikacji usługi Azure AD może być zaimplementowana przez specjalistów IT, którzy chcą publikować lokalne aplikacje sieci web zewnętrznie. Użytkownicy zdalni, którzy potrzebują dostępu do aplikacji wewnętrznych, mogą uzyskać do nich bezpieczny dostęp.

Możliwość bezpiecznego dostępu do wewnętrznych aplikacji spoza sieci staje się jeszcze bardziej krytyczna w nowoczesnym miejscu pracy. Dzięki scenariuszom takim jak BYOD (Bring Your Own Device) i urządzeniom mobilnym informatycy muszą osiągnąć dwa cele:

* Zwiększ produktywność użytkowników końcowych w dowolnym miejscu i czasie
* Ochrona zasobów korporacyjnych przez cały czas

Wiele organizacji uważa, że są pod kontrolą i chronione, gdy zasoby istnieją w granicach ich sieci firmowych. Jednak w dzisiejszym cyfrowym miejscu pracy granica ta rozszerzyła się o zarządzane urządzenia mobilne oraz zasoby i usługi w chmurze. Teraz musisz zarządzać złożonością ochrony tożsamości użytkowników i danych przechowywanych na ich urządzeniach i aplikacjach.

Być może używasz już usługi Azure AD do zarządzania użytkownikami w chmurze, którzy muszą uzyskać dostęp do usługi Office 365 i innych aplikacji SaaS, a także aplikacji sieci Web hostowanych lokalnie. Jeśli masz już usługę Azure AD, możesz wykorzystać ją jako jedną płaszczyznę sterowania, aby umożliwić bezproblemowy i bezpieczny dostęp do aplikacji lokalnych. A może nadal rozważasz przejście do chmury. Jeśli tak, możesz rozpocząć podróż do chmury, implementując serwer proxy aplikacji i wykonując pierwszy krok w kierunku tworzenia silnego fundamentu tożsamości.

Chociaż nie jest wyczerpująca, poniższa lista ilustruje niektóre rzeczy, które można włączyć, implementując serwer proxy aplikacji w scenariuszu współistnienia hybrydowego:

* Publikowanie lokalnych aplikacji internetowych zewnętrznie w uproszczony sposób bez strefy DMZ
* Obsługa logowania jednokrotnego (Logowanie jednokrotne) na różnych urządzeniach, zasobach i aplikacjach w chmurze i lokalnie
* Obsługa uwierzytelniania wieloskładnikowego dla aplikacji w chmurze i lokalnie
* Szybkie korzystanie z funkcji chmury dzięki bezpieczeństwu chmury Microsoft Cloud
* Centralizacja zarządzania kontem użytkowników
* Centralizacja kontroli tożsamości i bezpieczeństwa
* Automatyczne dodawanie lub usuwanie dostępu użytkowników do aplikacji na podstawie członkostwa w grupie

W tym artykule wyjaśniono, jak usługa Azure AD i serwer proxy aplikacji zapewniają użytkownikom zdalnym środowisko logowania jednokrotnego. Użytkownicy bezpiecznie łączą się z aplikacjami lokalnymi bez sieci VPN lub serwerów o podwójnym domu i reguł zapory. Ten artykuł pomaga zrozumieć, jak serwer proxy aplikacji przynosi możliwości i zalety zabezpieczeń chmury do lokalnych aplikacji sieci web. Opisano w nim również architekturę i topologie, które są możliwe.

## <a name="remote-access-in-the-past"></a>Dostęp zdalny w przeszłości

Wcześniej płaszczyzna sterowania do ochrony zasobów wewnętrznych przed osobami atakującymi podczas ułatwiania dostępu przez użytkowników zdalnych znajdowała się w strefie DMZ lub sieci obwodowej. Jednak rozwiązania VPN i reverse proxy wdrożone w strefie DMZ używane przez klientów zewnętrznych do uzyskiwania dostępu do zasobów firmowych nie są dostosowane do świata chmury. Zazwyczaj cierpią z powodu następujących wad:

* Koszty sprzętu
* Utrzymanie zabezpieczeń (łatanie, monitorowanie portów itp.)
* Uwierzytelnianie użytkowników na krawędzi
* Uwierzytelnianie użytkowników na serwerach sieci Web w sieci obwodowej
* Obsługa dostępu do sieci VPN dla użytkowników zdalnych z dystrybucją i konfiguracją oprogramowania klienta sieci VPN. Ponadto utrzymywanie serwerów przyłączonych do domeny w strefie DMZ, które mogą być narażone na ataki zewnętrzne.

W dzisiejszym świecie chmury pierwszy usługa Azure AD najlepiej nadaje się do kontrolowania, kto i co dostaje się do sieci. Usługa Azure AD Application Proxy integruje się z nowoczesnymi technologiami uwierzytelniania i chmurowymi, takimi jak aplikacje SaaS i dostawcy tożsamości. Ta integracja umożliwia użytkownikom dostęp do aplikacji z dowolnego miejsca. App Proxy jest nie tylko bardziej odpowiedni dla dzisiejszego cyfrowego miejsca pracy, ale jest bezpieczniejszy niż rozwiązania VPN i odwrotnego serwera proxy i łatwiejszy do wdrożenia. Użytkownicy zdalni mogą uzyskiwać dostęp do aplikacji lokalnych w taki sam sposób, w jaki uzyskują dostęp do usługi O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmieniać ani aktualizować aplikacji, aby móc pracować z serwerem proxy aplikacji. Ponadto serwer proxy aplikacji nie wymaga otwierania połączeń przychodzących za pośrednictwem zapory. Z App Proxy, po prostu ustawić go i zapomnieć.

## <a name="the-future-of-remote-access"></a>Przyszłość dostępu zdalnego

W dzisiejszym cyfrowym miejscu pracy użytkownicy pracują w dowolnym miejscu z wieloma urządzeniami i aplikacjami. Jedyną stałą jest tożsamość użytkownika. Dlatego pierwszym krokiem do bezpiecznej sieci jest dziś użycie funkcji [zarządzania tożsamościami usługi Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) jako płaszczyzny kontroli zabezpieczeń. Model, który używa tożsamości jako płaszczyzny sterowania, zazwyczaj składa się z następujących komponentów:

* Dostawca tożsamości do śledzenia użytkowników i informacji związanych z użytkownikiem.
* Katalog urządzeń do obsługi listy urządzeń, które mają dostęp do zasobów firmowych. Ten katalog zawiera odpowiednie informacje o urządzeniu (na przykład typ urządzenia, integralność itp.).
* Usługa oceny zasad w celu ustalenia, czy użytkownik i urządzenie są zgodne z zasadami określonymi przez administratorów zabezpieczeń.
* Możliwość udzielania lub odmawiania dostępu do zasobów organizacyjnych.

Dzięki serwerowi proxy aplikacji usługa Azure AD śledzi użytkowników, którzy muszą uzyskiwać dostęp do aplikacji sieci web opublikowanych lokalnie i w chmurze. Zapewnia centralny punkt zarządzania dla tych aplikacji. Chociaż nie jest to wymagane, zaleca się również włączenie dostępu warunkowego usługi Azure AD. Definiując warunki uwierzytelniania i uzyskiwania dostępu przez użytkowników, zapewniasz osobom właściwym dostęp do aplikacji.

**Uwaga:** Ważne jest, aby zrozumieć, że serwer proxy aplikacji usługi Azure AD jest przeznaczony jako zastępowanie sieci VPN lub odwrotnego serwera proxy dla użytkowników mobilnych (lub zdalnych), którzy potrzebują dostępu do zasobów wewnętrznych. Nie jest przeznaczony dla użytkowników wewnętrznych w sieci firmowej. Użytkownicy wewnętrzni, którzy niepotrzebnie korzystają z serwera proxy aplikacji, mogą powodować nieoczekiwane i niepożądane problemy z wydajnością.

![Usługa Azure Active Directory i wszystkie aplikacje](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Omówienie działania serwera proxy aplikacji

Proxy aplikacji to usługa usługi Azure AD skonfigurowana w witrynie Azure portal. Umożliwia publikowanie zewnętrznego publicznego punktu końcowego adresu URL HTTP/HTTPS w chmurze Azure, który łączy się z wewnętrznym adresem URL serwera aplikacji w organizacji. Te lokalne aplikacje sieci web można zintegrować z usługą Azure AD do obsługi logowania jednokrotnego. Użytkownicy końcowi mogą następnie uzyskiwać dostęp do lokalnych aplikacji sieci Web w taki sam sposób, w jaki uzyskują dostęp do usługi Office 365 i innych aplikacji SaaS.

Składniki tej funkcji obejmują usługę proxy aplikacji, która działa w chmurze, łącznik serwera proxy aplikacji, który jest lekkim agentem uruchamianym na serwerze lokalnym, oraz usługę Azure AD, która jest dostawcą tożsamości. Wszystkie trzy składniki współpracują ze sobą, aby zapewnić użytkownikowi środowisko logowania jednokrotnego dostępu do lokalnych aplikacji sieci web.

Po zalogowaniu użytkownicy zewnętrzni mogą uzyskać dostęp do lokalnych aplikacji internetowych przy użyciu znanego adresu URL lub [panelu dostępu MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) z komputera lub urządzeń iOS/MAC. Na przykład serwer proxy aplikacji może udostępniać dostęp zdalny i logowanie jednokrotne na pulpicie zdalnym, witrynach programu SharePoint, tableau, qlik, aplikacji Outlook w sieci Web i aplikacjach biznesowych (LOB).

![Architektura serwera proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Uwierzytelnianie

Istnieje kilka sposobów konfigurowania aplikacji do logowania jednokrotnego, a wybrana metoda zależy od uwierzytelniania używanego przez aplikację. Serwer proxy aplikacji obsługuje następujące typy aplikacji:

* Aplikacje internetowe
* Internetowe interfejsy API, które chcesz udostępnić bogatym aplikacjom na różnych urządzeniach
* Aplikacje hostowane za bramą pulpitu zdalnego
* Zaawansowane aplikacje klienckie zintegrowane z biblioteką uwierzytelniania usługi Active Directory (ADAL)

Serwer proxy aplikacji współpracuje z aplikacjami korzystającymi z następującego natywnego protokołu uwierzytelniania:

* **[Zintegrowane uwierzytelnianie systemu Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** W przypadku protokołu IWA łączniki serwera proxy aplikacji używają delegowania ograniczonego protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji Kerberos.

Serwer proxy aplikacji obsługuje również następujące protokoły uwierzytelniania z integracją innych firm lub w określonych scenariuszach konfiguracji:

* [**Uwierzytelnianie oparte na nagłówku**](application-proxy-configure-single-sign-on-with-ping-access.md). Ta metoda logowania używa usługi uwierzytelniania innej firmy o nazwie PingAccess i jest używana, gdy aplikacja używa nagłówków do uwierzytelniania. W tym scenariuszu uwierzytelnianie jest obsługiwane przez PingAccess.
* [**Uwierzytelnianie oparte na formularzach lub hasłach**](application-proxy-configure-single-sign-on-password-vaulting.md). Za pomocą tej metody uwierzytelniania użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła przy pierwszym dostępie do niej. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji. W tym scenariuszu uwierzytelnianie jest obsługiwane przez usługę Azure AD.
* [**Uwierzytelnianie SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Logowanie jednokrotne oparte na saml jest obsługiwane dla aplikacji korzystających z protokołów SAML 2.0 lub WS-Federation. W przypadku logowania jednokrotnego SAML usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika.

Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [Wybieranie metody logowania jednokrotnego.](what-is-single-sign-on.md#choosing-a-single-sign-on-method)

### <a name="security-benefits"></a>Korzyści z bezpieczeństwa

Rozwiązanie dostępu zdalnego oferowane przez usługi Application Proxy i Azure AD obsługuje kilka korzyści w zakresie bezpieczeństwa, z których mogą skorzystać klienci, w tym:

* **Uwierzytelniony dostęp**. Serwer proxy aplikacji najlepiej nadaje się do publikowania aplikacji z [uwierzytelnianiem wstępnym,](application-proxy-security.md#authenticated-access) aby upewnić się, że tylko uwierzytelnione połączenia trafiają do sieci. W przypadku aplikacji opublikowanych z uwierzytelniania wstępnego nie ruch jest dozwolony przez usługę serwera proxy aplikacji do środowiska lokalnego, bez prawidłowego tokenu. Uwierzytelnianie wstępne, ze swej natury, blokuje znaczną liczbę ataków ukierunkowanych, ponieważ tylko uwierzytelnione tożsamości mogą uzyskiwać dostęp do aplikacji wewnętrznej bazy danych.
* **Dostęp warunkowy**. Bogatsze formanty zasad mogą być stosowane przed nawiązaniem połączeń z siecią. Za pomocą dostępu warunkowego można zdefiniować ograniczenia ruchu, który pozwala trafić aplikacji wewnętrznej bazy danych. Tworzenie zasad, które ograniczają logowania na podstawie lokalizacji, siły uwierzytelniania i profilu ryzyka użytkownika. W miarę rozwoju dostępu warunkowego dodawane są dodatkowe formanty, aby zapewnić dodatkowe zabezpieczenia, takie jak integracja z programem Microsoft Cloud App Security (MCAS). Integracja z usługą MCAS umożliwia skonfigurowanie aplikacji lokalnej do monitorowania w [czasie rzeczywistym,](application-proxy-integrate-with-microsoft-cloud-application-security.md) wykorzystując dostęp warunkowy do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego.
* **Zakończenie ruchu**. Cały ruch do aplikacji wewnętrznej bazy danych jest zakończony w usłudze proxy aplikacji w chmurze, podczas gdy sesja jest ponownie ustanawiana z serwerem wewnętrznej bazy danych. Ta strategia połączenia oznacza, że serwery wewnętrznej bazy danych nie są narażone na bezpośredni ruch HTTP. Są one lepiej chronione przed ukierunkowanymi atakami DoS (denial-of-service), ponieważ zapora nie jest atakowana.
* **Cały dostęp jest wychodzący**. Łączniki serwera proxy aplikacji używają tylko połączeń wychodzących z usługą proxy aplikacji w chmurze za pomocą portów 80 i 443. Bez połączeń przychodzących nie ma potrzeby otwierania portów zapory dla połączeń przychodzących lub składników w strefie DMZ. Wszystkie połączenia są wychodzące i za każdym bezpiecznym kanałem.
* **Analizy oparte na analizie zabezpieczeń i uczeniu maszynowym (ML).** Ponieważ jest częścią usługi Azure Active Directory, serwer proxy aplikacji może korzystać z [usługi Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (wymaga [licencjonowania Premium P2).](https://azure.microsoft.com/pricing/details/active-directory/) Usługa Azure AD Identity Protection łączy analizy zabezpieczeń uczenia maszynowego z źródłami danych z [jednostki digital crimes](https://news.microsoft.com/stories/cybercrime/index.html) firmy Microsoft i [Centrum reagowania zabezpieczeń firmy Microsoft,](https://www.microsoft.com/msrc) aby aktywnie identyfikować konta, których bezpieczeństwo zostało naruszone. Ochrona tożsamości zapewnia ochronę w czasie rzeczywistym przed logowaniami wysokiego ryzyka. Bierze pod uwagę takie czynniki, jak dostęp z zainfekowanych urządzeń, za pośrednictwem sieci anonimizacji lub z nietypowych i mało prawdopodobnych lokalizacji, aby zwiększyć profil ryzyka sesji. Ten profil ryzyka służy do ochrony w czasie rzeczywistym. Wiele z tych raportów i zdarzeń są już dostępne za pośrednictwem interfejsu API do integracji z systemami SIEM.

* **Dostęp zdalny jako usługa**. Nie musisz się martwić o utrzymywanie i poprawianie serwerów lokalnych, aby umożliwić dostęp zdalny. Application Proxy to usługa skalowania internetowego, która jest właścicielem firmy Microsoft, dzięki czemu zawsze otrzymujesz najnowsze poprawki i uaktualnienia zabezpieczeń. Niezałatane oprogramowanie nadal stanowi dużą liczbę ataków. Według Departamentu Bezpieczeństwa Wewnętrznego, aż [85 procent ukierunkowanych ataków można zapobiec](https://www.us-cert.gov/ncas/alerts/TA15-119A). Dzięki temu modelowi usługi nie musisz już dźwigać dużego ciężaru zarządzania serwerami brzegowymi i szyfrować, aby je załatać w razie potrzeby.

* **Integracja z usłudze Intune**. W usłudze Intune ruch firmowy jest kierowany oddzielnie od ruchu osobistego. Serwer proxy aplikacji zapewnia uwierzytelnienie ruchu firmowego. Serwer proxy aplikacji i funkcja [Zarządzanej przeglądarki usługi Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) mogą być również używane razem, aby umożliwić użytkownikom zdalnym bezpieczny dostęp do wewnętrznych witryn sieci Web z urządzeń z systemem iOS i Android.

### <a name="roadmap-to-the-cloud"></a>Mapa drogowa chmury

Inną główną zaletą implementacji usługi Proxy aplikacji jest rozszerzenie usługi Azure AD na środowisko lokalne. W rzeczywistości implementowanie serwera proxy aplikacji jest kluczowym krokiem w przenoszeniu organizacji i aplikacji do chmury. Przechodząc do chmury i z dala od uwierzytelniania lokalnego, można zmniejszyć rozmiar lokalny i używać funkcji zarządzania tożsamościami usługi Azure AD jako płaszczyzny sterowania. Przy minimalnych lub żadnych aktualizacjach istniejących aplikacji masz dostęp do funkcji chmury, takich jak logowanie jednokrotne, uwierzytelnianie wieloskładnikowe i zarządzanie centralne. Instalowanie niezbędnych składników do serwera proxy aplikacji jest prosty proces tworzenia struktury dostępu zdalnego. Przechodząc do chmury, masz dostęp do najnowszych funkcji, aktualizacji i funkcji usługi Azure AD, takich jak wysoka dostępność i odzyskiwanie po awarii.

Aby dowiedzieć się więcej na temat migracji aplikacji do usługi Azure AD, zobacz oficjalny dokument ["Migrowanie aplikacji do usługi Azure Active Directory".](https://aka.ms/migrateapps/whitepaper)

## <a name="architecture"></a>Architektura

Na poniższym diagramie przedstawiono ogólnie, jak usługi uwierzytelniania usługi Azure AD i serwer proxy aplikacji współpracują ze sobą w celu zapewnienia logowania jednokrotnego do aplikacji lokalnych dla użytkowników końcowych.

![Przepływ uwierzytelniania serwera proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Po użytkownik ma dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest przekierowywane do strony logowania usługi Azure AD. Jeśli skonfigurowano zasady dostępu warunkowego, w tej chwili sprawdzane są określone warunki, aby upewnić się, że spełniasz wymagania dotyczące zabezpieczeń organizacji.
2. Po pomyślnym zalogowaniu usługa Azure AD wysyła token do urządzenia klienckiego użytkownika.
3. Klient wysyła token do usługi proxy aplikacji, która pobiera nazwę główną użytkownika (UPN) i nazwę głównego zabezpieczeń (SPN) z tokenu.
4. Serwer proxy aplikacji przekazuje żądanie, które jest odbierane przez [łącznik](application-proxy-connectors.md)serwera proxy aplikacji .
5. Łącznik wykonuje wszelkie dodatkowe uwierzytelnianie wymagane w imieniu użytkownika *(Opcjonalne w zależności od metody uwierzytelniania*), żąda wewnętrznego punktu końcowego serwera aplikacji i wysyła żądanie do aplikacji lokalnej.
6. Odpowiedź z serwera aplikacji jest wysyłana za pośrednictwem łącznika do usługi proxy aplikacji.
7. Odpowiedź jest wysyłana z usługi proxy aplikacji do użytkownika.

|**Składnik**|**Opis**|
|:-|:-|
|Endpoint|Punktem końcowym jest adres URL lub [portal użytkowników końcowych](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w sieci mogą uzyskiwać dostęp do aplikacji za pośrednictwem adresu URL lub portalu użytkownika końcowego. Gdy użytkownicy przejść do jednego z tych punktów końcowych, uwierzytelniają się w usłudze Azure AD, a następnie są kierowane za pośrednictwem łącznika do aplikacji lokalnej.|
|Azure AD|Usługa Azure AD wykonuje uwierzytelnianie przy użyciu katalogu dzierżawy przechowywanego w chmurze.|
|Usługa proxy aplikacji|Ta usługa serwera proxy aplikacji jest uruchamiana w chmurze w ramach usługi Azure AD. Przekazuje token logowania od użytkownika do łącznika serwera proxy aplikacji. Serwer proxy aplikacji przesyła dalej wszystkie dostępne nagłówki w żądaniu i ustawia nagłówki zgodnie z protokołem, na adres IP klienta. Jeśli żądanie przychodzące do serwera proxy ma już ten nagłówek, adres IP klienta jest dodawany na końcu listy oddzielonej przecinkami, która jest wartością nagłówka.|
|Złącze serwera proxy aplikacji|Łącznik jest lekkim agentem, który działa na serwerze Windows Server wewnątrz sieci. Łącznik zarządza komunikacją między usługą proxy aplikacji w chmurze a aplikacją lokalną. Łącznik używa tylko połączeń wychodzących, więc nie trzeba otwierać żadnych portów przychodzących ani umieszczać niczego w strefie DMZ. Łączniki są bezstanowe i ściągają informacje z chmury w razie potrzeby. Aby uzyskać więcej informacji na temat łączników, takich jak sposób równoważenia obciążenia i uwierzytelniania, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md).|
|Usługa Active Directory (AD)|Usługa Active Directory jest uruchamiana lokalnie w celu uwierzytelniania kont domeny. Po skonfigurowaniu logowania jednokrotnego łącznik komunikuje się z usługą AD w celu wykonania dodatkowego wymaganego uwierzytelniania.|
|Aplikacja lokalna|Na koniec użytkownik może uzyskać dostęp do aplikacji lokalnej.|

Usługa Azure AD Application Proxy składa się z usługi serwera proxy aplikacji opartej na chmurze i łącznika lokalnego. Łącznik nasłuchuje żądań z usługi Proxy aplikacji i obsługuje połączenia z aplikacjami wewnętrznymi. Należy pamiętać, że wszystkie komunikaty występują za ok. Oznacza to, że komunikacja jest tylko wychodząca. Łącznik używa certyfikatu klienta do uwierzytelniania w usłudze Proxy aplikacji dla wszystkich wywołań. Jedynym wyjątkiem od zabezpieczeń połączenia jest początkowy krok konfiguracji, w którym jest ustanawiany certyfikat klienta. Zobacz pełnomocnika aplikacji [pod maską, aby](application-proxy-security.md#under-the-hood) uzyskać więcej informacji.

### <a name="application-proxy-connectors"></a>Łączniki serwera proxy aplikacji

[Łączniki serwera proxy aplikacji](application-proxy-connectors.md) są lekkimi agentami wdrożonymi lokalnie, które ułatwiają połączenie wychodzące z usługą proxy aplikacji w chmurze. Łączniki muszą być zainstalowane w systemie Windows Server, który ma dostęp do aplikacji wewnętrznej bazy danych. Użytkownicy łączą się z usługą chmury serwera proxy aplikacji, która kieruje swój ruch do aplikacji za pośrednictwem łączników, jak pokazano poniżej.

![Połączenia sieciowe serwera proxy aplikacji usługi Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Konfiguracja i rejestracja między łącznikiem a usługą proxy aplikacji odbywa się w następujący sposób:

1. Administrator IT otwiera porty 80 i 443 do ruchu wychodzącego i umożliwia dostęp do kilku adresów URL, które są potrzebne przez łącznik, usługę serwera proxy aplikacji i usługi Azure AD.
2. Administrator loguje się do witryny Azure portal i uruchamia plik wykonywalny w celu zainstalowania łącznika na lokalnym serwerze windows.
3. Łącznik zaczyna "nasłuchiwać" usługi serwera proxy aplikacji.
4. Administrator dodaje aplikację lokalną do usługi Azure AD i konfiguruje ustawienia, takie jak adresy URL, które użytkownicy muszą połączyć się ze swoimi aplikacjami.

Aby uzyskać więcej informacji, zobacz [Planowanie wdrażania serwera proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).

Zaleca się, aby zawsze wdrażać wiele łączników w celu zapewnienia nadmiarowości i skalowania. Łączniki, w połączeniu z usługą, zajmują się wszystkimi zadaniami o wysokiej dostępności i mogą być dodawane lub usuwane dynamicznie. Za każdym razem, gdy pojawia się nowe żądanie, jest ono kierowane do jednego z dostępnych łączników. Gdy łącznik jest uruchomiony, pozostaje aktywny, ponieważ łączy się z usługą. Jeśli łącznik jest tymczasowo niedostępny, nie reaguje na ten ruch. Nieużywane łączniki są oznaczone jako nieaktywne i usuwane po 10 dniach braku aktywności.

Łączniki również sondowania serwera, aby dowiedzieć się, czy istnieje nowsza wersja łącznika. Chociaż można wykonać ręczną aktualizację, łączniki będą aktualizowane automatycznie tak długo, jak długo jest uruchomiona usługa aktualizacji łącznika serwera proxy aplikacji. W przypadku dzierżawców z wieloma łącznikami aktualizacje automatyczne są przeznaczone dla jednego łącznika naraz w każdej grupie, aby zapobiec przestojom w twoim środowisku.

**Uwaga:** Można monitorować [stronę historii wersji](application-proxy-release-version-history.md) serwera proxy aplikacji, aby otrzymywać powiadomienia o wydaniu aktualizacji, subskrybując jego kanał RSS.

Każdy łącznik serwera proxy aplikacji jest przypisany do [grupy łączników](application-proxy-connector-groups.md). Łączniki w tej samej grupie łączników działają jako pojedyncze jednostki dla wysokiej dostępności i równoważenia obciążenia. Można tworzyć nowe grupy, przypisywać łączniki do nich w witrynie Azure portal, a następnie przypisywać określone łączniki do obsługi określonych aplikacji. Zaleca się, aby mieć co najmniej dwa łączniki w każdej grupie łączników dla wysokiej dostępności.

Grupy łączników są przydatne, gdy trzeba obsługiwać następujące scenariusze:

* Publikowanie aplikacji geograficznych
* Segmentacja/izolacja aplikacji
* Publikowanie aplikacji sieci Web działających w chmurze lub lokalnie

Aby uzyskać więcej informacji na temat wybierania miejsca instalowania łączników i optymalizowania sieci, zobacz [Zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Inne przypadki użycia

Do tego momentu skupiliśmy się na użyciu serwera proxy aplikacji do publikowania aplikacji lokalnych na zewnątrz, jednocześnie umożliwiając logowanie jednokrotne we wszystkich aplikacjach w chmurze i lokalnych. Istnieją jednak inne przypadki użycia serwera proxy aplikacji, o których warto wspomnieć. Obejmują one następujące raporty:

* **Bezpiecznie publikuj interfejsy API REST**. Jeśli masz logikę biznesową lub interfejsy API uruchomione lokalnie lub hostowane na maszynach wirtualnych w chmurze, serwer proxy aplikacji zapewnia publiczny punkt końcowy dostępu do interfejsu API. Dostęp do punktu końcowego interfejsu API umożliwia kontrolowanie uwierzytelniania i autoryzacji bez konieczności portów przychodzących. Zapewnia dodatkowe zabezpieczenia za pośrednictwem funkcji usługi Azure AD Premium, takich jak uwierzytelnianie wieloskładnikowe i dostęp warunkowy oparty na urządzeniach dla komputerów stacjonarnych, iOS, MAC i android urządzeń korzystających z usługi Intune. Aby dowiedzieć się więcej, zobacz [Jak włączyć natywne aplikacje klienckie do interakcji z aplikacjami proxy](application-proxy-configure-native-client-application.md) i [chronić interfejs API przy użyciu usługi OAuth 2.0 z usługą Azure Active Directory i zarządzaniem interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Usługi pulpitu zdalnego** **(RDS)**. Standardowe wdrożenia usług PULPITU zdalnego wymagają otwartych połączeń przychodzących. Jednak [wdrożenie usługi RDS z serwerem proxy aplikacji](application-proxy-integrate-with-remote-desktop-services.md) ma stałe połączenie wychodzące z serwera z uruchomieniem usługi łącznika. W ten sposób można zaoferować więcej aplikacji użytkownikom końcowym, publikując aplikacje lokalne za pośrednictwem usług pulpitu zdalnego. Można również zmniejszyć obszar ataku wdrożenia z ograniczonym zestawem weryfikacji dwuetapowej i kontroli dostępu warunkowego do rds.
* **Publikowanie aplikacji, które łączą się za pomocą WebSockets**. Obsługa [qlik sense](application-proxy-qlik.md) jest w publicznej wersji zapoznawczej i zostanie rozszerzona na inne aplikacje w przyszłości.
* **Włącz natywne aplikacje klienckie do interakcji z aplikacjami proxy**. Za pomocą serwera proxy aplikacji usługi Azure AD można publikować aplikacje sieci Web, ale może również służyć do publikowania [natywnych aplikacji klienckich](application-proxy-configure-native-client-application.md) skonfigurowanych za pomocą biblioteki uwierzytelniania usługi Azure AD (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci Web, ponieważ są zainstalowane na urządzeniu, podczas gdy aplikacje sieci web są dostępne za pośrednictwem przeglądarki.

## <a name="conclusion"></a>Podsumowanie

Sposób, w jaki pracujemy i narzędzia, których używamy, szybko się zmienia. Wraz z tym, że coraz więcej pracowników wprowadza własne urządzenia do pracy i wszechobecne korzystanie z aplikacji Software-as-a-Service (SaaS), musi również ewoluować sposób, w jaki organizacje zarządzają i zabezpieczają swoje dane. Firmy nie działają już wyłącznie w obrębie własnych murów, chronionych fosą otaczającą ich granicę. Dane są przesyłane do większej liczby lokalizacji niż kiedykolwiek wcześniej — zarówno w środowiskach lokalnych, jak i chmurowych. Ewolucja ta pomogła zwiększyć produktywność i zdolność użytkowników do współpracy, ale także sprawia, że ochrona poufnych danych jest trudniejsza.

Niezależnie od tego, czy korzystasz obecnie z usługi Azure AD do zarządzania użytkownikami w scenariuszu współistnienia hybrydowego, czy chcesz rozpocząć podróż do chmury, wdrożenie serwera proxy aplikacji usługi Azure AD może pomóc zmniejszyć rozmiar lokalnego śladu, udostępniając zdalne środowisko dostępu jako usługi.

Organizacje powinny zacząć korzystać z usługi App Proxy już dziś, aby skorzystać z następujących korzyści:

* Publikowanie aplikacji lokalnych zewnętrznie bez kosztów związanych z utrzymaniem tradycyjnych rozwiązań VPN lub innych lokalnych rozwiązań do publikowania w sieci Web i podejścia DMZ
* Logowanie jednokrotne do wszystkich aplikacji, czy to usługi Office 365, czy innych aplikacji SaaS, w tym aplikacji lokalnych
* Zabezpieczenia skalowania w chmurze, w których usługa Azure AD wykorzystuje dane telemetryczne usługi Office 365, aby zapobiec nieautoryzowanemu dostępowi
* Integracja usługi Intune w celu zapewnienia uwierzytelnienia ruchu korporacyjnego
* Centralizacja zarządzania kontem użytkownika
* Automatyczne aktualizacje, aby upewnić się, że masz najnowsze poprawki zabezpieczeń
* Nowe funkcje w miarę ich uwalniania; najnowsza obsługa logowania jednokrotnego SAML i bardziej szczegółowe zarządzanie plikami cookie aplikacji

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące planowania, obsługi i zarządzania serwerem proxy aplikacji usługi Azure AD, zobacz [Planowanie wdrażania serwera proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).
* Aby zaplanować prezentację na żywo lub uzyskać bezpłatną 90-dniową wersję próbną do oceny, zobacz [Wprowadzenie do programu Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
