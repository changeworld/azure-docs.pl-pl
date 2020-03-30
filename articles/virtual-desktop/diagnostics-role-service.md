---
title: Pulpit wirtualny systemu Windows diagnozuje problemy — platforma Azure
description: Jak korzystać z funkcji diagnostyki pulpitu wirtualnego systemu Windows do diagnozowania problemów.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254263"
---
# <a name="identify-and-diagnose-issues"></a>Identyfikowanie i diagnozowanie problemów

Pulpit wirtualny systemu Windows oferuje funkcję diagnostyczną, która umożliwia administratorowi identyfikowanie problemów za pośrednictwem jednego interfejsu. Role pulpitu wirtualnego systemu Windows rejestrują działanie diagnostyczne za każdym razem, gdy użytkownik wchodzi w interakcję z systemem. Każdy dziennik zawiera istotne informacje, takie jak role pulpitu wirtualnego systemu Windows zaangażowane w transakcję, komunikaty o błędach, informacje o dzierżawie i informacje o użytkowniku. Działania diagnostyczne są tworzone zarówno przez użytkownika końcowego, jak i akcje administracyjne i można je podzielić na trzy główne przedziały:

* Działania związane z subskrypcją pliku danych: użytkownik końcowy wyzwala te działania za każdym razem, gdy próbuje połączyć się z kanałem informacyjnym za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
* Działania związane z połączeniem: użytkownik końcowy wyzwala te działania za każdym razem, gdy próbuje połączyć się z pulpitem lub aplikacją RemoteApp za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
* Działania związane z zarządzaniem: administrator wyzwala te działania za każdym razem, gdy wykonuje operacje zarządzania w systemie, takie jak tworzenie pul hostów, przypisywanie użytkowników do grup aplikacji i tworzenie przypisań ról.
  
Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik końcowy doświadcza problemów z łącznością sieciową.

Aby rozpocząć, [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnozowanie problemów z programem PowerShell

Diagnostyka pulpitu wirtualnego systemu Windows używa tylko jednego polecenia cmdlet programu PowerShell, ale zawiera wiele opcjonalnych parametrów ułatwiające zawężenie i wyizolowanie problemów. W poniższych sekcjach przedstawiono polecenia cmdlet, które można uruchomić w celu zdiagnozowania problemów. Większość filtrów można stosować razem. Wartości wymienione w nawiasach, takie jak `<tenantName>`, powinny zostać zastąpione wartościami, które mają zastosowanie do sytuacji.

>[!IMPORTANT]
>Funkcja diagnostyki jest do rozwiązywania problemów z jednym użytkownikiem. Wszystkie kwerendy korzystające z programu PowerShell muszą zawierać parametry *-UserName* lub *-ActivityID.* Aby uzyskać możliwości monitorowania, użyj usługi Log Analytics. Aby uzyskać więcej informacji na temat wysyłania danych diagnostycznych do obszaru roboczego, zobacz [Korzystanie z usługi Log Analytics.](diagnostics-log-analytics.md) 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrowanie działań diagnostycznych według użytkownika

**Parametr -UserName** zwraca listę działań diagnostycznych zainicjowanych przez określonego użytkownika, jak pokazano w poniższym przykładowym podaniu cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametr **-UserName** można również łączyć z innymi opcjonalnymi parametrami filtrowania.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrowanie działań diagnostycznych według czasu

Zwróconą listę działań diagnostycznych można filtrować za pomocą parametrów **-StartTime** i **-EndTime.** **Parametr -StartTime** zwróci listę działań diagnostycznych, począwszy od określonej daty, jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Parametr **-EndTime** można dodać do polecenia cmdlet za pomocą parametru **-StartTime,** aby określić określony okres czasu, dla którego chcesz otrzymywać wyniki. Poniższe przykładowe polecenie cmdlet zwróci listę działań diagnostycznych od 1 sierpnia do 10 sierpnia.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametry **-StartTime** i **-EndTime** można również łączyć z innymi opcjonalnymi parametrami filtrowania.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrowanie działań diagnostycznych według typu działania

Można również filtrować działania diagnostyczne według typu działania za pomocą parametru **-ActivityType.** Następujące polecenie cmdlet zwróci listę połączeń użytkownika końcowego:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Następujące polecenie cmdlet zwróci listę zadań zarządzania administratorami:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Polecenie cmdlet **Get-RdsDiagnosticActivities** nie obsługuje obecnie określania kanału informacyjnego jako activitytype.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrowanie działań diagnostycznych według wyniku

Listę zwróconych działań diagnostycznych można filtrować według wyniku za pomocą parametru **-Outcome.** Poniższe przykładowe polecenie cmdlet zwróci listę udanych działań diagnostycznych.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Poniższe przykładowe polecenie cmdlet zwróci listę nieudanych działań diagnostycznych.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Parametr **-Outcome** można również łączyć z innymi opcjonalnymi parametrami filtrowania.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Pobieranie określonego działania diagnostycznego według identyfikatora działania

**Parametr -ActivityId** zwraca określone działanie diagnostyczne, jeśli istnieje, jak pokazano w poniższym przykładowym powiększe polecenie cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Wyświetlanie komunikatów o błędach dla działania, które nie powiodło się według identyfikatora działania

Aby wyświetlić komunikaty o błędach dla działania nie powiodło się, należy uruchomić polecenie cmdlet z **parametrem -Detailed.** Listę błędów można wyświetlić, uruchamiając polecenie cmdlet **Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Pobieranie szczegółowych czynności diagnostycznych

**Parametr -Detailed** zawiera dodatkowe szczegóły dla każdego zwracanego działania diagnostycznego. Format dla każdego działania różni się w zależności od typu działania. **Parametr -Detailed** można dodać do dowolnej **kwerendy Get-RdsDiagnosticActivities,** jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Typowe scenariusze błędów

Scenariusze błędów są podzielone na wewnętrzne do usługi i zewnętrzne do pulpitu wirtualnego systemu Windows.

* Problem wewnętrzny: określa scenariusze, których nie można złagodzić przez administratora dzierżawy i muszą zostać rozwiązane jako problem z obsługą. Podczas przekazywania opinii za pośrednictwem [społeczności technicznej pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)należy podać identyfikator działania i przybliżone ramy czasowe wystąpienia problemu.
* Problem zewnętrzny: odnoszą się do scenariuszy, które mogą być złagodzone przez administratora systemu. Są one zewnętrzne do pulpitu wirtualnego systemu Windows.

W poniższej tabeli wymieniono typowe błędy, na które mogą napotkać administratorzy.

>[!NOTE]
>Lista ta zawiera najczęściej występujące błędy i jest aktualizowana w regularnym rytmie. Aby mieć pewność, że masz najbardziej aktualne informacje, należy sprawdzić ponownie w tym artykule co najmniej raz w miesiącu.

### <a name="external-management-error-codes"></a>Kody błędów zarządzania zewnętrznego

|Kod numeryczny|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|3|Nieautoryzowaneaccess|Użytkownik, który próbował uruchomić administracyjne polecenie cmdlet programu PowerShell, nie ma uprawnień do tego lub błędnie wpisał swoją nazwę użytkownika.|
|1000|NajemcaNieznaj|Wprowadzona nazwa dzierżawy nie jest zgodna z istniejącymi dzierżawcami. Przejrzyj nazwę dzierżawy dla literówek i spróbuj ponownie.|
|1006|NajemcaCannotBeRemovedHasSessionHostPools|Nie można usunąć dzierżawy, tak długo, jak zawiera obiekty. Najpierw usuń pule hosta sesji, a następnie spróbuj ponownie.|
|2000|HostPoolNotFound|Wprowadzona nazwa puli hosta nie jest zgodna z istniejącymi pulami hostów. Przejrzyj nazwę puli hostów dla literówek i spróbuj ponownie.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Nie można usunąć puli hosta, o ile zawiera obiekty. Najpierw usuń wszystkie grupy aplikacji w puli hostów.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Przed usunięciem puli hostów sesji należy najpierw usunąć wszystkie hosty sesji.|
|5001|SessionHostNotFound (HostFostFound)|Host sesji, którego dotyczy zapytanie, może być w trybie offline. Sprawdź stan puli hosta.|
|5008|SessionHostUserSessionsExist |Przed wykonaniem zamierzonego działania zarządzania należy wylogować wszystkich użytkowników na hoście sesji.|
|6000|AppGroupNotFound|Wprowadzona nazwa grupy aplikacji nie jest zgodna z istniejącymi grupami aplikacji. Przejrzyj nazwę grupy aplikacji dla literówek i spróbuj ponownie.|
|6022|Funkcja RemoteAppNotFound|Wprowadzona nazwa aplikacji RemoteApp nie jest zgodna z żadnymi plikami RemoteApp. Przejrzyj nazwę remoteapp dla literówek i spróbuj ponownie.|
|6010|PublishedItemsExist|Nazwa zasobu, który próbujesz opublikować, jest taka sama jak zasób, który już istnieje. Zmień nazwę zasobu i spróbuj ponownie.|
|7002|NazwaNotValidWhiteSpace|Nie używaj białych spacji w nazwie.|
|8000|InvalidAutoryzacjaRoleScope|Wprowadzona nazwa roli nie jest zgodna z żadnymi istniejącymi nazwami ról. Przejrzyj nazwę roli literówek i spróbuj ponownie. |
|8001|UserNotFound (Nieukrycie użytkownika) |Wprowadzona nazwa użytkownika nie jest zgodna z istniejącymi nazwami użytkowników. Przejrzyj nazwę literówek i spróbuj ponownie.|
|8005|UserNotFoundInAAD |Wprowadzona nazwa użytkownika nie jest zgodna z istniejącymi nazwami użytkowników. Przejrzyj nazwę literówek i spróbuj ponownie.|
|8008|Wymagane przyz.|Postępuj zgodnie z instrukcjami [tutaj,](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) aby udzielić zgody dla dzierżawy.|

### <a name="external-connection-error-codes"></a>Kody błędów połączeń zewnętrznych

|Kod numeryczny|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Host sesji nie jest poprawnie przyłączony do usługi Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsNiezdrowy|Połączenia nie powiodły się, ponieważ host sesji jest niedostępny. Sprawdź stan zdrowia gospodarza sesji.|
|-2146233088|PołączenieFailedClientDisconnect|Jeśli ten błąd jest często widoczny, upewnij się, że komputer użytkownika jest podłączony do sieci.|
|-2146233088|PołączenieFailedNoHealthyRdshDostępne|Sesja, z na które próbował się połączyć użytkownik hosta, nie jest w dobrej kondycji. Debugowanie maszyny wirtualnej.|
|-2146233088|ConnectionFailedUserNotAuthorized|Użytkownik nie ma uprawnień dostępu do opublikowanej aplikacji lub pulpitu. Błąd może pojawić się po usunięciu opublikowanych zasobów przez administratora. Poproś użytkownika o odświeżenie kanału informacyjnego w aplikacji Pulpit zdalny.|
|2|FileNotFound (PlikNotfound)|Aplikacja, do którą użytkownik próbował uzyskać dostęp, jest niepoprawnie zainstalowana lub ustawiona na niepoprawną ścieżkę.|
|3|Nieprawidłowecredentials|Nazwa użytkownika lub hasło wprowadzone przez użytkownika nie jest zgodne z istniejącymi nazwami użytkownika ani hasłami. Przejrzyj poświadczenia dla literówek i spróbuj ponownie.|
|8|PołączenieBroken|Połączenie między klientem a bramą lub serwerem porzucone. Nie jest potrzebne żadne działanie, chyba że dzieje się to nieoczekiwanie.|
|14|UnexpectedNetworkRozłącze|Połączenie z siecią porzucone. Poproś użytkownika o ponowne połączenie.|
|24|OdwróconyconnectPonietowany|Maszyna wirtualna hosta nie ma bezpośredniej linii wzroku do bramy usług pulpitu zdalnego. Upewnij się, że adres IP bramy może zostać rozwiązany.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rolach na pulpicie wirtualnym systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).

Aby wyświetlić listę dostępnych poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows, zobacz [odwołanie programu PowerShell](/powershell/windows-virtual-desktop/overview).
