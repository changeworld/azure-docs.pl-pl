---
title: Publikowanie aplikacji w środowisku lokalnym przy użyciu serwera Proxy aplikacji usługi Azure AD
description: Dowiedz się, dlaczego publikowanie lokalnych aplikacji sieci web zewnętrznie użytkowników zdalnych przy użyciu serwera Proxy aplikacji. Więcej informacji na temat architektury serwera Proxy aplikacji, łączniki, metody uwierzytelniania i korzyści w zakresie zabezpieczeń.
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
ms.openlocfilehash: 66403a18be8337939d457c061b07de948c3e34e8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730875"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Przy użyciu serwera Proxy aplikacji usługi Azure AD do publikowania lokalnych aplikacji dla użytkowników zdalnych

Azure Active Directory (Azure AD) oferuje wiele możliwości w zakresie ochrony użytkowników, aplikacji i danych w chmurze i lokalnych. W szczególności funkcji serwera Proxy aplikacji usługi Azure AD może być implementowany przez specjalistów IT, którzy mają zostać opublikowane aplikacje sieci web w środowisku lokalnym zewnętrznie. Użytkowników zdalnych, którzy muszą mieć dostęp do wewnętrznych aplikacji może następnie uzyskiwać do nich dostęp w bezpieczny sposób.

Możliwość bezpiecznego dostępu do aplikacji wewnętrznych z spoza sieci staje się bardziej krytyczne w nowoczesnych miejscach pracy. Przy użyciu scenariuszy, takich jak BYOD (Przynieś własne urządzenie) i urządzeniami przenośnymi informatyków stoją w dwóch celów:

* Zwiększenie możliwości dostępnych dla użytkowników końcowych do wydajnej dowolnym czasie i z dowolnego miejsca
* Ochrona zasobów firmy przez cały czas

W wielu organizacjach zdaniem są w kontrolce i chronione w sytuacji, gdy istnieją zasoby w granicach sieci firmowej. Ale w miejscu pracy cyfrowego dzisiejsza, rozwinął tę granicę z zarządzanych urządzeń przenośnych, zasobów i usług w chmurze. Teraz musisz zarządzanie złożonością ochrony tożsamości użytkownikach i danych przechowywanych na ich urządzeniami i aplikacjami.

Być może już używasz usługi Azure AD do zarządzania użytkownikami w chmurze, którzy chcą korzystać z usługi Office 365 i innych aplikacji SaaS, a także sieci web aplikacji hostowanych lokalnie. Jeśli masz już usługę Azure AD, jej wykorzystania jako jeden formant płaszczyzny umożliwia bezproblemową i bezpieczną dostęp do aplikacji w środowisku lokalnym. Możesz też może nadal masz rozważaniu przejście do chmury. Jeśli tak, możesz rozpocząć swoją podróż do chmury przez implementację serwera Proxy aplikacji i biorąc pierwszy krok w kierunku tworzenia foundation silna tożsamość.

Podczas, gdy nie są pełne, Poniższa lista przedstawia niektóre rzeczy, które można włączyć poprzez implementację serwera Proxy aplikacji w scenariuszu hybrydowym współistnienie:

* Publikowanie aplikacji sieci web w środowisku lokalnym zewnętrznie w uproszczony sposób bez sieci Obwodowej
* Obsługa logowania jednokrotnego (SSO) w wielu urządzeń, zasobów i aplikacji w chmurze i lokalnych
* Obsługa uwierzytelniania wieloskładnikowego w przypadku aplikacji w chmurze i lokalnych
* Szybko wykorzystać funkcje chmury z zabezpieczeń Microsoft Cloud
* Scentralizuj Zarządzanie kontami użytkowników
* Scentralizowanie kontrolę nad tożsamości i zabezpieczeń
* Automatycznie dodawała lub usuwała użytkownikom dostęp do aplikacji na podstawie przynależności do grupy

W tym artykule wyjaśniono, jak usługa Azure AD i serwera Proxy aplikacji usługi użytkownikom zdalnym środowisko logowania jednokrotnego (SSO). Użytkownicy bezpiecznie łączyć się z aplikacjami lokalnymi bez sieci VPN lub dwukierunkowy serwerów i reguł zapory. Ten artykuł pomoże Ci zrozumieć, jak serwer Proxy aplikacji udostępnia funkcje i korzyści związane z bezpieczeństwem chmury do aplikacji sieci web w środowisku lokalnym. Omówiono także architekturę i topologie, które są możliwe.

## <a name="remote-access-in-the-past"></a>Dostęp zdalny w przeszłości

Wcześniej swoje płaszczyznę kontroli dla ochrony przed atakami wewnętrznych zasobów, podczas ułatwiania dostępu przez użytkowników zdalnych w sieci Obwodowej lub sieci obwodowej. Jednak sieci VPN i rozwiązania zwrotnego serwera proxy wdrożone w sieci Obwodowej, używane przez klientów zewnętrznych dostęp do zasobów firmy, które nie są odpowiednie do środowiska chmury. Odczuwają ich zwykle następujące wady:

* Koszty sprzętu
* Obsługiwanie zabezpieczeń (poprawek, monitorowanie portów itp.)
* Uwierzytelnianie użytkowników na urządzeniach brzegowych
* Uwierzytelnianie użytkowników na serwerach sieci web w sieci obwodowej
* Obsługa dostępu do sieci VPN dla użytkowników zdalnych przy użyciu dystrybucji i konfiguracji oprogramowania klienta VPN. Ponadto konserwowaniem serwerów przyłączonych do domeny w sieci Obwodowej, może być narażony na ataki zewnętrzne.

W dzisiejszym świecie zdominowanym chmury usługi Azure AD jest najlepiej nadaje się do kontrolowania, kto i co pobiera w Twojej sieci. Serwer Proxy aplikacji usługi Azure AD integruje się z nowoczesnego uwierzytelniania i technologie chmurowe, takie jak aplikacje SaaS i dostawców tożsamości. Ta integracja umożliwia użytkownikom dostęp do aplikacji z dowolnego miejsca. Nie tylko serwer Proxy aplikacji bardziej odpowiednie dla współczesnych cyfrowych w miejscu pracy, jest bardziej bezpieczne niż sieci VPN i rozwiązania zwrotnego serwera proxy i łatwiejsze do wdrożenia. Użytkownicy zdalni mogą uzyskać dostęp do aplikacji w środowisku lokalnym taki sam sposób uzyskiwania dostępu do usług O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmienić lub zaktualizować aplikacji do pracy z serwerem Proxy aplikacji. Ponadto serwer Proxy aplikacji nie wymaga otworzenia przychodzącego połączenia przez zaporę. Za pomocą serwera Proxy aplikacji wystarczy ustawić go i zapomnij go.

## <a name="the-future-of-remote-access"></a>Przyszłość dostępu zdalnego

W miejscu pracy cyfrowego dzisiejsza użytkownicy wszędzie pracować z wieloma urządzeniami i aplikacjami. Tylko stała jest tożsamość użytkownika. Dlatego pierwszy krok do bezpiecznej sieci jest już dziś użycie [Zarządzanie tożsamościami usługi Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) możliwości jak płaszczyzna kontroli zabezpieczeń. Model, który korzysta z tożsamości jako płaszczyzna kontroli zwykle składa się z następujących składników:

* Dostawca tożsamości do śledzenia użytkowników i informacji dotyczących użytkownika.
* Katalog urządzeń do zarządzania listą urządzeń, które mają dostęp do zasobów firmy. Ten katalog zawiera odpowiednie informacje o urządzeniach (na przykład typ urządzenia, integralność itp.).
* Usługi oceny zasad w celu określenia, czy użytkowników i urządzeń jest zgodny z zasadami określonymi przez użytkowników jako administratorów zabezpieczeń.
* Możliwość udzielania lub odmawiania dostępu do zasobów organizacji.

Za pomocą serwera Proxy aplikacji usługi Azure AD śledzi użytkowników, którzy chcą korzystać z sieci web aplikacji opublikowanych lokalnie i w chmurze. Zapewnia punktu centralnego zarządzania dla tych aplikacji. Chociaż nie jest to wymagane, zaleca się, że również włączyć dostęp warunkowy usługi Azure AD. Definiując warunki jak użytkowników uwierzytelniania i uzyskania dostępu, możesz dodatkowo upewnij się, że odpowiednie osoby mają dostęp do aplikacji.

**Uwaga:** Jest ważne dowiedzieć się, że serwer Proxy aplikacji usługi Azure AD jest przeznaczony jako serwer VPN lub zastępuje zwrotny serwer proxy (zdalnych lub mobilnych) użytkowników, którzy potrzebują dostępu do zasobów wewnętrznych. Nie jest on przeznaczony dla użytkowników wewnętrznych w sieci firmowej. Użytkownicy wewnętrzni, którzy niepotrzebnie Użyj serwera Proxy aplikacji mogą powodować problemy nieoczekiwany i niepożądanych wydajność.

![Usługa Azure Active Directory i wszystkie Twoje aplikacje](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Omówienie sposobu działania serwera Proxy aplikacji

Serwer Proxy aplikacji jest skonfigurowana w witrynie Azure portal usługi Azure AD. Pozwala ona do opublikowania zewnętrznego punktu końcowego publicznego adresu URL protokołu HTTP/HTTPS w chmurze platformy Azure, która łączy się adres URL serwera wewnętrznej aplikacji w Twojej organizacji. Aplikacje sieci web w środowisku lokalnym, może zostać zintegrowany z usługą Azure AD w celu obsługi logowania jednokrotnego. Użytkownicy końcowi mogą następnie dostęp do lokalnych aplikacji sieci web w taki sam sposób uzyskiwania dostępu do usługi Office 365 i innych aplikacji SaaS.

Składniki z tej funkcji obejmują usługę serwera Proxy aplikacji, która działa w chmurze łącznika serwera Proxy aplikacji jest uproszczone agenta, który jest uruchamiany na lokalnym serwerze oraz usługi Azure AD, w którym dostawca tożsamości jest. Wszystkie trzy składniki współdziałanie pozwala udostępnić użytkownikowi za pomocą funkcji logowania jednokrotnego na dostęp do aplikacji sieci web lokalnie.

Po zalogowaniu się użytkowników zewnętrznych mają dostęp do aplikacji sieci web w środowisku lokalnym za pomocą dobrze znanych adresu URL lub [panelu dostępu MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) na swoich urządzeniach, pulpitu lub z systemem iOS lub MAC. Na przykład serwera Proxy aplikacji, można zapewnić dostęp zdalny i pojedynczego logowania jednokrotnego do zdalnego pulpitu, SharePoint lokacje, Tableau, Qlik, aplikacji Outlook w sieci web i line-of-business (LOB).

![Architektura usługi Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Authentication

Istnieje kilka sposobów, aby skonfigurować aplikację pod kątem logowania jednokrotnego, a metoda, którą wybierzesz zależy od używanych przez aplikację uwierzytelniania. Serwer Proxy aplikacji obsługuje następujące typy aplikacji:

* Aplikacje internetowe
* Interfejsy API, który chcesz udostępnić rozbudowane aplikacje na różnych urządzeniach w sieci Web
* Aplikacji hostowanych za bramą usługi pulpitu zdalnego
* Rozbudowane aplikacje klienckie, które są zintegrowane z Active Directory Authentication Library (ADAL)

Serwer Proxy aplikacji działa z aplikacjami, które używają następujących natywnego protokołu uwierzytelniania:

* **[Zintegrowane uwierzytelnianie Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Dla IWA łączników serwera Proxy aplikacji umożliwia delegowanie ograniczone protokołu Kerberos (KCD) uwierzytelniać użytkowników aplikacji protokołu Kerberos.

Serwer Proxy aplikacji również obsługuje następujące protokoły uwierzytelniania za pomocą rozwiązań innych firm lub w scenariuszach konfiguracji:

* [**Uwierzytelnianie za pomocą nagłówka**](application-proxy-configure-single-sign-on-with-ping-access.md). Ta metoda logowania jednokrotnego korzysta z usługi uwierzytelniania innej firmy o nazwie PingAccess i jest używany, gdy aplikacja używa nagłówków do uwierzytelniania. W tym scenariuszu uwierzytelnianie jest obsługiwane przez oprogramowanie PingAccess.
* [**Uwierzytelnianie oparte na formularzach lub hasło**](application-proxy-configure-single-sign-on-password-vaulting.md). Ta metoda uwierzytelniania użytkowników logowania do aplikacji przy użyciu nazwy użytkownika i hasła podczas pierwszego uzyskiwania dostępu do. Po pierwsze logowanie jednokrotne usługi Azure AD zawiera nazwę użytkownika i hasło do aplikacji. W tym scenariuszu uwierzytelnianie odbywa się przez usługę Azure AD.
* [**Uwierzytelnianie SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Opartej na SAML logowania jednokrotnego jest obsługiwana dla aplikacji, które używają protokołu SAML 2.0 i WS-Federation protokołów. Przy użyciu protokołu SAML logowania jednokrotnego usługa Azure AD uwierzytelnia się do aplikacji przy użyciu konta usługi Azure AD.

Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [wybranie jednej metody logowania jednokrotnego](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Korzyści w zakresie zabezpieczeń

Rozwiązanie dostępu zdalnego, oferowane przez serwer Proxy aplikacji i usługi Azure AD obsługuje kilka korzyści w zakresie zabezpieczeń, które klienci mogą korzystać z zalet, w tym:

* **Dostęp uwierzytelniany**. Serwer Proxy aplikacji jest najlepiej nadaje się do publikowania aplikacji za pomocą [wstępnego uwierzytelniania](application-proxy-security.md#authenticated-access) aby upewnić się, czy tylko uwierzytelnionych połączeń trafień w sieci. W przypadku aplikacji opublikowanych przy użyciu wstępnego uwierzytelniania żaden ruch nie może przekazać za pośrednictwem usługi Serwer Proxy aplikacji do środowiska lokalnego bez prawidłowego tokenu. Wstępnego uwierzytelniania ze swej natury blokuje znaczącą liczbę ataków ukierunkowanych, ponieważ tylko uwierzytelnionych tożsamości można uzyskiwać dostęp do aplikacji zaplecza.
* **Dostęp warunkowy**. Bardziej rozbudowane kontroli zasad można zastosować przed nawiązywane są połączenia z siecią. Przy użyciu dostępu warunkowego można definiować ograniczenia ruchu, który umożliwiają trafień aplikacji zaplecza. Możesz utworzyć zasady, które ograniczają logowania na podstawie lokalizacji, siły uwierzytelniania i profil ryzyka użytkownika. Miarę rozwoju dostępu warunkowego, aby zapewnić dodatkową ochronę takich jak integracja z zabezpieczeń aplikacji Microsoft Cloud (MCAS) są dodawane większej liczby kontrolek. Integracja MCAS umożliwia skonfigurowanie aplikacji w środowisku lokalnym [monitorowanie w czasie rzeczywistym](application-proxy-integrate-with-microsoft-cloud-application-security.md) przy użyciu dostępu warunkowego umożliwia monitorowanie i kontrolowanie sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego.
* **Ruch zakończenia**. Cały ruch do aplikacji zaplecza jest przerwane na usługę serwera Proxy aplikacji w chmurze ponownym ustanowieniu sesji z serwera wewnętrznej bazy danych. Ta strategia połączenia oznacza, że Twoje serwery nie są widoczne do kierowania ruchu HTTP wewnętrznej bazy danych. Lepiej są one chronione przed atakami ukierunkowanymi DoS (denial of service), ponieważ Zapora nie jest celem ataku.
* **Wszelki dostęp jest wychodzący**. Łączniki serwera Proxy aplikacji używać tylko połączeń wychodzących usługi Serwer Proxy aplikacji w chmurze przez porty 80 i 443. Za pomocą żadnych połączeń przychodzących nie ma potrzeby otwierania portów zapory dla połączeń przychodzących lub składników w sieci Obwodowej. Wszystkie połączenia są wychodzących i za pośrednictwem bezpiecznego kanału.
* **Zabezpieczenia Analytics i Machine Learning (ML) na podstawie analizy**. Ponieważ jest częścią usługi Azure Active Directory, mogą korzystać z serwera Proxy aplikacji [usługi Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (wymaga [licencji Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)). Usługa Azure AD Identity Protection stanowi połączenie funkcji analizy zabezpieczeń uczenia maszynowego strumieniowych źródeł danych z firmy Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) i [Microsoft Security Response Center](https://www.microsoft.com/msrc) do proaktywnej identyfikacji konta ze złamanymi zabezpieczeniami. Identity Protection zapewnia ochronę w czasie rzeczywistym z wysokiego ryzyka logowania. Trwa pod uwagę czynników, takich jak dostęp z zainfekowanych urządzeń za pośrednictwem sieci z zachowywanie anonimowości lub lokalizacje nietypowe i najprawdopodobniej nie będą zwiększyć profil ryzyka sesji. Ten profil ryzyka jest używany do ochrony w czasie rzeczywistym. Wiele z tych raportów i zdarzeń są już dostępne za pośrednictwem interfejsu API do integracji z systemów SIEM.

* **Dostęp zdalny w trybie usługi**. Nie trzeba martwić się o zachowaniu i stosowanie poprawek serwery lokalne, aby włączyć dostęp zdalny. Serwer Proxy aplikacji jest usługi skalowania internetowego, który jest właścicielem firmy Microsoft, dzięki czemu zawsze uzyskać najnowsze poprawki zabezpieczeń i uaktualnień. Nadal oprogramowania bez konta dla dużej liczby ataków. Zgodnie z działu z bezpieczeństwa wewnętrznego Stanów Zjednoczonych, ile [85 procent ataków ukierunkowanych są procedury](https://www.us-cert.gov/ncas/alerts/TA15-119A). W tym modelu usługi nie trzeba wykonać duże obciążenia już zarządzanie serwerami usługi edge i szyfrują je zastosowania poprawki, zgodnie z potrzebami.

* **Integracja z usługą Intune**. Przy użyciu usługi Intune, firmowej ruch odbywa się oddzielnie od osobistych ruchu. Serwer Proxy aplikacji zapewnia, że firmowej ruch jest uwierzytelniony. [Serwer Proxy aplikacji i Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) możliwości można również ze sobą, aby umożliwić użytkownikom zdalnym bezpieczny dostęp do wewnętrznych witryn sieci Web z systemami iOS i Android.

### <a name="roadmap-to-the-cloud"></a>Planu przejścia do chmury

Inną główną zaletą Implementowanie serwera Proxy aplikacji jest rozszerzenie usługi Azure AD do środowiska lokalnego. W rzeczywistości Implementowanie serwera Proxy aplikacji jest krokiem podczas przenoszenia organizacji i aplikacje w chmurze. Dzięki przeniesieniu do chmury i od uwierzytelniania lokalnego, można zmniejszyć Twojej obecności w środowisku lokalnym i używać możliwości zarządzania tożsamościami usługi Azure AD jako płaszczyzna kontroli. Przy minimalnym lub żadnych aktualizacji do istniejących aplikacji masz dostęp do możliwości, takie jak pojedynczego logowania jednokrotnego, uwierzytelniania wieloskładnikowego i centralne zarządzanie w chmurze. Instalowanie niezbędnych składników serwera Proxy aplikacji jest prostym procesem wykonywanym przez ustanawianie struktury dostępu zdalnego. A dzięki przeniesieniu do chmury, masz dostęp do najnowszych funkcji usługi Azure AD, aktualizacje i funkcje, takie jak wysoka dostępność i odzyskiwanie po awarii.

Aby dowiedzieć się więcej o migracji aplikacji do usługi Azure AD, zobacz [migracji Twojej aplikacji do usługi Azure Active Directory](https://aka.ms/migrateapps/whitepaper) oficjalny dokument.

## <a name="architecture"></a>Architektura

Na poniższym diagramie przedstawiono ogólnie rzecz biorąc usług jak Azure AD authentication i serwera Proxy aplikacji działają razem w celu zapewnienia logowania jednokrotnego do aplikacji użytkownikom końcowym lokalnych.

![Przepływ uwierzytelniania w usłudze Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Po użytkownik uzyskał dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest przekierowany do strony logowania usługi Azure AD. Jeśli skonfigurowano zasady dostępu warunkowego, określone warunki są sprawdzane w celu zapewnienia zgodności z wymaganiami dotyczącymi zabezpieczeń w organizacji.
2. Po pomyślnym zalogowaniu usługi Azure AD wysyła token do urządzenia klienckiego przez użytkownika.
3. Klient wysyła ten token do usługi serwera Proxy aplikacji, która pobiera główna nazwa użytkownika (UPN) i nazwy podmiotu zabezpieczeń (SPN) z tokenem.
4. Serwer Proxy aplikacji przekazuje żądania, które są pobierane przez serwer Proxy aplikacji [łącznika](application-proxy-connectors.md).
5. Łącznik wykonuje wszelkie dodatkowe uwierzytelnianie, wymagana w imieniu użytkownika (*opcjonalne, w zależności od metody uwierzytelniania*) żąda wewnętrzny punkt końcowy serwera aplikacji i wysyła żądanie do serwera lokalnego aplikacja.
6. Odpowiedź z serwera aplikacji jest wysyłane za pośrednictwem łącznika do usługi serwera Proxy aplikacji.
7. Odpowiedź jest wysyłana z usługi Serwer Proxy aplikacji dla użytkownika.

|**Składnik**|**Opis**|
|:-|:-|
|Endpoint|Punkt końcowy jest adresem URL lub [portalu użytkownika](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji znajduje się poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w Twojej sieci dostęp do aplikacji za pomocą adresu URL lub portalu użytkownika końcowego. Użytkownicy, przejdź do jednego z tych punktów końcowych, uwierzytelniania w usłudze Azure AD i następnie są kierowane za pośrednictwem łącznika do aplikacji w środowisku lokalnym.|
|Azure AD|Usługa Azure AD przeprowadza uwierzytelnianie przy użyciu katalogu dzierżawy, przechowywane w chmurze.|
|Usługa serwera Proxy aplikacji|Ta usługa serwera Proxy aplikacji działa w chmurze w ramach usługi Azure AD. Przekazaniem tokenu logowania użytkownika do łącznika serwera Proxy aplikacji. Serwer Proxy aplikacji przekazuje wszelkie dostępne nagłówki dla żądania i ustawia nagłówki zgodnie z jego protokół adresu IP klienta. Jeśli przychodzące żądanie do serwera proxy już nagłówka, adres IP klienta zostanie dodany na końcu rozdzielaną przecinkami listę, która jest wartość nagłówka.|
|Łącznik serwera Proxy aplikacji|Łącznik jest uproszczone agenta, który działa w systemie Windows Server w Twojej sieci. Łącznik zarządza komunikacją między usługą serwera Proxy aplikacji w chmurze i aplikacji w środowisku lokalnym. Łącznik używa tylko połączeń wychodzących, dzięki czemu nie trzeba otwierać żadnych portów przychodzących ani umieszczać niczego w strefie DMZ. Łączniki są bezstanowe i pobierania informacji z chmury, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat łączników, takich jak jak one Równoważenie obciążenia i uwierzytelniania, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md).|
|Active Directory (AD)|Usługi Active Directory działa lokalnie do uwierzytelniania dla kont domeny. Podczas logowania jednokrotnego jest skonfigurowany, łącznik komunikuje się z usługą AD, aby wykonać wszelkie wymagane dodatkowe uwierzytelnianie.|
|Aplikacja lokalna|Ponadto użytkownik jest w stanie uzyskać dostęp do aplikacji w środowisku lokalnym.|

Serwer Proxy aplikacji usługi Azure AD składa się z usługi Serwer Proxy aplikacji opartych na chmurze i lokalny łącznik. Łącznik nasłuchuje żądań z serwera Proxy aplikacji usługi i obsługuje połączenia z wewnętrznych aplikacji. Należy pamiętać, że cała komunikacja odbywa się za pośrednictwem protokołu SSL i zawsze pochodzi z łącznika usługi serwera Proxy aplikacji. Oznacza to, że komunikacja jest wychodzący tylko. Łącznik używa certyfikatu klienta do uwierzytelniania serwera Proxy aplikacji usługi dla wszystkich wywołań. Jedyny wyjątek od zabezpieczeń połączenia jest kroku konfiguracji początkowej, gdzie certyfikat klienta zostanie nawiązane. Zobacz serwera Proxy aplikacji [Kulisy](application-proxy-security.md#under-the-hood) Aby uzyskać więcej informacji.

### <a name="application-proxy-connectors"></a>Łączników serwera Proxy aplikacji

[Łączników serwera Proxy aplikacji](application-proxy-connectors.md) są uproszczone agentów wdrożonych lokalnie, które ułatwiają połączenie wychodzące do usługi serwera Proxy aplikacji w chmurze. Łączniki, musi być zainstalowany w systemie Windows Server, który ma dostęp do aplikacji zaplecza. Użytkownicy nawiązać połączenia serwera Proxy aplikacji usługi w chmurze tej trasy ruch do aplikacji za pomocą łączników, jak przedstawiono poniżej.

![Połączenia sieciowe w usłudze Azure AD Application Proxy](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Konfiguracja i rejestracja między łącznikiem i usługi Serwer Proxy aplikacji odbywa się w następujący sposób:

1. IT administrator otwiera porty 80 i 443 dla ruchu wychodzącego i umożliwia dostęp do kilku adresów URL, które są wymagane przez łącznik, usługi Serwer Proxy aplikacji i usługi Azure AD.
2. Administrator zaloguje się do witryny Azure portal i jest uruchamiany plik wykonywalny, aby zainstalować łącznik na serwerze Windows w środowisku lokalnym.
3. Łącznik rozpoczyna "słuchać" usługi Serwer Proxy aplikacji.
4. Administrator dodaje aplikacji lokalnych do usługi Azure AD i konfiguruje ustawienia, takie jak użytkownicy adresy URL muszą się łączyć swoje aplikacje.

Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia serwera Proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).

Zalecane jest, zawsze wdrażać wiele łączników na potrzeby nadmiarowości i skalowania. Łączniki, w połączeniu z usługą, zadbać o wszystkie zadania o wysokiej dostępności i można dodać lub usunąć dynamicznie. Każdorazowo, gdy nowe żądanie dociera jest kierowany do jednego z łączników, które jest dostępne. Łącznik jest uruchomiona, pozostaje aktywna jako nawiąże połączenie z usługą. Łącznik jest tymczasowo niedostępny, nie reagować na ten ruch. Nieużywane łączniki są oznaczone jako nieaktywne i usunięte po upływie 10 dni braku aktywności.

Łączniki sondować także serwera, aby sprawdzić, czy jest dostępna nowsza wersja łącznika. Mimo że możecie ręcznej aktualizacji łączniki automatycznie zaktualizuje tak długo, jak działa usługa aktualizator łącznika serwera Proxy aplikacji. Dla dzierżawców dzięki wielu łącznikom aktualizacje automatyczne docelowe jeden łącznik w czasie w każdej grupie, aby uniknąć przestojów w danym środowisku.

**Uwaga**: Serwer Proxy aplikacji można monitorować [strony historii wersji](application-proxy-release-version-history.md) Aby otrzymywać powiadomienia, gdy aktualizacje zostały zwolnione przez subskrybowanie jego źródło danych RSS.

Każdy łącznik serwera Proxy aplikacji jest przypisany do [grupy łączników](application-proxy-connector-groups.md). Łączniki w tej samej grupy łączników działają jako pojedyncza jednostka w celu zapewnienia wysokiej dostępności i równoważenia obciążenia. Można tworzyć nowe grupy, przypisać im łączników w witrynie Azure portal, a następnie przypisać określonych łączników do obsługi określonych aplikacji. Zaleca się mieć co najmniej dwa łączniki w każdej grupie łącznika w celu zapewnienia wysokiej dostępności.

Grupy łączników są przydatne, gdy muszą być obsługiwane w następujących scenariuszach:

* Publikowanie aplikacji geograficznych
* Izolacja segmentacji aplikacji
* Publikowanie aplikacji sieci web działające w chmurze lub lokalnie

Aby uzyskać więcej informacji na temat wybierania, gdzie zainstalować łączniki i Optymalizacja sieci, zobacz [zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy usługi Azure Active Directory Application](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Inne przypadki użycia

Do tej pory skoncentrowaliśmy się na publikowanie lokalnych aplikacji zewnętrznie podczas włączania logowania jednokrotnego do wszystkich aplikacji w chmurze i lokalnych przy użyciu serwera Proxy aplikacji. Istnieją jednak inne przypadki użycia dla serwera Proxy aplikacji, które warto wspomnieć o. Obejmują one:

* **Bezpieczne publikowanie interfejsów API REST**. Gdy masz logikę biznesową lub interfejsów API uruchamiania lokalnych lub hostowanych na maszynach wirtualnych w chmurze, serwer Proxy aplikacji udostępnia publicznego punktu końcowego uzyskać dostęp do interfejsu API. Dostęp do punktu końcowego interfejsu API umożliwia kontroli uwierzytelniania i autoryzacji, bez konieczności portów przychodzących. Zapewnia dodatkowe zabezpieczenia za pomocą funkcji usługi Azure AD Premium, takich jak uwierzytelnianie wieloskładnikowe i dostępu warunkowego opartego na urządzenie dla komputerów stacjonarnych, iOS, MAC i urządzeń z systemem Android przy użyciu usługi Intune. Aby dowiedzieć się więcej, zobacz [włączania natywne aplikacje klienckie do interakcji z serwera proxy aplikacji](application-proxy-configure-native-client-application.md) i [ochrona interfejsu API przy użyciu protokołu OAuth 2.0 przy użyciu usługi Azure Active Directory i usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Usługi pulpitu zdalnego** **(RDS)** . Standardowe wdrożenia usług pulpitu zdalnego wymagają otwartych połączeń przychodzących. Jednak [wdrożenia usług pulpitu zdalnego z serwerem Proxy aplikacji](application-proxy-integrate-with-remote-desktop-services.md) ma trwałe połączenie wychodzące z serwerem z uruchomioną usługą łącznika. Dzięki temu możesz zaoferować większej liczby aplikacji użytkownikom końcowym za publikowanie aplikacji w środowisku lokalnym za pomocą usług pulpitu zdalnego. Można także zmniejszyć obszar narażony na wdrożenie z ograniczonym zestawem weryfikacji dwuetapowej i kontroli dostępu warunkowego na RDS.
* **Publikowanie aplikacji przy użyciu funkcji WebSockets**. Obsługuje z [usługa Qlik Sense](application-proxy-qlik.md) znajduje się w publicznej wersji zapoznawczej i zostanie rozszerzona do innych aplikacji w przyszłości.
* **Włącz natywne aplikacje klienckie do interakcji z serwera proxy aplikacji**. Serwer Proxy aplikacji usługi Azure AD umożliwia publikowanie aplikacji sieci web, ale również może służyć do publikowania [natywne aplikacje klienckie](application-proxy-configure-native-client-application.md) skonfigurowanych za pomocą usługi Azure AD Authentication Library (ADAL). Natywne aplikacje klienckie różnią się od aplikacji sieci web, ponieważ są one zainstalowane na urządzeniu, natomiast aplikacje sieci web są dostępne za pośrednictwem przeglądarki.

## <a name="conclusion"></a>Podsumowanie

Ten sposób możemy współpracować i narzędzia, których firma Microsoft zmienia się tak szybko. Mając więcej pracowników przynosili własne urządzenia do pracy i rozpowszechniona użytkowania aplikacji Software-as-a-Service (SaaS) organizacje sposób zarządzać i bezpieczne swoje dane muszą również się rozwijać. Firmom nie będzie działać wyłącznie w ramach ich własnych ściany chronione przez moat, ich obramowania wokół. Dane wysyłane do większej liczby lokalizacji niż kiedykolwiek wcześniej — na lokalnych i środowiskach w chmurze. Ta zmiana pomogła zwiększyć produktywność użytkowników i możliwość współpracy, ale zapewnia także ochronę danych poufnych trudniejsze.

Czy używasz usługi Azure AD do zarządzania użytkownikami w scenariuszu hybrydowym współistnienie lub interesujący Cię od swoją podróż do chmury, implementowanie serwera Proxy aplikacji usługi Azure AD może pomóc zmniejszyć rozmiar Twojej lokalnej obecności, zapewniając zdalnego dostęp do jako usługa.

Organizacje powinny zacząć korzystać z serwera Proxy aplikacji już dziś korzystać z zalet następujące korzyści:

* Publikowanie aplikacji lokalnych zewnętrznie bez obciążenia związanego z utrzymywania tradycyjnej sieci VPN lub inne środowiska lokalnego publikowania w sieci web rozwiązania i podejścia DMZ
* Logowanie jednokrotne do wszystkich aplikacji, którymi są w usłudze Office 365 lub innych aplikacji SaaS oraz aplikacji lokalnych, takich jak
* Usługa cloud security skalowania, gdzie korzysta z danych telemetrycznych usługi Office 365 w celu uniemożliwienia nieupoważnionego dostępu usługi Azure AD
* Integracja usługi Intune, aby upewnić się, firmowej ruch jest uwierzytelniony.
* Centralnego zarządzania kontami użytkowników
* Aktualizacje automatyczne, aby zagwarantować, że masz najnowsze poprawki zabezpieczeń
* Nowe funkcje staną się dostępne; Zarządzanie najnowsze jest obsługa protokołu SAML logowania jednokrotnego i bardziej szczegółową plików cookie w aplikacji

## <a name="next-steps"></a>Kolejne kroki

* Informacji o planowaniu, operacyjne oraz zarządzania nimi serwera Proxy aplikacji usługi Azure AD, zobacz [Planowanie wdrożenia serwera Proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md).
* Aby zaplanować pokaz na żywo lub Uzyskaj bezpłatną wersję próbną 90-dniowej wersji ewaluacyjnej, zobacz [wprowadzenie do pakietu Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
