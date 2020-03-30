---
title: Planowanie wdrażania serwera proxy aplikacji usługi Azure Active Directory
description: Kompleksowy przewodnik dotyczący planowania wdrażania serwera proxy aplikacji w organizacji
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330908"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planowanie wdrożenia serwera proxy aplikacji usługi Azure AD

Usługa Azure Active Directory (Azure AD) Application Proxy to bezpieczne i ekonomiczne rozwiązanie dostępu zdalnego dla aplikacji lokalnych. Zapewnia natychmiastową ścieżkę przejścia dla organizacji "Cloud First", aby zarządzać dostępem do starszych aplikacji lokalnych, które nie są jeszcze w stanie używać nowoczesnych protokołów. Aby uzyskać dodatkowe informacje wprowadzające, zobacz [Co to jest serwer proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Serwer proxy aplikacji jest zalecany do udzielania użytkownikom zdalnym dostępu do zasobów wewnętrznych. Serwer proxy aplikacji zastępuje potrzebę korzystania z sieci VPN lub odwrotnego serwera proxy dla tych przypadków użycia dostępu zdalnego. Nie jest przeznaczony dla użytkowników, którzy są w sieci firmowej. Ci użytkownicy, którzy używają serwera proxy aplikacji do dostępu do intranetu, mogą wystąpić niepożądane problemy z wydajnością.

Ten artykuł zawiera zasoby potrzebne do planowania, obsługi i zarządzania serwerem proxy aplikacji usługi Azure AD. 

## <a name="plan-your-implementation"></a>Zaplanuj swoją implementację

Poniższa sekcja zawiera szeroki widok kluczowych elementów planowania, które będą skonfigurowane do wydajnego wdrażania. 

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem implementacji należy spełnić następujące wymagania wstępne. Więcej informacji na temat konfigurowania środowiska, w tym te wymagania wstępne, można zobaczyć w tym [samouczku](application-proxy-add-on-premises-application.md).

* **Łączniki:** Łączniki są lekkimi agentami, które można wdrożyć na:
   * Sprzęt fizyczny lokalnie
   * Maszyna wirtualna hostowana w dowolnym rozwiązaniu funkcji hypervisor
   * Maszyna wirtualna hostowana na platformie Azure w celu umożliwienia połączenia wychodzącego z usługą proxy aplikacji.

* Zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD, aby](application-proxy-connectors.md) uzyskać bardziej szczegółowe omówienie.

     * Przed zainstalowaniem złączy należy włączyć maszyny [złączy dla protokołu TLS 1.2.](application-proxy-add-on-premises-application.md)

     * Jeśli to możliwe, wdrażaj łączniki w [tej samej sieci](application-proxy-network-topology.md) i segmencie co serwery aplikacji sieci web zaplecza. Najlepiej wdrożyć łączniki po zakończeniu odnajdywania aplikacji.
     * Zaleca się, że każda grupa łączników ma co najmniej dwa łączniki, aby zapewnić wysoką dostępność i skalę. Posiadanie trzech złączy jest optymalne w przypadku, gdy może być konieczne serwis komputera w dowolnym momencie. Przejrzyj [tabelę pojemności łącznika,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) aby ułatwić podjęcie decyzji o typie komputera, na której mają być instalowane łączniki. Im większa maszyna, tym bardziej bufor i wydajny będzie złącze.

* **Ustawienia dostępu do sieci:** łączniki serwera proxy aplikacji usługi Azure AD [łączą się z platformą Azure za pośrednictwem protokołu HTTPS (TCP Port 443) i HTTP (TCP Port 80).](application-proxy-add-on-premises-application.md) 

   * Zakończenie ruchu TLS łącznika nie jest obsługiwane i uniemożliwi łącznikom ustanowienie bezpiecznego kanału z odpowiednimi punktami końcowymi usługi Azure App Proxy.

   * Unikaj wszystkich form inspekcji wbudowanej w wychodzącej komunikacji TLS między łącznikami a platformą Azure. Wewnętrzna inspekcja między aplikacjami łącznika i wewnętrznej bazy danych jest możliwe, ale może pogorszyć środowisko użytkownika i jako takie nie jest zalecane.

   * Równoważenie obciążenia samych złączy również nie jest obsługiwane, a nawet konieczne.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Ważne zagadnienia przed skonfigurowaniem serwera proxy aplikacji usługi Azure AD

Aby skonfigurować i zaimplementować serwer proxy aplikacji usługi Azure AD, muszą być spełnione następujące podstawowe wymagania.

*  **Dołączanie do platformy Azure:** Przed wdrożeniem serwera proxy aplikacji tożsamości użytkowników muszą być synchronizowane z katalogu lokalnego lub tworzone bezpośrednio w dzierżawach usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnianie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji usługi App Proxy i mieć informacje o identyfikatorze użytkownika niezbędne do wykonywania logowania jednokrotnego.Identity synchronization allows Azure AD to pre-authenticate users before granting them access to App Proxy published applications and to have the necessary user identifier information to perform single sign-on (SSO).

* **Wymagania dotyczące dostępu warunkowego:** Nie zaleca się używania serwera proxy aplikacji do dostępu intranetowego, ponieważ zwiększa to opóźnienie, które będzie miało wpływ na użytkowników. Zalecamy używanie serwera proxy aplikacji z prenajmowanie i zasady dostępu warunkowego dla dostępu zdalnego z Internetu.  Podejście do zapewnienia dostępu warunkowego do użytku w intranecie jest modernizacja aplikacji, dzięki czemu mogą one bezpośrednio uwierzytelniać się za pomocą funkcji AAD. Aby uzyskać więcej informacji, zapoznaj się z informacjami na temat [Zasobów do migracji aplikacji do usługi AAD.](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **Limity usług:** Aby chronić przed nadmierną konsumpcję zasobów przez poszczególnych dzierżawców są limity ograniczania ustawione dla aplikacji i dzierżawy. Aby wyświetlić te limity, zapoznaj się z [limitami i ograniczeniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Te limity ograniczania są oparte na wskaźniku znacznie powyżej typowego woluminu użycia i zapewnia duży bufor dla większości wdrożeń.

* **Certyfikat publiczny:** Jeśli używasz niestandardowych nazw domen, musisz zaopatrzyć się w certyfikat TLS/SSL. W zależności od wymagań organizacyjnych uzyskanie certyfikatu może zająć trochę czasu i zalecamy rozpoczęcie procesu tak wcześnie, jak to możliwe. Usługa Azure Application Proxy obsługuje certyfikaty standardowe, [wieloznaczne](application-proxy-wildcard.md)lub san. Aby uzyskać więcej informacji, zobacz [Konfigurowanie domen niestandardowych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

* **Wymagania dotyczące domeny:** Logowanie jednokrotne do opublikowanych aplikacji przy użyciu delegowania ograniczonego protokołu Kerberos (KCD) wymaga, aby serwer z systemem Łącznik i serwer z uruchomieniem aplikacji były przyłączone do domeny i były częścią tej samej domeny lub domen ufających.
Aby uzyskać szczegółowe informacje na ten temat, zobacz [KCD dla logowania jednokrotnego za](application-proxy-configure-single-sign-on-with-kcd.md) pomocą serwera proxy aplikacji. Usługa łącznika działa w kontekście systemu lokalnego i nie powinna być skonfigurowana do używania tożsamości niestandardowej.

* **Rekordy DNS dla adresów URL**

   * Przed użyciem domen niestandardowych w serwerze proxy aplikacji należy utworzyć rekord CNAME w publicznym systemie DNS, umożliwiając klientom rozpoznanie niestandardowego zdefiniowanego zewnętrznego adresu URL na wstępnie zdefiniowany adres serwera proxy aplikacji. Nie można utworzyć rekordu CNAME dla aplikacji korzystającej z domeny niestandardowej uniemożliwi użytkownikom zdalnym łączenie się z aplikacją. Kroki wymagane do dodania rekordów CNAME mogą się różnić w zależności od dostawcy DNS, dlatego dowiedz się, jak [zarządzać rekordami i zestawami rekordów DNS za pomocą portalu Azure.](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)

   * Podobnie hosty łącznika muszą być w stanie rozpoznać wewnętrzny adres URL publikowanych aplikacji.

* **Prawa i role administracyjne**

   * **Instalacja łącznika** wymaga lokalnych praw administratora do serwera Windows, na który jest instalowany. Wymaga również co najmniej roli *administratora aplikacji* do uwierzytelniania i rejestrowania wystąpienia łącznika w dzierżawie usługi Azure AD. 

   * **Publikowanie i administrowanie aplikacjami** wymaga roli *Administrator aplikacji.* Administratorzy aplikacji mogą zarządzać wszystkimi aplikacjami w katalogu, w tym rejestracjami, ustawieniami logowania dodatkowego, przypisaniami użytkowników i grup oraz licencjonowaniem, ustawieniami serwera proxy aplikacji i zgodą. Nie daje możliwość zarządzania dostępem warunkowym. Rola *Administrator aplikacji w chmurze* ma wszystkie możliwości administratora aplikacji, z tą różnicą, że nie zezwala na zarządzanie ustawieniami serwera proxy aplikacji.

* **Licencjonowanie:** Serwer proxy aplikacji jest dostępny za pośrednictwem subskrypcji usługi Azure AD Premium. Pełną listę opcji i funkcji licencjonowania można znaleźć na [stronie Cennik usługi Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)  

### <a name="application-discovery"></a>Odnajdowanie aplikacji

Skompiluj spis wszystkich aplikacji w zakresie, które są publikowane za pośrednictwem serwera proxy aplikacji, zbierając następujące informacje:

| Typ informacji| Informacje do zbierania |
|---|---|
| Typ usługi| Na przykład: SharePoint, SAP, CRM, Niestandardowa aplikacja sieci Web, INTERFEJS API |
| Platforma aplikacji | Na przykład: Windows IIS, Apache na Linuksie, Tomcat, NGINX |
| Członkostwo w domenie| W pełni kwalifikowana nazwa domeny serwera sieci Web (FQDN) |
| Lokalizacja aplikacji | Gdzie serwer www lub farma znajduje się w twojej infrastrukturze |
| Dostęp wewnętrzny | Dokładny adres URL używany podczas uzyskiwania dostępu do aplikacji wewnętrznie. <br> Jeśli gospodarstwo, jaki rodzaj równoważenia obciążenia jest w użyciu? <br> Czy aplikacja pobiera zawartość ze źródeł innych niż ona sama.<br> Określ, czy aplikacja działa za pośrednictwem WebSockets. |
| Dostęp zewnętrzny | Rozwiązanie dostawcy, że aplikacja jest już narażony na zewnątrz. <br> Adres URL, którego chcesz użyć do dostępu zewnętrznego. Jeśli program SharePoint, upewnij się, że mapowania dostępu alternatywnego są skonfigurowane zgodnie z [tymi wskazówkami](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Jeśli nie, musisz zdefiniować zewnętrzne adresy URL. |
| Certyfikat publiczny | Jeśli używasz domeny niestandardowej, zaopatrzyj się w certyfikat o odpowiedniej nazwie podmiotu. jeżeli istnieje certyfikat, należy zwrócić uwagę na numer seryjny i lokalizację, z której można go uzyskać. |
| Typ uwierzytelniania| Typ uwierzytelniania obsługiwane przez obsługę aplikacji, takich jak Podstawowe, Uwierzytelnianie integracji systemu Windows, formularze oparte, oparte na nagłówku i oświadczeń. <br>Jeśli aplikacja jest skonfigurowana do uruchamiania na określonym koncie domeny, zanotuj w pełni kwalifikowaną nazwę domeny (FQDN) konta usługi.<br> Jeśli oparte na SAML, identyfikator i odpowiedzi adresy URL. <br> Jeśli oparte na nagłówku, rozwiązanie dostawcy i określone wymagania dotyczące obsługi typu uwierzytelniania. |
| Nazwa grupy łączników | Logiczna nazwa grupy łączników, które zostaną wyznaczone w celu zapewnienia kanału i jednostki SSO do tej aplikacji wewnętrznej bazy danych. |
| Dostęp użytkowników/grup | Użytkownicy lub grupy użytkowników, które otrzymają zewnętrzny dostęp do aplikacji. |
| Dodatkowe wymagania | Należy zwrócić uwagę na wszelkie dodatkowe wymagania dotyczące dostępu zdalnego lub zabezpieczeń, które powinny być brane pod uwagę podczas publikowania aplikacji. |

Arkusz [kalkulacyjny spisu aplikacji](https://aka.ms/appdiscovery) można pobrać, aby zamieścić zasoby aplikacji.

### <a name="define-organizational-requirements"></a>Definiowanie wymagań organizacyjnych

Poniżej przedstawiono obszary, dla których należy zdefiniować wymagania biznesowe organizacji. Każdy obszar zawiera przykłady wymagań

 **Dostęp**

* Użytkownicy zdalni z przyłączony do domeny lub urządzeniach przyłączonych do usługi Azure AD mogą bezpiecznie uzyskiwać dostęp do opublikowanych aplikacji za pomocą bezproblemowego logowania jednokrotnego.Remote users with domain joined or Azure AD joined devices users can accessed applications securely with seamless single sign-on (SSO).

* Użytkownicy zdalni z zatwierdzonymi urządzeniami osobistymi mogą bezpiecznie uzyskiwać dostęp do opublikowanych aplikacji, pod warunkiem że są zarejestrowani w usłudze MFA i zarejestrowali aplikację Microsoft Authenticator na telefonie komórkowym jako metodę uwierzytelniania.

**Zarządzania** 

* Administratorzy mogą definiować i monitorować cykl życia przypisań użytkowników do aplikacji publikowanych za pośrednictwem serwera proxy aplikacji.

**Zabezpieczenia**

* Tylko użytkownicy przypisani do aplikacji za pośrednictwem członkostwa w grupie lub indywidualnie mogą uzyskiwać dostęp do tych aplikacji.

**Wydajność**

* Nie ma pogorszenia wydajności aplikacji w porównaniu do uzyskiwania dostępu do aplikacji z sieci wewnętrznej.

**Doświadczenie użytkownika**

* Użytkownicy są świadomi, jak uzyskać dostęp do swoich aplikacji przy użyciu znanych firmowych adresów URL na dowolnej platformie urządzeń.

**Inspekcja**
* Administratorzy mogą przeprowadzać inspekcję działań dostępu użytkowników.


### <a name="best-practices-for-a-pilot"></a>Najlepsze praktyki dla pilota

Określ czas i nakład pracy potrzebny do pełnego uruchomienia pojedynczej aplikacji do zdalnego dostępu za pomocą logowania jednokrotnego.Determine the amount of time and effort needed to fully commission a single application for remote access with Single sign-on (SSO). Zrób to, uruchamiając program pilotażowy, który uwzględnia jego początkowe odnajdowanie, publikowanie i ogólne testy. Użycie prostej aplikacji sieci web opartej na iis, która jest już wstępnie skonfigurowana do zintegrowanego uwierzytelniania systemu Windows (IWA), pomogłoby ustanowić punkt odniesienia, ponieważ ta konfiguracja wymaga minimalnego wysiłku, aby pomyślnie pilotować dostęp zdalny i jednokrotne.

Następujące elementy projektu powinny zwiększyć powodzenie implementacji pilotażowej bezpośrednio w dzierżawie produkcyjnej.  

**Zarządzanie łącznikami:**  

* Łączniki odgrywają kluczową rolę w dostarczaniu lokalnego przewodu do aplikacji. Użycie **domyślnej** grupy łączników jest odpowiednie do wstępnego testowania pilotażowego opublikowanych aplikacji przed uruchomieniem ich do produkcji. Pomyślnie przetestowane aplikacje można następnie przenieść do grup łączników produkcyjnych.

**Zarządzanie aplikacjami**:

* Pracownicy najprawdopodobniej pamiętają, że zewnętrzny adres URL jest znany i trafny. Unikaj publikowania aplikacji przy użyciu naszych wstępnie zdefiniowanych msappproxy.net lub onmicrosoft.com przyrostków. Zamiast tego należy podać znaną zweryfikowaną domenę najwyższego poziomu, poprzedzony logiczną nazwę hosta, taką jak *intranet.<customers_domain>.com*.

* Ogranicz widoczność ikony aplikacji pilotażowej do grupy pilotażowej, ukrywając ikonę uruchamiania w portalu Azure MyApps. Gdy jest gotowy do produkcji można zakres aplikacji do odpowiednich odbiorców docelowych, albo w tej samej dzierżawy przedprodukcji lub również przez publikowanie aplikacji w dzierżawie produkcyjnej.

**Ustawienia logowania jednokrotnego:** Niektóre ustawienia logowania jednokrotnego mają określone zależności, które mogą zająć trochę czasu, aby skonfigurować, więc uniknąć opóźnień kontroli zmian, zapewniając zależności są rozwiązywane z wyprzedzeniem. Obejmuje to hosty łącznika dołączania do domeny w celu wykonywania protokołu SSO przy użyciu ograniczonego delegowania Kerberos (KCD) i dbanie o inne czasochłonne działania. Na przykład Konfigurowanie wystąpienia dostępu PING, jeśli potrzebujesz samoso na podstawie nagłówka.

**TLS Między hostem łącznika a aplikacją docelową:** Bezpieczeństwo jest najważniejsze, dlatego TLS między hostem łącznika a aplikacjami docelowymi powinien być zawsze używany. Szczególnie jeśli aplikacja sieci web jest skonfigurowana do uwierzytelniania opartego na formularzach (FBA), ponieważ poświadczenia użytkownika są następnie skutecznie przesyłane w postaci zwykłego tekstu.

**Implementuj stopniowo i przetestuj każdy krok**. Przeprowadzić podstawowe testy funkcjonalne po opublikowaniu aplikacji, aby upewnić się, że wszystkie wymagania użytkownika i firmy są spełnione, postępując zgodnie z poniższymi wskazówkami:

1. Testowanie i sprawdzanie poprawności ogólnego dostępu do aplikacji sieci web z wyłączonym uwierzytelnianiem wstępnym.
2. Jeśli zakończy się pomyślnie, włącz uwierzytelnianie wstępne i przypisz użytkowników i grupy. Przetestuj i sprawdź poprawność dostępu.
3. Następnie dodaj metodę SSO dla aplikacji i przetestuj ponownie, aby sprawdzić poprawność dostępu.
4. Zastosuj zasady dostępu warunkowego i usługi MFA zgodnie z wymaganiami. Przetestuj i sprawdź poprawność dostępu.

Narzędzia do **rozwiązywania problemów:** Podczas rozwiązywania problemów zawsze należy rozpocząć od sprawdzania poprawności dostępu do opublikowanej aplikacji z przeglądarki na hoście łącznika i upewnij się, że aplikacja działa zgodnie z oczekiwaniami. Im prostsza konfiguracja, tym łatwiej określić przyczynę, więc należy rozważyć próbę odtworzenia problemów z minimalną konfiguracją, taką jak używanie tylko jednego łącznika i bez jednokrotnego ustawienia użytkownika. W niektórych przypadkach narzędzia do debugowania sieci Web, takie jak Fiddler firmy Telerik, mogą okazać się niezbędne do rozwiązywania problemów z dostępem lub zawartością w aplikacjach dostępnych za pośrednictwem serwera proxy. Fiddler może również działać jako serwer proxy, aby pomóc w śledzeniu i debugowaniu ruchu dla platform mobilnych, takich jak iOS i Android, i praktycznie wszystko, co można skonfigurować do trasy za pośrednictwem serwera proxy. Więcej informacji można znaleźć w [przewodniku po rozwiązywaniu problemów.](application-proxy-troubleshoot.md)

## <a name="implement-your-solution"></a>Zaimplementuj swoje rozwiązanie

### <a name="deploy-application-proxy"></a>Wdrażanie serwera proxy aplikacji

Kroki wdrażania serwera proxy aplikacji są opisane w tym [samouczku dodawania lokalnej aplikacji dostępu zdalnego.](application-proxy-add-on-premises-application.md) Jeśli instalacja nie powiedzie się, wybierz opcję **Rozwiązywanie problemów z serwerem proxy aplikacji** w portalu lub skorzystaj z przewodnika rozwiązywania [problemów z instalowaniem łącznika agenta proxy aplikacji](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikowanie aplikacji za pośrednictwem serwera proxy aplikacji

Aplikacje do publikowania zakładają, że wszystkie wymagania wstępne zostały spełnione i że masz kilka łączników wyświetlanych jako zarejestrowane i aktywne na stronie Serwera proxy aplikacji.

Aplikacje można również publikować za pomocą programu [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Poniżej znajduje się kilka najlepszych rozwiązań, które należy wykonać podczas publikowania aplikacji:

* **Użyj grup łączników:** Przypisz grupę łączników, która została wyznaczona do publikowania każdej odpowiedniej aplikacji. Zaleca się, że każda grupa łączników ma co najmniej dwa łączniki, aby zapewnić wysoką dostępność i skalę. Posiadanie trzech złączy jest optymalne w przypadku, gdy może być konieczne serwis komputera w dowolnym momencie. Ponadto zobacz [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników,](application-proxy-connector-groups.md) aby zobaczyć, jak można również używać grup łączników do segmentowania łączników według sieci lub lokalizacji.

* **Ustaw limit czasu aplikacji wewnętrznej bazy**danych: To ustawienie jest przydatne w scenariuszach, w których aplikacja może wymagać więcej niż 75 sekund na przetworzenie transakcji klienta. Na przykład, gdy klient wysyła kwerendę do aplikacji sieci web, która działa jako front-end do bazy danych. Front-end wysyła tę kwerendę do serwera bazy danych zaplecza i czeka na odpowiedź, ale do czasu otrzymania odpowiedzi, po stronie klienta konwersacji limit czasu. Ustawienie limitu czasu na Długi zapewnia 180 sekund na ukończenie dłuższych transakcji.

* **Użyj odpowiednich typów plików cookie**

   * **Plik cookie tylko do protokołu HTTP:** Zapewnia dodatkowe zabezpieczenia dzięki temu, że serwer proxy aplikacji zawiera flagę HTTPOnly w nagłówkach odpowiedzi HTTP set-cookie. To ustawienie pomaga ograniczyć exploity, takie jak skrypty między witrynami (XSS). Pozostaw ten zestaw na Nie dla klientów/agentów użytkowników, którzy wymagają dostępu do pliku cookie sesji. Na przykład klient RDP/MTSC łączący się z bramą pulpitu zdalnego opublikowaną za pośrednictwem serwera proxy aplikacji.

   * **Bezpieczny plik cookie:** Gdy plik cookie jest ustawiony z atrybutem Secure, agent użytkownika (aplikacja po stronie klienta) będzie zawierał plik cookie w żądaniach HTTP tylko wtedy, gdy żądanie jest przesyłane za pośrednictwem kanału zabezpieczonego TLS. Pomaga to zmniejszyć ryzyko naruszenia zabezpieczeń pliku cookie za pośrednictwem kanałów zwykłego tekstu, dlatego należy włączyć.

   * **Trwały plik cookie:** Umożliwia plik cookie sesji serwera proxy aplikacji, aby utrzymać się między zamknięciem przeglądarki, pozostając prawidłowym, dopóki nie wygaśnie lub nie zostanie usunięty. Używane w scenariuszach, w których rozbudowane aplikacji, takich jak biuro uzyskuje dostęp do dokumentu w ramach opublikowanej aplikacji sieci web, bez ponownego monitu użytkownika o uwierzytelnienie. Włącz jednak ostrożnie, ponieważ trwałe pliki cookie mogą ostatecznie pozostawić usługę na ryzyko nieautoryzowanego dostępu, jeśli nie będą używane w połączeniu z innymi formantami kompensacyjnymi. To ustawienie powinno być używane tylko w przypadku starszych aplikacji, które nie mogą udostępniać plików cookie między procesami. Lepiej zaktualizować aplikację do obsługi udostępniania plików cookie między procesami zamiast używać tego ustawienia.

* **Tłumaczenie adresów URL w nagłówkach:** Włącz tę opcję w scenariuszach, w których nie można skonfigurować wewnętrznego systemu DNS tak, aby odpowiadał publicznemu obszarowi nazw organizacji (aka Split DNS). Chyba że aplikacja wymaga oryginalnego nagłówka hosta w żądaniu klienta, pozostaw tę wartość ustawioną na Tak. Alternatywą jest użycie przez łącznik fqdn w wewnętrznym adresie URL do routingu rzeczywistego ruchu i FQDN w zewnętrznym adresie URL jako nagłówka hosta. W większości przypadków ta alternatywa powinna umożliwić aplikacji działać normalnie, gdy dostęp zdalnie, ale użytkownicy tracą korzyści z konieczności dopasowania wewnątrz & adres URL zewnętrzny.

* **Tłumaczenie adresów URL w treści aplikacji:** Włącz tłumaczenie łącza treści aplikacji dla aplikacji, jeśli chcesz, aby łącza z tej aplikacji były tłumaczone w odpowiedziach z powrotem do klienta. Jeśli ta funkcja jest włączona, zapewnia najlepszą próbę tłumaczenia wszystkich łączy wewnętrznych, które serwer proxy aplikacji znajduje w odpowiedziach HTML i CSS zwracanych do klientów. Jest to przydatne podczas publikowania aplikacji, które zawierają kodowane na skróty bezwzględne lub netbios skrócone łącza w zawartości lub aplikacje z zawartością, która łączy się z innymi aplikacjami lokalnymi.

W scenariuszach, w których opublikowana aplikacja łączy się z innymi opublikowanymi aplikacjami, włącz tłumaczenie łączy dla każdej aplikacji, aby mieć kontrolę nad środowiskom użytkownika na poziomie poszczególnych aplikacji.

Załóżmy na przykład, że masz trzy aplikacje opublikowane za pośrednictwem serwera proxy aplikacji, które wszystkie łącze do siebie: korzyści, wydatki i podróże, a także czwartą aplikację, Feedback, która nie jest publikowana za pośrednictwem serwera proxy aplikacji.

![Zdjęcie 1](media/App-proxy-deployment-plan/link-translation.png)

Po włączeniu tłumaczenia łączy dla aplikacji Korzyści łącza do wydatków i podróży są przekierowywane do zewnętrznych adresów URL tych aplikacji, dzięki czemu użytkownicy uzyskujący dostęp do aplikacji spoza sieci firmowej mogą uzyskać do nich dostęp. Linki z wydatków i podróży z powrotem do korzyści nie działają, ponieważ tłumaczenie linku nie zostało włączone dla tych dwóch aplikacji. Łącze do opinii nie jest przekierowywane, ponieważ nie ma zewnętrznego adresu URL, więc użytkownicy korzystający z aplikacji Korzyści nie będą mogli uzyskać dostępu do aplikacji opinii spoza sieci firmowej. Zobacz szczegółowe informacje na temat [tłumaczenia linków i innych opcji przekierowania](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Dostęp do aplikacji

Istnieje kilka opcji zarządzania dostępem do opublikowanych zasobów serwera proxy aplikacji, więc wybierz najbardziej odpowiednie dla danego scenariusza i potrzeb skalowalności. Typowe podejścia obejmują: przy użyciu grup lokalnych, które są synchronizowane za pośrednictwem usługi Azure AD Connect, tworzenie grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika, przy użyciu grup samoobsługowych, które są zarządzane przez właściciela zasobu lub kombinacji wszystkich z nich. Zobacz połączone zasoby dla korzyści każdego z nich.

Najbardziej prostym sposobem przypisywania użytkownikom dostępu do aplikacji jest przechodzenie do opcji **Użytkownicy i grupy** z lewego okienka opublikowanej aplikacji i bezpośrednie przypisywanie grup lub osób.

![Zdjęcie 24](media/App-proxy-deployment-plan/add-user.png)

Można również zezwolić użytkownikom na samoobsługowy dostęp do aplikacji, przypisując grupę, której nie są obecnie członkami, i konfigurując opcje samoobsługi.

![Zdjęcie 25](media/App-proxy-deployment-plan/allow-access.png)

Jeśli ta opcja jest włączona, użytkownicy będą mogli zalogować się do portalu MyApps i zażądać dostępu, a następnie automatycznie zatwierdzić i dodać ją do już dozwolonej grupy samoobsługowej lub uzyskać zatwierdzenie od wyznaczonej osoby zatwierdzającej.

Użytkownicy-goście mogą również zostać [zaproszeni do uzyskania dostępu do wewnętrznych aplikacji opublikowanych za pośrednictwem serwera proxy aplikacji za pośrednictwem usługi Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)

W przypadku aplikacji lokalnych, które są zwykle dostępne anonimowo, nie wymagające uwierzytelniania, można wyłączyć opcję znajdującą się we **właściwościach**aplikacji .

![Zdjęcie 26](media/App-proxy-deployment-plan/assignment-required.png)


Pozostawienie tej opcji ustawionej na Nie umożliwia użytkownikom dostęp do aplikacji lokalnej za pośrednictwem serwera proxy aplikacji usługi Azure AD bez uprawnień, dlatego należy zachować ostrożność.

Po opublikowaniu aplikacji powinna być dostępna, wpisując swój zewnętrzny adres URL [https://myapps.microsoft.com](https://myapps.microsoft.com/)w przeglądarce lub ikonę w pliku .

### <a name="enable-pre-authentication"></a>Włącz uwierzytelnianie wstępne

Sprawdź, czy aplikacja jest dostępna za pośrednictwem serwera proxy aplikacji uzyskującego do niej dostęp za pośrednictwem zewnętrznego adresu URL. 

1. Przejdź do**aplikacji** >  **Azure Active Directory** > Enterprise**Wszystkie aplikacje** i wybierz aplikację, którą chcesz zarządzać.

2. Wybierz **pozycję Proxy aplikacji**.

3. W polu **Uwierzytelnianie wstępne** użyj listy rozwijanej, aby wybrać **pozycję Azure Active Directory**i wybierz pozycję **Zapisz**.

Po włączeniu uwierzytelniania wstępnego usługa Azure AD będzie najpierw kwestionować użytkowników do uwierzytelniania, a jeśli skonfigurowano logowanie jednokrotne, aplikacja zaplecza również zweryfikuje użytkownika przed udzieleniem dostępu do aplikacji. Zmiana trybu uwierzytelniania wstępnego z Passthrough na Usługę Azure AD również konfiguruje zewnętrzny adres URL z https, więc każda aplikacja początkowo skonfigurowana dla protokołu HTTP będzie teraz zabezpieczona za pomocą protokołu HTTPS.

### <a name="enable-single-sign-on"></a>Włączanie logowania jednokrotnego

Logowanie jedno i cofnięcia zapewnia najlepsze środowisko użytkownika i zabezpieczenia, ponieważ użytkownicy muszą zalogować się tylko raz podczas uzyskiwania dostępu do usługi Azure AD. Po wstępnej uwierzytelnieniu użytkownika, SSO jest wykonywane przez łącznik serwera proxy aplikacji uwierzytelniania do aplikacji lokalnej, w imieniu użytkownika. Aplikacja wewnętrznej bazy danych przetwarza logowanie tak, jakby był to sam użytkownik. 

Wybranie opcji **Przekazywanie** umożliwia użytkownikom dostęp do opublikowanej aplikacji bez konieczności uwierzytelniania w usłudze Azure AD.

Wykonywanie jednokrotnego dostępu jest możliwe tylko wtedy, gdy usługa Azure AD może zidentyfikować użytkownika żądającego dostępu do zasobu, więc aplikacja musi być skonfigurowana do wstępnego uwierzytelniania użytkowników z usługą Azure AD po uzyskaniu dostępu do usługi SSO do działania, w przeciwnym razie opcje jednokrotnego są wyłączone.

Przeczytaj [logowanie jednokrotne w aplikacjach usługi Azure AD,](what-is-single-sign-on.md) aby ułatwić wybór najbardziej odpowiedniej metody logowania jednokrotnego podczas konfigurowania aplikacji.

###  <a name="working-with-other-types-of-applications"></a>Praca z innymi rodzajami aplikacji

Usługa Azure AD Application Proxy może również obsługiwać aplikacje opracowane w celu korzystania z naszej biblioteki uwierzytelniania usługi Azure AD[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)lub biblioteki uwierzytelniania firmy[Microsoft (MSAL).](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/) Obsługuje natywne aplikacje klienckie, korzystając z tokenów wystawionych przez usługę Azure AD otrzymanych w informacjach nagłówka żądania klienta w celu wykonania uwierzytelniania wstępnego w imieniu użytkowników.

Przeczytaj [publikowanie natywnych i mobilnych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) i [aplikacji opartych na oświadczeniach,](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) aby dowiedzieć się więcej o dostępnych konfiguracjach serwera proxy aplikacji.

### <a name="use-conditional-access-to-strengthen-security"></a>Użyj dostępu warunkowego, aby wzmocnić bezpieczeństwo

Zabezpieczenia aplikacji wymaga zaawansowanego zestawu funkcji zabezpieczeń, które mogą chronić przed złożonymi zagrożeniami i reagować na nie w środowisku lokalnym i w chmurze. Osoby atakujące najczęściej uzyskują dostęp do sieci firmowej za pośrednictwem słabych, domyślnych lub skradzionych poświadczeń użytkownika.  Zabezpieczenia oparte na tożsamości firmy Microsoft zmniejszają użycie skradzionych poświadczeń, zarządzając tożsamościami uprzywilejowanymi i nieuprzywilejowanymi i chroniąc je.

Następujące funkcje mogą służyć do obsługi serwera proxy aplikacji usługi Azure AD:

* Dostęp warunkowy oparty na użytkowniku i lokalizacji: Ochrona poufnych danych przez ograniczenie dostępu użytkowników na podstawie lokalizacji geograficznej lub adresu IP za pomocą [zasad dostępu warunkowego opartego na lokalizacji](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Dostęp warunkowy oparty na urządzeniu: upewnij się, że tylko zarejestrowane, zatwierdzone i zgodne urządzenia mogą uzyskiwać dostęp do danych firmowych za pomocą [dostępu warunkowego opartego na urządzeniach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Dostęp warunkowy oparty na aplikacji: praca nie musi się zatrzymywać, gdy użytkownik nie jest w sieci firmowej. [Bezpieczny dostęp do firmowych aplikacji w chmurze i lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) oraz zachowaj kontrolę dzięki dostępowi warunkowemu.

* Dostęp warunkowy oparty na ryzyku: Chroń dane przed złośliwymi hakerami za [pomocą opartych na ryzyku zasad dostępu warunkowego,](https://www.microsoft.com/cloud-platform/conditional-access) które można zastosować do wszystkich aplikacji i wszystkich użytkowników, zarówno lokalnie, jak i w chmurze.

* Panel dostępu usługi Azure AD: po wdrożeniu usługi serwera proxy aplikacji i bezpiecznie opublikowanych aplikacjach zaoferuj użytkownikom proste centrum do odnajdowania i uzyskiwania dostępu do wszystkich aplikacji. Zwiększ produktywność dzięki funkcjom samoobsługowym, takim jak żądanie dostępu do nowych aplikacji i grup lub zarządzanie dostępem do tych zasobów w imieniu innych osób za pośrednictwem [Panelu dostępu](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Zarządzanie implementacją

### <a name="required-roles"></a>Wymagane role

Firma Microsoft popiera zasadę udzielania jak najmniejszych uprawnień do wykonywania potrzebnych zadań za pomocą usługi Azure AD. [Przejrzyj różne role platformy Azure, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) i wybierz odpowiednią, aby zaspokoić potrzeby każdej persony. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Rola biznesowa| Zadania biznesowe| Role usługi Azure AD |
|---|---|---|
| Administrator działu pomocy technicznej | Zazwyczaj ograniczone do kwalifikujących się użytkowników końcowych zgłaszanych problemów i wykonywania ograniczonych zadań, takich jak zmiana haseł użytkowników, unieważnienie tokenów odświeżania i monitorowanie kondycji usługi. | Administrator działu pomocy technicznej |
| Administrator tożsamości| Przeczytaj raporty logowania usługi Azure AD i dzienniki inspekcji, aby debugować problemy związane z serwerem proxy aplikacji.| Czytelnik zabezpieczeń |
| Właściciel aplikacji| Twórz wszystkie aspekty aplikacji korporacyjnych, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzaj nimi.| Administrator aplikacji |
| Administrator infrastruktury | Właściciel przerzucenia certyfikatu | Administrator aplikacji |

Zminimalizowanie liczby osób, które mają dostęp do bezpiecznych informacji lub zasobów, pomoże zmniejszyć ryzyko uzyskania przez złośliwego aktora nieautoryzowanego dostępu lub nieautoryzowanego użytkownika, który nieumyślnie wpłynie na poufny zasób. 
 
Jednak użytkownicy nadal muszą wykonywać codzienne operacje uprzywilejowane, więc wymuszanie zasad [zarządzania uprzywilejowanymi tożsamościami](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) opartych na czasie (JIT) w celu zapewnienia uprzywilejowanego dostępu na żądanie do zasobów platformy Azure i usługi Azure AD jest naszym zalecanym podejściem do skutecznego zarządzania dostępem administracyjnym i inspekcją.

### <a name="reporting-and-monitoring"></a>Raportowanie i monitorowanie

Usługa Azure AD zapewnia dodatkowe informacje na temat użycia aplikacji i kondycji operacyjnej organizacji za pośrednictwem [dzienników inspekcji i raportów.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Serwer proxy aplikacji ułatwia również monitorowanie łączników z portalu usługi Azure AD i dzienników zdarzeń systemu Windows.

#### <a name="application-audit-logs"></a>Dzienniki inspekcji aplikacji

Dzienniki te zawierają szczegółowe informacje o logowaniach do aplikacji skonfigurowanych za pomocą serwera proxy aplikacji i urządzenia oraz użytkownika uzyskującego dostęp do aplikacji. [Dzienniki inspekcji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) znajdują się w witrynie Azure portal i w [interfejsie API inspekcji](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) do eksportu. Ponadto [raporty użycia i szczegółowych informacji](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) są również dostępne dla aplikacji.

#### <a name="application-proxy-connector-monitoring"></a>Monitorowanie złącza serwera proxy aplikacji

Złącza i usługa zajmują się wszystkimi zadaniami wysokiej dostępności. Stan łączników można monitorować na stronie Serwera proxy aplikacji w portalu usługi Azure AD Portal. Aby uzyskać więcej informacji na temat konserwacji łączników, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Przykład: łączniki serwera proxy aplikacji usługi Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Dzienniki zdarzeń systemu Windows i liczniki wydajności

Łączniki mają dzienniki administracyjne i sesji. Dzienniki administratora zawierają kluczowe zdarzenia i ich błędy. Dzienniki sesji zawierają wszystkie transakcje i ich szczegóły przetwarzania. Dzienniki i liczniki znajdują się w dziennikach zdarzeń systemu Windows, aby uzyskać więcej informacji, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Wykonaj ten [samouczek, aby skonfigurować źródła danych dziennika zdarzeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Przewodnik po rozwiązywaniu problemów i kroki

Dowiedz się więcej o typowych problemach i o tym, jak je rozwiązać, za pomocą naszego przewodnika po [rozwiązywaniu](application-proxy-troubleshoot.md) komunikatów o błędach. 

Poniższe artykuły opisują typowe scenariusze, które mogą być również używane do tworzenia przewodników rozwiązywania problemów dla organizacji pomocy technicznej. 

* [Problem z wyświetlaniem strony aplikacji](application-proxy-page-appearance-broken-problem.md)
* [Ładowanie aplikacji trwa zbyt długo](application-proxy-page-load-speed-problem.md)
* [Linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)
* [Jakie porty należy otworzyć dla mojej aplikacji](application-proxy-connectivity-ports-how-to.md)
* [Brak działającego łącznika w grupie łączników mojej aplikacji](application-proxy-connectivity-no-working-connector.md)
* [Konfigurowanie w portalu administracyjnym](application-proxy-config-how-to.md)
* [Konfigurowanie logowania jednokrotnego w mojej aplikacji](application-proxy-config-sso-how-to.md)
* [Problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)
* [Konfigurowanie ograniczonego delegowania protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurowanie przy użyciu usługi PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)
* [Problem z logowaniem](application-sign-in-problem-on-premises-application-proxy.md)
