---
title: Wybierz metodę uwierzytelniania prawo do rozwiązania tożsamości hybrydowej usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten przewodnik jest przeznaczony dla ułatwienia naczelnych, dyrektorzy działu informatyki, CISOs, architektów tożsamości główny, architektów przedsiębiorstwa i zabezpieczeń i IT inne osoby podejmujące decyzje odpowiedzialny za wyboru metody uwierzytelniania dla ich rozwiązania z tożsamością hybrydową usługi Azure AD w średnich i dużych organizacje.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wybieranie metody uwierzytelniania prawo do rozwiązania tożsamości hybrydowej usługi Azure Active Directory 

W tym artykule jest pierwszy z serii artykułów ułatwiające organizacjom implementuje pełnego rozwiązania z tożsamością hybrydową usługi Azure AD. Kompletna rozwiązania z tożsamością hybrydową usługi Azure AD został opisany jako hybrydowe tożsamości cyfrowych przekształcania Framework i obejmuje wyników biznesowych i cele organizacji skoncentrować się na upewnij się, że zostały one wdrożone tożsamość hybrydowa niezawodną i bezpieczną rozwiązanie. Pierwszy wyników biznesowych platformy wskazuje wymagań organizacji w celu zabezpieczenia procesu uwierzytelniania, gdy użytkownicy uzyskują dostęp do aplikacji w chmurze. Pierwszy celem biznesowych w wyniku zabezpieczonych firm uwierzytelniania jest przez użytkowników do logowania do aplikacji przy użyciu ich lokalnych użytkowników i haseł w chmurze. Ten proces logowania i jak uwierzytelniać użytkowników umożliwia wszystko w chmurze.

Wybieranie metody poprawnego uwierzytelnienia jest pierwszym znaczenie dla organizacji chce przenieść swoje aplikacje w chmurze. Ta decyzja nie powinien podjąć lekkim z następujących powodów:

1. Jest pierwsza decyzja dla organizacji, która pragnie przenosić do chmury. 

2. Metoda uwierzytelniania jest składnikiem krytycznym obecności organizacji w chmurze, kontrolowania dostępu do wszystkich danych w chmurze i zasobów.

3. Jest foundation wszystkich zaawansowanych zabezpieczeń i funkcji środowiska użytkownika w usłudze Azure AD.

4. Trudności podczas zmieniania raz zaimplementowano metody uwierzytelniania.

O tożsamości jako nowe płaszczyzny kontroli bezpieczeństwa informatycznego uwierzytelnianie jest ochrona dostępu do organizacji do nowego środowiska chmury. Organizacje powinny upewnij się, że płaszczyzny sterowania tożsamości sprawia, że ich silniejszych zabezpieczeń i Zachowaj przed intruzami aplikacji w chmurze.

### <a name="out-of-scope"></a>Poza zakresem

Organizacje, które nie mają istniejących rozmiaru katalogu lokalnego nie ma fokus w tym artykule. Zazwyczaj te firmy tworzenia tożsamości tylko w chmurze, która nie wymaga rozwiązania z tożsamością hybrydową. Tożsamości tylko w chmurze istnieje wyłącznie w chmurze i nie są skojarzone z odpowiedniego tożsamości użytkownika lokalnego.  

## <a name="choosing-the-right-authentication-method"></a>Wybieranie metody uwierzytelniania w prawo

Z usługi Azure AD rozwiązania z tożsamością hybrydową jako nowe płaszczyzny sterowania uwierzytelnianie jest podstawą dostęp do chmury. Wybieranie metody poprawnego uwierzytelnienia jest kluczową rolę pierwszej decyzji dotyczących konfigurowania rozwiązania z tożsamością hybrydową usługi Azure AD. Implementacja metody uwierzytelniania jest skonfigurowane za pomocą usługi Azure AD Connect, które również udostępniać użytkownikom w chmurze. 

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącej infrastruktury, złożoność i kosztów wdrożenia wybór. Czynniki te są różne dla każdej organizacji i może ulec zmianie. 

Usługi Azure AD obsługuje następujące metody uwierzytelniania dla hybrydowych rozwiązań tożsamości:

### <a name="cloud-authentication"></a>Uwierzytelnianie w chmurze
Po wybraniu tej metody uwierzytelniania usługi Azure AD obsługuje proces logowania dla użytkowników. W połączeniu z bezproblemowe logowanie jednokrotne (SSO), można logowania użytkownika do aplikacji w chmurze bez konieczności ponownego wprowadzania poświadczeń. Z uwierzytelniania w chmurze są dostępne dwie opcje: 

**Synchronizacja skrótów (PHS, Service który) hasło** — Najprostszym sposobem, aby włączyć uwierzytelnianie dla obiektów katalogu lokalnego w usłudze Azure AD. Synchronizacja skrótów haseł umożliwia użytkownikom używanie tej samej nazwy użytkownika i hasła używanego lokalnymi bez konieczności wdrażania wszelkie dodatkowe infrastruktury. Niektóre funkcje premium usługi Azure AD, takie jak Identity Protection wymaga synchronizacji skrótów haseł, niezależnie od tego, który wybrano metodę uwierzytelniania.

> [!NOTE] 
> Hasła nigdy nie są przechowywane w postaci zwykłego tekstu lub zaszyfrowanych za pomocą algorytmu odwracalnego w usłudze Azure AD. Aby uzyskać więcej informacji na rzeczywisty proces synchronizacji skrótów haseł, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Uwierzytelniania przekazywanego (PTA)** — udostępnia weryfikację prostych haseł, usługi uwierzytelniania usługi Azure AD przy użyciu agenta oprogramowania uruchomionego na jeden lub więcej serwerów lokalnych do weryfikowania użytkowników bezpośrednio z aktywnych lokalnych Katalog zapewnienie sprawdzenie poprawności hasła nie jest realizowane w chmurze. Stany firm z zabezpieczeń konieczności natychmiastowym wymuszeniu lokalnego konta użytkownika, zasady haseł i godziny logowania użyje tej metody uwierzytelniania. Uzyskać więcej informacji o procesie uwierzytelniania przekazywanego rzeczywiste, zobacz [logowania użytkownika z usługi Azure Active Directory, uwierzytelnianie przekazywane](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne
Po wybraniu tej metody uwierzytelniania usługi Azure AD ręce poza procesu uwierzytelniania do systemu oddzielne zaufanych uwierzytelniania, na przykład lokalnymi Active Directory Federation Services (AD FS) do sprawdzania poprawności hasła użytkownika. System uwierzytelniania zapewniają wymagania dodatkowego uwierzytelniania, na przykład uwierzytelnianie za pomocą karty inteligentnej lub uwierzytelnianie wieloskładnikowe innych firm. Aby uzyskać więcej informacji, zobacz [wdrażania usług federacyjnych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Poniższa sekcja pomoże Ci zdecydować, która metoda uwierzytelniania jest odpowiednie dla Ciebie przy użyciu drzewa decyzyjnego. Ułatwi to określanie, czy do wdrożenia w chmurze lub uwierzytelnianie federacyjne dla rozwiązania tożsamości hybrydowej usługi Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Drzewo decyzyjne uwierzytelniania w usłudze Azure AD

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Opis szczegółowych zagadnień na metody uwierzytelniania

### <a name="cloud-authentication-password-hash-sync"></a>Uwierzytelnianie w chmurze: synchronizacja skrótów haseł 

* **Nakładu:** synchronizacji skrótów haseł wymaga co najmniej działania dotyczące wdrażania, obsługi i infrastruktury dla organizacji, potrzebują tylko umożliwienie użytkownikom zalogować się do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD. Po włączeniu synchronizacji skrótów haseł jest częścią procesu synchronizacji Azure AD Connect i uruchamia co dwie minuty. 

* **Środowisko użytkownika:** zaleca się wdrożeniu organizacje bezproblemowe logowanie jednokrotne (SSO) z synchronizacji skrótów haseł, aby poprawić środowisko logowania użytkownika, unikając niepotrzebnego monity podpisany.

* **Zaawansowanych scenariuszy:** Jeśli organizacjami, istnieje możliwość wgląd w dane dotyczące tożsamości za pomocą usługi Azure AD Identity Protection raportów, takich jak raport ujawnione poświadczenia. Usługi Windows Hello dla firm jest inną opcją, która ma [określone wymagania, korzystając z synchronizacji skrótów haseł](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Organizacji wymagających uwierzytelniania wieloskładnikowego z synchronizacji skrótów haseł należy użyć uwierzytelniania wieloskładnikowego usługi Azure AD i nie można użyć innych firm lub lokalnymi metod uwierzytelniania wieloskładnikowego.

* **Ciągłość prowadzenia działalności biznesowej:** synchronizacji skrótów haseł jest z założenia wysokiej dostępności jako usługa w chmurze, która może obsłużyć wszystkie centrach danych firmy Microsoft. Zaleca się, że drugi serwer Azure AD Connect jest wdrażany w trybie rezerwy konfiguracji na potrzeby odzyskiwania po awarii przejściowym.

* **Kwestie do rozważenia:** synchronizacji skrótów haseł nie natychmiast wymusza zmiany stanów konta lokalnego obecnie. W takiej sytuacji użytkownik ma dostęp do aplikacji w chmurze, dopóki stan konta użytkowników są synchronizowane z usługą Azure AD. Aby obejść to ograniczenie organizacji, zaleca się nowy cykl synchronizacji jest aktywowana po Administratorzy zbiorcze aktualizacje stanu konta użytkowników lokalnych, takie jak wyłączenie konta. 

> [!NOTE] 
> Hasło wygasło i Konto zablokowane stanów nie są zsynchronizowane z usługą Azure AD z programem Azure AD Connect. 

Zapoznaj się [Implementowanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) dla procedury wdrażania.

### <a name="cloud-authentication-pass-through-authentication"></a>Uwierzytelnianie w chmurze: przekazywanego uwierzytelniania  

* **Nakładu pracy:** dla uwierzytelniania przekazywanego, potrzebna jest jedna lub więcej (trzy zalecane) lekkie agentów zainstalowanych na istniejących serwerów z dostępem do sieci lokalnej usług domenowych Active Directory, łącznie z dostępem do lokalnej domeny usługi AD kontrolery. Agenci muszą wychodzący dostęp do Internetu i mają dostęp do kontrolerów domeny. Z tego powodu jest nieobsługiwany do wdrożenia agentów w sieci obwodowej, ponieważ wymaga ona sieci nieograniczonego dostępu do kontrolerów domeny. Cały ruch sieciowy jest szyfrowany i ograniczona do uwierzytelniania żądań. Aby uzyskać więcej informacji na ten proces, zobacz [nowości zabezpieczeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) na uwierzytelnianie przekazywane.

* **Środowisko użytkownika:** zaleca się wdrożenia bezproblemowe logowanie jednokrotne przy użyciu uwierzytelniania przekazywanego zwiększające użytkownika logowania, unikając niepotrzebnego monity podpisany organizacji.

* **Zaawansowanych scenariuszy:** uwierzytelniania przekazywanego wymusza zasady konta lokalnego podczas logowania. Na przykład odmowa dostępu, gdy konto użytkownika lokalnego, stan jest wyłączony, zablokowane, [hasło wygasło](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication ) lub wypada poza użytkownika dozwolone godziny logowania.  Organizacje, wymagając uwierzytelniania wieloskładnikowego przy użyciu przekazywanego uwierzytelniania należy użyć usługi Azure Multi-Factor Authentication (MFA) i nie można użyć metody uwierzytelniania wieloskładnikowego innych firm lub lokalne. Zaawansowane funkcje, takie jak raport ujawnione poświadczenia tożsamości ochrony wymaga synchronizacji skrótów haseł jest wdrożenia niezależnie od wybranie opcji uwierzytelnianie przekazywane.

* **Ciągłość prowadzenia działalności biznesowej:** zaleca się wdrożenie dwóch agentów dodatkowe uwierzytelnianie, oprócz pierwszego agenta na serwerze Azure AD Connect, aby zapewnić wysoką dostępność żądania uwierzytelnienia. Jeśli masz trzech agentów wdrożonych jeden agent może nadal się niepowodzeniem po innego agenta jest nieczynny. Inną zaletą Wdrażanie synchronizacji skrótów haseł oprócz uwierzytelnianie przekazywane jest może działać jako metody uwierzytelniania kopii zapasowych, gdy metoda uwierzytelniania podstawowego nie jest już dostępne, na przykład w przypadku serwerów lokalnych nie są dostępne.

* **Kwestie do rozważenia:** Jeśli używasz synchronizacji skrótów haseł jako metody uwierzytelniania kopii zapasowej dla uwierzytelniania przekazywanego i agentów nie można sprawdzić poprawności poświadczeń użytkownika, a następnie tryb failover, aby synchronizacja skrótów haseł nie odbywa się automatycznie. Musisz przełączyć metodę logowania jednokrotnego ręcznie za pomocą usługi Azure AD Connect. Uwierzytelniania przekazywanego obsługuje tylko aplikacje w chmurze, korzystających z nowoczesnego uwierzytelniania oraz określonych protokołów usługi Exchange Online, takie jak ActiveSync, POP3 i IMAP4. Na przykład [Microsoft Office 2013 i nowsze obsługuje nowoczesnego uwierzytelniania, ale nie jego wcześniejszych wersji](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) na więcej informacji Obsługa aplikacji pakietu Office. Zobacz [— często zadawane pytania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) i innych kwestii na uwierzytelnianie, tym alternatywny identyfikator pomocy technicznej.

Zapoznaj się [Implementowanie uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) dla procedury wdrażania.

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne

* **Nakładu:** przy użyciu systemu uwierzytelniania federacyjnego zależą od zewnętrznego systemu do uwierzytelniania użytkowników. Niektóre firmy chcesz ponownie użyć dotychczasowych inwestycji federacyjnych systemu z ich rozwiązania z tożsamością hybrydową usługi Azure AD. Konserwacji i zarządzania federacyjnych systemu wypada poza kontrolą usługi Azure AD. Jest organizacji, upewnij się, że jest bezpieczne wdrożona i może obsłużyć obciążenia uwierzytelniania za pomocą systemu federacyjnych. 

* **Środowisko użytkownika:** interfejsu użytkownika uwierzytelniania federacyjnego zależy od implementacji funkcji, topologii i konfiguracji kolektywu serwerów federacyjnych. Niektóre organizacje wymagają możliwość dostosowania i skonfigurować dostęp do farmy federacyjnych do ich potrzebami zabezpieczeń. Na przykład jest możliwe do skonfigurowania wewnętrznie połączonych użytkowników i urządzeń do logowania użytkowników automatycznie, bez wyświetlania monitu o poświadczenia, ponieważ ich ma już zalogowany na ich urządzeniach. Z drugiej strony Jeśli to konieczne, niektóre funkcje zaawansowane zabezpieczenia mogą utrudnić procesu logowania użytkownika.

* **Zaawansowanych scenariuszy:** rozwiązania uwierzytelnianie federacyjne jest zwykle wymagany, jeśli klienci mają wymaganie uwierzytelniania nieobsługiwane natywnie przez usługę Azure AD, są szczegółowe informacje [wymienione w tym miejscu](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), ale wspólnej wymagania dotyczące obejmują:

    * Wymaga kart inteligentnych lub certyfikatów uwierzytelniania
    * Za pomocą lokalnego serwera usługi MFA lub dostawca MFA innych firm.
    * Uwierzytelnianie za pomocą rozwiązania uwierzytelniania innej firmy. Zobacz [listę zgodności federacyjnych usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Użytkownicy muszą logować się za pomocą ich sAMAccountName, na przykład domena azwa_użytkownika zamiast za pomocą głównej nazwy użytkownika (UPN), na przykład user@domain.com

* **Ciągłość prowadzenia działalności biznesowej:** systemów federacyjnym zwykle wymagają obciążenia zrównoważonym macierz serwerów, znanej także jako farmy, skonfigurowane w wewnętrznej sieci i obwód topologii sieci aby zapewnić wysoką dostępność dla żądań uwierzytelniania. Synchronizacja skrótów haseł można wdrożyć oraz uwierzytelnianie federacyjne jako metody uwierzytelniania kopii zapasowej gdy metoda uwierzytelniania podstawowego nie jest już dostępne, na przykład w przypadku serwerów lokalnych nie są dostępne. W niektórych organizacjach dużych przedsiębiorstw wymagają federacyjnego rozwiązanie do obsługi wielu punktów wejściowych Internet skonfigurowano geograficznie DNS dla żądań uwierzytelniania małe opóźnienia.

* **Kwestie do rozważenia:** systemów federacyjnym zwykle wymagają bardziej znaczących inwestycji w infrastrukturę lokalną. Większość organizacji wybierz tę opcję, jeśli mają już inwestycji Federacji lokalnego i jest wymagane silne biznesowych do używania dostawcy jednej tożsamości. Federacja znajduje się bardziej złożone do obsługi i rozwiązywania problemów w porównaniu do rozwiązań uwierzytelniania w chmurze. Przy użyciu nazwy użytkownika z domeną bez obsługi routingu, którego nie można zweryfikować w usłudze Azure AD do podpisania potrzebujących dodatkowej konfiguracji do wdrożenia. To wymaganie jest określany jako alternatywnego Identyfikatora pomocy technicznej. Zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) dla ograniczeń i wymagań. Jeśli chcesz użyć dostawcy uwierzytelniania wieloskładnikowego innych firm z Federacją, upewnij się, że dostawca obsługuje WS-Trust, aby zezwolić urządzeniom na usługi Azure AD join.

Zapoznaj się [Implementowanie usług federacyjnych](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) dla procedury wdrażania.

> [!NOTE] 
> Podczas wdrażania rozwiązania tożsamości hybrydowej usługi Azure AD, należy implementować jeden z obsługiwanych topologii programu Azure AD Connect. Dowiedz się więcej o obsługiwane i nieobsługiwane konfiguracje w [topologii dla usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramów architektury

Wytyczne diagramu wysokiego poziomu architektury składników wymaganych dla każdej z metod uwierzytelniania używanych wraz z rozwiązaniem tożsamości hybrydowej usługi Azure AD. Umożliwia przegląd do porównania różnic między rozwiązania.

Poniższy diagram przedstawia prostotę rozwiązania synchronizacji skrótów haseł:

![PHS, KTÓRY](media/azure-ad/azure-ad-authn-image2.png)

Poniższy diagram przedstawia wymagania uwierzytelnianie przekazywane agenta:

![PTA](media/azure-ad/azure-ad-authn-image3.png)

Na poniższym diagramie przedstawiono składniki wymagane dla federacyjnych w sieci obwodowej i sieci wewnętrznej organizacji:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>Porównanie metod uwierzytelniania

|Kwestia|Synchronizacja skrótów haseł i bezproblemowego logowania jednokrotnego|Uwierzytelniania przekazywanego + bezproblemowe logowanie Jednokrotne|Federacja z usługami AD FS|
|:-----|:-----|:-----|:-----|
|Gdy jest wykonywana uwierzytelniania|W chmurze|W chmurze, po wymianie weryfikacji bezpieczne hasło, z agentem uwierzytelniania lokalnego|Lokalnie|
|Jakie są wymagania dotyczące serwera lokalnego poza inicjowania obsługi administracyjnej systemu: Azure AD Connect?|Brak|Serwer 1 dla każdego dodatkowego uwierzytelniania agenta|2 lub większą liczbą serwerów usług AD FS<br>2 lub więcej serwerów proxy w sieci obwodowej/DMZ|
|Jakie są Internet lokalnymi i wymagań poza inicjowania obsługi administracyjnej systemu sieciowych?|Brak|[Ruch wychodzący do Internetu](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) z serwerów z agentów uwierzytelniania|[Dostęp do Internetu przychodzące](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) do serwerów proxy w obwodzie<br>Dostępu do sieci dla ruchu przychodzącego na serwerach usług AD FS z serwerów proxy w obwodzie<br>Równoważenie obciążenia sieciowego|
|Istnieje już certyfikat SSL jest wymagany?|Nie|Nie|Yes|
|Dostępne jest rozwiązanie monitorujące, kondycję?|Niewymagane|Stan agenta podał [Centrum administracyjnego usługi Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Czy użytkownicy uzyskują logowanie jednokrotne do zasobów w chmurze z urządzeń przyłączonych do domeny w sieci firmowej?|Tak [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Tak [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Yes|
|Jakie typy logowania są obsługiwane?|UserPrincipalName + hasła<br>Przy użyciu zintegrowanego uwierzytelniania systemu Windows [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + hasła<br>Przy użyciu zintegrowanego uwierzytelniania systemu Windows [bezproblemowe logowanie Jednokrotne](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + hasła<br>sAMAccountName + hasła<br>Zintegrowane uwierzytelnianie systemu Windows<br>[Uwierzytelnianie certyfikatu i za pomocą kart inteligentnych](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[Alternatywnego Identyfikatora logowania](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|To jest Windows Hello dla firm obsługiwane?|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model zaufania certyfikatów przy użyciu usługi Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model zaufania klucza](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model zaufania certyfikatów](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jakie są opcje uwierzytelnianie wieloskładnikowe?|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Usługa Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Serwer usługi Azure MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[MFA innych firm](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Jakie stanu konta użytkowników są obsługiwane?|Wyłączone konta<br>(maksymalnie 30 minut)|Wyłączone konta<br>Konto zablokowane<br>Hasło wygasło<br>Liczba godzin korzystania z logowania|Wyłączone konta<br>Konto zablokowane<br>Hasło wygasło<br>Liczba godzin korzystania z logowania|
|Jakie są opcje dostępu warunkowego?|[Dostęp warunkowy usługi Azure AD ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Dostęp warunkowy usługi Azure AD ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[Reguły oświadczeń usług AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blokuje starszych protokołów obsługiwane?|Nie|Nie|[Tak](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Można dostosować logo, obrazu i opis na stronach logowania?|[Tak, z usługą Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Tak, z usługą Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Tak](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jakie zaawansowane scenariusze są obsługiwane?|[Blokady inteligentnej hasła](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Ujawnione poświadczenia raportów](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Blokady inteligentnej hasła](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Małe opóźnienia obejmujący wiele lokacji uwierzytelniania systemu<br>[Blokowanie ekstranetu w usługach AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integracja z systemów tożsamości innych firm](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Zalecenia i zagadnienia, z usługi Azure AD

Tożsamość systemu gwarantuje, że użytkownicy będą mieli dostęp do usług w chmurze, aplikacji i aplikacji biznesowych z migracji i udostępnić w chmurze. Uwierzytelnianie kontroluje dostęp do aplikacji, aby zachować produktywność autoryzowanych użytkowników i nieupoważnione osoby spoza organizacji poufnych danych. Z tego powodu należy wziąć pod uwagę następujące zalecenia dotyczące wybór metody uwierzytelniania prawo dla Twojej organizacji. 

Użyj lub włączanie synchronizacji skrótów haseł, niezależnie od tego, która metoda uwierzytelniania możesz wybrać, z następujących powodów:

1. **Wysoka dostępność i odzyskiwanie po awarii:** uwierzytelniania przekazywanego i Federacji zależne od infrastruktury lokalnej. W przypadku uwierzytelniania przekazywanego rozmiaru lokalnej obejmuje sprzętu serwera i sieci, które wymagają uwierzytelniania przekazywanego agentów. Dla Federacji rozmiaru lokalnej jest jeszcze większym, ponieważ wymaga serwerów w sieci obwodowej, aby żądania uwierzytelnienia serwera proxy i serwery federacyjne wewnętrznego. Aby uniknąć pojedynczych punktów awarii, organizacja Wdróż nadmiarowe serwery, aby upewnić się, żądania uwierzytelniania zawsze są obsługiwane, jeśli dowolny składnik nie powiedzie się. Zarówno uwierzytelniania przekazywanego i federacji są również zależna kontrolerów domeny odpowiada na żądania uwierzytelniania, które można również zakończyć się niepowodzeniem. Wiele z tych składników muszą konserwacji pozostanie w dobrej kondycji, a awarie są bardziej prawdopodobne podczas obsługi nie jest planowane i poprawnie zaimplementowana. Można uniknąć awarii przy użyciu synchronizacji skrótów haseł, ponieważ usługa uwierzytelniania usługi Azure AD firmy Microsoft w chmurze skaluje globalnie i są zawsze dostępne.

2. **Po awarii lokalnymi:** skutków awarii lokalnej z powodu przez atak lub po awarii może być istotne, począwszy od marki reputational uszkodzenia sparaliżowanych organizacji mógł postępowania w przypadku ataku. W ciągu ostatniego roku w wielu organizacjach zostały ofiary atakami złośliwego oprogramowania, w tym ransomware docelowych, które spowodowało ich lokalnych serwerów został wyłączony. Pozwala klientom postępowania w przypadku tego rodzaju ataki, Microsoft zauważyć dwie kategorie organizacji:

   a. Organizacje, które wcześniej włączony synchronizacja skrótów haseł był trybu online w ciągu kilku godzin, zmieniając ich metodę uwierzytelniania używaną synchronizacji skrótów haseł. Za pomocą dostępu do poczty e-mail za pośrednictwem usługi Office 365, można działają Rozwiązywanie problemów i uzyskiwania dostępu do innych obciążeń oparte na chmurze.

   b. Organizacje, które nie zostały wcześniej Włączanie synchronizacji skrótów haseł było odwołać się do niezaufanych zewnętrznych e-mail systemy komunikacji i rozwiązywanie problemów. W takich przypadkach zajęło ich tygodniach lub więcej, aby zostać uruchomione ponownie.

3. **Ochrona tożsamości:** jest jednym z najlepszych sposobów ochrony użytkowników w chmurze Azure AD Identity Protection. Microsoft stale skanuje Internet dla użytkownika i hasło, list, nieupoważnione osoby sprzedaży i udostępnia ciemny sieci Web. Usługi Azure AD można dzięki tym informacjom można zweryfikować nazwy użytkowników i hasłami w organizacji naruszone. Dlatego bardzo ważne jest włączanie synchronizacji skrótów haseł niezależnie od tego, w jakiej metody uwierzytelniania można użyć, czy to federacyjnych lub przekazywanego uwierzytelniania. Ujawnione poświadczenia są przedstawione jako raport i może służyć do blokowania lub wymusić użytkownika do zmiany hasła, gdy próbują zalogować się przy użyciu hasła ujawnione.

Ponadto stosownie do [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), firma Microsoft oferuje zestaw najbardziej kompletne funkcji Zarządzanie tożsamościami i dostępem. Uchwyty Microsoft [żądania uwierzytelniania MLD 450](https://www.microsoft.com/en-us/security/intelligence-report) co miesiąc, w celu zapewnienia dostępu do tysięcy aplikacji SaaS, takich jak usługi Office 365 z niemal dowolnego urządzenia. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule wymieniono różne opcje uwierzytelniania, organizacji, można skonfigurować i wdrożyć do obsługi dostępu do aplikacji w chmurze. Aby spełnić różne business, zabezpieczeń i wymagania techniczne, organizacje mogą wybrać między synchronizacji skrótów haseł, uwierzytelnianie i federacji. Z każdą z tych metod uwierzytelniania organizacji można wybrać, czy wymagania biznesowe zostały opisane w celu wdrożenia rozwiązania i środowisko użytkownika procesu logowania. Należy również ocenić, czy organizacja wymaga zaawansowanych scenariuszy i funkcjach ciągłości biznesowej metod uwierzytelniania. Na koniec należy ocenić zagadnienia, aby zobaczyć, jeśli dowolne uniemożliwiać wykonania wybór metod uwierzytelniania.

## <a name="next-steps"></a>Kolejne kroki

W świecie współczesnych zagrożeń są obecne 24 godziny na dobę i pochodzić z wszędzie. Implementacja metody uwierzytelniania poprawne pomoże eliminowania zabezpieczeń i ochrony Twojej tożsamości. 

[Rozpoczynanie pracy](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) z usługą Azure AD i wdrożyć rozwiązanie prawym uwierzytelniania dla Twojej organizacji.

Planując migrację z federacyjnych do uwierzytelniania w chmurze, więcej [o znak w metodzie zmiana](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Aby ułatwić planowanie i wdrażanie migracji, można użyć [planów ułatwi tych projektów](http://aka.ms/deploymentplans).
