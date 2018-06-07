---
title: 'Azure AD Connect: Rozwiązywanie problemów z uwierzytelniania przekazywanego | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób rozwiązywania uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Rozwiązywanie problemów z usługi Azure AD Connect uwierzytelniania przekazywanego, należy zainstalować usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 3296ee114d3e285e77070995156ef6a242c1fc87
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592201"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Rozwiązywanie problemów z uwierzytelniania przekazywanego usługi Azure Active Directory

Ten artykuł pomaga informacje o typowych problemów dotyczących usługi Azure AD przekazywanego uwierzytelniania.

>[!IMPORTANT]
>Jeśli są ukierunkowane użytkownika logowania problemów przy użyciu przekazywanego uwierzytelniania, nie wyłączyć funkcję lub odinstalować agentów uwierzytelniania przekazywanego bez tylko w chmurze konta administratora globalnego na potrzeby. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest krytyczna i zapewnia, że należy przed zablokowaniem dzierżawy.

## <a name="general-issues"></a>Ogólne problemy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Sprawdź stan funkcji i uwierzytelniania agentów

Upewnij się, że funkcji uwierzytelniania przekazywanego jest nadal **włączone** na dzierżawy i stan agentów uwierzytelniania pokazuje **Active**i nie **nieaktywne**. Stan można sprawdzić, przechodząc do **Azure AD Connect** bloku na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory — blok Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centrum administracyjne usługi Active Directory platformy Azure — blok przekazywanego uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Błąd logowania dla użytkownika wiadomości

Jeśli użytkownik nie może zalogować się do przy użyciu uwierzytelniania przekazywanego, ich może zostać wyświetlony jeden z następujących błędów dla użytkownika na ekranie logowania w usłudze Azure AD: 

|Błąd|Opis|Rozwiązanie
| --- | --- | ---
|AADSTS80001|Nie można nawiązać połączenia z usługi Active Directory|Upewnij się, że agent serwery są członkami tego samego lasu usługi AD jako użytkowników, których hasła muszą być weryfikowane i są w stanie połączyć z usługą Active Directory.  
|AADSTS8002|Upłynął limit czasu połączenia usługi Active Directory|Sprawdź, czy usługi Active Directory jest dostępny i odpowiada na żądania z agentów.
|AADSTS80004|Nazwa użytkownika przekazany do agenta jest nieprawidłowy|Upewnij się, że użytkownik próbuje się zalogować za pomocą NazwaUżytkownika prawej.
|AADSTS80005|Sprawdzanie poprawności napotkano nieprzewidywalne WebException|Błąd przejściowy. Ponów żądanie. Jeśli go zakończy się niepowodzeniem, skontaktuj się z pomocą techniczną firmy Microsoft.
|AADSTS80007|Wystąpił błąd podczas komunikacji z usługą Active Directory|Więcej informacji w dzienniku agenta i sprawdź, czy usługi Active Directory działa zgodnie z oczekiwaniami.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Przyczyny niepowodzenia logowania na Centrum administracyjnego usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawy ma licencji usługi Azure AD Premium skojarzonych z nim, można też przyjrzeć się [raport aktywności logowania](../active-directory-reporting-activity-sign-ins.md) na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Active Directory platformy Azure — raport logowania](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Przejdź do **usługi Azure Active Directory** -> **logowania** na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/) i kliknij przycisk działań logowania określonego użytkownika. Wyszukaj **kod błędu logowania w** pola. Wartość tego pola mapowania na Przyczyna niepowodzenia i rozpoznawanie przy użyciu poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 50144 | Ważność hasła użytkownika usługi Active Directory wygasła. | Zresetuj hasło użytkownika w lokalnej usługi Active Directory.
| 80001 | Brak dostępnych agentów uwierzytelniania. | Zainstaluj i zarejestruj Agent uwierzytelniania.
| 80002 | Upłynął limit czasu żądania weryfikacji hasła agenta uwierzytelniania. | Sprawdź, czy usługi Active Directory jest dostępny z agentem uwierzytelniania.
| 80003 | Agent uwierzytelniania odebrał nieprawidłową odpowiedź. | Jeśli problem się stale powtarza przez wielu użytkowników, sprawdź konfigurację usługi Active Directory.
| 80004 | W żądaniu logowania użyto nieprawidłowej głównej nazwy użytkownika (UPN). | Monitowanie użytkownika o Zaloguj się przy użyciu prawidłową nazwę użytkownika.
| 80005 | Agent uwierzytelniania: wystąpił błąd. | Błąd przejściowy. Spróbuj ponownie później.
| 80007 | Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. | Sprawdź, czy usługi Active Directory jest dostępny z agentem uwierzytelniania.
| 80010 | Agent uwierzytelniania nie może odszyfrować hasła. | Jeśli problem się stale powtarza, zainstaluj i Zarejestruj nowy Agent uwierzytelniania. I Odinstaluj bieżący. 
| 80011 | Agent uwierzytelniania nie może pobrać klucza odszyfrowującego. | Jeśli problem się stale powtarza, zainstaluj i Zarejestruj nowy Agent uwierzytelniania. I Odinstaluj bieżący.

## <a name="authentication-agent-installation-issues"></a>Problemy z instalacją agentów uwierzytelniania

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agenta](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z Microsoft Support w rozwiązaniu problemu.

## <a name="authentication-agent-registration-issues"></a>Problemy z uwierzytelnianiem agenta rejestracji

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu zablokowane porty

Upewnij się, że serwer, na którym został zainstalowany Agent uwierzytelniania może komunikować się z usługą adresów URL i portów wyświetlane [tutaj](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, użyj konta administratora globalnego tylko w chmurze Azure AD Connect lub autonomicznej Agent uwierzytelniania i operacji rejestracji. Jest to znany problem z konta administratora globalnego włączone uwierzytelnianie MFA; jako obejście, wyłącz funkcję MFA tymczasowo (tylko w celu ukończenia operacji).

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agenta](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z Microsoft Support w rozwiązaniu problemu.

## <a name="authentication-agent-uninstallation-issues"></a>Problemy z uwierzytelnianiem agenta dezinstalacji

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Ostrzeżenie podczas odinstalowywania Azure AD Connect

Jeśli masz włączone dzierżawy uwierzytelniania przekazywanego i spróbować odinstalować program Azure AD Connect, zawiera następujące ostrzeżenie: "użytkownicy nie będą mogli się zalogować do usługi Azure AD w przypadku braku innych agentów uwierzytelniania przekazywanego zainstalowanych na inne serwery."

Upewnij się, że ustawienia są [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) przed odinstalowaniem Azure AD Connect, aby uniknąć dzielenia logowania użytkownika.

## <a name="issues-with-enabling-the-feature"></a>Problemy z włączenie funkcji

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Włączenie funkcji nie powiodło się, ponieważ nie było żadnych agentów uwierzytelniania

Musisz mieć co najmniej jeden aktywny Agent uwierzytelniania przekazywanego uwierzytelniania, Włącz dzierżawy. Można zainstalować agenta uwierzytelniania przez zainstalowanie usługi Azure AD Connect lub autonomiczny Agent uwierzytelniania.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Włączenie funkcji nie powiodło się z powodu zablokowane porty

Upewnij się, że serwer, na którym zainstalowano Azure AD Connect może komunikować się z usługą adresów URL i portów wyświetlane [tutaj](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Włączenie funkcji nie powiodło się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że używasz konta administratora globalnego tylko w chmurze, podczas włączania funkcji. Jest to znany problem z uwierzytelnianiem wieloskładnikowym (MFA)-włączone konta administratora globalnego; Wyłącz funkcję MFA tymczasowo (tylko w celu ukończenia operacji) jako obejście.

## <a name="exchange-activesync-configuration-issues"></a>Problemy z konfiguracją programu Exchange ActiveSync

Są to typowe problemy podczas konfigurowania programu Exchange ActiveSync obsługę uwierzytelniania przekazywanego.

### <a name="exchange-powershell-issue"></a>Problem PowerShell programu Exchange

Jeśli widzisz "**nie można odnaleźć parametru odpowiadającej nazwie parametru"PerTenantSwitchToESTSEnabled"\.**" Błąd podczas uruchamiania `Set-OrganizationConfig` środowiska PowerShell usługi Exchange polecenie, skontaktuj się z Microsoft Support.

### <a name="exchange-activesync-not-working"></a>Program Exchange ActiveSync nie działa

Konfiguracja dopiero po pewnym czasie zaczęły obowiązywać — czas zależy od środowiska. Jeśli problem będzie się powtarzał przez długi czas, skontaktuj się z Microsoft Support.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Zbieranie dzienników przekazywanego uwierzytelniania agenta

W zależności od typu problemu, który może mieć trzeba sprawdzić w różnych miejscach w dziennikach Agent uwierzytelniania przekazywanego.

### <a name="azure-ad-connect-logs"></a>Dzienniki usługi Azure AD Connect

Dla błędów związanych z instalacji, sprawdź dzienniki programu Azure AD Connect na **%ProgramData%\AADConnect\trace-\*log**.

### <a name="authentication-agent-event-logs"></a>Dzienniki zdarzeń uwierzytelniania agenta

Błędów związanych z agentem uwierzytelniania, otwórz Podgląd zdarzeń aplikacji na serwerze i sprawdź w obszarze **aplikacji i usług Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Szczegółowe analizy Włącz dziennik "Session". Nie uruchamiaj Agent uwierzytelniania z tym dziennikiem włączona podczas wykonywania normalnych operacji; używana tylko w celu rozwiązania problemu. Zawartość dziennika są widoczne tylko po dziennik jest wyłączony ponownie.

### <a name="detailed-trace-logs"></a>Dzienniki śledzenia szczegółowe

Do rozwiązywania problemów dotyczących błędów logowania użytkownika należy szukać dzienników śledzenia na **%ProgramData%\Microsoft\Azure AD Connect Agent\Trace uwierzytelniania\\**. Dzienniki te obejmują powodów dlaczego określonego użytkownika logowania nie powiodła się przy użyciu funkcji uwierzytelniania przekazywanego. Te błędy są również zamapowany na podanych w poprzednim przyczyn niepowodzenia logowania [tabeli](#sign-in-failure-reasons-on-the-Azure-portal). Poniżej przedstawiono przykładowy wpis dziennika:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Opisową szczegóły błędu ("1328" w poprzednim przykładzie) można uzyskać przez otwarcie wiersza polecenia i uruchom następujące polecenie (Uwaga: Zamień "1328" numer błędu, który pojawi się w dziennikach):

`Net helpmsg 1328`

![Uwierzytelnianie przekazywane](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Kontroler domeny

Jeśli włączono rejestrowanie inspekcji, dodatkowe informacje można znaleźć w dzienniki zabezpieczeń kontrolerów domeny. Prostym sposobem logowania żądania wysyłane przez agentów uwierzytelniania przekazywanego zapytania jest następujący:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Liczniki Monitora wydajności

Innym sposobem monitorowania agentów uwierzytelniania jest do śledzenia liczniki Monitora wydajności dotyczące na każdym serwerze, w którym jest zainstalowany Agent uwierzytelniania. Służą następujące liczniki globalne (**uwierzytelnienia # PTA**, **#PTA nie powiodło się uwierzytelnienia** i **#PTA informacji o pomyślnym uwierzytelnieniu**) i liczniki błędu (**Błędy uwierzytelniania # PTA**):

![Liczniki Monitora wydajności uwierzytelniania przekazywanego](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Uwierzytelniania przekazywanego zapewnia wysoką dostępność, za pomocą wielu agentów uwierzytelniania, i _nie_ Równoważenie obciążenia. W zależności od konfiguracji _nie_ wszystkich agentów uwierzytelniania odbierania około _równy_ liczby żądań. Możliwe jest określony Agent uwierzytelniania na wszystkich odbiera żaden ruch.
