---
title: Planowanie wdrożenia serwera Proxy aplikacji usługi Azure Active Directory
description: Przewodnik end-to-end planowania wdrożenia serwera proxy aplikacji w Twojej organizacji
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 04a2e9968e8716818637a34adea86de88e1f848c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388308"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planowanie wdrożenia serwera Proxy aplikacji usługi Azure AD

Serwer Proxy aplikacji usługi Azure Active Directory (Azure AD) to rozwiązanie bezpieczny i ekonomiczny dostęp zdalny do aplikacji lokalnych. Zapewnia ścieżką natychmiastowego przejścia "Chmura First" organizacjom zarządzanie dostępem do starszych lokalnych aplikacji, które nie są jeszcze stanie przy użyciu nowoczesnych protokołów. Aby uzyskać dodatkowe informacje wprowadzające, zobacz [co to jest serwer Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Serwer Proxy aplikacji jest zalecane w przypadku dając użytkownikom zdalnym dostęp do zasobów wewnętrznych. Serwer Proxy aplikacji zastępuje konieczności stosowania sieci VPN i zwrotny serwer proxy dla tych przypadków użycia dostępu zdalnego. Nie jest on przeznaczony dla użytkowników, którzy znajdują się w sieci firmowej. Tych użytkowników, którzy korzystają z serwera Proxy aplikacji w przypadku dostępu z intranetu, mogą wystąpić problemy z wydajnością niepożądane.

Ten artykuł zawiera zasoby, których potrzebujesz do planowania, obsługi i zarządzania serwera Proxy aplikacji usługi Azure AD. 

## <a name="plan-your-implementation"></a>Planowanie implementacji

W poniższej sekcji przedstawiono szeroki obraz klucza planowania elementy, które będą ukierunkowane na środowisko obsługi wydajnych wdrożeń. 

### <a name="prerequisites"></a>Wymagania wstępne

Musisz spełnić następujące wymagania wstępne, przed rozpoczęciem implementacji. Zostaną wyświetlone dodatkowe informacje na temat konfigurowania środowiska, w tym te wymagania wstępne, w tym [samouczek](application-proxy-add-on-premises-application.md).

* **Łączniki**: Łączniki są uproszczone agentów, które można wdrożyć na:
   * Sprzętu fizycznego w środowisku lokalnym
   * Maszyny Wirtualnej hostowanej w dowolnym rozwiązaniu funkcji hypervisor
   * Maszyna wirtualna hostowana na platformie Azure, aby umożliwić połączenie wychodzące do usługi serwera Proxy aplikacji.

* Zobacz [Omówienie usługi Azure AD serwera Proxy aplikacji usługi łączników](application-proxy-connectors.md) dla bardziej szczegółowym omówieniem.

     * Łącznik maszyn muszą [można włączyć dla protokołu TLS 1.2](application-proxy-add-on-premises-application.md) przed zainstalowaniem łączników.

     * Jeśli to możliwe, Wdróż łączników w [tej samej sieci](application-proxy-network-topology.md) i segmentów jako serwery aplikacji sieci web zaplecza. Najlepiej wdrożyć łączników, po zakończeniu odnajdowania aplikacji.
     * Zaleca się, że każda grupa łączników nie ma co najmniej dwa łączniki w celu zapewnienia wysokiej dostępności i skalowalności. Masz trzy łączniki jest optymalne, w przypadku, gdy może być konieczne do obsługi maszyny w dowolnym momencie. Przegląd [tabeli pojemności łącznika](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) pomagające w podjęciu decyzji o jakiego rodzaju maszyny do zainstalowania łączników na. Im większy maszyny więcej buforu i wydajne są łącznika.

* **Ustawienia dostępu do sieci**: Łączników usługi Azure AD Application Proxy [łączenie z platformą Azure za pośrednictwem protokołu TCP Port 443 protokołu HTTPS i HTTP (Port TCP 80)](application-proxy-add-on-premises-application.md). 

   * Trwa przerywanie działania łącznik ruchu TLS nie jest obsługiwana i uniemożliwi łączników ustanowienie bezpiecznego kanału z ich odpowiednich punktów końcowych serwera Proxy aplikacji usługi Azure.

   * Należy unikać wszystkie formularze wbudowanej kontroli komunikacja wychodząca TLS między łączników i platformą Azure. Wewnętrznej kontroli między aplikacjami łącznika i wewnętrznej bazy danych jest możliwe, ale może spowodować pogorszenie środowiska użytkownika, a jako takie nie jest zalecane.

   * Równoważenie obciążenia sieciowego z łączników, samodzielnie również nie jest obsługiwana, lub nawet niezbędne.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Istotne zagadnienia przed skonfigurowaniem serwera Proxy aplikacji usługi Azure AD

Aby skonfigurować i wdrożyć serwer Proxy aplikacji usługi Azure AD, muszą być spełnione następujące wymagania podstawowe.

*  **Dołączanie do usługi Azure**: Przed wdrożeniem serwera proxy aplikacji, tożsamości użytkowników musi być synchronizowane z katalogu lokalnego lub utworzonych bezpośrednio w dzierżawcy usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD do wstępnie uwierzytelnia użytkowników, zanim nadawania im praw do serwera Proxy aplikacji opublikowane aplikacje i udostępniają informacje identyfikator użytkownika wymagane do wykonywania logowania jednokrotnego (SSO).

* **Wymagania dotyczące dostępu warunkowego**: Firma Microsoft nie zaleca się przy użyciu serwera Proxy aplikacji w przypadku dostępu z intranetu, ponieważ spowoduje to dodanie opóźnienia, które mają wpływ na użytkowników. Firma Microsoft zaleca używanie serwera Proxy aplikacji przy użyciu wstępnego uwierzytelniania i warunkowego dostępu do zasad dostępu zdalnego z Internetu.  Podejście w celu zapewnienia, że dostęp warunkowy do użytku w sieci intranet, modernizuj aplikacje, dzięki czemu mogą oni diretly uwierzytelnianie za pomocą usługi AAD. Zapoznaj się [zasoby dotyczące migrowania aplikacji do usługi AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) Aby uzyskać więcej informacji. 

* **Limitów usług**: Aby zapewnić ochronę przed nadmierne zużycie zasobów przez poszczególne dzierżawy są limity ograniczania ustawiana dla poszczególnych aplikacji i dzierżawy. Aby zobaczyć te limity dotyczą [usługi Azure AD, ograniczenia i limity](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Te limity ograniczania przepływności opierają się na ekstrapolacji znacznie powyżej typowy woluminu i udostępnia bufor wystarczającą dla większości wdrożeń.

* **Certyfikat publiczny**: Jeśli używasz niestandardowych nazw domen muszą nabywania publicznego certyfikatu wystawionego przez firmy innej niż Microsoft zaufanego urzędu certyfikacji. W zależności od wymagań organizacji uzyskiwanie certyfikatu może zająć trochę czasu, a firma Microsoft zaleca, począwszy od procesu, tak szybko, jak to możliwe. Serwer Proxy aplikacji platformy Azure obsługuje standard, [symboli wieloznacznych](application-proxy-wildcard.md), lub certyfikatów opartych na sieci SAN.

* **Wymagania dotyczące domeny**: Logowanie jednokrotne do aplikacji opublikowanych przy użyciu delegowanie ograniczone protokołu Kerberos (KCD) wymaga hosta łącznika przyłączone do tej samej domeny usługi AD jako aplikacje, trwa publikowanie. Aby uzyskać szczegółowe informacje na temat, zobacz [ograniczonego delegowania protokołu Kerberos do logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md) przy użyciu serwera Proxy aplikacji. Usługa łącznika jest uruchamiany w kontekście systemu lokalnego i nie powinno być skonfigurowane do korzystania z tożsamości niestandardowej.

* **Rekordy DNS dla adresu URL**

   * Przed rozpoczęciem korzystania z niestandardowej domeny na serwerze Proxy aplikacji należy utworzyć rekord CNAME w publicznym systemie DNS, umożliwiając klientom rozpoznawanie niestandardowe zdefiniowane zewnętrzny adres URL do wstępnie zdefiniowanych adresu serwera Proxy aplikacji. Kończy się niepowodzeniem utworzyć rekord CNAME dla aplikacji, która używa domenę niestandardową uniemożliwi użytkownikom zdalne łączenie z aplikacją. Dowiedz się kroki wymagane do dodania, rekordy CNAME mogą się różnić od DNS od dostawcy, jak [zarządzać rekordami i zestawami rekordów DNS przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Podobnie hosty łącznika musi mieć możliwość rozpoznania wewnętrzny adres URL opublikowanej aplikacji.

* **Prawa administracyjne i ról**

   * **Instalacja łącznika** wymaga uprawnień administratora lokalnego na serwerze Windows, który jest instalowany na. Także wymaga co najmniej *Administrator aplikacji* roli w celu uwierzytelniania i zarejestrować wystąpienie łącznika dla Twojej dzierżawy usługi Azure AD. 

   * **Publikowanie aplikacji i administrowanie** wymagają *Administrator aplikacji* roli. Aplikacja Administratorzy mogą zarządzać wszystkich aplikacji w katalogu, w tym rejestracji, ustawień logowania jednokrotnego, użytkownika i przypisania grupy i licencjonowania, ustawienia serwera Proxy aplikacji i zgody. Go nie są przyznawane możliwość zarządzania dostępem warunkowym. *Administrator aplikacji w chmurze* rola ma wszystkie możliwości programu Administrator aplikacji z tą różnicą, że nie zezwala na zarządzanie ustawieniami serwera Proxy aplikacji.

* **Licencjonowanie**: Serwer Proxy aplikacji jest dostępna w ramach subskrypcji usługi Azure AD podstawowa. Zapoznaj się [usługi Azure Active Directory stronie cennika usługi](https://azure.microsoft.com/pricing/details/active-directory/) pełną listę licencjonowania opcje i funkcje.  

### <a name="application-discovery"></a>Odnajdywanie aplikacji

Skompilować spis wszystkich aplikacji w zakresie, które są publikowane za pośrednictwem serwera Proxy aplikacji wymaga to zebrania następujących informacji:

| Typ informacji| Informacji do zebrania |
|---|---|
| Typ usługi| Na przykład: Program SharePoint, SAP, CRM, niestandardowej aplikacji sieci Web, interfejsów API |
| Platforma aplikacji | Na przykład: Windows usług IIS, Apache dla systemu Linux, Tomcat, serwera NGINX |
| Członkostwo w domenie| Serwer sieci Web w pełni kwalifikowaną nazwę domeny (FQDN) |
| Lokalizacja aplikacji | Gdzie znajduje się serwer sieci web lub farmy w infrastrukturze |
| Dostęp do | Adres URL używany podczas uzyskiwania dostępu do aplikacji wewnętrznie. <br> Jeśli farma, jakiego rodzaju równoważenia obciążenia jest używana? <br> Czy aplikacja rysuje zawartość ze źródeł innych niż sam.<br> Określ, jeśli aplikacja działa przez protokół WebSockets. |
| Dostęp zewnętrzny | Rozwiązania dostawcy aplikacji już udostępniany zewnętrznie. <br> Adres URL, którego chcesz użyć na potrzeby dostępu zewnętrznego. Jeśli program SharePoint, upewnij się, są konfigurowane dla mapowania dostępu alternatywnego poszczególnych [Niniejsze wskazówki](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). W przeciwnym razie trzeba będzie określić zewnętrzne adresy URL. |
| Certyfikat publiczny | Jeśli używasz domeny niestandardowej, nabywania certyfikatów przy użyciu odpowiedniej nazwy podmiotu. Jeśli istnieje certyfikatu należy pamiętać, numer seryjny i lokalizację, z którym można uzyskać. |
| Typ uwierzytelniania| Typ uwierzytelniania obsługiwany przez obsługę aplikacji, np. Basic, Windows integracja uwierzytelniania opartego na formularzach, opartej na nagłówkach i oświadczeń. <br>Jeśli aplikacja jest skonfigurowana do uruchamiania w ramach konta domeny, należy pamiętać, w pełni kwalifikowanej domeny nazwę (FQDN) konta usługi.<br> Jeśli opartej na SAML, adresy URL identyfikatora i odpowiedzi. <br> Opartej na nagłówkach, typ dostawcy rozwiązania i określone wymagania dotyczące obsługi uwierzytelniania. |
| Nazwa grupy łączników | Nazwa logiczna grupa łączników, które będą oznaczone w celu zapewnienia kanał i logowania jednokrotnego do tej aplikacji zaplecza. |
| Dostęp użytkowników/grup | Użytkownicy lub grupy użytkowników, które będą mieć dostępu do aplikacji zewnętrznych. |
| Wymagania dodatkowe | Należy pamiętać, wszelkie dodatkowe dostępu zdalnego lub wymagań dotyczących zabezpieczeń, które powinny być umieszczane w publikowania aplikacji. |

Możesz pobrać ten [spisu aplikacji w arkuszu kalkulacyjnym](https://aka.ms/appdiscovery) do spisu aplikacji.

### <a name="define-organizational-requirements"></a>Definiowanie wymagań organizacyjnych

Dostępne są następujące obszary, dla których należy zdefiniować wymagania biznesowe Twojej organizacji. Każdy obszar zawiera przykładowe wymagania

 **Dostęp**

* Użytkownicy zdalni, za pomocą przyłączonych do domeny lub użytkownicy urządzeń przyłączonych do usługi Azure AD można korzystać z opublikowanych aplikacji bezpiecznego za pomocą bezproblemowego logowania jednokrotnego (SSO).

* Użytkownikom zdalnym zatwierdzonych urządzeń osobistych można bezpiecznie korzystać z opublikowanych aplikacji, ile są zarejestrowane w usłudze MFA i zarejestrowano aplikację Microsoft Authenticator na telefon komórkowy jako metodę uwierzytelniania.

**Nadzoru** 

* Administratorzy mogą określać i monitorować cyklu życia przypisania użytkownika do aplikacji opublikowanych przez serwer Proxy aplikacji.

**Zabezpieczenia**

* Tylko użytkowników przypisanych do aplikacji za pośrednictwem członkostwa w grupie lub indywidualnie mogą uzyskiwać dostęp do tych aplikacji.

**Wydajność**

* Brak bez spadku wydajności aplikacji, w porównaniu do uzyskiwania dostępu do aplikacji z siecią wewnętrzną.

**Środowisko użytkownika**

* Użytkownicy zapoznali się jak uzyskują dostęp do aplikacji przy użyciu adresów URL znanych firmy na wszystkich platformach urządzeń.

**Inspekcja**
* Administratorzy będą mogli przeprowadzanie inspekcji aktywności dostępu użytkowników.


### <a name="best-practices-for-a-pilot"></a>Najlepsze rozwiązania dotyczące pilotażu

Określ ilość czasu i nakład pracy wymagany do w pełni Komisji pojedynczej aplikacji dostępu zdalnego przy użyciu logowania jednokrotnego (SSO). W tym celu programu pilotażowego, który traktuje jego początkowego odnajdywania, publikowanie i testy ogólne. Za pomocą aplikacji proste oparty na usługach sieci web, która jest już wstępnie skonfigurowanego dla zintegrowanego uwierzytelniania Windows (IWA) może pomóc ustalić punkt odniesienia, ponieważ ten Instalator wymaga minimalnym nakładzie pracy pomyślnie pilotażowego dostępu zdalnego i logowania jednokrotnego.

Następujące elementy projektu, należy zwiększyć Powodzenie wdrożenia pilotażowego bezpośrednio w dzierżawie produkcji.  

**Łącznik zarządzania**:  

* Łączniki odgrywają kluczową rolę w dostarczaniu kanał w środowisku lokalnym, do aplikacji. Za pomocą **domyślne** grupy łączników jest odpowiednia dla początkowej pilotażowego testowanie opublikowanych aplikacji przed oddanie je do środowiska produkcyjnego. Następnie można przenieść pomyślnie przetestowane aplikacji do grupy łączników w środowisku produkcyjnym.

**Zarządzanie aplikacjami**:

* Pracownikom najprawdopodobniej należy pamiętać, że zewnętrzny adres URL jest znany i istotne. Unikaj publikowania aplikacji za pomocą naszych predefiniowanych sufiksy msappproxy.net lub onmicrosoft.com. Zamiast tego Podaj znanych najwyższego poziomu domenę zweryfikowaną, takie jak prefiks logicznej nazwy hosta *intranetu. < customers_domain > .com*.

* Ograniczanie widoczności ikony aplikacji pilotażowego w grupie pilotażowej przez ukrycie formularza ikonę jego uruchomienie portalu MyApps platformy Azure. Gdy wszystko będzie gotowe do produkcji, możesz ograniczyć aplikację do jego odpowiednich docelowych odbiorców w tej samej dzierżawie produkcji wstępnej lub przez także publikowanie aplikacji w ramach dzierżawy w środowisku produkcyjnym.

**Pojedynczy ustawień logowania jednokrotnego**: Niektóre ustawienia logowania jednokrotnego mają szczególne współzależności, które może trwać, aby skonfigurować, dlatego należy unikać Zmień kontrolę, opóźnienia, zapewniając zależności są kierowane wcześniej. W tym domeny, dołączenie do hostów łącznika do wykonywania logowania jednokrotnego przy użyciu delegowanie ograniczone protokołu Kerberos (KCD) i zwracając szczególną uwagę innych działań czasochłonne. Na przykład konfigurowanie wystąpienia dostępu usługi PING, jeśli wymagające opartej na nagłówkach logowania jednokrotnego.

**Protokół SSL między hostem łącznika, a aplikacja docelowa**: Zabezpieczenia są kluczowymi czynnikami, więc zawsze należy używać protokołu TLS między aplikacjami źródłowa i docelowa łącznika. Szczególnie w przypadku, gdy aplikacja sieci web jest skonfigurowana do uwierzytelniania opartego na formularzach (formularzach), jako użytkownik są następnie efektywnie przekazywane poświadczenia w postaci zwykłego tekstu.

**Stopniowo zaimplementować i przetestować każdego kroku**. Należy przeprowadzić, podstawowe testy funkcjonalne po opublikowaniu aplikacji, aby upewnić się, że spełniono wszystkich użytkowników i wymaganiami biznesowymi, wykonując czynności opisane poniżej:

1. Testowanie i weryfikowanie ogólnego dostępu do aplikacji sieci web przy użyciu wstępnego uwierzytelniania wyłączone.
2. Jeśli to się powiedzie, Włącz wstępnego uwierzytelniania i Przypisz użytkowników i grup. Testowanie i weryfikowanie dostępu.
3. Następnie dodaj metodę logowania jednokrotnego dla aplikacji i ponownie przetestuj można zweryfikować dostępu.
4. Zastosowanie dostępu warunkowego i zasad MFA zgodnie z potrzebami. Testowanie i weryfikowanie dostępu.

**Narzędzia do rozwiązywania problemów**: Podczas rozwiązywania problemów, zawsze należy zacząć od sprawdzania dostępu do opublikowanych aplikacji w przeglądarce na hoście łącznika i upewnij się, że aplikacja działa zgodnie z oczekiwaniami. Prostsze konfigurację, łatwiej, aby ustalić przyczynę, dlatego należy rozważyć próby odtworzenia problemów z minimalną konfiguracją, takiej jak pojedynczy łącznik i brak logowania jednokrotnego. W niektórych przypadkach debugowania narzędzi, takich jak narzędzie firmy Telerik Fiddler w sieci web można udowodnić, że niezbędne do rozwiązywania problemów dostępu lub zawartości w aplikacjach, dostępne za pośrednictwem serwera proxy. Narzędzie fiddler, mogą również działać jako serwer proxy, aby ułatwić śledzenie i debugowanie ruchu dla platform urządzeń przenośnych, takich jak systemy iOS i Android oraz praktycznie wszystko, można skonfigurować w przypadku kierowania za pośrednictwem serwera proxy. Zobacz [przewodnik rozwiązywania problemów z](application-proxy-troubleshoot.md) Aby uzyskać więcej informacji.

## <a name="implement-your-solution"></a>Implementowania rozwiązania

### <a name="deploy-application-proxy"></a>Wdrażanie serwera Proxy aplikacji

Kroki, aby wdrożyć serwer Proxy aplikacji zostały omówione w tym [samouczek dotyczący dodawania aplikacji w środowisku lokalnym dostępu zdalnego](application-proxy-add-on-premises-application.md). Jeśli instalacja nie jest pomyślne, wybierz **Rozwiązywanie problemów z serwera Proxy aplikacji** w portalu lub użyj przewodnik rozwiązywania problemów [występują problemy z instalacją łącznika agenta serwera Proxy aplikacji](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikowanie aplikacji za pośrednictwem serwera Proxy aplikacji

Publikowanie aplikacji zakłada, że zostały spełnione wszystkie wymagania wstępne i ma kilka łączników wyświetlane jako zarejestrowane i aktywne na stronie serwer Proxy aplikacji.

Można również publikować aplikacje za pomocą [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Poniżej przedstawiono kilka najlepszych zasad, które należy wykonać podczas publikowania aplikacji:

* **Za pomocą grup łączników**: Przypisz grupy łączników, które zostały wyznaczone do publikowania każdej odpowiedniej aplikacji. Zaleca się, że każda grupa łączników nie ma co najmniej dwa łączniki w celu zapewnienia wysokiej dostępności i skalowalności. Masz trzy łączniki jest optymalne, w przypadku, gdy może być konieczne do obsługi maszyny w dowolnym momencie. Ponadto zobacz [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md) aby zobaczyć, jak również można użyć grupy łączników na segmenty łączników, sieci lub lokalizacji.

* **Ustaw limit czasu aplikacji zaplecza**: To ustawienie jest przydatne w scenariuszach, w których aplikacja może wymagać więcej niż 75 sekund, aby przetworzyć transakcji klienta. Na przykład gdy klient wysyła zapytanie do aplikacji sieci web, działa jako fronton do bazy danych. Frontonu wysyła to zapytanie do serwera wewnętrznej bazy danych i czeka na odpowiedź, ale według czasu odbierze odpowiedź, limit czasu po stronie klienta konwersacji. Ustawienie limitu czasu na długo udostępnia 180 sekund dłużej transakcji.

* **Używanie typów odpowiedniego pliku Cookie**

   * **Plik Cookie tylko HTTP**: Zapewnia dodatkowe zabezpieczenia, konfigurując serwer Proxy aplikacji objęte flagi HTTPOnly nagłówków odpowiedzi HTTP set-cookie. To ustawienie pozwala, aby uniknąć zagrożenia, takie jak skryptów między witrynami (XSS). Pozostaw ten zestaw nie agentów klientów/użytkowników, które wymagają dostępu do pliku cookie sesji. Na przykład łączenie do bramy usług pulpitu zdalnego klienta RDP/MTSC opublikowane za pośrednictwem serwera Proxy aplikacji.

   * **Bezpieczny plik Cookie**: Jeśli plik cookie jest ustawiony atrybut Secure, agent użytkownika (aplikacji po stronie klienta) będzie zawierał tylko pliki cookie w żądaniach HTTP Jeśli żądania są przesyłane za pośrednictwem kanału zabezpieczonego protokołu TLS. Dzięki temu można ograniczyć ryzyko naruszenia zabezpieczeń za pośrednictwem kanałów w postaci zwykłego tekstu, więc powinno być włączone pliku cookie.

   * **Trwały plik Cookie**: Umożliwia pliku cookie sesji serwera Proxy aplikacji zachować między zamknięcia przeglądarki przez pozostałe prawidłowe, aż do jego wygaśnięcia lub został usunięty. Używany w scenariuszach, w którym rozbudowanych aplikacji, takich jak office uzyskuje dostęp do dokumentu w opublikowanej aplikacji sieci web, bez ponownego monitowanie użytkownika o uwierzytelnienie. Włącz ostrożnie, jako trwałe pliki cookie ostatecznie pozostawić usługi zagrożone przed nieautoryzowanym dostępem, jeśli nie jest używany w połączeniu z innymi formantami wyrównującej. Tego ustawienia należy używać tylko dla starszych aplikacji, które nie mogą udostępniać pliki cookie między procesami. Zaleca się zaktualizować aplikację w celu obsługi udostępniania plików cookie między procesami, a nie przy użyciu tego ustawienia.

* **Przekształć adresy URL w nagłówkach**: Możesz włączyć tę opcję dla scenariuszy, w którym wewnętrznego serwera DNS nie można skonfigurować w taki sposób, aby dopasować publicznej przestrzeni nazw w organizacji (przez podział DNS). Jeśli aplikacja wymaga oryginalnego nagłówka hosta w żądaniu klienta, należy pozostawić wartość Yes (tak). Alternatywą jest łącznik, użyj nazwy FQDN w adresie URL, wewnętrznej routingu ruchu rzeczywiste i nazwy FQDN w zewnętrzny adres URL jako nagłówek hosta. W większości przypadków ta alternatywa powinien zezwalać na działanie aplikacji w zwykły sposób podczas uzyskiwania dostępu do zdalnego, ale użytkownicy utracą korzyści pasujący obiekt typu wewnątrz & poza adresu URL.

* **Przekształć adresy URL w treści aplikacji**: Jeśli chcesz, aby linki z aplikacji do tłumaczenia w odpowiedzi do klienta, należy włączyć funkcję tłumaczenia łącze treść aplikacji dla aplikacji. Jeśli włączona, ta funkcja udostępnia Najlepsza próba nakładu pracy na tłumaczenia wszystkich łączy wewnętrznych, które serwera Proxy aplikacji znajduje się w odpowiedzi HTML i CSS, są zwracane do klientów. Może to być przydatne, gdy aplikacje, które zawierają ustaloną bezwzględne lub NetBIOS shortname łącza w zawartości, czy aplikacje z zawartością, który stanowi łącze do innych aplikacji lokalnych.

W przypadku scenariuszy, w którym łącza opublikowanej aplikacji, do innych aplikacji opublikowanych należy włączyć translację link dla każdej aplikacji, tak, aby mieć kontrolę nad środowiskiem użytkownika na poziomie poszczególnych aplikacji.

Na przykład załóżmy, że masz trzy aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji, że wszystkie łączą się wzajemnie: Korzyści, koszty i podróży, a także aplikacji czwarty opinię, która nie jest opublikowana przy użyciu serwera Proxy aplikacji.

![Obraz 1](media/App-proxy-deployment-plan/link-translation.png)

Po włączeniu tłumaczenia link aplikacji korzyści, łącza do kosztów i podróże są przekierowywane do zewnętrznych adresów URL dla tych aplikacji tak, aby użytkownicy uzyskujący dostęp do aplikacji z spoza sieci firmowej można uzyskiwać do nich dostęp. Linki z wydatków i podróży do korzyści nie działają, ponieważ link tłumaczenia nie została włączona dla tych dwóch aplikacji. Link do opinii nie jest przekierowanie, ponieważ nie istnieje żadne zewnętrzny adres URL, więc użytkowników za pomocą aplikacji korzyści, nie będzie można uzyskać dostęp do aplikacji opinii z spoza sieci firmowej. Wyświetlić szczegółowe informacje dotyczące [link tłumaczenia i inne opcje przekierowania](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Dostęp do aplikacji

Istnieje kilka opcji w celu zarządzania dostępem do serwera Proxy aplikacji opublikowanych zasobów, więc wybrać najbardziej odpowiednią do potrzeb danego scenariusza i skalowalności. Typowe metody wdrożenia to: przy użyciu grup lokalnych, synchronizowane za pomocą usługi Azure AD Connect, tworzenia grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika, za pomocą grup samoobsługi, które są zarządzane przez właściciela zasobu lub kombinację wszystkich tych. Zobacz połączonych zasobów do korzyści z każdego z nich.

Najbardziej proste sposób przypisywania użytkownikom dostępu do aplikacji jest przesyłane do **użytkowników i grup** opcje w okienku po lewej stronie opublikowanej aplikacji i bezpośrednie przypisywanie grup lub użytkowników indywidualnych.

![Obraz 24](media/App-proxy-deployment-plan/add-user.png)

Można również zezwolić użytkownikom na dostęp do samoobsługi do aplikacji, Przypisywanie grupy, która obecnie nie jest członkiem oraz konfigurowanie opcji samodzielnego.

![Obraz 25](media/App-proxy-deployment-plan/allow-access.png)

Jeśli włączona, użytkownicy będą w stanie zalogować się do portalu i żądać dostępu MyApps i mieć automatycznie zatwierdzona i dodana do zezwolonej wcześniej grupami samoobsługi lub wymagające zatwierdzenia z wyznaczonej osoby zatwierdzającej.

Użytkownicy-goście, może być również [zaproszeni do wewnętrzne aplikacje publikowane za pośrednictwem serwera Proxy aplikacji za pomocą usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Dla aplikacji lokalnych, które są zwykle dostępne anonimowo wymagające bez uwierzytelniania warto wyłączyć opcję znajduje się w aplikacji **właściwości**.

![Obraz 26](media/App-proxy-deployment-plan/assignment-required.png)


Brak zaznaczenia tej opcji wartość nie zezwala użytkownikom na dostęp do aplikacji w środowisku lokalnym za pośrednictwem serwera Proxy aplikacji usługi Azure AD bez uprawnień, dlatego należy używać ostrożnie.

Po opublikowaniu aplikacji powinna być dostępna, wpisując jego zewnętrzny adres URL w przeglądarce lub przez jego ikonę w [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Włącz uwierzytelnianie wstępne

Sprawdź, czy aplikacja jest dostępna za pośrednictwem serwera Proxy aplikacji, uzyskiwanie do niej dostępu za pomocą zewnętrznego adresu URL. 

1. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** i wybierz aplikację, którą chcesz zarządzać.

2. Wybierz **serwera Proxy aplikacji**.

3. W **wstępnego uwierzytelniania** pola, użyj listy rozwijanej, aby wybrać **usługi Azure Active Directory**i wybierz **Zapisz**.

Przy użyciu wstępnego uwierzytelniania są włączone usługi Azure AD zażąda użytkowników najpierw do uwierzytelniania, a jeśli logowanie jednokrotne jest configued aplikacji zaplecza będzie także Sprawdź użytkownika przed uzyskaniem dostępu do aplikacji. Zmienianie trybu uwierzytelniania wstępnego z przekazywanie do usługi Azure AD konfiguruje również zewnętrzny adres URL przy użyciu protokołu HTTPS, aby każda aplikacja, wstępnie skonfigurowane dla protokołu HTTP zostanie teraz zabezpieczone przy użyciu protokołu HTTPS.

### <a name="enable-single-sign-on"></a>Włącz logowanie jednokrotne

Logowanie Jednokrotne zapewnia najlepsze możliwe środowisko korzystania i bezpieczeństwa, ponieważ użytkownicy muszą tylko zalogować jeden raz podczas uzyskiwania dostępu do usługi Azure AD. Po użytkownik dokonał uwierzytelnienia wstępnego, logowanie Jednokrotne jest wykonywane przez łącznik serwera Proxy aplikacji uwierzytelniania do aplikacji w środowisku lokalnym, w imieniu użytkownika. Aplikacja wewnętrznej bazy danych przetwarza dane logowania, tak jakby użytkownik samodzielnie. 

Wybieranie **przekazywanie** opcja umożliwia użytkownikom dostęp do opublikowanej aplikacji bez konieczności uwierzytelniania w usłudze Azure AD.

Wykonywanie logowania jednokrotnego jest możliwe tylko w przypadku usługi Azure AD można zidentyfikować użytkownika żądającego dostępu do zasobów, dzięki czemu aplikacja musi być skonfigurowana do wstępnego uwierzytelniania użytkowników za pomocą usługi Azure AD na dostęp do logowania jednokrotnego do funkcji, w przeciwnym razie opcje logowania jednokrotnego zostanie wyłączona.

Odczyt [logowanie jednokrotne do aplikacji w usłudze Azure AD](what-is-single-sign-on.md) ułatwiające wybór najbardziej odpowiedniej metody logowania jednokrotnego, podczas konfigurowania aplikacji.

###  <a name="working-with-other-types-of-applications"></a>Praca z innych typów aplikacji

Serwer Proxy aplikacji usługi Azure AD może także obsługiwać aplikacje, które zostały opracowane w celu użycia naszych Azure AD Authentication Library ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) lub biblioteka Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Obsługuje ona aplikacje klienta natywnego za korzystanie z usługi Azure AD wystawionych tokenów trafiła do informacji w nagłówku żądania klienta do uwierzytelniania wstępnego w imieniu użytkowników.

Odczyt [publikowania aplikacji klienta natywnego i na urządzeniach przenośnych](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) i [aplikacji opartej na oświadczeniach](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) Aby dowiedzieć się więcej o dostępnych konfiguracji serwera Proxy aplikacji.

### <a name="use-conditional-access-to-strengthen-security"></a>Korzystanie z dostępu warunkowego do zwiększenia poziomu zabezpieczeń

Zabezpieczenia aplikacji wymaga zaawansowanego zestawu funkcji zabezpieczeń, które można chronić przed odpowiadać złożone zagrożenia w środowisku lokalnym i w chmurze. Osoby atakujące często uzyskać dostępu do sieci firmowej za pośrednictwem słabych, domyślnych lub skradzionych poświadczeń użytkowników.  Zabezpieczenia oparte na tożsamościach firmy Microsoft zmniejsza korzystanie ze skradzionych poświadczeń dzięki ochronie uprzywilejowanego i nieuprzywilejowanego tożsamości i zarządzanie nimi.

Następujące funkcje może służyć do obsługi serwera Proxy aplikacji usługi Azure AD:

* I dostępu warunkowego na podstawie lokalizacji użytkownika: Utrzymywanie poufnych danych chronionych przez ograniczenie dostępu użytkowników na podstawie lokalizacji geograficznej lub adres IP o [zasady dostępu warunkowego na podstawie lokalizacji](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Dostęp warunkowy oparty na urządzeniu: Upewnij się, tylko zarejestrowane, zatwierdzone i zgodne urządzenia mają dostęp do danych firmowych za pomocą [dostępu warunkowego opartego na urządzeniu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Dostęp warunkowy oparty na aplikacji: Pracy nie ma na celu zatrzymywania po użytkownik nie znajduje się w sieci firmowej. [Bezpieczny dostęp do firmowych aplikacji w chmurze i lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) i zachowywania kontroli przy użyciu dostępu warunkowego.

* Dostęp warunkowy na podstawie ryzyka: Ochrona danych przed złośliwych hakerów [zasad dostępu warunkowego na podstawie ryzyka](https://www.microsoft.com/cloud-platform/conditional-access) , można zastosować do wszystkich aplikacji i wszystkich użytkowników, czy w środowisku lokalnym lub w chmurze.

* Panel dostępu usługi Azure AD: Z usługi Serwer Proxy aplikacji wdrożonych i aplikacjach opublikowanych w bezpieczny oferują użytkownikom proste koncentratora do odnajdywania i uzyskują dostęp do wszystkich aplikacji. Zwiększanie produktywności za pomocą usługi możliwości samoobsługi, takie jak możliwość żądania dostępu do nowych aplikacji i grup lub zarządzania dostęp do tych zasobów w imieniu innych użytkowników, za pomocą [panelu dostępu](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Zarządzać swoją implementacją

### <a name="required-roles"></a>Wymagane role

Microsoft funkcję specjalistów zasady udzielania najniższe możliwe uprawnienia, aby wykonać niezbędne zadania z usługą Azure AD. [Przejrzyj różne role platformy Azure, które są dostępne](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) i wybrać właściwy, aby zaspokoić potrzeby każda osoba. Niektóre role może być konieczne może być stosowane tymczasowo i usunięte po ukończeniu wdrażania.

| Roli biznesowej| Zadania biznesowe| Role usługi Azure AD |
|---|---|---|
| Pomoc techniczna administratora | Zazwyczaj ograniczone do kwalifikujących się użytkownika końcowego zgłaszanych problemów i wykonywania tylko określone zadania, takie jak zmienianie haseł użytkowników, co unieważniło tokeny odświeżania i monitorowania kondycji usługi. | Administrator pomocy technicznej |
| Administrator tożsamości| Problemy związane z odczytu AD logowanie do platformy Azure raporty i dzienników inspekcji można debugować serwera Proxy aplikacji.| Czytelnik zabezpieczeń |
| Właściciel aplikacji| Utwórz i Zarządzaj wszystkimi aspektami aplikacje dla przedsiębiorstw i rejestracje aplikacji, ustawienia serwera proxy aplikacji.| Administrator aplikacji |
| Administrator infrastruktury | Certyfikat przerzucania właściciela | Administrator aplikacji |

Minimalizacja liczby osób, które mają dostęp do zabezpieczania informacji lub zasobów zapewni pomoc przy określaniu zmniejszyć ryzyko złośliwych aktora, uzyskania nieautoryzowanego dostępu lub autoryzowany użytkownik przypadkowo wpływające na ochronę poufnych zasobów. 
 
Jednak użytkownicy nadal muszą wykonywać operacje uprzywilejowane codziennie, dlatego wymuszanie just-in-time (JIT) na podstawie [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) zasad w celu zapewnienia na żądanie uprzywilejowanego dostępu do zasobów platformy Azure i usługa Azure AD jest naszym zalecane podejście do efektywnego zarządzania dostępem administracyjnym i inspekcji.

### <a name="reporting-and-monitoring"></a>Monitorowanie i raportowanie

Usługi Azure AD może zapewnić dodatkowe szczegółowe informacje dotyczące przypisywania użycia i kondycji operacyjnej za pomocą dzienników inspekcji i raporty użytkowników w organizacji. 

#### <a name="application-audit-logs"></a>Dzienniki inspekcji aplikacji

Te dzienniki zawierają szczegółowe informacje na temat logowania się do aplikacji skonfigurowanych przy użyciu serwera Proxy aplikacji usługi i urządzenia i użytkowników uzyskujących dostęp do aplikacji. Dzienniki inspekcji znajdują się w witrynie Azure portal i interfejs API inspekcji dla eksportu.

#### <a name="windows-event-logs-and-performance-counters"></a>Windows, dzienniki zdarzeń i liczniki wydajności

Łączniki mają zarówno administratora, jak i sesji dzienniki. Dzienniki administracyjne obejmują kluczowych zdarzeń i ich błędy. Dzienniki sesji obejmują wszystkie transakcje i ich szczegóły przetwarzania. Dzienniki i liczniki znajdują się w dziennikach zdarzeń Windows i postępuj zgodnie z tym [samouczka, aby skonfigurować źródła danych dziennika zdarzeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Przewodnik rozwiązywania problemów oraz kroki

Dowiedz się więcej na temat typowych problemów i ich rozwiązania przy użyciu zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](application-proxy-troubleshoot.md) komunikaty o błędach. 

Następujące artykuły obejmują typowe scenariusze, które mogą również służyć do tworzenia przewodniki dotyczące rozwiązywania problemów dla Twojej organizacji pomocy technicznej. 

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
* [Nie można uzyskać dostępu to błąd aplikacji firmowych](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem z instalacją łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)
* [Problem podczas logowania](application-sign-in-problem-on-premises-application-proxy.md)
