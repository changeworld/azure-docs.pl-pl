---
title: Uwierzytelnianie przekazywane usługi AD platformy Azure — szybki start | Dokumentacja firmy Microsoft
description: W tym artykule opisano Rozpoczynanie pracy z usługą uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fc98f15303f23937d58131de971d5c60017c9034
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917714"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Usługi Azure Active Directory uwierzytelnianie przekazywane: Szybki start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Wdróż uwierzytelnianie przekazywane usługi Azure AD

Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) umożliwia użytkownikom logować się do zarówno lokalnych, jak i aplikacji działających w chmurze przy użyciu tego samego hasła. Uwierzytelnianie przekazywane logowania użytkowników przez weryfikowanie ich hasła, bezpośrednio w odniesieniu do usługi Active Directory w środowisku lokalnym.

>[!IMPORTANT]
>Możesz użyć tej funkcji za pomocą wersji zapoznawczej, upewnij się, uaktualnić wersje zapoznawcze agentów uwierzytelniania przy użyciu z instrukcjami podanymi w [uwierzytelnianie przekazywane usługi Azure Active Directory: uaktualnienia (wersja zapoznawcza) Agentów uwierzytelniania](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Wykonaj te instrukcje, aby wdrożyć uwierzytelnianie przekazujących:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdzanie wymagań wstępnych

Upewnij się, że następujące wymagania wstępne zostały spełnione.

### <a name="in-the-azure-active-directory-admin-center"></a>W Centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy należy usług lokalnych się nie powieść lub staną się niedostępne. Dowiedz się więcej o [dodawania konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie czynności opisanych w tym kroku jest krytyczne, aby upewnić się, że możesz zablokowaniu dzierżawy.
2. Dodaj jeden lub kilka [niestandardowych nazw domen](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się do jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku w środowisku lokalnym

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszej, aby uruchomić program Azure AD Connect. Dodaj serwer do tego samego lasu usługi Active Directory jako użytkownicy, których hasła, należy dokonać weryfikacji.
2. Zainstaluj [najnowszą wersję programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serwerze, który został zidentyfikowany w poprzednim kroku. Jeśli masz już program Azure AD Connect działa, upewnij się, że wersja 1.1.644.0 lub nowszej.

    >[!NOTE]
    >Usługa Azure AD Connect w wersjach 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacją skrótów haseł. Jeśli użytkownik _nie_ zamierza się używać synchronizacji skrótów haseł w połączeniu z uwierzytelniania przekazywanego, przeczytaj [informacje o wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identyfikowanie dodatkowy serwer (systemem Windows Server 2012 R2 lub nowszym) gdzie można uruchomić autonomicznego agenta uwierzytelniania. Wersja agenta uwierzytelniania, musi być 1.5.193.0 lub nowszej. Ten dodatkowy serwer jest potrzebny, aby zapewnić wysoką dostępność, żądań, aby zalogować się. Dodaj serwer do tego samego lasu usługi Active Directory jako użytkownicy, których hasła, należy dokonać weryfikacji.
4. W przypadku zapory między serwerami i usługi Azure AD, skonfiguruj następujące elementy:
   - Upewnij się, że agentów uwierzytelniania mogą przesłać *wychodzącego* żądań do usługi Azure AD za pośrednictwem następujących portów:
   
    | Numer portu | Jak są używane |
    | --- | --- |
    | **80** | Pliki do pobrania list odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu SSL |
    | **443** | Obsługuje cała komunikacja wychodząca z usługą |
   
    Jeśli Zapora wymusza zasady zgodnie z źródłowy użytkowników, należy otworzyć następujące porty dla ruchu z usług Windows, które są uruchamiane jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia DNS umieszczania na białej liście, lista dozwolonych połączeń z  **\*. msappproxy.net** i  **\*. servicebus.windows.net**. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień.
   - Agentów uwierzytelniania muszą mieć dostęp do **login.windows.net** i **login.microsoftonline.com** dla wstępnej rejestracji. Otwierania zapory dla tych adresów URL również.
   - Sprawdzanie poprawności certyfikatu, można odblokować w następujących adresów URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, i  **www.microsoft.com:80**. Te adresy URL są używane do weryfikacji certyfikatu z innymi produktami firmy Microsoft. Te adresy URL odblokowany mogą już istnieć.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Krok 2: Włącz obsługę protokołu Exchange ActiveSync (opcjonalnie)

Wykonaj te instrukcje, aby włączyć obsługę programu Exchange ActiveSync:

1. Użyj [PowerShell programu Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) uruchomić następujące polecenie:
```
Get-OrganizationConfig | fl per*
```

2. Sprawdź wartość `PerTenantSwitchToESTSEnabled` ustawienie. Jeśli wartość jest **true**, Twoja dzierżawa jest poprawnie skonfigurowana. Zwykle jest to w przypadku większości klientów. Jeśli wartość jest **false**, uruchom następujące polecenie:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Upewnij się, że wartość `PerTenantSwitchToESTSEnabled` ustawienie ma teraz wartość **true**. Zaczekaj godzinę przed przejściem do następnego kroku.

Jeśli napotkasz żadnych problemów podczas wykonywania tego kroku sprawdź [przewodnik rozwiązywania problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Krok 3: Włączanie funkcji

Włącz uwierzytelnianie przekazywane za pośrednictwem [programu Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Można włączyć uwierzytelnianie przekazywane na serwer podstawowy lub tymczasowej Azure AD Connect. Należy włączyć je z serwera podstawowego.

Jeśli instalujesz program Azure AD Connect po raz pierwszy, wybierz opcję [niestandardową ścieżkę](active-directory-aadconnect-get-started-custom.md). W **logowania użytkownika** wybierz **uwierzytelniania przekazywanego** jako **metodę logowania**. Po pomyślnym zakończeniu agenta uwierzytelniania przekazywanego jest zainstalowany na tym samym serwerze co program Azure AD Connect. Ponadto funkcja uwierzytelnianie przekazywane jest włączona w dzierżawie.

![Usługi Azure AD Connect: Logowanie użytkownika](./media/active-directory-aadconnect-sso/sso3.png)

Jeśli zainstalowano już Azure AD Connect przy użyciu [ekspresowa instalacja](active-directory-aadconnect-get-started-express.md) lub [instalacji niestandardowej](active-directory-aadconnect-get-started-custom.md) ścieżkę, wybierz opcję **zmiana użytkownika logowania** zadania w usłudze Azure AD Połącz, a następnie wybierz **dalej**. Następnie wybierz pozycję **uwierzytelniania przekazywanego** jako metodę logowania. Po pomyślnym zakończeniu agenta uwierzytelniania przekazywanego jest zainstalowany na tym samym serwerze co program Azure AD Connect, a ta funkcja jest włączona w dzierżawie.

![Azure AD Connect: Zmiana użytkownika logowania](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane jest funkcją poziomu dzierżawy. Włączanie logowania dla użytkowników w jej wpływa na _wszystkich_ domen zarządzanych w Twojej dzierżawie. Jeśli rozpoczynasz korzystanie z usługi Active Directory Federation Services (AD FS) do uwierzytelniania przekazywanego, należy poczekać co najmniej 12 godzin przed zamknięciem infrastruktury usług AD FS. Czas oczekiwania jest zapewnienie, że użytkownicy mogą zachować logowania się do programu Exchange ActiveSync podczas przejścia.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Wykonaj te instrukcje, aby sprawdzić prawidłowo włączone uwierzytelnianie przekazujących:

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **programu Azure AD Connect**.
4. Upewnij się, że **uwierzytelniania przekazywanego** funkcji pojawia się jako **włączone**.
5. Wybierz **uwierzytelniania przekazywanego**. **Uwierzytelniania przekazywanego** okienko zawiera listę serwerów, na których zainstalowano agentów uwierzytelniania.

![Centrum administracyjne usługi Azure Active Directory: okienko program Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centrum administracyjne usługi Azure Active Directory: okienko uwierzytelniania przekazywanego](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Na tym etapie użytkownicy, z domen zarządzanych w Twojej dzierżawie mogą się logować przy użyciu uwierzytelniania przekazywanego. Jednak użytkownicy z domen federacyjnych nadal Zaloguj się przy użyciu usług AD FS lub innego dostawcy federacyjnego, który został wcześniej skonfigurowany. Po przekonwertowaniu do domeny z federacyjnego na zarządzanego, wszyscy użytkownicy z tej domeny automatyczne uruchamianie, logowanie przy użyciu uwierzytelniania przekazywanego. Funkcja uwierzytelnianie przekazywane nie ma wpływu na użytkowników tylko w chmurze.

## <a name="step-5-ensure-high-availability"></a>Krok 5: Zapewnić wysoką dostępność

Jeśli planujesz wdrożyć uwierzytelnianie przekazywane w środowisku produkcyjnym, należy zainstalować co najmniej jednej autonomicznej więcej agenta uwierzytelniania. Zainstalowanie tych agentów uwierzytelniania na serwery _innych_ niż jeden uruchomiony program Azure AD Connect. Ta konfiguracja zapewnia wysoką dostępność dla żądań logowania użytkownika.

Wykonaj te instrukcje, aby pobrać oprogramowanie agenta uwierzytelniania:

1. Aby pobrać najnowszą wersję agenta uwierzytelniania (wersja 1.5.193.0 lub nowszej), zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy usługi.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **program Azure AD Connect**, wybierz opcję **uwierzytelniania przekazywanego**, a następnie wybierz pozycję **Pobierz agenta**.
4. Wybierz **Zaakceptuj warunki i Pobierz** przycisku.

![Centrum administracyjne usługi Azure Active Directory: przycisk Pobierz agenta uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centrum administracyjne usługi Azure Active Directory: okienko Pobierz agenta](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Można również bezpośrednio pobrać oprogramowanie agenta uwierzytelniania [tutaj](https://aka.ms/getauthagent). Przejrzyj i zaakceptuj agenta uwierzytelniania [warunki użytkowania usługi](https://aka.ms/authagenteula) _przed_ go zainstalować.

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

## <a name="next-steps"></a>Kolejne kroki
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): informacje dotyczące konfigurowania funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane przy użyciu uwierzytelniania przekazywanego i te, które nie są.
- [Rozbudowana technicznie](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumienie sposobu działania funkcji uwierzytelniania przekazywanego.
- [Często zadawane pytania dotyczące](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Uzyskaj informacje techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](active-directory-aadconnect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.

