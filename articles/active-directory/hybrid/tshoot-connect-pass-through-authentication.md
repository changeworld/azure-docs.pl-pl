---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z uwierzytelnianiem przekazywania | Dokumenty firmy Microsoft'
description: W tym artykule opisano sposób rozwiązywania problemów z uwierzytelnianiem przekazu usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Rozwiązywanie problemów z uwierzytelnianiem przekazywaniem usługi Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456179"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem przekazywanym usługi Azure Active Directory

Ten artykuł ułatwia znajdowanie informacji dotyczących rozwiązywania typowych problemów dotyczących uwierzytelniania przekazywania usługi Azure AD.

>[!IMPORTANT]
>Jeśli występują problemy z logowaniem użytkownika z uwierzytelnianiem przekazywania, nie wyłączaj tej funkcji ani nie odinstaluj agentów uwierzytelniania przekazywanego bez konieczności ponownego włączania konta administratora globalnego tylko w chmurze. Dowiedz się więcej o [dodawaniu konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest krytyczne i zapewnia, że nie zostaniesz zablokowany z dzierżawy.

## <a name="general-issues"></a>Ogólne problemy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Sprawdź stan funkcji i agentów uwierzytelniania

Upewnij się, że funkcja uwierzytelniania przekazywanego jest nadal **włączona** w dzierżawie, a stan agentów uwierzytelniania jest wyświetlany **jako aktywny,** a nie **nieaktywny.** Stan można sprawdzić, przechodząc do bloku **Usługi Azure AD Connect** w centrum [administracyjnym usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory — blok Usługi Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centrum administracyjne usługi Azure Active Directory — narzędzie Uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Komunikaty o błędach logowania skierowane do użytkownika

Jeśli użytkownik nie może zalogować się przy użyciu uwierzytelniania przekazywanego, może zobaczyć jeden z następujących błędów skierowanych do użytkownika na ekranie logowania usługi Azure AD: 

|Błąd|Opis|Rozwiązanie
| --- | --- | ---
|AADSTS80001|Nie można połączyć się z usługą Active Directory|Upewnij się, że serwery agentów są członkami tego samego lasu usługi AD co użytkownicy, których hasła muszą zostać zweryfikowane i mogą połączyć się z usługą Active Directory.  
|AADSTS8002|Wystąpił limit czasu nawiązywanie połączenia z usługą Active Directory|Sprawdź, czy usługa Active Directory jest dostępna i odpowiada na żądania agentów.
|AADSTS80004|Nazwa użytkownika przekazana agentowi była nieprawidłowa|Upewnij się, że użytkownik próbuje zalogować się przy użyciu odpowiedniej nazwy użytkownika.
|AADSTS80005|Sprawdzanie poprawności napotkał nieprzewidywalne WebException|Błąd przejściowy. Ponów próbę żądania. Jeśli nadal nie powiedzie się, skontaktuj się z pomocą techniczną firmy Microsoft.
|AADSTS80007|Wystąpił błąd komunikacji z usługą Active Directory|Sprawdź dzienniki agenta, aby uzyskać więcej informacji i sprawdź, czy usługa Active Directory działa zgodnie z oczekiwaniami.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Przyczyny niepowodzenia logowania w centrum administracyjnym usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa ma skojarzoną z nią licencję usługi Azure AD Premium, możesz również sprawdzić [raport aktywności logowania w](../reports-monitoring/concept-sign-ins.md) centrum [administracyjnym usługi Azure Active Directory.](https://aad.portal.azure.com/)

![Centrum administracyjne usługi Azure Active Directory — raport logowania](./media/tshoot-connect-pass-through-authentication/pta4.png)

Przejdź do pozycji Logowania **usługi Azure Active Directory** -> **w** [centrum administracyjnym usługi Azure Active Directory](https://aad.portal.azure.com/) i kliknij działanie logowania określonego użytkownika. Poszukaj pola **KOD BŁĘDU LOGOWANIA.** Zamapuj wartość tego pola na przyczynę błędu i rozdzielczość, korzystając z poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 50144 | Ważność hasła użytkownika usługi Active Directory wygasła. | Zresetuj hasło użytkownika w lokalnej usłudze Active Directory.
| 80001 | Brak dostępnych agentów uwierzytelniania. | Zainstaluj i zarejestruj agenta uwierzytelniania.
| 80002 | Upłynął limit czasu żądania weryfikacji hasła agenta uwierzytelniania. | Sprawdź, czy usługa Active Directory jest osiągalna z agenta uwierzytelniania.
| 80003 | Agent uwierzytelniania odebrał nieprawidłową odpowiedź. | Jeśli problem jest stale powtarzalny dla wielu użytkowników, sprawdź konfigurację usługi Active Directory.
| 80004 | W żądaniu logowania użyto nieprawidłowej głównej nazwy użytkownika (UPN). | Poproś użytkownika o zalogowanie się przy użyciu poprawnej nazwy użytkownika.
| 80005 | Agent uwierzytelniania: wystąpił błąd. | Błąd przejściowy. Spróbuj ponownie później.
| 80007 | Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. | Sprawdź, czy usługa Active Directory jest osiągalna z agenta uwierzytelniania.
| 80010 | Agent uwierzytelniania nie może odszyfrować hasła. | Jeśli problem jest stale powtarzalny, zainstaluj i zarejestruj nowego agenta uwierzytelniania. I odinstaluj bieżący. 
| 80011 | Agent uwierzytelniania nie może pobrać klucza odszyfrowującego. | Jeśli problem jest stale powtarzalny, zainstaluj i zarejestruj nowego agenta uwierzytelniania. I odinstaluj bieżący.

>[!IMPORTANT]
>Agenci uwierzytelniania przekazywania uwierzytelniają użytkowników usługi Azure AD, sprawdzając ich nazwy użytkowników i hasła względem usługi Active Directory, wywołując [interfejs API logowania użytkownika usługi Win32.](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) W rezultacie, jeśli w usłudze Active Directory ustawisz ustawienie "Logowanie do", aby ograniczyć dostęp do logowania do stacji roboczej, musisz dodać serwery obsługujące agentów uwierzytelniania przekazywania do listy serwerów "Logowanie do". Nie można tego zrobić zablokuje użytkownikom logowanie się do usługi Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemy z instalacją agenta uwierzytelniania

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agentów](#collecting-pass-through-authentication-agent-logs) z serwera i kontaktowanie się z pomocą techniczną firmy Microsoft w celu rozwiązania problemu.

## <a name="authentication-agent-registration-issues"></a>Problemy z rejestracją agenta uwierzytelniania

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu zablokowanych portów

Upewnij się, że serwer, na którym zainstalowano Agent uwierzytelniania, może komunikować się z adresami URL i portami naszej usługi wymienionymi [tutaj](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że używasz konta administratora globalnego tylko w chmurze dla wszystkich operacji instalacji i rejestracji usługi Azure AD Connect lub autonomicznego agenta uwierzytelniania. Istnieje znany problem z kontami administratora globalnego z obsługą usługi MFA; tymczasowo wyłączyć mfa (tylko do ukończenia operacji) jako obejście problemu.

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agentów](#collecting-pass-through-authentication-agent-logs) z serwera i kontaktowanie się z pomocą techniczną firmy Microsoft w celu rozwiązania problemu.

## <a name="authentication-agent-uninstallation-issues"></a>Problemy z dezinstalacją agenta uwierzytelniania

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Komunikat ostrzegawczy podczas odinstalowywania usługi Azure AD Connect

Jeśli w dzierżawie włączono uwierzytelnianie przekazywane i próbujesz odinstalować usługę Azure AD Connect, zostanie wyświetlony następujący komunikat ostrzegawczy: "Użytkownicy nie będą mogli zalogować się do usługi Azure AD, chyba że na niej są zainstalowane inne agenty uwierzytelniania przekazywania innych serwerów."

Upewnij się, że konfiguracja jest [wysoce dostępna](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) przed odinstalowaniem usługi Azure AD Connect, aby uniknąć przerwania logowania użytkownika.

## <a name="issues-with-enabling-the-feature"></a>Problemy z włączaniem funkcji

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Włączenie tej funkcji nie powiodło się, ponieważ nie było dostępnych agentów uwierzytelniania

Musisz mieć co najmniej jeden aktywny agent uwierzytelniania, aby włączyć uwierzytelnianie przekazywane w dzierżawie. Agenta uwierzytelniania można zainstalować, instalując usługę Azure AD Connect lub autonomicznego agenta uwierzytelniania.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Włączenie tej funkcji nie powiodło się z powodu zablokowanych portów

Upewnij się, że serwer, na którym jest zainstalowany serwis Azure AD Connect, może komunikować się z adresami URL i portami usługi wymienionymi [tutaj.](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Włączenie funkcji nie powiodło się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że podczas włączania tej funkcji używasz konta administratora globalnego tylko w chmurze. Istnieje znany problem z kontami administratora globalnego z obsługą uwierzytelniania wieloskładnikowego (MFA); tymczasowo wyłączyć mfa (tylko w celu ukończenia operacji) jako obejście problemu.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Zbieranie dzienników agenta uwierzytelniania przekazywanego

W zależności od typu problemu, który może mieć, należy szukać w różnych miejscach dla dzienników agenta uwierzytelniania przekazywania.

### <a name="azure-ad-connect-logs"></a>Dzienniki usługi Azure AD Connect

W przypadku błędów związanych z instalacją sprawdź dzienniki usługi Azure AD Connect pod adresem **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Dzienniki zdarzeń agenta uwierzytelniania

W przypadku błędów związanych z agentem uwierzytelniania otwórz aplikację Podgląd zdarzeń na serwerze i zaznacz w obszarze **Dzienniki aplikacji i usług\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Aby uzyskać szczegółowe analizy, włącz dziennik "Sesja" (kliknij prawym przyciskiem myszy w aplikacji Podgląd zdarzeń, aby znaleźć tę opcję). Nie uruchamiaj agenta uwierzytelniania z włączonym dziennikiem podczas normalnych operacji; używać tylko do rozwiązywania problemów. Zawartość dziennika są widoczne tylko po ponownym wyłączona dziennik.

### <a name="detailed-trace-logs"></a>Szczegółowe dzienniki śledzenia

Aby rozwiązać problemy z błędami logowania użytkownika, poszukaj dzienników śledzenia w **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Te dzienniki zawierają powody, dla których logowanie określonego użytkownika nie powiodło się przy użyciu funkcji uwierzytelniania przekazywania. Błędy te są również mapowane na przyczyny niepowodzenia logowania pokazane w poprzedniej tabeli przyczyn niepowodzenia logowania. Poniżej znajduje się przykładowy wpis dziennika:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Możesz uzyskać opisowe szczegóły błędu ("1328" w poprzednim przykładzie), otwierając wiersz polecenia i uruchamiając następujące polecenie (Uwaga: Zamień '1328' na rzeczywisty numer błędu widoczny w dziennikach):

`Net helpmsg 1328`

![Uwierzytelnianie przekazywane](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączone, dodatkowe informacje można znaleźć w dziennikach zabezpieczeń kontrolerów domeny. Prosty sposób na zapytanie żądań logowania wysyłanych przez agentów uwierzytelniania przekazywania jest następujący:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Liczniki Monitora wydajności

Innym sposobem monitorowania agentów uwierzytelniania jest śledzenie określonych liczników Monitora wydajności na każdym serwerze, na którym jest zainstalowany Agent uwierzytelniania. Użyj następujących liczników globalnych (**# uwierzytelnianie PTA**, **#PTA nieudanych uwierzytelnień** i **#PTA pomyślne uwierzytelnianie)** i liczniki błędów (**# błędy uwierzytelniania PTA):**

![Liczniki Monitora wydajności uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane zapewnia wysoką dostępność przy użyciu wielu agentów uwierzytelniania, a _nie_ równoważenia obciążenia. W zależności od konfiguracji _nie_ wszystkie agenci uwierzytelniania otrzymują mniej więcej _taką samą_ liczbę żądań. Jest możliwe, że określony agent uwierzytelniania nie odbiera żadnego ruchu.
