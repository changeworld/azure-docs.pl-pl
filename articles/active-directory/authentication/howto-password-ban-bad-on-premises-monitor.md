---
title: Monitorowanie lokalnej ochrony hasłem usługi Azure AD
description: Dowiedz się, jak monitorować i przeglądać dzienniki ochrony hasłem usługi Azure AD dla lokalnego środowiska usług domenowych Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb533d5565009fb22d686e4082c9b4bfaae6dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671660"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitorowanie i przeglądanie dzienników lokalnych środowisk ochrony hasłem usługi Azure AD

Po wdrożeniu usługi Azure AD Password Protection monitorowanie i raportowanie są podstawowymi zadaniami. W tym artykule opisano szczegółowo, aby ułatwić zrozumienie różnych technik monitorowania, w tym gdzie każda usługa rejestruje informacje i jak raportować na temat korzystania z ochrony hasłem usługi Azure AD.

Monitorowanie i raportowanie są wykonywane przez komunikaty dziennika zdarzeń lub przez uruchomienie poleceń cmdlet programu PowerShell. Agent kontrolera domeny i usługi proxy zarówno komunikaty dziennika zdarzeń. Wszystkie poniżej polecenia cmdlet programu PowerShell są dostępne tylko na serwerze proxy (zobacz moduł AzureADPasswordProtection PowerShell). Oprogramowanie agenta kontrolera domeny nie instaluje modułu programu PowerShell.

## <a name="dc-agent-event-logging"></a>Rejestrowanie zdarzeń agenta kontrolera domeny

Na każdym kontrolerze domeny oprogramowanie usługi agenta kontrolera domeny zapisuje wyniki każdej indywidualnej operacji sprawdzania poprawności hasła (i innego stanu) w lokalnym dzienniku zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Dziennik administratora agenta kontrolera domeny jest głównym źródłem informacji o zachowaniu oprogramowania.

Należy zauważyć, że dziennik śledzenia jest domyślnie wyłączony.

Zdarzenia rejestrowane przez różne składniki agenta kontrolera domeny mieszczą się w następujących zakresach:

|Składnik |Zakres identyfikatora zdarzenia|
| --- | --- |
|Biblioteka dll filtru haseł agenta kontrolera domeny| 10000-19999|
|Proces hostingu usługi agenta kontrolera domeny| 20000-29999|
|Logika sprawdzania poprawności zasad usługi agenta kontrolera domeny| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Dziennik zdarzeń administratora agenta kontrolera domeny

### <a name="password-validation-outcome-events"></a>Zdarzenia dotyczące wyników sprawdzania poprawności hasła

Na każdym kontrolerze domeny oprogramowanie usługi agenta kontrolera domeny zapisuje wyniki weryfikacji poszczególnych haseł w dzienniku zdarzeń administratora agenta kontrolera domeny.

W przypadku pomyślnej operacji sprawdzania poprawności hasła zazwyczaj jest jedno zdarzenie rejestrowane z biblioteki dll filtru haseł agenta kontrolera domeny. W przypadku nieudanej operacji sprawdzania poprawności hasła zazwyczaj rejestrowane są dwa zdarzenia, jedno z usługi agenta kontrolera domeny i jedno z biblioteki dll filtru haseł agenta kontrolera domeny.

Dyskretne zdarzenia do przechwytywania tych sytuacji są rejestrowane, w oparciu o następujące czynniki:

* Czy dane hasło jest ustawiane, czy zmieniane.
* Czy sprawdzanie poprawności danego hasła minęło, czy nie powiodło się.
* Czy sprawdzanie poprawności nie powiodło się z powodu zasad globalnych firmy Microsoft, zasad organizacyjnych lub kombinacji.
* Czy tryb tylko inspekcji jest obecnie włączony lub wyłączony dla bieżącej zasady haseł.

Zdarzenia związane z sprawdzaniem poprawności klucza są następujące:

|   |Zmiana hasła |Zestaw haseł|
| --- | :---: | :---: |
|Przekazać |10014 |10015|
|Niepowodzenie (ze względu na zasady haseł klienta)| 10016, 30002| 10017, 30003|
|Niepowodzenie (ze względu na zasady haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Niepowodzenie (ze względu na połączone zasady haseł firmy Microsoft i klienta)| 10016, 30026| 10017, 30027|
|Przepuł po przejściu tylko do inspekcji (nie powiodłaby się zasada haseł klienta)| 10024, 30008| 10025, 30007|
|Przepustek tylko do inspekcji (nie powiódłby się zasady haseł firmy Microsoft)| 10024, 30010| 10025, 30009|
|Przepułon tylko do inspekcji (nie powiódłby się połączone zasady firmy Microsoft i hasła klienta)| 10024, 30028| 10025, 30029|

Przypadki w powyższej tabeli, które odnoszą się do "połączonych zasad" odnoszą się do sytuacji, w których hasło użytkownika okazało się zawierać co najmniej jeden token zarówno z listy zablokowanych haseł firmy Microsoft, jak i listy haseł zablokowanych przez klienta.

Gdy para zdarzeń jest rejestrowana razem, oba zdarzenia są jawnie skojarzone przez posiadanie tego samego CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Raportowanie podsumowania sprawdzania poprawności hasła za pośrednictwem programu PowerShell

Polecenie `Get-AzureADPasswordProtectionSummaryReport` cmdlet może służyć do tworzenia podsumowania działania sprawdzania poprawności hasła. Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

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

Zakres raportowania polecenia cmdlet może mieć wpływ przy użyciu jednego z —Forest, -Domain lub -DomainController parametrów. Nieokreślenie parametru oznacza –Forest.

Polecenie `Get-AzureADPasswordProtectionSummaryReport` cmdlet działa przez wykonywanie zapytań dziennika zdarzeń administratora agenta kontrolera domeny, a następnie zliczanie całkowitej liczby zdarzeń, które odpowiadają każdej wyświetlanej kategorii wyników. Poniższa tabela zawiera mapowania między każdym wynikiem i odpowiadającym mu identyfikatorem zdarzenia:

|Właściwość Get-AzureADPasswordProtectionSummaryReport |Odpowiedni identyfikator zdarzenia|
| :---: | :---: |
|Zmiana hasłaWzwolona |10014|
|Zestawy hasełWoryzowane |10015|
|Hasło ZmianaWysunięty |10016|
|HasłoSetsWysukaj wysunięty |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|HasłaSetErrors |10013|

Należy zauważyć, że polecenie `Get-AzureADPasswordProtectionSummaryReport` cmdlet jest dostarczane w formularzu skryptu programu PowerShell i w razie potrzeby może się odwoływać bezpośrednio w następującej lokalizacji:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> To polecenie cmdlet działa przez otwarcie sesji programu PowerShell dla każdego kontrolera domeny. Aby zapewnić powodowanie, obsługa sesji zdalnej programu PowerShell musi być włączona na każdym kontrolerze domeny, a klient musi mieć wystarczające uprawnienia. Aby uzyskać więcej informacji na temat wymagań dotyczących sesji zdalnej programu PowerShell, uruchom "Get-Help about_Remote_Troubleshooting" w oknie programu PowerShell.

> [!NOTE]
> To polecenie cmdlet działa przez zdalne wykonywanie zapytań dziennika zdarzeń administratora każdej usługi agenta kontrolera domeny. Jeśli dzienniki zdarzeń zawierają dużą liczbę zdarzeń, polecenie cmdlet może zająć dużo czasu. Ponadto zbiorcze zapytania sieciowe dużych zestawów danych mogą mieć wpływ na wydajność kontrolera domeny. W związku z tym to polecenie cmdlet należy stosować ostrożnie w środowiskach produkcyjnych.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Przykładowy komunikat dziennika zdarzeń dla identyfikatora zdarzenia 10014 (pomyślna zmiana hasła)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla identyfikatora zdarzenia 10017 i 30003 (zestaw haseł nie powiodło się)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o identyfikatorze 30001 (hasło zaakceptowane z powodu braku dostępnych zasad)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Przykładowy komunikat dziennika zdarzeń dla identyfikatora zdarzenia 30006 (nowe zasady są wymuszane)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Przykładowy komunikat dziennika zdarzeń dla identyfikatora zdarzenia 30019 (ochrona hasłem usługi Azure AD jest wyłączona)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Dziennik operacyjny agenta kontrolera domeny

Usługa agenta kontrolera domeny będzie również rejestrować zdarzenia związane z funkcją operacyjną w następującym dzienniku:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Dziennik śledzenia agenta kontrolera domeny

Usługa agenta kontrolera domeny może również rejestrować pełne zdarzenia śledzenia na poziomie debugowania do następującego dziennika:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Rejestrowanie śledzenia jest domyślnie wyłączone.

> [!WARNING]
> Po włączeniu dziennik śledzenia odbiera dużą liczbę zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym ten rozszerzony dziennik powinien być włączony tylko wtedy, gdy problem wymaga głębszego badania, a następnie tylko przez minimalny czas.

## <a name="dc-agent-text-logging"></a>Rejestrowanie tekstu agenta kontrolera domeny

Usługę agenta kontrolera domeny można skonfigurować do zapisu w dzienniku tekstowym, ustawiając następującą wartość rejestru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Rejestrowanie tekstu jest domyślnie wyłączone. Ponowne uruchomienie usługi agenta kontrolera domeny jest wymagane, aby zmiany tej wartości zostały wprowadzone. Po włączeniu usługa agenta kontrolera domeny zapisze do pliku dziennika znajdującego się pod:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Dziennik tekstu odbiera te same wpisy na poziomie debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zazwyczaj jest w łatwiejszym formacie do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu ten dziennik odbiera dużą liczbę zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym ten rozszerzony dziennik powinien być włączony tylko wtedy, gdy problem wymaga głębszego badania, a następnie tylko przez minimalny czas.

## <a name="dc-agent-performance-monitoring"></a>Monitorowanie wydajności agenta kontrolera domeny

Oprogramowanie usługi agenta kontrolera domeny instaluje obiekt licznika wydajności o nazwie **Ochrona hasłem usługi Azure AD**. Obecnie dostępne są następujące liczniki perf:

|Nazwa licznika perf | Opis|
| --- | --- |
|Przetwarzane hasła |Ten licznik wyświetla całkowitą liczbę haseł przetworzonych (zaakceptowanych lub odrzuconych) od ostatniego ponownego uruchomienia.|
|Akceptowane hasła |Ten licznik wyświetla całkowitą liczbę haseł, które zostały zaakceptowane od ostatniego ponownego uruchomienia.|
|Odrzucone hasła |Ten licznik wyświetla całkowitą liczbę haseł, które zostały odrzucone od ostatniego ponownego uruchomienia.|
|Trwa sprawdzanie żądań filtrowania haseł |Ten licznik wyświetla liczbę żądań filtrowania haseł, które są obecnie w toku.|
|Szczytowe żądania filtrowania haseł |Ten licznik wyświetla szczytową liczbę żądań filtrowania haseł od ostatniego ponownego uruchomienia.|
|Błędy żądania filtru hasła |Ten licznik wyświetla całkowitą liczbę żądań filtrowania haseł, które nie powiodły się z powodu błędu od ostatniego ponownego uruchomienia. Błędy mogą wystąpić, gdy usługa agenta kontrolera domeny ochrony haseł usługi Azure AD nie jest uruchomiona.|
|Żądania filtru hasła/s |Ten licznik wyświetla szybkość przetwarzania haseł.|
|Czas przetwarzania żądania filtru hasła |Ten licznik wyświetla średni czas wymagany do przetworzenia żądania filtrowania haseł.|
|Maksymalny czas przetwarzania żądania filtrowania haseł |Ten licznik wyświetla czas przetwarzania żądania filtru szczytowego od ostatniego ponownego uruchomienia.|
|Hasła akceptowane z powodu trybu inspekcji |Ten licznik wyświetla całkowitą liczbę haseł, które normalnie zostałyby odrzucone, ale zostały zaakceptowane, ponieważ zasady haseł zostały skonfigurowane tak, aby były w trybie inspekcji (od ostatniego ponownego uruchomienia).|

## <a name="dc-agent-discovery"></a>Odnajdowanie agenta kontrolera domeny

Polecenie `Get-AzureADPasswordProtectionDCAgent` cmdlet może służyć do wyświetlania podstawowych informacji o różnych agentów kontrolera domeny działających w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowanych przez uruchomione usługi agenta kontrolera domeny.

Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Różne właściwości są aktualizowane przez każdą usługę agenta kontrolera domeny w przybliżeniu co godzinę. Dane nadal podlegają opóźnieniu replikacji usługi Active Directory.

Zakres zapytania polecenia cmdlet może mieć wpływ przy użyciu parametrów —Forest lub –Domain.

Jeśli wartość Pulsu Przeciwnego dostępu przestarzałe, może to być objaw, że agent kontrolera domeny ochrony haseł usługi Azure AD na tym kontrolerze domeny nie jest uruchomiony lub został odinstalowany lub komputer został obniżony i nie jest już kontrolerem domeny.

Jeśli wartość PasswordPolicyDateUTC przestarzałe, może to być objaw, że agent kontrolera kontrolera azure ad protection na tym komputerze nie działa poprawnie.

## <a name="dc-agent-newer-version-available"></a>Dostępna nowsza wersja agenta kontrolera domeny

Usługa agenta kontrolera domeny zarejestruje zdarzenie ostrzegawcze 30034 w dzienniku operacyjnym po wykryciu, że dostępna jest nowsza wersja oprogramowania agenta kontrolera domeny, na przykład:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Powyższe zdarzenie nie określa wersji nowszego oprogramowania. Należy przejść do łącza w wiadomości o zdarzeniu, aby uzyskać te informacje.

> [!NOTE]
> Pomimo odwołań do "autoupgrade" w powyższym komunikacie o zdarzeniu, oprogramowanie agenta kontrolera domeny nie obsługuje obecnie tej funkcji.

## <a name="proxy-service-event-logging"></a>Rejestrowanie zdarzeń usługi proxy

Usługa proxy emituje minimalny zestaw zdarzeń do następujących dzienników zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Należy zauważyć, że dziennik śledzenia jest domyślnie wyłączony.

> [!WARNING]
> Po włączeniu dziennik śledzenia odbiera dużą liczbę zdarzeń, co może mieć wpływ na wydajność hosta serwera proxy. W związku z tym ten dziennik powinien być włączony tylko wtedy, gdy problem wymaga głębszego badania, a następnie tylko przez minimalny czas.

Zdarzenia są rejestrowane przez różne składniki serwera proxy przy użyciu następujących zakresów:

|Składnik |Zakres identyfikatora zdarzenia|
| --- | --- |
|Proces hostingu usługi proxy| 10000-19999|
|Podstawowa logika biznesowa usługi proxy| 20000-29999|
|Polecenia cmdlet programu PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Rejestrowanie tekstu usługi proxy

Usługę proxy można skonfigurować do zapisu w dzienniku tekstowym, ustawiając następującą wartość rejestru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (wartość REG_DWORD)

Rejestrowanie tekstu jest domyślnie wyłączone. Ponowne uruchomienie usługi proxy jest wymagane, aby zmiany tej wartości zostały wprowadzone. Po włączeniu usługa proxy zapisze się do pliku dziennika znajdującego się pod:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Dziennik tekstu odbiera te same wpisy na poziomie debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zazwyczaj jest w łatwiejszym formacie do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu ten dziennik odbiera dużą liczbę zdarzeń i może mieć wpływ na wydajność komputera. W związku z tym ten rozszerzony dziennik powinien być włączony tylko wtedy, gdy problem wymaga głębszego badania, a następnie tylko przez minimalny czas.

## <a name="powershell-cmdlet-logging"></a>Rejestrowanie polecenia cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, które powodują zmianę stanu (na przykład Register-AzureADPasswordProtectionProxy) zwykle rejestruje zdarzenie wyniku w dzienniku operacyjnym.

Ponadto większość poleceń cmdlet programu PowerShell ochrony hasłem usługi Azure AD będzie zapisywać w dzienniku tekstowym znajdującym się pod:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Jeśli wystąpi błąd polecenia cmdlet i przyczyna i\lub rozwiązanie nie jest łatwo widoczne, te dzienniki tekstu mogą być również konsultowane.

## <a name="proxy-discovery"></a>Odnajdowanie serwera proxy

Polecenie `Get-AzureADPasswordProtectionProxy` cmdlet może służyć do wyświetlania podstawowych informacji o różnych usługach proxy ochrony hasłem usługi Azure AD działających w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowanych przez uruchomione usługi proxy.

Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Różne właściwości są aktualizowane przez każdą usługę proxy w przybliżeniu co godzinę. Dane nadal podlegają opóźnieniu replikacji usługi Active Directory.

Zakres zapytania polecenia cmdlet może mieć wpływ przy użyciu parametrów —Forest lub –Domain.

Jeśli wartość PulsuUtC przestarzałe, może to być objaw, że serwer proxy ochrony hasłem usługi Azure AD na tym komputerze nie jest uruchomiony lub został odinstalowany.

## <a name="proxy-agent-newer-version-available"></a>Dostępna nowsza wersja agenta proxy

Usługa proxy zarejestruje zdarzenie ostrzegawcze 20002 w dzienniku operacyjnym po wykryciu, że jest dostępna nowsza wersja oprogramowania proxy, na przykład:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Powyższe zdarzenie nie określa wersji nowszego oprogramowania. Należy przejść do łącza w wiadomości o zdarzeniu, aby uzyskać te informacje.

To zdarzenie zostanie wyemitowane, nawet jeśli agent proxy jest skonfigurowany z włączoną automatyczną przetężoną funkcją.

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów z ochroną hasłem usługi Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Aby uzyskać więcej informacji na temat globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zakaz złych haseł](concept-password-ban-bad.md)
