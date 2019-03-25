---
title: Identyfikowanie problemów z funkcją Diagnostyka Windows wirtualnego pulpitu (wersja zapoznawcza) — Azure
description: Zawiera opis funkcji diagnostyki Windows wirtualnego pulpitu (wersja zapoznawcza) i jak z niej korzystać.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: a9b8be58e8dfb27fbe896cf1c8d8dc0e91e3b24c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402851"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identyfikowanie problemów z funkcją Diagnostyka

Windows wirtualnego pulpitu (wersja zapoznawcza) oferuje funkcję diagnostyki, która pozwala administratorowi na identyfikowaniu problemów za pomocą jednego interfejsu. Role pulpitu wirtualnego Windows rejestrowanie diagnostyczne działania zawsze wtedy, gdy użytkownik korzysta z systemu. Każdy dziennik zawiera istotne informacje, takie jak role pulpitu wirtualnego Windows zaangażowane w transakcji, komunikaty o błędach, informacji o dzierżawie i informacje o użytkowniku. Działania diagnostyczne są tworzone przez użytkowników końcowych i czynności administracyjnych i mogą zostać podzielone na trzy przedziały główne:

* Źródła danych subskrypcji działań: użytkownik końcowy uruchamia te działania zawsze wtedy, gdy użytkownik próbuje nawiązać połączenie z ich źródła danych za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
* Działania połączenia: użytkownik końcowy uruchamia te działania zawsze wtedy, gdy spróbują połączyć się z pulpitu lub programów RemoteApp za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
* Działania związane z zarządzaniem: administrator wyzwala te działania, gdy wykonują operacje zarządzania w systemie, takie jak tworzenie pul hosta, przypisywanie użytkowników do grupy aplikacji i tworzenie przypisań ról.
  
Połączenia, w których nie pulpitu wirtualnego Windows nie będą widoczne wyniki diagnostyki ponieważ sama usługa roli diagnostyki jest częścią Windows pulpitu wirtualnego. Problemy z połączeniem pulpitu wirtualnego Windows może się zdarzyć, gdy użytkownik końcowy występują problemy z połączeniem sieciowym.

Aby rozpocząć pracę, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="diagnose-issues-with-powershell"></a>Diagnozowanie problemów przy użyciu programu PowerShell

Wirtualny diagnostyki pulpitu Windows używa tylko jednego polecenia cmdlet programu PowerShell, ale zawiera wiele parametrów opcjonalnych Zawęź i wyizolować problemy. Poniższe sekcje zawierają polecenia cmdlet można uruchomić w celu diagnozowania problemów. Większość filtrów mogą być stosowane razem. Wartości wymienione w nawiasy kwadratowe, takie jak `<tenantName>`, należy zastąpić wartościami, które są stosowane do swojej sytuacji.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Pobrać diagnostycznych działania w ramach dzierżawy

Możesz pobrać działania diagnostyczne, wprowadzając **Get RdsDiagnosticsActivities** polecenia cmdlet. Następujące przykładowe polecenie cmdlet zwróci listę działań diagnostycznych, posortowane od najczęściej do najrzadziej najstarsza.

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName>
```

Podobnie jak inne polecenia cmdlet programu PowerShell pulpitu wirtualnego Windows, należy użyć **- TenantName** parametru do określenia nazwy dzierżawy, którego chcesz użyć dla zapytania. Nazwa dzierżawy jest stosowana dla prawie wszystkich zapytań działania diagnostyczne.

### <a name="retrieve-detailed-diagnostic-activities"></a>Pobrać szczegółowe działania diagnostyczne

**— Szczegółowe** parametru udostępnia dodatkowe szczegóły dla każdego działania diagnostyczne zwracane. Format dla każdego działania różni się w zależności od typu działania. **— Szczegółowe** parametru można dodać do dowolnego **Get RdsDiagnosticsActivities** zbadać, jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Pobieranie określonego Identyfikatora działania przez działania diagnostyczne

**- ActivityId** parametr zwraca określonego działania diagnostyczne, jeśli istnieje, jak pokazano na następującego przykładowego polecenia cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrowanie działań diagnostycznych przez użytkownika

**- UserName** parametr zwraca listę działań diagnostycznych zainicjowane przez określonego użytkownika, zgodnie z następującego przykładowego polecenia cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**- UserName** parametru można również łączyć z innymi opcjonalne parametry filtrowania.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrowanie działań diagnostycznych według czasu

Można filtrować listę zwróconych działania diagnostyczne z **— godzina rozpoczęcia** i **- EndTime** parametrów. **— Godzina rozpoczęcia** parametr zwróci listę działania diagnostyczne, zaczynając od określonej daty, jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

**- EndTime** można dodać parametr do polecenia cmdlet z **— godzina rozpoczęcia** parametru do określenia określonym okresie, które chcesz otrzymywać wyniki. Następujące przykładowe polecenie cmdlet zwróci listę działań diagnostycznych znajdujące 1 sierpnia i 10 sierpnia.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**— Godzina rozpoczęcia** i **- EndTime** parametrów można również łączyć z innymi opcjonalne parametry filtrowania.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrowanie działań diagnostycznych według typu działania

Można również filtrować diagnostycznych działań w oparciu o typ działania z **- ActivityType** parametru. Następujące polecenie cmdlet zwróci listę połączeń użytkowników końcowych:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Następujące polecenie cmdlet zwróci listę zadań zarządzania administratora:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get RdsDiagnosticActivities** polecenia cmdlet obecnie nie obsługuje określania źródła danych jako właściwości ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrowanie działań diagnostycznych według wyników

Lista zwróconych działania diagnostyczne można filtrować według wyniku z **— wynik** parametru. Następujące przykładowe polecenie cmdlet zwróci listę pomyślnego działania diagnostyczne.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Następujące przykładowe polecenie cmdlet zwróci listę działań diagnostyki nie powiodło się.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**— Wynik** parametru można również łączyć z innymi opcjonalne parametry filtrowania.

## <a name="common-error-scenarios"></a>Typowe scenariusze błąd

Błąd scenariusze są podzielone na wewnętrznych z usługą i zewnętrzne w stosunku do wirtualnego pulpitu Windows.

* Problem wewnętrzny: Określa scenariuszy, których nie da się ograniczyć przez administratora dzierżawy i muszą zostać rozpoznane jako problemu wymagającego pomocy technicznej. Gdy tworząc bilet wprowadź identyfikator działania, nazwa dzierżawy oraz przybliżony przedział czasu problem wystąpił.
* Zewnętrzny problem: odnoszą się do scenariuszy, które może być ograniczona przez administratora systemu. Są to zewnętrzne w stosunku do wirtualnego pulpitu Windows.

W poniższej tabeli wymieniono typowe błędy napotkać administratorów.

>[!NOTE]
>Ta wersja zapoznawcza nie obejmuje pełną Kategoryzacja błędów i zostanie zaktualizowana w regularnych odstępach czasu. Aby upewnić się, że masz aktualne informacje, należy zapoznać ponownie w tym artykule co najmniej raz w miesiącu.

### <a name="external-management-error-codes"></a>Kody błędów zarządzanie zewnętrzne

|Kod liczbowy|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|3|UnauthorizedAccess|Użytkownik, który próbowano uruchomić administracyjne polecenia cmdlet programu PowerShell nie ma uprawnień, aby to zrobić lub źle wpisano swoją nazwę użytkownika.|
|1000|TenantNotFound|Wprowadzona nazwa dzierżawy nie odpowiada wszelkich istniejących dzierżaw. Przejrzyj nazwę dzierżawcy literówki i spróbuj ponownie.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Nie można usunąć dzierżawę, jak długo zawiera obiekty. Najpierw usuń pule hosta sesji, a następnie spróbuj ponownie.|
|2000|HostPoolNotFound|Wprowadzona nazwa puli hosta nie odpowiada wszelkich istniejących pul hosta. Przejrzyj nazwę puli hosta literówki i spróbuj ponownie.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Nie można usunąć puli hosta, tak długo, jak zawiera obiekty. Najpierw usuń wszystkie grupy aplikacji w puli hosta.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Usuń wszystkie hosty sesji przed usunięciem puli hosta sesji.|
|5001|SessionHostNotFound|Host sesji, którą możesz zapytań może być w trybie offline. Sprawdź stan puli hosta.|
|5008|SessionHostUserSessionsExist |Przed wykonaniem swoje działania związane z zarządzaniem zamierzony, musisz zalogować się wszyscy użytkownicy na hoście sesji.|
|6000|AppGroupNotFound|Wprowadzona nazwa grupy aplikacji nie odpowiada wszelkich istniejących grup aplikacji. Nazwa grupy aplikacji pisowni przeglądu i spróbuj ponownie.|
|6022|RemoteAppNotFound|Wprowadzona nazwa usługi RemoteApp nie odpowiada wszelkich RemoteApps. Sprawdź nazwę usługi RemoteApp pisowni i spróbuj ponownie.|
|6010|PublishedItemsExist|Nazwa zasobu, który próbujesz opublikować jest taka sama jak zasób, który już istnieje. Zmień nazwę zasobu i spróbuj ponownie.|
|7002|NameNotValidWhiteSpace|Nie używaj biały znak w nazwie.|
|8000|InvalidAuthorizationRoleScope|Wprowadzona nazwa roli nie odpowiada wszelkich istniejących nazw ról. Sprawdź nazwę roli literówki i spróbuj ponownie. |
|8001|UserNotFound |Wprowadzona nazwa użytkownika nie odpowiada wszelkich istniejących nazw użytkowników. Sprawdź nazwę pisowni i spróbuj ponownie.|
|8005|UserNotFoundInAAD |Wprowadzona nazwa użytkownika nie odpowiada wszelkich istniejących nazw użytkowników. Sprawdź nazwę pisowni i spróbuj ponownie.|
|8008|TenantConsentRequired|Postępuj zgodnie z instrukcjami [tutaj](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) dostarczyć zgodę dla dzierżawy.|

### <a name="external-connection-error-codes"></a>Kody błędów połączenia zewnętrznego

|Kod liczbowy|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Host sesji nie jest prawidłowo dołączony do usługi Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Połączenia nie powiodło się, ponieważ host sesji jest niedostępny. Sprawdzanie kondycji hosta sesji.|
|-2146233088|ConnectionFailedClientDisconnect|Jeśli zostanie wyświetlony ten błąd często upewnij się, że na komputerze użytkownika jest podłączony do sieci.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Sesję, którą użytkownik hosta próbujesz się połączyć nie jest w dobrej kondycji. Debugowanie maszyny wirtualnej.|
|-2146233088|ConnectionFailedUserNotAuthorized|Użytkownik nie ma uprawnień dostępu do opublikowanej aplikacji lub pulpitu. Ten błąd może pojawić się po usunięciu programu Administrator opublikowanych zasobów. Poproś użytkownika, aby odświeżyć źródło danych w aplikacji pulpitu zdalnego.|
|2|FileNotFound|Użytkownik próbował uzyskać dostęp do aplikacji jest nieprawidłowo zainstalowana lub równa ścieżka jest niepoprawna.|
|3|InvalidCredentials|Nazwa użytkownika lub hasło które użytkownik wprowadził nie odpowiada istniejącej nazwy użytkowników ani haseł. Sprawdź poświadczenia literówki i spróbuj ponownie.|
|8|ConnectionBroken|Połączenie od klienta do bramy lub serwera zostało przerwane. Nie jest wymagane, chyba że zdarza się nieoczekiwanie żadne działanie.|
|14|UnexpectedNetworkDisconnect|Połączenie z siecią zostało przerwane. Poproś użytkownika próbę nawiązania połączenia.|
|24|ReverseConnectFailed|Maszyna wirtualna hosta ma nie bezpośrednich linii wzroku do bramy usług pulpitu zdalnego. Upewnij się, że może zostać rozpoznany adres IP bramy.|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat ról w ramach pulpitu wirtualnego Windows, zobacz [środowisko w wersji zapoznawczej pulpitu wirtualnego Windows](environment-setup.md).

Aby wyświetlić listę dostępnych poleceń cmdlet programu PowerShell dla pulpitu wirtualnego Windows, zobacz [dokumentacja programu PowerShell](/powershell/windows-virtual-desktop/overview).