---
title: Wybierz metodę uwierzytelniania prawo do rozwiązania tożsamości hybrydowej usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten przewodnik pomaga naczelnych, dyrektorzy działu informatyki CISOs, architektów tożsamości główny, architektów przedsiębiorstwa i zabezpieczeń i IT inne osoby podejmujące decyzje odpowiedzialny za wyboru metody uwierzytelniania dla ich rozwiązania z tożsamością hybrydową usługi Azure AD w średnich i dużych organizacji.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 062b5e48cfba5de64aa11f79629e82645df87f96
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809264"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wybierz metodę uwierzytelniania prawo do rozwiązania tożsamości hybrydowej usługi Azure Active Directory 

W tym artykule rozpoczyna się szereg artykuły, które pomagają organizacjom implementacji kompletnego rozwiązania tożsamości hybrydowej usługi Azure Active Directory (Azure AD). To rozwiązanie zostało przedstawione jako [hybrydowego tożsamości cyfrowych przekształcania Framework](https://aka.ms/aadframework). Obejmuje on wyników biznesowych i cele organizacji można skupić się na do implementowania rozwiązania z tożsamością hybrydową niezawodne i bezpieczne. 

Pierwszy wyników biznesowych platformy wskazuje wymagań organizacji w celu zabezpieczenia procesu uwierzytelniania, gdy użytkownicy uzyskują dostęp do aplikacji w chmurze. Pierwszy celem biznesowych w wyniku zabezpieczonych firm uwierzytelniania jest użytkownikom zalogować się do aplikacji w chmurze przy użyciu ich lokalnych użytkowników i hasła. Ten proces logowania się i jak użytkownicy są uwierzytelniani, umożliwiają wszystko w chmurze.

Wybieranie metody poprawnego uwierzytelnienia jest pierwszym znaczenie dla organizacji chce przenieść swoje aplikacje w chmurze. Nie wykonuj tej decyzji przed dokonaniem tej zmiany, z następujących powodów:

1. Jest to pierwsza decyzja dla organizacji, która chce przenieść do chmury. 

2. Metoda uwierzytelniania jest składnikiem krytycznym obecności organizacji w chmurze. Kontroluje dostęp do wszystkich danych w chmurze i zasobów.

3. Jest foundation wszystkich zaawansowanych zabezpieczeń i funkcji środowiska użytkownika w usłudze Azure AD.

4. Metoda uwierzytelniania jest trudne zmienić po jego wdrożeniu.

Tożsamość jest nowa płaszczyzna kontroli zabezpieczeń IT. Dlatego uwierzytelnianie jest ochrona dostępu do organizacji do nowego środowiska chmury. Organizacje wymagają płaszczyzna kontroli tożsamości, ich ich zabezpieczeń i przechowuje przed intruzami aplikacji w chmurze.

### <a name="out-of-scope"></a>Poza zakresem
Organizacje, które nie mają istniejących rozmiaru katalogu lokalnego nie ma fokus w tym artykule. Zazwyczaj te firmy tworzenia tożsamości tylko w chmurze, która nie wymaga rozwiązania z tożsamością hybrydową. Tożsamości tylko w chmurze istnieje wyłącznie w chmurze i nie są skojarzone z odpowiedniego tożsamości użytkownika lokalnego.

## <a name="authentication-methods"></a>Metody uwierzytelniania
Gdy rozwiązania z tożsamością hybrydową usługi Azure AD jest nowe płaszczyzny kontroli, uwierzytelnianie jest podstawą dostęp do chmury. Wybieranie metody poprawnego uwierzytelnienia jest kluczową rolę pierwszej decyzji dotyczących konfigurowania rozwiązania z tożsamością hybrydową usługi Azure AD. Implementuje metodę uwierzytelniania, która jest konfigurowana przy użyciu usługi Azure AD Connect, który udostępnia również użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącej infrastruktury, złożoność i kosztów wdrożenia wybór. Czynniki te są różne dla każdej organizacji i może ulec zmianie. 

Usługi Azure AD obsługuje następujące metody uwierzytelniania dla hybrydowych rozwiązań tożsamości.

### <a name="cloud-authentication"></a>Uwierzytelnianie w chmurze
Po wybraniu tej metody uwierzytelniania usługi Azure AD obsługuje procesu logowania użytkowników. W połączeniu z bezproblemowe logowanie jednokrotne (SSO), można logowania użytkownika do aplikacji w chmurze bez konieczności ponownego wprowadzania poświadczeń. Z uwierzytelniania w chmurze można wybrać jeden z dwóch opcji: 

**Synchronizacji skrótów haseł w usłudze Azure AD**. Najprostszym sposobem, aby włączyć uwierzytelnianie dla obiektów katalogu lokalnego w usłudze Azure AD. Użytkownicy mogą używać tej samej nazwy użytkownika i hasła używanego lokalnymi bez konieczności wdrażania wszelkie dodatkowe infrastruktury. Niektóre funkcje premium usługi Azure AD, takich jak Identity Protection wymaga synchronizacji skrótu hasła dla niezależnie od tego, która metoda uwierzytelniania należy wybrać.

> [!NOTE] 
> Hasła nigdy nie są przechowywane w postaci zwykłego tekstu lub zaszyfrowanych za pomocą algorytmu odwracalnego w usłudze Azure AD. Aby uzyskać więcej informacji na rzeczywisty proces synchronizacji skrótów haseł, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Azure AD przekazywanego uwierzytelniania**. Udostępnia proste hasło weryfikacji dla usługi uwierzytelniania usługi Azure AD przy użyciu agenta oprogramowania, który jest uruchamiany na jeden lub więcej serwerów lokalnych. Serwery weryfikowania użytkowników bezpośrednio z lokalnej usługi Active Directory, który zapewnia, że sprawdzanie poprawności hasła nie jest realizowane w chmurze. 

Przedsiębiorstwa z wymaganie dotyczące zabezpieczeń, aby wymusić natychmiastowe użytkownika lokalnego konta stanów, zasady haseł, a godziny logowania może użyć tej metody uwierzytelniania. Uzyskać więcej informacji o procesie uwierzytelniania przekazywanego rzeczywiste, zobacz [logowania użytkownika przy użyciu przekazywanego uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne
Po wybraniu tej metody uwierzytelniania usługi Azure AD ręce wyłączone procesu uwierzytelniania do systemu oddzielne uwierzytelniania zaufanych, takich jak lokalnymi Active Directory Federation Services (AD FS), do sprawdzania poprawności hasła użytkownika.

System uwierzytelniania może zapewnić dodatkowe zaawansowanego uwierzytelniania wymogi. Przykłady są uwierzytelnianie za pomocą karty inteligentnej lub uwierzytelnianie wieloskładnikowe innych firm. Aby uzyskać więcej informacji, zobacz [wdrażania usług federacyjnych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Poniższa sekcja pomoże Ci zdecyduj, która metoda uwierzytelniania jest odpowiednie dla Ciebie za pomocą drzewa decyzyjnego. Pomaga ustalić, czy do wdrożenia w chmurze lub uwierzytelnianie federacyjne dla rozwiązania tożsamości hybrydowej usługi Azure AD.

## <a name="decision-tree"></a>Drzewo decyzyjne

![Drzewo decyzyjne uwierzytelniania w usłudze Azure AD](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Opis szczegółowych zagadnień

### <a name="cloud-authentication-password-hash-synchronization"></a>Uwierzytelnianie w chmurze: synchronizacji skrótu hasła

* **Nakładu**. Synchronizacji skrótu hasła wymaga co najmniej dotyczące wdrażania, obsługi i infrastruktury.  Ten poziom nakładu pracy zwykle ma zastosowanie do organizacji, które potrzebują tylko użytkownikom logować się do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD. Po włączeniu, synchronizacji skrótu hasła jest częścią procesu synchronizacji Azure AD Connect i uruchamia co dwie minuty.

* **Środowisko użytkownika**. Aby zwiększyć środowiska logowania użytkowników, wdrożyć bezproblemowe rejestracji Jednokrotnej z synchronizacji skrótu hasła. Bezproblemowe logowanie Jednokrotne eliminuje niepotrzebne monitów, gdy użytkownicy są zalogowani.

* **Zaawansowanych scenariuszy**. Jeśli organizacjami, istnieje możliwość wgląd w dane dotyczące tożsamości za pomocą raportów usługi Azure AD Identity Protection. Przykładem jest jako raport ujawnione poświadczenia. Usługi Windows Hello dla firm jest inną opcją, która ma [określone wymagania, korzystając z synchronizacji skrótu hasła](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego z synchronizacji skrótu hasła muszą używać uwierzytelniania wieloskładnikowego usługi Azure AD. Te organizacje nie można użyć metody uwierzytelniania wieloskładnikowego innych firm lub lokalne.

* **Ciągłość prowadzenia działalności biznesowej**. Używanie synchronizacji skrótu hasła uwierzytelniania w chmurze jest wysoce dostępna jako usługa w chmurze, która może obsłużyć wszystkie centrach danych firmy Microsoft. Aby upewnić się, że synchronizacja skrótów haseł nie przestaną działać przez dłuższy czas, należy wdrożyć drugiego serwera Azure AD Connect w tryb wstrzymania konfiguracji przejściowy.

* **Zagadnienia dotyczące**. Obecnie synchronizacji skrótu hasła nie natychmiastowym wymuszeniu zmiany stanów konta lokalnego. W takiej sytuacji użytkownik ma dostęp do aplikacji w chmurze, dopóki stan konta użytkowników są synchronizowane z usługą Azure AD. Organizacje mogą chcieć obejść to ograniczenie, uruchamiając nowy cykl synchronizacji po Administratorzy zbiorcze aktualizacje stanu konta użytkowników lokalnych. Przykładem jest wyłączenie konta.

> [!NOTE]
> Hasło wygasło i stanów zablokowanego konta aktualnie nie są zsynchronizowane z usługą Azure AD z programem Azure AD Connect. 

Zapoznaj się [zaimplementowanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) dla procedury wdrażania.

### <a name="cloud-authentication-pass-through-authentication"></a>Uwierzytelnianie w chmurze: przekazywanego uwierzytelniania  

* **Nakładu**. Dla uwierzytelniania przekazywanego, potrzebujesz co najmniej jednego (zalecamy trzy) lekkie agentów zainstalowanych na istniejących serwerach. Agenci musi mieć dostęp do sieci lokalnej usług domenowych Active Directory, łącznie z lokalnymi kontrolerów domeny usługi AD. Potrzebują dostępu do kontrolerów domeny i wychodzący dostęp do Internetu. Z tego powodu nie jest obsługiwane wdrożenia agentów w sieci obwodowej. 

    Uwierzytelniania przekazywanego wymaga sieci nieograniczonego dostępu do kontrolerów domeny. Cały ruch sieciowy jest szyfrowany i ograniczona do uwierzytelniania żądań. Aby uzyskać więcej informacji na ten proces, zobacz [nowości zabezpieczeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) na uwierzytelnianie przekazywane.

* **Środowisko użytkownika**. Aby zwiększyć środowiska logowania użytkowników, wdrożyć bezproblemowe logowania jednokrotnego przy użyciu przekazywanego uwierzytelniania. Bezproblemowe logowanie Jednokrotne eliminuje niepotrzebne monity po zalogowaniu użytkownicy.

* **Zaawansowanych scenariuszy**. Uwierzytelniania przekazywanego wymusza zasady konta lokalnego podczas logowania. Na przykład odmowa dostępu, gdy konto użytkownika lokalnego, stan jest wyłączony, zablokowane, lub [hasło wygasło](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) lub znajduje się poza godzinami, gdy użytkownik może się zalogować. 

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego przy użyciu przekazywanego uwierzytelniania należy użyć usługi Azure Multi-Factor Authentication (MFA). Te organizacje nie można użyć metody uwierzytelniania wieloskładnikowego innych firm lub lokalne. Zaawansowane funkcje wymagają wdrożeniu synchronizacji skrótu hasła lub nie możesz wybrać uwierzytelnianie przekazywane. Przykładem jest raport ujawnione poświadczenia tożsamości ochrony.

* **Ciągłość prowadzenia działalności biznesowej**. Zaleca się wdrożenie dwóch agentów o dodatkowe uwierzytelnianie. Te dodatkowe są oprócz pierwszego agenta na serwerze programu Azure AD Connect. To dodatkowe wdrożenie zapewnia wysoką dostępność żądania uwierzytelnienia. Jeśli masz trzech agentów wdrożonych jeden agent może nadal się niepowodzeniem po innego agenta jest nieczynny. 

    Brak kolejna korzyść związana z wdrażaniem synchronizacji skrótu hasła oprócz uwierzytelnianie przekazywane. Działa jako metodę uwierzytelniania kopii zapasowych, gdy metoda uwierzytelniania podstawowego nie jest już dostępny.

* **Zagadnienia dotyczące**. Można na przykład synchronizacji skrótu hasła jako metody uwierzytelniania kopii zapasowej dla uwierzytelniania przekazywanego i agentów nie można sprawdzić poprawności poświadczeń użytkownika. Następnie trybu failover do synchronizacji skrótu hasła nie jest realizowane automatycznie. Ręcznie przełączać metodę logowania jednokrotnego przy użyciu usługi Azure AD Connect. 

    Uwierzytelniania przekazywanego obsługuje tylko aplikacje w chmurze, korzystających z nowoczesnego uwierzytelniania oraz określonych protokołów usługi Exchange Online. Niektóre protokoły są ActiveSync POP3 i IMAP4. Na przykład Microsoft Office 2013 i nowsze Obsługa nowoczesnego uwierzytelniania, ale starszych wersjach nie. Aby uzyskać więcej informacji na temat obsługi aplikacji pakietu Office, zobacz [nowoczesne uwierzytelnianie usługi Office 365 aktualizacji](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Dla innych kwestii dotyczących uwierzytelniania przekazywanego, w tym alternatywny identyfikator pomocy technicznej, zobacz [— często zadawane pytania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Zapoznaj się [Implementowanie uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) dla procedury wdrażania.

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne

* **Nakładu**. System uwierzytelniania federacyjnego zależy od zaufanego zewnętrznego systemu do uwierzytelniania użytkowników. Niektóre firmy chcesz ponownie użyć dotychczasowych inwestycji federacyjnych systemu z ich rozwiązania z tożsamością hybrydową usługi Azure AD. Konserwacji i zarządzania federacyjnych systemu wypada poza kontrolą usługi Azure AD. To organizacji za pomocą federacyjnych systemu i upewnij się, że jest bezpieczne wdrożona i może obsłużyć obciążenia uwierzytelniania. 

* **Środowisko użytkownika**. Środowisko użytkownika uwierzytelniania federacyjnego zależy od implementacji funkcji, topologii i konfiguracji kolektywu serwerów federacyjnych. Niektóre organizacje muszą także możliwość dostosowania i skonfigurować dostęp do farmy federacyjnych do ich potrzebami zabezpieczeń. Na przykład istnieje możliwość skonfigurowania wewnętrznie połączonych użytkowników i urządzeń do logowania użytkowników automatycznie, bez wyświetlania monitu o poświadczenia. Ta konfiguracja działa, ponieważ one już zalogowany na ich urządzeniach. Jeśli to konieczne, niektóre funkcje zaawansowane zabezpieczenia utrudnić procesu logowania użytkowników.

* **Zaawansowanych scenariuszy**. Rozwiązanie uwierzytelniania federacyjnego jest zwykle wymagany, gdy klienci mają wymaganie uwierzytelniania usługi Azure AD obsługuje natywnie. Zobacz szczegółowe informacje ułatwiające [wybierz opcję prawo logowania](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Należy wziąć pod uwagę następujące typowe wymagania:

    * Uwierzytelnianie, która wymaga kart inteligentnych lub certyfikatów.
    * Serwery lokalne, MFA lub wieloskładnikowej dostawców.
    * Uwierzytelnianie przy użyciu rozwiązań uwierzytelniania innej firmy. Zobacz [listę zgodności federacyjnych usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Zaloguj się, że wymaga sAMAccountName, na przykład domena azwa_użytkownika, zamiast główną nazwę użytkownika (UPN), na przykład user@domain.com.

* **Ciągłość prowadzenia działalności biznesowej**. Systemy federacyjnych zwykle wymagają tablicą równoważenia obciążenia serwerów, znane jako farmy. Tej farmy został skonfigurowany w sieci wewnętrznej i topologii sieci obwodowej, aby zapewnić wysoką dostępność dla żądań uwierzytelniania.

    Wdrażanie synchronizacji skrótu hasła oraz uwierzytelniania federacyjnego, jako metody uwierzytelniania kopii zapasowej, gdy metoda uwierzytelniania podstawowego nie jest już dostępny. Przykładem jest w przypadku serwerów lokalnych nie są dostępne. W niektórych organizacjach dużych przedsiębiorstw wymagają federacyjnego rozwiązanie do obsługi wielu punktów wejściowych Internet skonfigurowano geograficznie DNS dla żądań uwierzytelniania małe opóźnienia.

* **Zagadnienia dotyczące**. Systemy federacyjnych zwykle wymagają bardziej znaczących inwestycji w infrastrukturę lokalną. Większość organizacji wybierz tę opcję, jeśli mają inwestycji Federacji lokalnego. I, jeśli jest wymagane silne biznesowych do używania dostawcy tożsamości jednym. Federacja znajduje się bardziej złożone do obsługi i rozwiązywania problemów w porównaniu do rozwiązań uwierzytelniania w chmurze.

Domeny nonroutable, którego nie można zweryfikować w usłudze Azure AD wymaga dodatkowej konfiguracji do zaimplementowania użytkownika identyfikator logowania. To wymaganie jest określany jako alternatywnego Identyfikatora pomocy technicznej. Zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) dla ograniczeń i wymagań. Jeśli chcesz użyć dostawcy uwierzytelniania wieloskładnikowego innych firm z Federacją, upewnij się, że dostawca obsługuje WS-Trust, aby zezwolić urządzeniom na usługi Azure AD join.

Zapoznaj się [wdrażania serwerów federacyjnych](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) dla procedury wdrażania.

> [!NOTE] 
> Podczas wdrażania rozwiązania tożsamości hybrydowej usługi Azure AD, muszą implementować jeden z obsługiwanych topologii programu Azure AD Connect. Dowiedz się więcej o obsługiwane i nieobsługiwane konfiguracje w [topologii dla usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramów architektury

Poniższych diagramach omówiono składniki wysokiego poziomu architektury wymagane dla każdej z metod uwierzytelniania używanych wraz z rozwiązaniem tożsamości hybrydowej usługi Azure AD. Zawierają informacje ułatwiające porównania różnic między rozwiązania.

* Prostota rozwiązania synchronizacji skrótu hasła:

    ![Hybrydowe usługi Azure AD identity z synchronizacji skrótu hasła](media/azure-ad/azure-ad-authn-image2.png)

* Wymagania agenta uwierzytelnianie przekazywane:

    ![Tożsamość hybrydowa platformy Azure AD przy użyciu przekazywanego uwierzytelniania](media/azure-ad/azure-ad-authn-image3.png)

* Składniki wymagane w Federacji w sieci obwodowej i sieci wewnętrznej organizacji:

    ![Hybrydowe usługi Azure AD identity z uwierzytelniania federacyjnego](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porównanie metod

|Kwestia|Synchronizacja skrótów haseł i bezproblemowego logowania jednokrotnego|Uwierzytelniania przekazywanego + bezproblemowe logowanie Jednokrotne|Federacja z usługami AD FS|
|:-----|:-----|:-----|:-----|
|Gdy jest wykonywana uwierzytelniania|W chmurze|W chmurze po wymianie weryfikacji bezpieczne hasło, z agentem uwierzytelniania lokalnego|Lokalnie|
|Jakie są wymagania dotyczące serwera lokalnego poza inicjowania obsługi administracyjnej systemu: Azure AD Connect?|Brak|Jeden serwer dla każdego dodatkowego uwierzytelniania agenta|Co najmniej dwa serwery usług AD FS<br><br>Co najmniej dwa serwery proxy w sieci obwodowej/DMZ|
|Jakie są wymagania dotyczące Internet lokalnych i sieci poza inicjowania obsługi administracyjnej systemu?|Brak|[Ruch wychodzący do Internetu](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) z serwerów z agentów uwierzytelniania|[Dostęp do Internetu przychodzące](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) do serwerów proxy w obwodzie<br><br>Dostępu do sieci dla ruchu przychodzącego na serwerach usług AD FS z serwerów proxy w obwodzie<br><br>Równoważenie obciążenia sieciowego|
|Istnieje już certyfikat SSL jest wymagany?|Nie|Nie|Yes|
|Dostępne jest rozwiązanie monitorujące, kondycję?|Niewymagane|Stan agenta podał [Centrum administracyjnego usługi Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Czy użytkownicy uzyskują logowanie jednokrotne do zasobów w chmurze z urządzeń przyłączonych do domeny w sieci firmowej?|Tak [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Tak [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Yes|
|Jakie typy logowania są obsługiwane?|UserPrincipalName + hasła<br><br>Zintegrowane uwierzytelnianie systemu Windows przy użyciu [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + hasła<br><br>Zintegrowane uwierzytelnianie systemu Windows przy użyciu [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + hasła<br><br>sAMAccountName + hasła<br><br>Zintegrowane uwierzytelnianie systemu Windows<br><br>[Uwierzytelnianie certyfikatu i kart inteligentnych](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|To jest Windows Hello dla firm obsługiwane?|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jakie są opcje uwierzytelnianie wieloskładnikowe?|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br><br>[Serwer systemu Azure MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA innych firm](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Jakie stanu konta użytkowników są obsługiwane?|Wyłączone konta<br>(maksymalnie 30 minut)|Wyłączone konta<br><br>Konto zablokowane<br><br>Hasło wygasło<br><br>Godziny korzystania z logowania|Wyłączone konta<br><br>Konto zablokowane<br><br>Hasło wygasło<br><br>Godziny korzystania z logowania|
|Jakie są opcje dostępu warunkowego?|[Azure AD dostęp warunkowy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD dostęp warunkowy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD dostęp warunkowy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Reguły oświadczeń usług AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blokuje starszych protokołów obsługiwane?|[Tak](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Tak](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Tak](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Można dostosować logo, obrazu i opis na stronach logowania?|[Tak, za pomocą usługi Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Tak, za pomocą usługi Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Tak](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jakie zaawansowane scenariusze są obsługiwane?|[Blokady inteligentnej hasła](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br><br>[Ujawnione poświadczenia raportów](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Blokady inteligentnej hasła](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|System wielooddziałowości uwierzytelniania małych opóźnieniach<br><br>[Blokowanie ekstranetu w usługach AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integracja z systemów tożsamości innych firm](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Zalecenia
Tożsamość systemu zapewnia dostęp użytkowników do aplikacji w chmurze i aplikacji biznesowych z migracji i udostępnić w chmurze. Aby zachować produktywność autoryzowanych użytkowników i nieupoważnione osoby spoza organizacji poufnych danych, uwierzytelnianie kontroluje dostęp do aplikacji.

Użyj lub włączanie synchronizacji skrótów haseł dla jednego z tych metod uwierzytelniania, możesz wybrać z następujących powodów:

1. **Wysoka dostępność i odzyskiwanie po awarii**. Uwierzytelniania przekazywanego i federacyjnego korzystają z infrastruktury lokalnej. Dla uwierzytelniania przekazywanego rozmiaru lokalnej obejmuje sprzętu serwera i sieci uwierzytelniania przekazywanego wymagają agentów. Czy dla Federacji nakłady zasobów lokalnych jest jeszcze większym. Wymaga to serwery w sieci obwodowej, aby żądania uwierzytelnienia serwera proxy i serwery federacyjne wewnętrznego. 

    Aby uniknąć pojedynczych punktów awarii, należy wdrożyć serwery nadmiarowe. Następnie żądania uwierzytelniania zawsze będzie obsługiwany, jeśli dowolny składnik nie powiedzie się. Zarówno uwierzytelniania przekazywanego i Federacji również polegać na kontrolerach domeny odpowiada na żądania uwierzytelniania, które można również zakończyć się niepowodzeniem. Wiele z tych składników należy konserwacji pozostanie w dobrej kondycji. Awarie są bardziej prawdopodobne podczas obsługi nie jest planowane i poprawnie zaimplementowana. Za pomocą synchronizacji skrótu hasła, ponieważ usługa uwierzytelniania w chmurze usługi Microsoft Azure AD skaluje globalnie i są zawsze dostępne, aby uniknąć awarii.

2. **Po awarii lokalnymi**.  Konsekwencje lokalnymi awarii z powodu przez atak lub po awarii może być istotne, począwszy od marki reputational uszkodzenia sparaliżowanych organizacji mógł postępowania w przypadku ataku. W wielu organizacjach były ostatnio, ofiary atakami złośliwego oprogramowania, w tym ransomware docelowych, które spowodowało ich serwery lokalne przejść w dół. Firma Microsoft zapewnia klientom postępowania w przypadku tego rodzaju ataki, widzi dwie kategorie organizacji:

   * Organizacje, które wcześniej włączony synchronizacja skrótów haseł zmienić ich metodę uwierzytelniania używaną synchronizacji skrótu hasła. Zostały one trybu online w ciągu kilku godzin. Za pomocą dostępu do poczty e-mail za pośrednictwem usługi Office 365, Ci poszło Rozwiązywanie problemów i dostępu do innych obciążeń oparte na chmurze.

   * Organizacje, które nie zostały wcześniej włączyć synchronizacji skrótu hasła było odwołać się do niezaufanych zewnętrznych e-mail systemy komunikacji i rozwiązywanie problemów. W takich przypadkach zajęło ich tygodniach lub więcej, aby zostać uruchomione ponownie.

3. **Ochronę tożsamości**. Jest jednym z najlepszych sposobów ochrony użytkowników w chmurze Azure AD Identity Protection. Microsoft stale skanuje Internet dla użytkownika i hasło listy nieupoważnione sprzedawać oraz i udostępnić w sieci web ciemny. Usługi Azure AD można dzięki tym informacjom można zweryfikować nazwy użytkowników i hasłami w organizacji naruszone. Dlatego bardzo ważne jest włączanie synchronizacji skrótów haseł, niezależnie od tego, jakiego używasz, czy, który ma być federacyjne metody uwierzytelniania lub uwierzytelnianie przekazywane. Ujawnione poświadczenia są prezentowane w postaci raportu. Dzięki tym informacjom można zablokować lub wymusić użytkownikom na zmianę ich haseł, gdy próbują zalogować się przy użyciu hasła ujawnione.

Ponadto stosownie do [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), firma Microsoft oferuje zestaw najbardziej kompletne funkcje zarządzania tożsamościami i dostępem. Uchwyty Microsoft [żądania uwierzytelniania MLD 450](https://www.microsoft.com/en-us/security/intelligence-report) co miesiąc, w celu zapewnienia dostępu do tysięcy aplikacji SaaS, takich jak usługi Office 365 z niemal dowolnego urządzenia. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule opisano różne opcje uwierzytelniania, które organizacji można skonfigurować i wdrożyć w celu obsługi dostępu do aplikacji w chmurze. Aby spełnić różne business, zabezpieczeń i wymagania techniczne, organizacje mogą wybrać między synchronizacji skrótu hasła, uwierzytelniania przekazywanego i federacji. 

Należy wziąć pod uwagę każda metoda uwierzytelniania. Czy starań, aby wdrożyć rozwiązanie i środowisko użytkownika procesu logowania, adresów wymagania biznesowe? Sprawdzić, czy organizacja wymaga zaawansowanych scenariuszy i funkcjach ciągłości biznesowej metod uwierzytelniania. Na koniec oceń zagadnienia dotyczące metod uwierzytelniania. Czy z nich uniemożliwiają dokonanego Implementowanie?

## <a name="next-steps"></a>Kolejne kroki

W świecie współczesnych zagrożeń są obecne 24 godziny na dobę i pochodzić z wszędzie. Zaimplementuj metodę poprawnego uwierzytelnienia i zostanie eliminowania zabezpieczeń i ochrony Twojej tożsamości.

[Rozpoczynanie pracy](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) z usługą Azure AD i wdrożyć rozwiązanie prawym uwierzytelniania dla Twojej organizacji.

Jeśli myślisz o migracji z federacyjnych do uwierzytelniania w chmurze, Dowiedz się więcej o [zmiana metody logowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Aby ułatwić planowanie i wdrażanie migracji, należy użyć [planów wdrożenia tych projektów](http://aka.ms/deploymentplans).
