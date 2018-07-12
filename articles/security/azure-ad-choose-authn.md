---
title: Wybierz metodę uwierzytelniania odpowiednie dla Twojego rozwiązania tożsamości hybrydowej usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten przewodnik ułatwi naczelnych, dyrektorów działów IT, CISOs, dyrektor tożsamości architektów, architektów w przedsiębiorstwach i zabezpieczeń i decydenci IT odpowiedzialny za wyboru metody uwierzytelniania dla ich rozwiązania tożsamości hybrydowej usługi Azure AD w średnich i dużych organizacji.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: d0d72694fe2d88e257a8684dc37a250bc0ad7f3f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970993"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wybierz metodę uwierzytelniania odpowiednie dla Twojego rozwiązania tożsamości hybrydowej usługi Azure Active Directory 

W tym artykule rozpoczyna się seria artykułów, które pomagają organizacjom implementacji kompletnego rozwiązania tożsamości hybrydowej usługi Azure Active Directory (Azure AD). To rozwiązanie zostało opisane jako [platformy cyfrowej transformacji tożsamości hybrydowego](https://aka.ms/aadframework). Poruszono w nim wyników biznesowych i cele organizacji może skupić się na implementacji rozwiązania tożsamości hybrydowej niezawodne i bezpieczne. 

Pierwszy wyników biznesowych Framework opisujemy wymagania organizacji w celu zabezpieczenia procesu uwierzytelniania, gdy użytkownicy uzyskują dostęp do aplikacji w chmurze. Pierwszym celem firm wyników biznesowych bezpiecznego uwierzytelniania jest użytkownikom logować się do aplikacji w chmurze przy użyciu ich nazw lokalnych użytkowników i haseł. Ten proces logowania, aby i jak użytkownicy są uwierzytelniani, umożliwiają wszystko w chmurze.

Wybranie poprawnej metody uwierzytelniania jest pierwszą kwestią w przypadku organizacji chce przenieść swoje aplikacje do chmury. Nie dokonaniem tej decyzji, z następujących powodów:

1. To pierwszy decyzji dla organizacji, która chce, aby przejść do chmury. 

2. Metoda uwierzytelniania to kluczowy składnik obecność organizacji w chmurze. Kontroluje dostęp do wszystkich danych w chmurze i zasobów.

3. Stanowi ona podstawę do wszystkich zaawansowane zabezpieczenia i funkcje środowiska użytkownika w usłudze Azure AD.

4. Metoda uwierzytelniania jest trudne, można zmienić po jego wdrożeniu.

Tożsamość jest nowy płaszczyznę kontroli zabezpieczeń IT. Dlatego uwierzytelnianie jest ochrona dostępu w organizacji do nowego środowiska chmury. Organizacje wymagają płaszczyznę kontroli tożsamości, wzmacnia ich zabezpieczeń, która przechowuje swoje aplikacje w chmurze przed intruzami.

### <a name="out-of-scope"></a>Poza zakresem
Organizacje, które nie mają istniejącej zużycie lokalnych w katalogu nie ma fokus w tym artykule. Zazwyczaj te firmom tworzenie tożsamości tylko w chmurze, które nie wymagają rozwiązania z tożsamością hybrydową. Tożsamości oparte tylko na chmurze istnieje wyłącznie w chmurze i nie są skojarzone z odpowiedniej tożsamości użytkownika lokalnego.

## <a name="authentication-methods"></a>Metody uwierzytelniania
Rozwiązania tożsamości hybrydowej usługi Azure AD po swojej nowej warstwy kontroli uwierzytelniania jest podstawą dostęp do chmury. Wybranie poprawnej metody uwierzytelniania jest niezwykle istotne pierwszej decyzji w procesie konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Implementuje metodę uwierzytelniania, która jest konfigurowana przy użyciu usługi Azure AD Connect, która również Inicjuje obsługę użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącej infrastruktury, złożoności i kosztów wykonania wybranych. Czynniki te różnią się dla każdej organizacji i mogą ulec zmianie wraz z upływem czasu. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Usługa Azure AD obsługuje następujące metody uwierzytelniania dla hybrydowych rozwiązań tożsamości.

### <a name="cloud-authentication"></a>Uwierzytelnianie w chmurze
W przypadku wybrania tej metody uwierzytelniania, usługi Azure AD obsługuje proces logowania użytkowników. Powiązane z bezproblemowego logowania jednokrotnego (SSO), użytkownicy mogą się logować do aplikacji w chmurze bez konieczności ponownego wprowadzania poświadczeń. Za pomocą uwierzytelniania w chmurze można wybrać jeden z dwóch opcji: 

**Synchronizacja skrótów haseł w usłudze Azure AD**. Najprostszym sposobem, aby włączyć uwierzytelnianie dla obiektów katalogu lokalnego, w usłudze Azure AD. Użytkownicy mogą używać tej samej nazwy użytkownika i hasło, którego używają lokalnie bez konieczności wdrażania jakiejkolwiek dodatkowej infrastruktury. Niektóre funkcje premium usługi Azure AD, takie jak ochrona tożsamości wymagają synchronizacji skrótów haseł dla niezależnie od tego, którą metodę uwierzytelniania wybierz.

> [!NOTE] 
> Hasła nigdy nie są przechowywane w postaci zwykłego tekstu lub szyfrowane przy użyciu algorytmu odwracalnego w usłudze Azure AD. Aby uzyskać więcej informacji na temat procesu rzeczywistej synchronizacji skrótów haseł, zobacz [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Uwierzytelnianie przekazywane usługi Azure AD**. Udostępnia weryfikację prostych haseł, usługi uwierzytelniania usługi Azure AD za pomocą agenta oprogramowania, które jest uruchamiane na lokalnych serwerach. Serwery weryfikowanie użytkowników bezpośrednio ze środowiska lokalnego usługi Active Directory, który zapewnia, że sprawdzenie poprawności hasła nie jest realizowane w chmurze. 

Firm z wymaganiami bezpieczeństwa, o natychmiastowym wymuszeniu użytkownika lokalnego konta stany, zasady haseł i godziny logowania może użyć tej metody uwierzytelniania. Aby uzyskać więcej informacji na temat procesu rzeczywistej uwierzytelniania przekazywanego, zobacz [użytkownika Zaloguj się przy użyciu uwierzytelniania przekazywanego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne
W przypadku wybrania tej metody uwierzytelniania, ręce usługi Azure AD wyłączone procesu uwierzytelniania, aby oddzielnym zaufanych systemem uwierzytelniania, takich jak lokalne Active Directory Federation Services (AD FS), aby zweryfikować hasła użytkownika.

System uwierzytelniania może zapewnić dodatkowe uwierzytelnianie zaawansowanych wymagań. Przykładami są uwierzytelnianie za pomocą karty inteligentnej lub uwierzytelnianie wieloskładnikowe innych firm. Aby uzyskać więcej informacji, zobacz [wdrażania usług federacyjnych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Poniższa sekcja pomoże Ci zdecydować, której metody uwierzytelniania jest odpowiedni dla Ciebie przy użyciu drzewa decyzyjnego. Pomaga ustalić, czy mają zostać wdrożone w chmurze lub uwierzytelnianie Sfederowane dla Twojego rozwiązania tożsamości hybrydowej usługi Azure AD.

## <a name="decision-tree"></a>Drzewo decyzyjne

![Drzewo decyzyjne uwierzytelniania w usłudze Azure AD](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Opis szczegółowych zagadnień

### <a name="cloud-authentication-password-hash-synchronization"></a>Uwierzytelnianie w chmurze: synchronizacja skrótów haseł

* **Nakład pracy**. Synchronizacja skrótów haseł wymaga co najmniej nakład pracy dotyczące wdrażania, obsługi i infrastruktury.  Ten poziom nakładu pracy zwykle ma zastosowanie do organizacji, którzy muszą tylko użytkownikom logować się do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD. Po jej włączeniu synchronizacji skrótów haseł jest częścią procesu synchronizacji Azure AD Connect i jest uruchamiane co dwie minuty.

* **Środowisko użytkownika**. Aby poprawić środowisko logowania użytkowników, należy wdrożyć bezproblemowe logowanie Jednokrotne za pomocą synchronizacji skrótów haseł. Bezproblemowe logowanie Jednokrotne eliminuje niepotrzebne monitów, gdy użytkownicy są zalogowani.

* **Zaawansowane scenariusze**. Jeśli chce się organizacji jest możliwość użycia szczegółowych informacji z tożsamości za pomocą raportów usługi Azure AD Identity Protection. Przykładem jest jako raport ujawnione poświadczenia. Windows Hello dla firm to kolejna opcja, która ma [konkretne wymagania w sytuacji, gdy używasz synchronizacji skrótów haseł](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego za pomocą synchronizacji skrótów haseł, należy użyć uwierzytelniania wieloskładnikowego w usłudze Azure AD. Organizacje, nie można używać innych firm lub lokalne metod uwierzytelniania wieloskładnikowego.

* **Ciągłość prowadzenia działalności biznesowej**. Synchronizacja skrótów haseł przy użyciu uwierzytelniania w chmurze jest wysoce dostępna jako usługa w chmurze, która skaluje się do wszystkich centrów danych firmy Microsoft. Aby upewnić się, że synchronizacja skrótów haseł nie są wyłączane przez dłuższy czas, należy wdrożyć drugi serwer usługi Azure AD Connect w trybie rezerwy dynamicznej konfiguracji przejściowym.

* **Zagadnienia dotyczące**. Obecnie usługa synchronizacji skrótów haseł nie natychmiastowym wymuszeniu zmiany w Stanach konta lokalnego. W takiej sytuacji użytkownik ma dostęp do aplikacji w chmurze, aż stan konta użytkowników są synchronizowane z usługą Azure AD. Organizacje, być może chcesz ominąć to ograniczenie, uruchamiając nowe cykl synchronizacji po Administratorzy zbiorczej aktualizacji do Państwa konto użytkownika lokalnego. Przykładem jest wyłączenie konta.

> [!NOTE]
> Hasło wygasło i Stany zablokowanego konta nie są obecnie synchronizowane z usługą Azure AD z usługą Azure AD Connect. 

Zapoznaj się [Implementowanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) dla procedury wdrażania.

### <a name="cloud-authentication-pass-through-authentication"></a>Uwierzytelnianie w chmurze: uwierzytelnianie przekazywane  

* **Nakład pracy**. Dla uwierzytelniania przekazywanego, potrzebujesz co najmniej jeden (zalecamy trzy) uproszczone agentów zainstalowanych na istniejących serwerach. Agenty muszą mieć dostęp do swojej lokalnej Active Directory Domain Services, łącznie z lokalną kontrolery domeny usługi AD. Potrzebują wychodzącego dostępu do Internetu oraz dostęp do kontrolerów domeny. Z tego powodu nie jest obsługiwane wdrażanie agentów w sieci obwodowej. 

    Uwierzytelnianie przekazywane wymaga dostępu do sieci nieograniczonego na kontrolerach domeny. Cały ruch sieciowy jest szyfrowany i ograniczone do żądań uwierzytelniania. Aby uzyskać więcej informacji na temat tego procesu, zobacz [zagłębia zabezpieczeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) uwierzytelnianie przekazujących.

* **Środowisko użytkownika**. Aby poprawić środowisko logowania użytkowników, należy wdrożyć bezproblemowe logowanie Jednokrotne przy użyciu uwierzytelniania przekazywanego. Bezproblemowe logowanie Jednokrotne eliminuje niepotrzebne monity po logowania.

* **Zaawansowane scenariusze**. Uwierzytelnianie przekazywane wymusza w lokalnych zasadach konta podczas logowania. Na przykład odmowa dostępu, gdy konto użytkownika lokalnego, stan jest wyłączony, zablokowane, lub [hasło wygasło](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) lub znajduje się poza godzinami po użytkownik może się zalogować. 

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego przy użyciu uwierzytelniania przekazywanego, należy użyć usługi Azure Multi-Factor Authentication (MFA). Organizacje, nie można użyć innych firm lub lokalne metody uwierzytelniania wieloskładnikowego. Zaawansowane funkcje wymagają, że synchronizacja skrótów haseł jest wdrażany informację określającą, czy wybierzesz uwierzytelnianie przekazywane. Przykładem jest raport ujawnione poświadczenia Identity Protection.

* **Ciągłość prowadzenia działalności biznesowej**. Zaleca się wdrożenie dwóch agentów uwierzytelniania przekazywanego dodatkowych. Te dodatki w niniejszym dokumencie stanowią pierwszy agent na serwerze programu Azure AD Connect. To wdrożenie dodatkowych zapewnia wysoką dostępność żądania uwierzytelnienia. W przypadku trzech agentów wdrożonych jednego agenta może nadal się nie powieść podczas innego agenta jest wyłączona w celu przeprowadzenia konserwacji. 

    Występuje kolejna korzyść związana z wdrażaniem synchronizacji skrótów haseł, oprócz uwierzytelniania przekazywanego. Działa ona jako metodę uwierzytelniania kopii zapasowej, gdy metoda uwierzytelniania podstawowego nie jest już dostępna.

* **Zagadnienia dotyczące**. Można na przykład synchronizacji skrótów haseł w kopii zapasowej uwierzytelnienia dla uwierzytelniania przekazywanego i agentów nie można zweryfikować poświadczeń użytkownika. Następnie trybu failover, aby synchronizacja skrótów haseł nie jest realizowane automatycznie. Ręcznie przełączać metody logowania jednokrotnego przy użyciu usługi Azure AD Connect. 

    Uwierzytelnianie przekazywane obsługuje tylko aplikacje w chmurze, które używają nowoczesnego uwierzytelniania i określonych protokołów usługi Exchange Online. Niektóre protokoły są ActiveSync, POP3 i IMAP4. Na przykład Microsoft Office 2013 i nowsze Obsługa nowoczesnego uwierzytelniania, ale wcześniejszych wersji nie. Aby uzyskać więcej informacji na temat obsługi aplikacji pakietu Office, zobacz [nowoczesne uwierzytelnianie usługi Office 365 zaktualizowane](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Inne zagadnienia na uwierzytelnianie przekazujących, w tym alternatywny identyfikator pomocy technicznej, zobacz [— często zadawane pytania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Zapoznaj się [Implementowanie uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) dla procedury wdrażania.

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne

* **Nakład pracy**. System uwierzytelniania federacyjnego zależy od zaufanego zewnętrznego systemu do uwierzytelniania użytkowników. Niektóre firmy chcesz ponownie użyć dotychczasowych inwestycji federacyjnych systemu za pomocą ich rozwiązania tożsamości hybrydowej usługi Azure AD. Konserwacji i zarządzania systemem federacyjnego znajduje się poza kontrolą usługi Azure AD. Jest w organizacji za pomocą federacyjnego systemu do bezpiecznego wdrażania i czy może obsłużyć obciążenie uwierzytelniania. 

* **Środowisko użytkownika**. Środowisko użytkownika uwierzytelniania federacyjnego zależy od implementacji funkcji, topologii i konfiguracji kolektywu serwerów federacyjnych. Niektóre organizacje muszą tej elastyczności, dostosowania i konfigurowanie dostępu do farmy federacyjnych, aby odpowiadał wymaganiom ich zabezpieczeń. Na przykład istnieje możliwość konfigurowania wewnętrznie połączonych użytkowników i urządzeń, logowania użytkowników automatycznie bez wyświetlania monitu o poświadczenia. Ta konfiguracja działa, ponieważ one już zalogowali się do swoich urządzeń. Jeśli to konieczne, niektóre zaawansowane funkcje zabezpieczeń utrudniają procesu logowania użytkowników.

* **Zaawansowane scenariusze**. Rozwiązanie uwierzytelniania federacyjnego jest zazwyczaj wymagane, gdy klienci mają wymogu uwierzytelniania, który usługa Azure AD nie obsługuje natywnie. Zobacz szczegółowe informacje ułatwiające [opcję bezpośrednio logowania](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Należy wziąć pod uwagę następujące typowe wymagania:

    * Uwierzytelnianie, które wymaga kart inteligentnych lub certyfikatów.
    * Lokalne serwery usługi MFA lub wieloskładnikowej dostawców.
    * Uwierzytelnianie przy użyciu rozwiązania do uwierzytelniania innych firm. Zobacz [listę zgodności federacyjnych usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Na przykład sAMAccountName, na przykład domena\nazwa_użytkownika, zamiast nazwy głównej nazwy użytkownika (UPN), wymaga logowania user@domain.com.

* **Ciągłość prowadzenia działalności biznesowej**. Systemy federacyjnego zwykle wymagają tablicą ze zrównoważonym obciążeniem serwerów, znane jako farmy. Tej farmy został skonfigurowany w sieci wewnętrznej i topologii sieci obwodowej, aby zapewnić wysoką dostępność dla żądań uwierzytelniania.

    Wdrażać synchronizacji skrótów haseł oraz uwierzytelnianie federacyjne jako metoda uwierzytelniania kopii zapasowych, gdy metoda uwierzytelniania podstawowego nie jest już dostępna. Na przykład sytuacja lokalnych serwerów nie są dostępne. W niektórych organizacjach dużych przedsiębiorstw wymagają Federacji rozwiązania do obsługi wielu punktów ruch przychodzący Internet skonfigurowano geograficznie DNS dla żądań uwierzytelniania z małym opóźnieniem.

* **Zagadnienia dotyczące**. Systemy federacyjnego zwykle wymagają bardziej znaczących inwestycji związanych z infrastrukturą lokalną. Większość organizacji tę opcję, jeśli już mają inwestycji federacji w środowisku lokalnym. A jeśli jest wymaganie biznesowe silne, aby korzystać z obsługą jednej tożsamości — dostawcy. Federacja znajduje się bardziej złożone do obsługi i rozwiązywania problemów w porównaniu z rozwiązaniami uwierzytelniania w chmurze.

Domeny nonroutable, którego nie można zweryfikować w usłudze Azure AD wymaga dodatkowej konfiguracji do zaimplementowania identyfikator logowanie użytkownika. Wymóg ten jest znany jako alternatywne identyfikator pomocy technicznej. Zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) dla ograniczenia i wymagania. Jeśli zdecydujesz się używać dostawcy uwierzytelniania wieloskładnikowego innych firm z Federacją, upewnij się, że dostawca obsługuje WS-Trust, aby umożliwić dołączanie urządzeń do usługi Azure AD.

Zapoznaj się [wdrażania serwerów federacyjnych](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) dla procedury wdrażania.

> [!NOTE] 
> Podczas wdrażania rozwiązania tożsamości hybrydowej usługi Azure AD, musi implementować jeden z obsługiwanych topologii program Azure AD Connect. Dowiedz się więcej o obsługiwane i nieobsługiwane konfiguracje w [topologie obsługiwane w programie Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramy architektury

Następujące diagramy przedstawiają składniki Architektura wysokiego poziomu wymaganych dla poszczególnych metod uwierzytelniania, których można używać z danym rozwiązaniu tożsamości hybrydowej usługi Azure AD. Zapewniają one Przegląd ułatwiające porównywanie różnic między rozwiązania.

* Prostota rozwiązania synchronizacji skrótów haseł:

    ![Usługi Azure AD identity hybrydowego za pomocą synchronizacji skrótów haseł](media/azure-ad/azure-ad-authn-image2.png)

* Wymagania agenta uwierzytelniania przekazywanego:

    ![Usługi Azure AD identity hybrydowych przy użyciu uwierzytelniania przekazywanego](media/azure-ad/azure-ad-authn-image3.png)

* Składniki wymagane do federacyjnych w sieci obwodowej i sieci wewnętrznej organizacji:

    ![Usługi Azure AD identity hybrydowych przy użyciu uwierzytelniania federacyjnego](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porównanie metod

|Zagadnienia|Synchronizacja skrótów haseł i bezproblemowe logowanie Jednokrotne|Uwierzytelnianie przekazywane i bezproblemowe logowanie Jednokrotne|Federacja z usługami AD FS|
|:-----|:-----|:-----|:-----|
|Gdy odbywa się uwierzytelnianie?|W chmurze|W chmurze po wymianie weryfikacji bezpieczne hasło, za pomocą agenta uwierzytelniania w środowisku lokalnym|Lokalnie|
|Jakie są wymagania dotyczące serwera lokalnego poza inicjowania obsługi administracyjnej systemu: program Azure AD Connect?|Brak|Jeden serwer dla każdego dodatkowego uwierzytelniania agenta|Co najmniej dwóch serwerów usług AD FS<br><br>Co najmniej dwóch serwerów proxy aplikacji sieci Web w sieci obwodowej/sieci Obwodowej|
|Jakie są wymagania dotyczące Internet w środowisku lokalnym i sieci poza inicjowania obsługi administracyjnej systemu?|Brak|[Ruch wychodzący do Internetu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) z serwerów uruchomionych agentów uwierzytelniania|[Dostęp do Internetu przychodzący](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) na serwerach proxy aplikacji sieci Web w sieci obwodowej<br><br>Dostęp do sieci dla ruchu przychodzącego do serwerów usług AD FS z serwerów proxy aplikacji sieci Web w sieci obwodowej<br><br>Równoważenie obciążenia sieciowego|
|Jest to wymaganie dotyczące certyfikatów SSL?|Nie|Nie|Yes|
|Czy istnieje rozwiązanie do monitorowania kondycji?|Niewymagane|Stan agenta, dostarczone przez [Centrum administracyjne usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Czy użytkownicy uzyskują logowanie jednokrotne do zasobów w chmurze z urządzeń przyłączonych do domeny w sieci firmowej?|Tak, za pomocą [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)|Tak, za pomocą [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)|Yes|
|Jakie typy logowania są obsługiwane?|UserPrincipalName i hasło<br><br>Zintegrowane uwierzytelnianie Windows za pomocą [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Identyfikatora logowania alternatywnej](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName i hasło<br><br>Zintegrowane uwierzytelnianie Windows za pomocą [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Identyfikatora logowania alternatywnej](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName i hasło<br><br>Element sAMAccountName + hasła<br><br>Zintegrowane uwierzytelnianie systemu Windows<br><br>[Uwierzytelnianie certyfikatu i kart inteligentnych](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Identyfikatora logowania alternatywnej](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Czy Windows Hello dla firm obsługiwane?|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jakie są opcje uwierzytelniania wieloskładnikowego?|[Usługa Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Serwer usługi Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[Usługa MFA innych firm](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Jakie stanów konta użytkowników są obsługiwane?|Wyłączone konta<br>(maksymalnie 30 minut)|Wyłączone konta<br><br>Konto zablokowane<br><br>Hasło wygasło<br><br>Godziny logowania|Wyłączone konta<br><br>Konto zablokowane<br><br>Hasło wygasło<br><br>Godziny logowania|
|Jakie są opcje dostępu warunkowego?|[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Reguł oświadczeń usług AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blokuje starszych protokołów obsługiwane?|[Tak](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Tak](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Tak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Można dostosować logo, obrazu i opis na stronach logowania?|[Tak, za pomocą usługi Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Tak, za pomocą usługi Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Tak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jakie zaawansowane scenariusze są obsługiwane?|[Inteligentna blokada haseł](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Ujawnione poświadczenia raportów](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Inteligentna blokada haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|System wielu lokacjach uwierzytelniania o małych opóźnieniach<br><br>[Blokowanie ekstranetu w usługach AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integracja z systemami tożsamości innych firm](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Zalecenia
System tożsamości zapewnia dostęp użytkowników do aplikacji w chmurze i aplikacje line-of-business, migracja, a następnie udostępnić w chmurze. Aby zachować produktywność autoryzowanych użytkowników i nieupoważnione osoby spoza organizacji poufnych danych, uwierzytelnianie steruje dostępem do aplikacji.

Użyj lub włączanie synchronizacji skrótów haseł, niezależnie od metody uwierzytelniania, możesz wybrać z następujących powodów:

1. **Wysoka dostępność i odzyskiwanie awaryjne**. Uwierzytelniania przekazywanego i Federacji zależy od infrastruktury lokalnej. Dla uwierzytelniania przekazywanego śladu lokalnych obejmuje sprzęt serwera i sieci uwierzytelniania przekazywanego agenci wymagają. W przypadku Federacji zużycia w środowisku lokalnym jest jeszcze większym. Wymaga serwerów w sieci obwodowej do żądań uwierzytelniania serwera proxy i serwery federacyjne wewnętrznego. 

    Aby uniknąć pojedynczych punktów awarii, należy wdrożyć nadmiarowych serwerów. Następnie żądania uwierzytelniania zawsze będzie obsługiwany, jeśli dowolny składnik nie powiedzie się. Polegają również federacyjnych i uwierzytelniania przekazywanego na kontrolerach domeny, aby odpowiadać na żądania uwierzytelniania, które może również zakończyć się niepowodzeniem. Wiele z nich muszą konserwacji pozostanie w dobrej kondycji. Awarie są bardziej prawdopodobne, podczas obsługi nie jest planowane i wdrażane poprawnie. Unikaj przerw przy użyciu synchronizacji skrótów haseł, ponieważ usługi uwierzytelniania w chmurze usługi Microsoft Azure AD rośnie globalnie razem i jest zawsze dostępny.

2. **W środowisku lokalnym przetrwania awarii**.  Skutków awarii w środowisku lokalnym, ze względu na ataku cybernetycznego lub po awarii mogą być istotne, od marki reputational szkody sparaliżowanych organizacji radzenia sobie z ataku. Niedawno w wielu organizacjach były ofiarami ataków złośliwego oprogramowania, włącznie z docelowym przed oprogramowaniem wymuszającym okup, który spowodował swoje serwery w środowisku lokalnym przejść w dół. Microsoft pomaga klientom pracę z tego rodzaju ataki, widzi dwie kategorie organizacji:

   * Organizacje, które wcześniej włączona synchronizacja skrótów haseł, zmienić ich metodę uwierzytelniania używaną synchronizacji skrótów haseł. Zostały one online w ciągu kilku godzin. Korzystając z dostępu do poczty e-mail za pośrednictwem usługi Office 365, jego pracownicy współpracowali do rozwiązywania problemów i dostępu do innych obciążeń opartych na chmurze.

   * Organizacje, które nie zostały wcześniej Włączanie synchronizacji skrótów haseł było odwołać się do niezaufanych zewnętrzny adres e-mail systemy komunikacji i rozwiązywanie problemów. W takich przypadkach zajęło ich tygodniach lub więcej, aby zostać uruchomione ponownie.

3. **Ochrona tożsamości**. Jednym z najlepszych sposobów ochrony użytkowników w chmurze jest usługi Azure AD Identity Protection. Microsoft stale skanuje internetowego dla użytkownika i hasło zawiera listę, nieupoważnione osoby sprzedaży i udostępnić go w sieci web ciemny. Usługa Azure AD umożliwia Sprawdź nazwy użytkowników i haseł w organizacji naruszone te informacje. Dlatego bardzo ważne jest włączanie synchronizacji skrótów haseł, niezależnie od tego, jakie metody uwierzytelniania, możesz korzystać, które są Sfederowane lub uwierzytelniania przekazywanego. Ujawnione poświadczenia są prezentowane w postaci raportu. Te informacje służą do blokowania lub wymusić użytkownikom zmiany swoich haseł, gdy użytkownik próbuje zalogować się przy użyciu hasła ujawnione.

Wreszcie, zgodnie z opisem w [firmy Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), firma Microsoft ma zestaw najbardziej kompleksowe funkcje zarządzania tożsamościami i dostępem. Uchwyty Microsoft [450 MLD uwierzytelnień](https://www.microsoft.com/en-us/security/intelligence-report) co miesiąc, aby uzyskać dostęp do tysięcy aplikacji SaaS, takich jak Office 365 z niemal każdego urządzenia. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule opisano różne opcje uwierzytelniania, które w organizacji można skonfigurować i wdrożyć do obsługi dostępu do aplikacji w chmurze. Aby spełnić różne, zabezpieczeń, wymagania biznesowe i techniczne, organizacje mogą wybrać między synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. 

Należy wziąć pod uwagę każdej metody uwierzytelniania. Czy starań, aby wdrożyć rozwiązanie i środowisko procesu logowania, adresów Twoje wymagania biznesowe? Należy ocenić, czy Twoja organizacja potrzebuje zaawansowane scenariusze i funkcje ciągłości działania metod uwierzytelniania. Na koniec oceń zagadnienia dotyczące metod uwierzytelniania. Czy któryś z nich uniemożliwić wdrażanie wybranej?

## <a name="next-steps"></a>Kolejne kroki

W dzisiejszym świecie zagrożeń są obecne 24 godziny na dobę i pochodzić z dowolnego miejsca. Implementowanie poprawnej metody uwierzytelniania, a wówczas eliminowanie zagrożeń bezpieczeństwa i ochrony tożsamości.

[Rozpoczynanie pracy](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) z usługą Azure AD i wdróż rozwiązanie uwierzytelniania odpowiednie dla Twojej organizacji.

Jeśli myślisz o migracji z federacyjnego uwierzytelniania w chmurze, Dowiedz się więcej o [zmiana metody logowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Aby ułatwić planowanie i implementowanie migracji, należy użyć [planów wdrożenia tych projektów](http://aka.ms/deploymentplans).
