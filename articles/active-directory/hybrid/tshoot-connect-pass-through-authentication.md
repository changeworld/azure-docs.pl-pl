---
title: 'Program Azure AD Connect: Rozwiązywanie problemów z uwierzytelniania przekazywanego | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak rozwiązywać problemy z uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Rozwiązywanie problemów z usługi Azure AD Connect uwierzytelniania przekazywanego, należy zainstalować usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456179"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Rozwiązywanie problemów z usługi Azure Active Directory uwierzytelnianie przekazywane

Ten artykuł zawiera informacje dotyczące typowych problemów dotyczących uwierzytelniania przekazywanego usługi AD platformy Azure.

>[!IMPORTANT]
>Jeśli ten użytkownik problemy dotyczące logowania przy użyciu uwierzytelniania przekazywanego nie wyłączyć funkcję lub odinstalować agentów uwierzytelniania przekazywanego bez konta administratora globalnego tylko w chmurze na potrzeby. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Ten krok ma kluczowe znaczenie i gwarantuje, że możesz zablokowaniu dzierżawy.

## <a name="general-issues"></a>Zagadnienia ogólne

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Sprawdź stan funkcji i agentów uwierzytelniania

Upewnij się, że funkcja uwierzytelnianie przekazywane jest nadal **włączone** w dzierżawie i stan agentów uwierzytelniania pokazuje **Active**, a nie **nieaktywny**. Stan możesz sprawdzić, przechodząc do **program Azure AD Connect** bloku [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Active Directory systemu Azure — bloku usługi Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centrum administracyjne usługi Active Directory systemu Azure — bloku uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Komunikaty o błędach logowania użytkownika

Jeśli użytkownik nie może zalogować się do korzystania z uwierzytelniania przekazywanego, mogą zobaczyć jeden z następujących błędów przeznaczonych dla użytkowników na ekranie logowania w usłudze Azure AD: 

|Błąd|Opis|Rozwiązanie
| --- | --- | ---
|AADSTS80001|Nie można nawiązać połączenia z usługi Active Directory|Upewnij się, że agent serwery należą do tego samego lasu usługi AD jako użytkownicy, których hasła muszą być weryfikowane i będą mogły nawiązać połączenia z usługi Active Directory.  
|AADSTS8002|Przekroczono limit czasu połączenia z usługi Active Directory|Zaznacz, aby upewnić się, że usługi Active Directory jest dostępna i odpowiada na żądania z agentów.
|AADSTS80004|Nazwa użytkownika, przekazana do agenta nie jest prawidłowa|Upewnij się, że użytkownik próbuje zalogować się przy użyciu odpowiednie nazwy użytkownika.
|AADSTS80005|Sprawdzanie poprawności napotkano nieprzewidywalne WebException|Błąd przejściowy. Ponów żądanie. Jeśli nadal nie będzie działać, skontaktuj się z pomocą techniczną firmy Microsoft.
|AADSTS80007|Wystąpił błąd podczas komunikacji z usługą Active Directory|Sprawdź dzienniki agenta, aby uzyskać więcej informacji i upewnij się, że usługi Active Directory działa zgodnie z oczekiwaniami.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Przyczyny niepowodzenia logowania w Centrum administracyjnym usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa usługi ma skojarzoną licencję usługi Azure AD Premium, możesz także obejrzeć [raport aktywności logowania](../reports-monitoring/concept-sign-ins.md) na [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Active Directory systemu Azure — raporcie logowań](./media/tshoot-connect-pass-through-authentication/pta4.png)

Przejdź do **usługi Azure Active Directory** -> **logowania** na [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/) i kliknij przycisk aktywności logowania dla określonego użytkownika. Wyszukaj **kod błędu logowania** pola. Mapowania wartości tego pola Przyczyna niepowodzenia i rozpoznawanie przy użyciu poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 50144 | Ważność hasła użytkownika usługi Active Directory wygasła. | Resetowanie hasła użytkownika w usłudze Active Directory w środowisku lokalnym.
| 80001 | Brak dostępnych agentów uwierzytelniania. | Instalowanie i rejestrowanie agenta uwierzytelniania.
| 80002 | Upłynął limit czasu żądania weryfikacji hasła agenta uwierzytelniania. | Sprawdź, czy dostępny z poziomu agenta uwierzytelniania usługi Active Directory.
| 80003 | Agent uwierzytelniania odebrał nieprawidłową odpowiedź. | Jeśli problem się stale powtarza dla wielu użytkowników, sprawdź konfigurację usługi Active Directory.
| 80004 | W żądaniu logowania użyto nieprawidłowej głównej nazwy użytkownika (UPN). | Poproś użytkownika o zalogowanie się prawidłową nazwę użytkownika.
| 80005 | Agent uwierzytelniania: Wystąpił błąd. | Błąd przejściowy. Spróbuj ponownie później.
| 80007 | Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. | Sprawdź, czy dostępny z poziomu agenta uwierzytelniania usługi Active Directory.
| 80010 | Agent uwierzytelniania nie może odszyfrować hasła. | Jeśli problem się stale powtarza, należy zainstalować i zarejestrować nowego agenta uwierzytelniania. I odinstalowywania bieżącej. 
| 80011 | Agent uwierzytelniania nie może pobrać klucza odszyfrowującego. | Jeśli problem się stale powtarza, należy zainstalować i zarejestrować nowego agenta uwierzytelniania. I odinstalowywania bieżącej.

>[!IMPORTANT]
>Agentów uwierzytelniania przekazywanego uwierzytelniania użytkowników usługi Azure AD, sprawdzając poprawność ich nazw użytkowników i haseł w usłudze Active Directory, wywołując [Win32 API funkcji LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx). W rezultacie ustawienie "Do logowania" zostały ustawione w usłudze Active Directory, aby ograniczyć dostęp logowania stacji roboczej, należy dodać serwery hostujące agentów uwierzytelniania przekazywanego do listy "Logowanie do" serwerów. Kończy się niepowodzeniem, w tym celu zablokuje użytkownikom logowanie się do usługi Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemy z instalacją agenta uwierzytelniania

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agenta](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z Microsoft Support w rozwiązaniu problemu.

## <a name="authentication-agent-registration-issues"></a>Problemy dotyczące rejestracji agenta uwierzytelniania

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu zablokowane porty

Upewnij się, że serwer, na którym został zainstalowany Agent uwierzytelniania może komunikować się za pomocą naszej usługi, na liście adresów URL i portów [tutaj](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że używasz konta administratora globalnego tylko w chmurze Azure AD Connect lub autonomicznej instalacji agenta uwierzytelniania i operacje rejestracji. Jest to znany problem przy użyciu konta administratora globalnego z obsługą usługi MFA; obejść ten problem, wyłącz funkcję MFA tymczasowo (tylko w celu ukończenia operacji).

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbieranie dzienników agenta](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z Microsoft Support w rozwiązaniu problemu.

## <a name="authentication-agent-uninstallation-issues"></a>Problemy dotyczące dezinstalacji agenta uwierzytelniania

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Ostrzeżenie podczas odinstalowywania program Azure AD Connect

Jeśli uwierzytelnianie przekazywane jest włączone w dzierżawie usługi i spróbuj odinstalować program Azure AD Connect, przedstawia on następujący komunikat ostrzegawczy: "Użytkownicy nie będą mogli zalogować się do usługi Azure AD, chyba że masz inne uwierzytelniania przekazywanego zainstalowanych agentów na innych serwerach."

Upewnij się, że ustawienia są [o wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) przed odinstalowaniem usługi Azure AD Connect, aby uniknąć dzielenia logowania użytkownika.

## <a name="issues-with-enabling-the-feature"></a>Problemy z włączaniem funkcji

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Włączenie funkcji nie powiodło się, ponieważ nie było żadnych agentów uwierzytelniania

Musisz mieć co najmniej jednego aktywnego agenta uwierzytelniania, aby włączyć uwierzytelnianie przekazujących w dzierżawie usługi. Można zainstalować agenta uwierzytelniania, instalowanie usługi Azure AD Connect lub autonomicznego agenta uwierzytelniania.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Włączenie funkcji nie powiodło się z powodu zablokowane porty

Upewnij się, że serwer, na którym jest zainstalowany program Azure AD Connect może komunikować się za pomocą naszej usługi, na liście adresów URL i portów [tutaj](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Włączenie funkcji nie powiodło się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, użyj konta administratora globalnego tylko w chmurze, podczas włączania funkcji. Jest to znany problem z uwierzytelnianiem wieloskładnikowym (MFA) — włączone konta administratora globalnego obejść ten problem, wyłącz funkcję MFA tymczasowo (tylko w celu ukończenia operacji).

## <a name="collecting-pass-through-authentication-agent-logs"></a>Zbieranie dzienników agenta uwierzytelniania przekazywanego

W zależności od typu problemu, który może być konieczne do wyszukania w różnych miejscach dzienniki agenta uwierzytelniania przekazywanego.

### <a name="azure-ad-connect-logs"></a>Dzienniki usługi Azure AD Connect

Dla błędów związanych z instalacji, sprawdź dzienniki usługi Azure AD Connect w lokalizacji **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Dzienniki zdarzeń agenta uwierzytelniania

Błędów związanych z agenta uwierzytelniania, Otwórz aplikację Podgląd zdarzeń na serwerze, a następnie sprawdź w obszarze **aplikacji i usług Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Szczegółowe analizy Włącz dziennik "Sesja" (kliknij prawym przyciskiem myszy wewnątrz aplikacji Podgląd zdarzeń, aby znaleźć tej opcji). Nie uruchamiaj agenta uwierzytelniania za pomocą tego dziennika włączone podczas wykonywania zwykłych operacji; Użyj tej opcji tylko w przypadku rozwiązywania problemów. Zawartość dziennika są widoczne tylko po wyłączeniu dziennika ponownie.

### <a name="detailed-trace-logs"></a>Dzienniki śledzenia szczegółowe

Aby rozwiązać problemy użytkownika logowań, wyszukaj dzienniki śledzenia w **%ProgramData%\Microsoft\Azure AD Connect Agent\Trace uwierzytelniania\\**. Dzienniki te obejmują powodów dlaczego określonego użytkownika logowania nie powiodła się przy użyciu funkcji uwierzytelniania przekazywanego. Te błędy, również są mapowane na podanych w powyższej tabeli przyczyn niepowodzenia logowania przyczyn błąd logowania. Poniżej przedstawiono przykładowy wpis dziennika:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Opisowy szczegóły błędu ("1328" w poprzednim przykładzie) można uzyskać, otwierając wiersz polecenia i uruchom następujące polecenie (Uwaga: Zastąp "1328" numer błędu, zobacz w dziennikach):

`Net helpmsg 1328`

![Uwierzytelnianie przekazywane](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Rejestruje kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączona, dodatkowe informacje można znaleźć w dzienniku zabezpieczeń kontrolerów domeny. Prosty sposób rejestrowania żądań wysyłanych przez agentów uwierzytelniania przekazywanego zapytań jest w następujący sposób:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Liczniki Monitora wydajności

Innym sposobem monitorowania agentów uwierzytelniania jest śledzenie liczniki Monitora wydajności dotyczące na każdym serwerze, w którym jest zainstalowany Agent uwierzytelniania. Służą następujące liczniki globalne (**uwierzytelnień # PTA**, **#PTA nie powiodło się uwierzytelnień** i **#PTA pomyślnych uwierzytelnień**) i liczniki błędu (**Błędy uwierzytelniania # PTA**):

![Liczniki Monitora wydajności uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane zapewnia wysoką dostępność przy użyciu wielu agentów uwierzytelniania, i _nie_ równoważenia obciążenia. W zależności od konfiguracji _nie_ wszystkich agentów uwierzytelniania wyświetlany w przybliżeniu _równy_ liczby żądań. Istnieje możliwość, określonego agenta uwierzytelniania na wszystkich odbiera żadnego ruchu.
