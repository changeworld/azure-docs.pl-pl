---
title: Uwierzytelnianie przekazywane usługi AD platformy Azure — szybki start | Dokumentacja firmy Microsoft
description: W tym artykule opisano Rozpoczynanie pracy z usługą uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba5455680647b90b113d31c55816a2e0b0131b33
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617805"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: Szybki start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Wdróż uwierzytelnianie przekazywane usługi Azure AD

Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) umożliwia użytkownikom logować się do zarówno lokalnych, jak i aplikacji działających w chmurze przy użyciu tego samego hasła. Uwierzytelnianie przekazywane logowania użytkowników przez weryfikowanie ich hasła, bezpośrednio w odniesieniu do usługi Active Directory w środowisku lokalnym.

>[!IMPORTANT]
>Jeśli migrujesz z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego, zdecydowanie zalecamy, skorzystaj z naszego przewodnika wdrożenia są szczegółowo opublikowane [tutaj](https://aka.ms/adfstoPTADPDownload).

Wykonaj te instrukcje, aby wdrożyć uwierzytelnianie przekazywane w dzierżawie:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdzanie wymagań wstępnych

Upewnij się, że następujące wymagania wstępne zostały spełnione.

### <a name="in-the-azure-active-directory-admin-center"></a>W Centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy należy usług lokalnych się nie powieść lub staną się niedostępne. Dowiedz się więcej o [dodawania konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie czynności opisanych w tym kroku jest krytyczne, aby upewnić się, że możesz zablokowaniu dzierżawy.
2. Dodaj jeden lub kilka [niestandardowych nazw domen](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się do jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku w środowisku lokalnym

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszej, aby uruchomić program Azure AD Connect. Jeśli nie jest już włączona [Włącz szyfrowanie TLS 1.2 na serwerze](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Dodaj serwer do tego samego lasu usługi Active Directory jako użytkownicy, których hasła, należy dokonać weryfikacji.
2. Zainstaluj [najnowszą wersję programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serwerze, który został zidentyfikowany w poprzednim kroku. Jeśli masz już program Azure AD Connect działa, upewnij się, że wersja 1.1.750.0 lub nowszej.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli użytkownik _nie_ zamierza się używać synchronizacji skrótów haseł w połączeniu z uwierzytelniania przekazywanego, przeczytaj [informacje o wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identyfikowanie jeden lub więcej dodatkowych serwerów (systemem Windows Server 2012 R2 lub nowszym, z protokołem TLS 1.2, włączone) gdzie można uruchomić agentów uwierzytelniania autonomicznego. Te dodatkowe serwery są wymagane, aby zapewnić wysoką dostępność, żądań, aby zalogować się. Dodaj serwery do tego samego lasu usługi Active Directory jako użytkownicy, których hasła, należy dokonać weryfikacji.

    >[!IMPORTANT]
    >W środowiskach produkcyjnych zalecamy czy masz co najmniej 3 agentów uwierzytelniania uruchamiania w dzierżawie. Istnieje limit systemowy wynoszący 40 agentów uwierzytelniania dla dzierżawy. I najlepszych praktyk warto traktować wszystkie serwery uruchomionych agentów uwierzytelniania, w jak warstwy 0 systemów (zobacz [odwołania](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. W przypadku zapory między serwerami i usługi Azure AD, skonfiguruj następujące elementy:
   - Upewnij się, że agentów uwierzytelniania mogą przesłać *wychodzącego* żądań do usługi Azure AD za pośrednictwem następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pliki do pobrania list odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu SSL |
     | **443** | Obsługuje cała komunikacja wychodząca z usługą |
     | **8080** (opcjonalnie) | Agentów uwierzytelniania zgłaszają swój stan dziesięć minut za pośrednictwem portu 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. Port 8080 jest _nie_ używane do logowania użytkowników. |
     
     Jeśli Zapora wymusza zasady zgodnie z źródłowy użytkowników, należy otworzyć następujące porty dla ruchu z usług Windows, które są uruchamiane jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia DNS umieszczania na białej liście, lista dozwolonych połączeń z  **\*. msappproxy.net** i  **\*. servicebus.windows.net**. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień.
   - Agentów uwierzytelniania muszą mieć dostęp do **login.windows.net** i **login.microsoftonline.com** dla wstępnej rejestracji. Otwierania zapory dla tych adresów URL również.
   - Sprawdzanie poprawności certyfikatu, można odblokować w następujących adresów URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, i **www \.microsoft.com:80**. Ponieważ te adresy URL są używane do weryfikacji certyfikatu z innych produktów firmy Microsoft, może już być tych adresów URL odblokowane.

## <a name="step-2-enable-the-feature"></a>Krok 2: Włącz tę funkcję

Włącz uwierzytelnianie przekazywane za pośrednictwem [programu Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Można włączyć uwierzytelnianie przekazywane na serwer podstawowy lub tymczasowej Azure AD Connect. Zdecydowanie zaleca się go włączyć z poziomu serwera podstawowego. Jeśli konfigurujesz przejściowego serwera usługi Azure AD Connect w przyszłości, możesz **musi** nadal wybierz uwierzytelnianie przekazywane jako opcji logowania, wybierając inną opcję będzie **Wyłącz** Uwierzytelnianie przekazywane na dzierżawę i zastępowanie ustawienia na serwerze podstawowym.

Jeśli instalujesz program Azure AD Connect po raz pierwszy, wybierz opcję [niestandardową ścieżkę](how-to-connect-install-custom.md). W **logowania użytkownika** wybierz **uwierzytelniania przekazywanego** jako **metodę logowania**. Po pomyślnym zakończeniu agenta uwierzytelniania przekazywanego jest zainstalowany na tym samym serwerze co program Azure AD Connect. Ponadto funkcja uwierzytelnianie przekazywane jest włączona w dzierżawie.

![Program Azure AD Connect: Logowanie użytkowników](./media/how-to-connect-pta-quick-start/sso3.png)

Jeśli zainstalowano już Azure AD Connect przy użyciu [ekspresowa instalacja](how-to-connect-install-express.md) lub [instalacji niestandardowej](how-to-connect-install-custom.md) ścieżkę, wybierz opcję **zmiana użytkownika logowania** zadania w usłudze Azure AD Połącz, a następnie wybierz **dalej**. Następnie wybierz pozycję **uwierzytelniania przekazywanego** jako metodę logowania. Po pomyślnym zakończeniu agenta uwierzytelniania przekazywanego jest zainstalowany na tym samym serwerze co program Azure AD Connect, a ta funkcja jest włączona w dzierżawie.

![Program Azure AD Connect: Zmień dane logowania użytkownika](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane jest funkcją poziomu dzierżawy. Włączanie logowania dla użytkowników w jej wpływa na _wszystkich_ domen zarządzanych w Twojej dzierżawie. Jeśli rozpoczynasz korzystanie z usługi Active Directory Federation Services (AD FS) do uwierzytelniania przekazywanego, należy poczekać co najmniej 12 godzin przed zamknięciem infrastruktury usług AD FS. Czas oczekiwania jest zapewnienie, że użytkownicy mogą zachować logowania się do programu Exchange ActiveSync podczas przejścia. Aby uzyskać więcej informacji na temat migracji z usług AD FS do uwierzytelniania przekazywanego, zapoznaj się z naszego planu wdrożenia są szczegółowo opublikowane [tutaj](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Krok 3: Testowanie funkcji

Wykonaj te instrukcje, aby sprawdzić prawidłowo włączone uwierzytelnianie przekazujących:

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **programu Azure AD Connect**.
4. Upewnij się, że **uwierzytelniania przekazywanego** funkcji pojawia się jako **włączone**.
5. Wybierz **uwierzytelniania przekazywanego**. **Uwierzytelniania przekazywanego** okienko zawiera listę serwerów, na których zainstalowano agentów uwierzytelniania.

![Centrum administracyjne usługi Azure Active Directory: Okienko usługi Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centrum administracyjne usługi Azure Active Directory: Okienko uwierzytelniania przekazywanego](./media/how-to-connect-pta-quick-start/pta8.png)

Na tym etapie użytkownicy, z domen zarządzanych w Twojej dzierżawie mogą się logować przy użyciu uwierzytelniania przekazywanego. Jednak użytkownicy z domen federacyjnych nadal Zaloguj się przy użyciu usług AD FS lub innego dostawcy federacyjnego, który został wcześniej skonfigurowany. Po przekonwertowaniu do domeny z federacyjnego na zarządzanego, wszyscy użytkownicy z tej domeny automatyczne uruchamianie, logowanie przy użyciu uwierzytelniania przekazywanego. Funkcja uwierzytelnianie przekazywane nie ma wpływu na użytkowników tylko w chmurze.

## <a name="step-4-ensure-high-availability"></a>Krok 4: Zapewni to wysoką dostępność

Jeśli planujesz wdrożyć uwierzytelnianie przekazywane w środowisku produkcyjnym, należy zainstalować agentów uwierzytelniania dodatkowe autonomiczne. Zainstalowanie tych agentów uwierzytelniania na serwery _innych_ niż jeden uruchomiony program Azure AD Connect. Ta konfiguracja zapewnia wysoką dostępność dla żądań logowania użytkownika.

>[!IMPORTANT]
>W środowiskach produkcyjnych zalecamy czy masz co najmniej 3 agentów uwierzytelniania uruchamiania w dzierżawie. Istnieje limit systemowy wynoszący 40 agentów uwierzytelniania dla dzierżawy. I najlepszych praktyk warto traktować wszystkie serwery uruchomionych agentów uwierzytelniania, w jak warstwy 0 systemów (zobacz [odwołania](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Instalowanie wielu agentów uwierzytelniania przekazywanego zapewnia wysoką dostępność, ale nie deterministyczne Równoważenie obciążenia między agentów uwierzytelniania. Aby ustalić, ile agentów uwierzytelniania, należy dla Twojej dzierżawy, należy wziąć pod uwagę szczytowe i średnie obciążenie żądań logowania, które powinny być widoczne w dzierżawie. Ekstrapolacji jednego agenta uwierzytelniania może obsługiwać uwierzytelnień 300 i 400 w standardowych 4-rdzeniowy Procesor, 16 GB pamięci RAM serwera na sekundę.

Aby oszacować ruchu sieciowego, użyj poniższych wskazówek ustalania rozmiaru:
- Każde żądanie ma rozmiar ładunku (0,5 k + 1 K * num_of_agents) bajtów; tzn. dane z usługi Azure AD do agenta uwierzytelniania. W tym miejscu "num_of_agents" oznacza, że liczba agentów uwierzytelniania zarejestrowany w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku 1 KB; tzn. dane z agenta uwierzytelniania do usługi Azure AD.

W przypadku większości klientów trzech agentów uwierzytelniania w sumie są wystarczające dla wysokiej dostępności i wydajności. Blisko kontrolerów domeny do Zmniejszaj opóźnienia logowania, należy zainstalować agentów uwierzytelniania.

Aby rozpocząć, wykonaj te instrukcje, aby pobrać oprogramowanie agenta uwierzytelniania:

1. Aby pobrać najnowszą wersję agenta uwierzytelniania (wersja 1.5.193.0 lub nowszej), zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy usługi.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **program Azure AD Connect**, wybierz opcję **uwierzytelniania przekazywanego**, a następnie wybierz pozycję **Pobierz agenta**.
4. Wybierz **Zaakceptuj warunki i Pobierz** przycisku.

![Centrum administracyjne usługi Azure Active Directory: Agent uwierzytelniania przycisk Pobierz](./media/how-to-connect-pta-quick-start/pta9.png)

![Centrum administracyjne usługi Azure Active Directory: Pobierz agenta okienko](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Możesz również bezpośrednio [Pobierz oprogramowanie agenta uwierzytelniania programu](https://aka.ms/getauthagent). Przejrzyj i zaakceptuj agenta uwierzytelniania [warunki użytkowania usługi](https://aka.ms/authagenteula) _przed_ go zainstalować.

Istnieją dwa sposoby wdrożyć autonomicznego agenta uwierzytelniania:

Najpierw należy go interaktywnie, po prostu działa pobranego pliku wykonywalnego agenta uwierzytelniania i podaje poświadczenia administratora globalnego swojej dzierżawy, po wyświetleniu monitu.

Po drugie można tworzyć i uruchom skrypt instalacji nienadzorowanej wdrożenia. Jest to przydatne, jeśli chcesz od razu wdrażać wielu agentów uwierzytelniania lub zainstalować agentów uwierzytelniania na serwerach Windows, nie mają interfejsu użytkownika, włączone lub nie można uzyskać dostępu przy użyciu pulpitu zdalnego. Poniżej znajdują się instrukcje dotyczące sposobu używania tej metody:

1. Uruchom następujące polecenie, aby zainstalować agenta uwierzytelniania: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Agent uwierzytelniania można zarejestrować za pomocą naszej usługi za pomocą programu Windows PowerShell. Utwórz obiekt poświadczeń PowerShell `$cred` zawierający nazwę i hasło użytkownika administratora globalnego dla dzierżawy. Uruchom następujące polecenie, zastępując *\<username\>* i  *\<hasło\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Przejdź do **agenta: C:\Program Files\Microsoft Azure AD Connect uwierzytelniania** i uruchom następujący skrypt, używając `$cred` obiektu, który został utworzony:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Jeśli Agent uwierzytelniania jest zainstalowany na maszynie wirtualnej, nie można sklonować maszynę wirtualną, aby skonfigurować inny Agent uwierzytelniania. Ta metoda jest **nieobsługiwany**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5. Konfigurowanie funkcji inteligentnej blokady

Pomaga inteligentnej blokady za pomocą metod siłowych do pobrania w lub blokowanie złośliwych podmiotów, którzy próby odgadnięcia haseł użytkowników. Konfigurując ustawienia inteligentnej blokady w usłudze Azure AD i / lub ustawienia odpowiednich blokady w usłudze Active Directory w środowisku lokalnym, ataków można odfiltrowane zanim osiągną one usługi Active Directory. Odczyt [w tym artykule](../authentication/howto-password-smart-lockout.md) Aby dowiedzieć się więcej na temat sposobu konfigurowania ustawień inteligentnej blokady na swoją dzierżawę, aby chronić Twoje konta użytkownika.

## <a name="next-steps"></a>Kolejne kroki
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadp) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): Dowiedz się, jak skonfigurować funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane przy użyciu uwierzytelniania przekazywanego i te, które nie są.
- [Rozbudowana technicznie](how-to-connect-pta-how-it-works.md): Dowiedz się, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania dotyczące](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj informacje techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie Jednokrotne usługi Azure AD](how-to-connect-sso.md): Dowiedz się więcej na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Forum usługi Azure Active Directory umożliwia pliku sugestie dotyczące nowych funkcji.
