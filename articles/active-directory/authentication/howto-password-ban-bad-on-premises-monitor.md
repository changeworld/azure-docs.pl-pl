---
title: Monitorowanie i rejestrowanie w ochrony haseł usługi Azure AD — usługi Azure Active Directory
description: Omówienie ochrony haseł usługi Azure AD, monitorowanie i rejestrowanie
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a029135da79d1a0b24b2941873a0fe3187ac9f7c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479728"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Usługa Azure AD ochrona za pomocą hasła monitorowanie i rejestrowanie

Po wdrożeniu ochrony haseł usługi Azure AD monitorowania i raportowania są podstawowe zadania. W tym artykule przechodzi do szczegółów, aby pomóc, że rozumiesz różnych technik monitorowania, w tym, gdzie każda usługa rejestruje informacje i instrukcje sporządzić raport na temat stosowania ochrony haseł usługi Azure AD.

Monitorowanie i raportowanie są wykonywane tylko przez komunikaty w dzienniku zdarzeń lub przez uruchomienie polecenia cmdlet programu PowerShell. DC agent i serwer proxy usługi zarówno rejestrowanie komunikatów dziennika zdarzeń. Wszystkie polecenia cmdlet programu PowerShell opisanych poniżej są dostępne tylko na serwerze proxy (patrz modułu programu AzureADPasswordProtection PowerShell). Oprogramowanie agenta kontrolera domeny nie można zainstalować moduł programu PowerShell.

## <a name="dc-agent-event-logging"></a>Rejestrowanie zdarzeń agenta kontrolera domeny

Na każdym kontrolerze domeny DC oprogramowani usługi zapisuje wyniki każdej operacji sprawdzania poprawności poszczególnych hasła (i inne stanu) w lokalnym dzienniku zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Dziennik administratora agenta kontrolera domeny jest podstawowym źródłem informacji dotyczących sposobu zachowuje się oprogramowania.

Należy pamiętać, że w dzienniku śledzenia jest domyślnie wyłączona.

Zdarzenia zarejestrowane przez różne składniki agentów DC mieszczą się w następujących zakresów:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Biblioteki dll filtru haseł agenta kontrolera domeny| 10000-19999|
|Proces hostingu usługi agenta kontrolera domeny| 20000-29999|
|Logikę walidacji zasad usługi agenta kontrolera domeny| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Dziennik zdarzeń administratora agenta kontrolera domeny

### <a name="password-validation-outcome-events"></a>Zdarzenia wynik sprawdzania poprawności hasła

Na każdym kontrolerze domeny DC oprogramowani usługi zapisuje wyniki każdego weryfikacji haseł poszczególnych w dzienniku zdarzeń administratora agenta kontrolera domeny.

Dla operacji sprawdzania poprawności hasła pomyślne jest zazwyczaj jedno zdarzenie zarejestrowane z dll filtru haseł agenta kontrolera domeny. Na potrzeby przechodzenia operacji sprawdzania poprawności hasła, są zwykle dwa zdarzenia zarejestrowane: jeden z usługi agenta kontrolera domeny i jeden z dll filtru haseł agenta kontrolera domeny.

Dyskretne zdarzenia do przechwycenia tych sytuacji są rejestrowane na podstawie następujących czynników:

* Czy jest podanym hasłem ustawił lub zmienił.
* Czy sprawdzanie poprawności hasła podanego zakończony powodzeniem lub niepowodzeniem.
* Czy Weryfikacja nie powiodła się z powodu Microsoft globalnych zasad, zasady organizacji lub kombinację.
* Czy trybu tylko inspekcja jest aktualnie włączone czy wyłączone dla bieżących zasad haseł.

Kluczowe informacje o zdarzeniach powiązanych sprawdzania poprawności hasła są następujące:

|   |Zmiana hasła |Ustawianie hasła|
| --- | :---: | :---: |
|Powodzenie |10014 |10015|
|Niepowodzenie (ze względu na zasady haseł klienta)| 10016, 30002| 10017, 30003|
|Niepowodzenie (ze względu na zasady haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Niepowodzenie (z powodu połączone firmy Microsoft i klienta zasady haseł)| 10016, 30026| 10017, 30027|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasady haseł klienta)| 10024, 30008| 10025, 30007|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasadami haseł firmy Microsoft)| 10024, 30010| 10025, 30009|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się połączone zasadami haseł firmy Microsoft i klienta)| 10024, 30028| 10025, 30029|

Przypadków w powyższej tabeli, które odwołują się do "połączone zasady" odnosi się do sytuacji, w których hasło użytkownika stwierdzono, zawiera co najmniej jeden token zarówno z listy hasło Microsoft zablokowane i lista hasła klienta zablokowany.

Po zalogowaniu ze sobą dwa zdarzenia, zarówno zdarzeń są jawnie skojarzone przez ten sam identyfikator korelacji.

### <a name="password-validation-summary-reporting-via-powershell"></a>Podsumowanie sprawdzania poprawności hasła raportowania za pomocą programu PowerShell

`Get-AzureADPasswordProtectionSummaryReport` Polecenie cmdlet może być użyta do wyprodukowania widok podsumowania działanie sprawdzania poprawności hasła. Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Zakres polecenia cmdlet raportowania może wpływać przy użyciu jednego z parametrów — lasu, - domeny lub kontroler domeny —. Nie został podany parametr oznacza — lasu.

`Get-AzureADPasswordProtectionSummaryReport` Polecenia cmdlet polega na wykonywanie zapytań w dzienniku zdarzeń administratora agenta kontrolera domeny, a następnie zliczanie łączna liczba zdarzeń, które odpowiadają każdej kategorii wyświetlany wynik. Poniższa tabela zawiera mapowania między każdego wyniku i jego odpowiedni identyfikator zdarzenia:

|Właściwość Get AzureADPasswordProtectionSummaryReport |Odpowiedni identyfikator zdarzenia|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Należy pamiętać, że `Get-AzureADPasswordProtectionSummaryReport` polecenia cmdlet jest dostarczany w formie skryptu programu PowerShell i w razie potrzeby może odwoływać się bezpośrednio w następującej lokalizacji:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> To polecenie cmdlet działa, otwierając sesję programu PowerShell na każdym kontrolerze domeny. Aby można było pomyślnie, obsługa sesji zdalnej programu PowerShell musi być włączona na każdym kontrolerze domeny, a klient musi mieć wystarczające uprawnienia. Aby uzyskać więcej informacji na temat wymagań sesji zdalnej programu PowerShell należy uruchomić "Get-Help about_Remote_Troubleshooting" w oknie programu PowerShell.

> [!NOTE]
> To polecenie cmdlet działa zdalnie, Sondując dzienniku zdarzeń administratora każda usługa agenta kontrolera domeny. Jeśli dzienniki zdarzeń zawierają dużą liczbę zdarzeń, polecenie cmdlet może potrwać bardzo długo. Ponadto zbiorcze sieci zapytania dotyczące dużych zestawów danych może mieć wpływ na wydajność kontrolera domeny. W związku z tym tego polecenia cmdlet należy używać ostrożnie w środowiskach produkcyjnych.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 10014 (pomyślnymi zmianami haseł)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 10017 i 30003 (Ustawianie hasła nie powiodło się)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30001 (hasło zaakceptowane ze względu na brak zasad, które są dostępne)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30006 (nowe zasady są wymuszane)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30019 (Ochrona haseł usługi Azure AD jest wyłączona)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Dziennik operacyjny agenta kontrolera domeny

Usługa agenta DC zarejestruje także zdarzenia operacyjne związane z następujących dzienników:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>W dzienniku śledzenia agenta kontrolera domeny

Usługa agenta kontrolera domeny także zalogować się następujący dziennik zdarzeń pełnego śledzenia na poziomie debugowania:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Rejestrowanie śledzenia jest domyślnie wyłączona.

> [!WARNING]
> Po włączeniu dziennika śledzenia odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

## <a name="dc-agent-text-logging"></a>DC Agent rejestrowania w pliku tekstowym

Można skonfigurować usługę agenta kontrolera domeny do zapisu do dziennika tekstowego, ustawiając następującą wartość rejestru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Rejestrowania w pliku tekstowym jest domyślnie wyłączona. Ponowne uruchomienie usługi agenta kontrolera domeny jest wymagana dla zmiany tej wartości, aby zastosować zmiany. Po włączeniu kontroler domeny usługi agenta są zapisywane w pliku dziennika, znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Dziennik tekstowy odbiera tych samych wpisy poziom debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zwykle jest w formacie łatwiejsze do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

## <a name="dc-agent-performance-monitoring"></a>Monitorowanie wydajności agenta kontrolera domeny

Oprogramowanie usługi agenta DC instaluje obiektu licznika wydajności, o nazwie **ochrony haseł usługi Azure AD**. Obecnie dostępne są następujące liczniki wydajności:

|Nazwa licznika wydajności | Opis|
| --- | --- |
|Przetworzone hasła |Ten licznik wskazuje całkowitą liczbę haseł przetwarzane (zaakceptowane lub odrzucone) od momentu ostatniego uruchomienia.|
|Zaakceptowane hasła |Ten licznik wskazuje całkowitą liczbę haseł, które zostały zaakceptowane od momentu ostatniego uruchomienia.|
|Hasła odrzucone |Ten licznik wskazuje całkowitą liczbę haseł, które zostały odrzucone od momentu ostatniego uruchomienia.|
|Hasło Filtrowanie żądań w toku |Ten licznik wskazuje liczbę żądań filtru haseł obecnie w toku.|
|Szczytowy hasło Filtrowanie żądań |Ten licznik przedstawia szczytową liczbę jednoczesnych hasło Filtrowanie żądań od momentu ostatniego uruchomienia.|
|Błędy żądań filtru haseł |Ten licznik wyświetla całkowitą liczbę żądań filtru haseł, które nie powiodło się z powodu błędu od momentu ostatniego uruchomienia. Mogą wystąpić błędy, kiedy nie jest uruchomiona usługa agenta DC ochronę haseł usługi Azure AD.|
|Filtr haseł żądania/s |Ten licznik wyświetla szybkość jaką są przetwarzane hasła.|
|Czas przetwarzania żądania filtru haseł |Ten licznik wyświetla średni czas wymagany do przetwarzania żądań filtru haseł.|
|Czas przetwarzania żądania filtru haseł szczytu |Ten licznik wyświetla żądanie filtru hasła szczytu przetwarzania czasu od momentu ostatniego uruchomienia.|
|Hasła są akceptowane z powodu tryb inspekcji |Ten licznik wskazuje całkowitą liczbę haseł, zwykle będzie został odrzucony, które zostały zaakceptowane, ponieważ zasady haseł został skonfigurowany w trybie inspekcji (od momentu ostatniego uruchomienia).|

## <a name="dc-agent-discovery"></a>Odnajdywanie agenta kontrolera domeny

`Get-AzureADPasswordProtectionDCAgent` Polecenia cmdlet mogą służyć do wyświetlania podstawowych informacji o różnych agentów kontrolera domeny w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowane przez uruchamianie usług agenta kontrolera domeny.

Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi agenta kontrolera domeny w systemie godzinowym przybliżone. Dane są nadal podlega procesowi opóźnienie replikacji usługi Active Directory.

Zakres kwerendy polecenie cmdlet może wpływać przy użyciu parametrów — domenie lub lesie —.

Wartość HeartbeatUTC pobiera starych, może to być objawem agenta DC ochronę haseł usługi Azure AD na tym kontrolerze domeny nie jest uruchomiony lub został odinstalowany lub komputer został obniżony i nie jest już kontrolerem domeny.

Jeśli pobiera wartość PasswordPolicyDateUTC starych, może to być objawem mającego agenta ochrony kontrolera domeny haseł usługi Azure AD na tej maszynie nie działa prawidłowo.

## <a name="proxy-service-event-logging"></a>Rejestrowanie zdarzeń usługi serwera proxy

Usługa serwera Proxy emituje to minimalny zestaw zdarzeń do następujących dzienników zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Należy pamiętać, że w dzienniku śledzenia jest domyślnie wyłączona.

> [!WARNING]
> Po włączeniu dziennika śledzenia odbiera dużej liczby zdarzeń i może to mieć wpływ na wydajność hosta serwera proxy. W związku z tym, ten dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

Zdarzenia są rejestrowane przez różne składniki serwera Proxy, za pomocą następujących zakresów:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Proces hostingu usługi serwera proxy| 10000-19999|
|Logika biznesowa podstawowej usługi serwera proxy| 20000-29999|
|Polecenia cmdlet programu PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Rejestrowania w pliku tekstowym usługa serwera proxy

Usługa serwera Proxy można skonfigurować do zapisu do dziennika tekstowego, ustawiając następującą wartość rejestru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Rejestrowania w pliku tekstowym jest domyślnie wyłączona. Ponowne uruchomienie usługi serwera Proxy jest wymagany dla zmiany tej wartości, aby zastosować zmiany. Po włączeniu serwera Proxy usługi są zapisywane w pliku dziennika, znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Dziennik tekstowy odbiera tych samych wpisy poziom debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zwykle jest w formacie łatwiejsze do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność komputera. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

## <a name="powershell-cmdlet-logging"></a>Funkcje rejestrowania polecenia cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, który powoduje zmiany stanu (na przykład, Register-AzureADPasswordProtectionProxy) zwykle będzie rejestrować zdarzenia wynik do dziennika operacji.

Ponadto większość poleceń cmdlet programu PowerShell ochrony haseł usługi Azure AD będzie zapisywała Dziennik tekstowy znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Jeśli wystąpi błąd polecenia cmdlet i rozwiązanie i/lub Przyczyna nie jest łatwe do rozwiązania, mogą również konsultacje te dzienniki tekstowe.

## <a name="proxy-discovery"></a>Odnajdywanie serwera proxy

`Get-AzureADPasswordProtectionProxy` Polecenia cmdlet mogą służyć do wyświetlania podstawowych informacji o różnych usług serwera Proxy ochrony haseł usługi Azure AD w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowane przez uruchamianie usługi serwera Proxy.

Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi serwera Proxy w systemie godzinowym przybliżone. Dane są nadal podlega procesowi opóźnienie replikacji usługi Active Directory.

Zakres kwerendy polecenie cmdlet może wpływać przy użyciu parametrów — domenie lub lesie —.

Jeśli wartość HeartbeatUTC pobiera starych, może to być objawem Proxy ochrony haseł usługi Azure AD na tym komputerze nie jest uruchomiony lub został odinstalowany.

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów z ochrony haseł usługi Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Aby uzyskać więcej informacji na temat list globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zablokuj błędnego hasła](concept-password-ban-bad.md)
