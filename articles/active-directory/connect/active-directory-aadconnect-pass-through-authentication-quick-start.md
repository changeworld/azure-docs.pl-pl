---
title: Azure AD uwierzytelniania przekazywanego — szybki start | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozpocząć pracę z usługi Azure Active Directory (Azure AD) przekazywanego uwierzytelniania.
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
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
ms.openlocfilehash: 365d9cde29292b40847ec2a0ac6f7d4011222f2c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028935"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Usługi Azure Active Directory przekazywanego uwierzytelniania: Szybki start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Wdrażanie usługi Azure AD przekazywanego uwierzytelniania

Azure Active Directory (Azure AD) przekazywanego uwierzytelniania umożliwia użytkownikom logowania się zarówno lokalnie, jak i aplikacje oparte na chmurze przy użyciu takich samych haseł. Przekazywanego uwierzytelniania logowania użytkowników weryfikując haseł bezpośrednio z lokalnej usługi Active Directory.

>[!IMPORTANT]
>Jeśli używasz tej funkcji za pomocą wcześniejszej wersji zapoznawczej, upewnij się, uaktualnienie wersji zapoznawczych agentów uwierzytelniania przy użyciu instrukcji w [uwierzytelniania przekazywanego Azure Active Directory: uaktualnienia wersji zapoznawczej Agenci uwierzytelniania](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Wykonaj te instrukcje, aby wdrożyć przekazywanego uwierzytelniania:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Sprawdź wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne.

### <a name="in-the-azure-active-directory-admin-center"></a>W Centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy powinna zakończyć się niepowodzeniem z lokalnymi usługami lub staną się niedostępne. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest szczególnie ważne, aby upewnić się, że możesz przed zablokowaniem dzierżawy.
2. Dodaj jeden lub kilka [niestandardowych nazw domen](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy będą mogli logować się przy użyciu jednego z następujących nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszej, aby uruchomić Azure AD Connect. Dodaj serwer do tego samego lasu usługi Active Directory jako użytkowników, których hasła, należy dokonać weryfikacji.
2. Zainstaluj [najnowszą wersję programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serwerze, który został zidentyfikowany w poprzednim kroku. Jeśli masz już uruchomiona usługa Azure AD Connect, upewnij się, że wersja zawiera się 1.1.644.0 lub nowszym.

    >[!NOTE]
    >Wersje usługi Azure AD Connect, 1.1.557.0, 1.1.558.0 1.1.561.0 i 1.1.614.0 ma problem związany z synchronizacji skrótu hasła. Jeśli użytkownik _nie_ ma być używany w połączeniu z uwierzytelniania przekazywanego, przeczytaj synchronizacji skrótu hasła [wersji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Zidentyfikować dodatkowego serwera (system Windows Server 2012 R2 lub nowszy) realizującym autonomiczny Agent uwierzytelniania. Wersja agenta uwierzytelniania musi być 1.5.193.0 lub nowszym. Aby zapewnić wysoką dostępność żądań do logowania, potrzebny jest ten dodatkowy serwer. Dodaj serwer do tego samego lasu usługi Active Directory jako użytkowników, których hasła, należy dokonać weryfikacji.
4. W przypadku zapory między serwerami a usługą Azure AD, skonfiguruj następujące elementy:
   - Upewnij się, czy agenci uwierzytelniania można wprowadzać *wychodzących* żądań do usługi Azure AD przez następujące porty:
   
    | Numer portu | Jak jest używany |
    | --- | --- |
    | **80** | Pobieranie listy odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu SSL |
    | **443** | Obsługuje całą komunikację wychodzące z usługą |
   
    Jeśli Zapora wymusza zasady zgodnie z źródłowego użytkowników, należy otworzyć te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy zezwala na listę dozwolonych podobnej DNS, połączeń dozwolonych  **\*. msappproxy.net** i  **\*. servicebus.windows.net**. Jeśli nie, Zezwalaj na dostęp do [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653), która jest aktualizowana co tydzień.
   - Agentów uwierzytelniania wymagany jest dostęp do **login.windows.net** i **login.microsoftonline.com** do pierwszej rejestracji. Otwórz zaporę dla tych adresów URL również.
   - Do weryfikacji certyfikatu odblokować następujące adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, i  **www.microsoft.com:80**. Te adresy URL są używane do weryfikacji certyfikatu z innymi produktami firmy Microsoft. Te adresy URL odblokowany mogą już istnieć.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Krok 2: Włącz obsługę protokołu Exchange ActiveSync (opcjonalnie)

Wykonaj te instrukcje, aby włączyć obsługę programu Exchange ActiveSync:

1. Użyj [środowiska PowerShell usługi Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) do uruchom następujące polecenie:
```
Get-OrganizationConfig | fl per*
```

2. Sprawdź wartość `PerTenantSwitchToESTSEnabled` ustawienie. Jeśli wartość jest **true**, dzierżawy jest poprawnie skonfigurowany. Zwykle jest to w przypadku większości klientów. Jeśli wartość jest **false**, uruchom następujące polecenie:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Sprawdź, czy wartość `PerTenantSwitchToESTSEnabled` teraz mają ustawioną wartość **true**. Zaczekaj godzinę przed przejściem do następnego kroku.

Jeśli w tym kroku stają przed wszelkie problemy, sprawdź [przewodnik rozwiązywania problemów](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Krok 3: Włącz tę funkcję

Włączenie uwierzytelniania przekazywanego przez [programu Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Można włączyć przekazywanego uwierzytelniania na serwerze podstawowym lub tymczasowej Azure AD Connect. Należy włączyć ją z serwera podstawowego.

Jeśli instalujesz program Azure AD Connect po raz pierwszy, wybierz [niestandardową ścieżkę](active-directory-aadconnect-get-started-custom.md). W **logowania użytkownika** wybierz pozycję **uwierzytelniania przekazywanego** jako **metoda rejestracji jednokrotnej**. Po pomyślnym ukończeniu przekazywanego uwierzytelniania Agent jest zainstalowany na tym samym serwerze co usługi Azure AD Connect. Ponadto funkcja przekazywanego uwierzytelniania jest włączona w dzierżawie.

![Usługi Azure AD Connect: Logowanie użytkownika](./media/active-directory-aadconnect-sso/sso3.png)

Jeśli użytkownik zainstalował już Azure AD Connect przy użyciu [instalacji ekspresowej](active-directory-aadconnect-get-started-express.md) lub [instalacji niestandardowej](active-directory-aadconnect-get-started-custom.md) ścieżkę, wybierz opcję **zmienić logowania użytkownika** zadania w usłudze Azure AD Połącz, a następnie wybierz **dalej**. Następnie wybierz **uwierzytelniania przekazywanego** jako metodę logowania. Po pomyślnym ukończeniu Agent uwierzytelniania przekazywanego jest zainstalowany na tym samym serwerze co usługi Azure AD Connect, a ta funkcja jest włączona w dzierżawie.

![Azure AD Connect: Zmień logowanie użytkowników](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Przekazywanego uwierzytelniania jest funkcją poziomie dzierżawy. Włączania tej wpływa na logowanie użytkowników między _wszystkie_ domen zarządzanych w Twojej dzierżawie. Jeśli do uwierzytelniania przekazywanego rozpoczynasz korzystanie z usługi Active Directory Federation Services (AD FS), należy poczekać co najmniej 12 godzin przed zamknięciem infrastruktury usług AD FS. Czas oczekiwania jest zapewnienie, że użytkownicy mogą zachować zalogowanie się do programu Exchange ActiveSync podczas przejścia.

## <a name="step-4-test-the-feature"></a>Krok 4: Testowanie funkcji

Wykonaj te instrukcje, aby sprawdzić prawidłowo włączone przekazywanego uwierzytelniania:

1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **programu Azure AD Connect**.
4. Sprawdź, czy **uwierzytelniania przekazywanego** funkcji jest wyświetlany jako **włączone**.
5. Wybierz **uwierzytelniania przekazywanego**. **Uwierzytelniania przekazywanego** okienku listy serwerów, na których zainstalowano agentów uwierzytelniania.

![Centrum administracyjne usługi Azure Active Directory: okienko Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centrum administracyjne usługi Azure Active Directory: uwierzytelniania przekazywanego okienko](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Na tym etapie użytkowników ze wszystkich domen zarządzanych w Twojej dzierżawie zalogować się przy użyciu uwierzytelniania przekazywanego. Jednak użytkownicy z domen federacyjnych nadal Zaloguj się przy użyciu usług AD FS lub innego dostawcy federacyjnego, który został wcześniej skonfigurowany. Po przekonwertowaniu do domeny z federacyjnym zarządzane, wszyscy użytkownicy z tej domeny automatyczne uruchamianie, logowanie przy użyciu uwierzytelniania przekazywanego. Funkcja uwierzytelniania przekazywanego nie wpływa na użytkowników tylko w chmurze.

## <a name="step-5-ensure-high-availability"></a>Krok 5: Zapewni to wysoką dostępność

Jeśli planujesz wdrożenie uwierzytelniania przekazywanego w środowisku produkcyjnym, należy zainstalować co najmniej jednej autonomicznej więcej Agent uwierzytelniania. Zainstaluj tych agentów uwierzytelniania na serwerach _innych_ niż jednym systemem Azure AD Connect. Ta konfiguracja zapewnia wysoka dostępność dla żądań logowania użytkownika.

Wykonaj te instrukcje, aby pobrać oprogramowanie pośredniczące uwierzytelniania:

1. Aby pobrać najnowszą wersję agenta uwierzytelniania (wersja 1.5.193.0 lub nowsza), zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego Twojej dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku po lewej stronie.
3. Wybierz **Azure AD Connect**, wybierz pozycję **uwierzytelniania przekazywanego**, a następnie wybierz **Pobierz agenta**.
4. Wybierz **zaakceptować & pobieranie** przycisku.

![Centrum administracyjne usługi Azure Active Directory: przycisk Pobierz agenta uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centrum administracyjne usługi Azure Active Directory: Pobierz agenta okienko](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Można również bezpośrednio pobrać oprogramowanie pośredniczące uwierzytelniania [tutaj](https://aka.ms/getauthagent). Przejrzyj i zaakceptuj Agent uwierzytelniania [warunki świadczenia usługi](https://aka.ms/authagenteula) _przed_ instalacji.

Istnieją dwa sposoby wdrożenia autonomicznego Agent uwierzytelniania:

Po pierwsze możesz zrobić to interaktywnie przez właśnie uruchomienie pobranego pliku wykonywalnego Agent uwierzytelniania i podawania poświadczeń administratora globalnego Twojej dzierżawy, po wyświetleniu monitu.

Po drugie można tworzyć i uruchom skrypt instalacji nienadzorowanej wdrożenia. Jest to przydatne, gdy chcesz jednocześnie wdrażać wielu agentów uwierzytelniania lub zainstaluj agentów uwierzytelniania na serwerach systemu Windows, które nie mają włączone interfejsu użytkownika, lub nie masz dostępu przy użyciu pulpitu zdalnego. Oto instrukcje dotyczące sposobu używania tej metody:

1. Uruchom następujące polecenie, aby zainstalować agenta uwierzytelniania: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Agent uwierzytelniania można zarejestrować naszej usługi przy użyciu programu Windows PowerShell. Utwórz obiekt poświadczeń PowerShell `$cred` zawierający nazwę i hasło użytkownika administratora globalnego dla dzierżawy. Uruchom następujące polecenie, zastępując *\<username\>* i  *\<hasło\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Przejdź do **agenta: C:\Program Files\Microsoft Azure AD Connect uwierzytelniania** i uruchomić następujący skrypt przy użyciu `$cred` obiekt, który został utworzony:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Kolejne kroki
- [Inteligentne blokady](../authentication/howto-password-smart-lockout.md): informacje o sposobie konfigurowania funkcji blokady inteligentnej na swojej dzierżawy, aby chronić kont użytkowników.
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane przy użyciu uwierzytelniania przekazywanego i te, które nie są.
- [Nowości techniczne](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumieć sposób działania funkcji uwierzytelniania przekazywanego.
- [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Nowości zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Pobierz informacje techniczne na temat funkcji przekazywania uwierzytelniania.
- [Azure AD SSO bezproblemowe](active-directory-aadconnect-sso.md): Dowiedz się więcej o tej funkcji uzupełniające.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): plik nowego żądania funkcji za pomocą na Forum usługi Azure Active Directory.

