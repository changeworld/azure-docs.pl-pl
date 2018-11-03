---
title: Rozwiązywanie problemów i rejestrowanie w wersji zapoznawczej ochrony haseł usługi Azure AD
description: Zrozumienie ochrona za pomocą hasła usługi Azure AD w wersji zapoznawczej rejestrowanie i rozwiązywanie typowych problemów
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1e5782ce3421cc5f0d2e0e51484d4bbe6b9eb6ab
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978642"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Wersja zapoznawcza: Usługi Azure AD monitorowania ochrony hasłem, raportowanie i rozwiązywania problemów

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po wdrożeniu ochrona za pomocą hasła usługi Azure AD monitorowania i raportowania są podstawowe zadania. W tym artykule przechodzi do szczegółów, aby pomóc, że rozumiesz, gdzie każda usługa rejestruje informacje i instrukcje sporządzić raport na temat użytkowania ochrona za pomocą hasła usługi Azure AD.

## <a name="on-premises-logs-and-events"></a>W środowisku lokalnym dzienników i zdarzeń

### <a name="dc-agent-admin-log"></a>Dziennik administratora agenta kontrolera domeny

Na każdym kontrolerze domeny DC oprogramowani usługi zapisuje wyniki jego poprawności hasła (i inne stanu) w lokalnym dzienniku zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Zdarzenia są rejestrowane przez różne składniki agenta kontrolera domeny przy użyciu następujących zakresów:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Biblioteki dll filtru haseł agenta kontrolera domeny| 10000 19999|
|Proces hostingu usługi agenta kontrolera domeny| 20000 29999|
|Logikę walidacji zasad usługi agenta kontrolera domeny| 30000 39999|

Dla operacji sprawdzania poprawności hasła pomyślne jest zazwyczaj jedno zdarzenie zarejestrowane z dll filtru haseł agenta kontrolera domeny. Na potrzeby przechodzenia operacji sprawdzania poprawności hasła, są zwykle dwa zdarzenia zarejestrowane: jeden z usługi agenta kontrolera domeny i jeden z dll filtru haseł agenta kontrolera domeny.

Dyskretne zdarzenia do przechwycenia tych sytuacji są rejestrowane na podstawie następujących czynników:

* Czy jest podanym hasłem ustawił lub zmienił.
* Czy sprawdzanie poprawności hasła podanego zakończony powodzeniem lub niepowodzeniem.
* Czy Weryfikacja nie powiodła się z powodu Microsoft vs zasady globalne zasady organizacji.
* Czy trybu tylko inspekcja jest aktualnie włączone czy wyłączone dla bieżących zasad haseł.

Kluczowe informacje o zdarzeniach powiązanych sprawdzania poprawności hasła są następujące:

|   |Zmiana hasła |Ustawianie hasła|
| --- | :---: | :---: |
|Powodzenie |10014 |10015|
|Niepowodzenie (nie przeszedł klienta zasady haseł)| 10016, 30002| 10017, 30003|
|Niepowodzenie (nie przeszedł zasadami haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasady haseł klienta)| 10024, 30008| 10025, 30007|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasadami haseł firmy Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Przychodzące hasła są sprawdzane Microsoft hasło globalne listy najpierw; w przypadku niepowodzenia nie dalsze przetwarzanie odbywa się. Jest to takie samo zachowanie, ponieważ jest wykonywane na zmiany hasła w systemie Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Komunikat dziennika zdarzeń przykładowy identyfikator zdarzenia 10014 (Ustawianie hasła pomyślne)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 10017 i 30003 (Ustawianie hasła nie powiodło się)

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30001 (hasło zaakceptowane ze względu na brak zasad, które są dostępne)

```
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

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30006 (nowe zasady są wymuszane)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Dziennik operacyjny agenta kontrolera domeny

Usługa agenta DC zarejestruje także zdarzenia operacyjne związane z następujących dzienników:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>W dzienniku śledzenia agenta kontrolera domeny

Usługa agenta kontrolera domeny także zalogować się następujący dziennik zdarzeń pełnego śledzenia na poziomie debugowania:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Rejestrowanie śledzenia jest domyślnie wyłączona.

> [!WARNING]
>  Po włączeniu dziennika śledzenia odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

#### <a name="dc-agent-text-logging"></a>DC Agent rejestrowania w pliku tekstowym

Można skonfigurować usługę agenta kontrolera domeny do zapisu do dziennika tekstowego, ustawiając następującą wartość rejestru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters! EnableTextLogging = 1 (wartość REG_DWORD)

Rejestrowania w pliku tekstowym jest domyślnie wyłączona. Ponowne uruchomienie usługi agenta kontrolera domeny jest wymagana dla zmiany tej wartości, aby zastosować zmiany. Po włączeniu kontroler domeny usługi agenta są zapisywane w pliku dziennika, znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Dziennik tekstowy odbiera tych samych wpisy poziom debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zwykle jest w formacie łatwiejsze do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

### <a name="azure-ad-password-protection-proxy-service"></a>Hasło ochrony serwera proxy usługi programu Azure AD

#### <a name="proxy-service-event-logs"></a>Dzienniki zdarzeń usługi serwera proxy

Usługa serwera Proxy emituje to minimalny zestaw zdarzeń do następujących dzienników zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Usługa serwera Proxy także zalogować się następujący dziennik zdarzeń pełnego śledzenia na poziomie debugowania:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Rejestrowanie śledzenia jest domyślnie wyłączona.

> [!WARNING]
> Po włączeniu dziennika śledzenia odbiera dużej liczby zdarzeń i może to mieć wpływ na wydajność hosta serwera proxy. W związku z tym, ten dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

#### <a name="proxy-service-text-logging"></a>Rejestrowania w pliku tekstowym usługa serwera proxy

Usługa serwera Proxy można skonfigurować do zapisu do dziennika tekstowego, ustawiając następującą wartość rejestru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (wartość REG_DWORD)

Rejestrowania w pliku tekstowym jest domyślnie wyłączona. Ponowne uruchomienie usługi serwera Proxy jest wymagany dla zmiany tej wartości, aby zastosować zmiany. Po włączeniu serwera Proxy usługi są zapisywane w pliku dziennika, znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Dziennik tekstowy odbiera tych samych wpisy poziom debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zwykle jest w formacie łatwiejsze do przeglądania i analizowania.

> [!WARNING]
> Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

#### <a name="powershell-cmdlet-logging"></a>Funkcje rejestrowania polecenia cmdlet programu PowerShell

Większość ochrony hasłem usługi Azure AD poleceń cmdlet programu Powershell będzie zapisywała Dziennik tekstowy znajdujący się w folderze:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Jeśli wystąpi błąd polecenia cmdlet i rozwiązanie i/lub Przyczyna nie jest łatwe do rozwiązania, mogą również konsultacje te dzienniki tekstowe.

### <a name="emergency-remediation"></a>Korygowanie awaryjnego

Jeśli sytuacja występuje, gdy usługa agenta kontrolera domeny powoduje problemy, Usługa agenta kontrolera domeny może być natychmiast zamknięty. Dll filtru haseł agenta kontrolera domeny nadal próbuje wywołać bez działającej usługi i będzie rejestrować zdarzenia ostrzeżeń (10012, 10013), ale wszystkie przychodzące hasła są akceptowane w tym samym czasie. Usługa agenta kontrolera domeny może także można skonfigurować za pomocą Windows Menedżer sterowania usługami za pomocą typu uruchamiania "Disabled" zgodnie z potrzebami.

### <a name="performance-monitoring"></a>Monitorowanie wydajności

Oprogramowanie usługi agenta DC instaluje obiektu licznika wydajności, o nazwie **ochrony haseł usługi Azure AD**. Obecnie dostępne są następujące liczniki wydajności:

|Nazwa licznika wydajności | Opis|
| --- | --- |
|Przetworzone hasła |Ten licznik wskazuje całkowitą liczbę haseł przetwarzane (zaakceptowane lub odrzucone) od momentu ostatniego uruchomienia.|
|Zaakceptowane hasła |Ten licznik wskazuje całkowitą liczbę haseł, które zostały zaakceptowane od momentu ostatniego uruchomienia.|
|Hasła odrzucone |Ten licznik wskazuje całkowitą liczbę haseł, które zostały odrzucone od momentu ostatniego uruchomienia.|
|Hasło Filtrowanie żądań w toku |Ten licznik wskazuje liczbę żądań filtru haseł obecnie w toku.|
|Szczytowy hasło Filtrowanie żądań |Ten licznik przedstawia szczytową liczbę jednoczesnych hasło Filtrowanie żądań od momentu ostatniego uruchomienia.|
|Błędy żądań filtru haseł |Ten licznik wyświetla całkowitą liczbę żądań filtru haseł, które nie powiodło się z powodu błędu od momentu ostatniego uruchomienia. Mogą wystąpić błędy, kiedy nie jest uruchomiona usługa agenta ochrony kontrolera domeny hasła usługi Azure AD.|
|Filtr haseł żądania/s |Ten licznik wyświetla szybkość jaką są przetwarzane hasła.|
|Czas przetwarzania żądania filtru haseł |Ten licznik wyświetla średni czas wymagany do przetwarzania żądań filtru haseł.|
|Czas przetwarzania żądania filtru haseł szczytu |Ten licznik wyświetla żądanie filtru hasła szczytu przetwarzania czasu od momentu ostatniego uruchomienia.|
|Hasła są akceptowane z powodu tryb inspekcji |Ten licznik wskazuje całkowitą liczbę haseł, zwykle będzie został odrzucony, które zostały zaakceptowane, ponieważ zasady haseł został skonfigurowany w trybie inspekcji (od momentu ostatniego uruchomienia).|

## <a name="directory-services-repair-mode"></a>Trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchomiony w trybie naprawy usług katalogowych, Usługa agenta kontrolera domeny wykrywa to powoduje wszystkich sprawdzenie poprawności hasła lub działań wykonawczych, można wyłączyć niezależnie od aktualnie aktywne zasady konfiguracji.

## <a name="domain-controller-demotion"></a>Obniżenia poziomu kontrolera domeny

Możliwe jest obniżenie poziomu kontrolera domeny, który wciąż działa oprogramowanie agenta kontrolera domeny. Administratorzy Pamiętaj jednak, oprogramowanie agenta kontrolera domeny jest uruchomiony i jest kontynuowane wymuszanie bieżące zasady haseł podczas wykonywania procedury obniżania poziomu. Nowe hasło administratora lokalnego konta (określonego jako część operacji obniżania poziomu) jest weryfikowana, podobnie jak inne hasło. Firma Microsoft zaleca, można wybrać bezpiecznych haseł dla konta administratora lokalnego w ramach procedury obniżania poziomu kontrolera domeny; Jednakże weryfikacji nowe hasło administratora lokalnego konta przez oprogramowanie agenta kontrolera domeny może być znaczący wpływ na istniejące procedury operacyjne obniżania poziomu.

Po obniżania poziomu zakończyła się pomyślnie, a kontroler domeny zostanie ponownie uruchomiony i jest ponownie uruchomiony jako serwer członkowski normalne, przywraca uruchomiony w trybie pasywnym oprogramowanie agenta kontrolera domeny. Może on następnie odinstalowano w dowolnym momencie.

## <a name="removal"></a>Usunięcie

Jeśli zostanie podjęta decyzja, aby odinstalować oprogramowanie w publicznej wersji zapoznawczej i czyszczenia stan wszystkich powiązanych z domeny i lasu, to zadanie można osiągnąć wykonując następujące czynności:

> [!IMPORTANT]
> Należy wykonać następujące kroki w kolejności. Jeśli dowolne wystąpienie usługi serwera Proxy zostanie pozostawiony jako uruchomiony okresowo ponownie utworzy jej obiektu serviceConnectionPoint. Jeśli dowolne wystąpienie usługi agenta kontrolera domeny jest w trybie ciągłym okresowo ponownie utworzy jej obiektu serviceConnectionPoint i folderu sysvol stanu.

1. Odinstaluj ochrony hasłem oprogramowanie serwera Proxy z wszystkich maszyn. Ten krok jest **nie** konieczne jest ponowne uruchomienie.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. W tym kroku **wymaga** ponowne uruchomienie komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera Proxy w każdym kontekście nazewnictwa domeny. Może zostać odnalezionych lokalizacji tych obiektów za pomocą następującego polecenia środowiska Powershell usługi Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie. 

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekście nazewnictwa domeny. Może istnieć tylko jeden tych obiektów na kontrolerze domeny w lesie, w zależności od tego, jak powszechnie prapremiery publicznej została wdrożona. Lokalizacja tego obiektu może zostać odnalezionych za pomocą następującego polecenia środowiska Powershell usługi Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywana w kontenerze w kontekście nazewnictwa konfiguracji w usłudze Active Directory. Można go odnalezione i usunięte w następujący sposób:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Ręcznie usuń wszystkie sysvol związane stanu przez ręczne usuwanie następujący folder i całą jego zawartość:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   W razie potrzeby tę ścieżkę można również uzyskać dostęp lokalnie na kontrolerze danej domeny; Domyślna lokalizacja będzie podobny do następującej ścieżki:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ta ścieżka jest inny, jeśli skonfigurowano udziału sysvol, w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat list globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zablokuj błędnego hasła](concept-password-ban-bad.md)
