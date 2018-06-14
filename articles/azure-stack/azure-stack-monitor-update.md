---
title: Monitorowanie aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych | Dokumentacja firmy Microsoft
description: Informacje o sposobie użycia uprzywilejowanego punktu końcowego do monitorowania stanu aktualizacji dla stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 96eebf340f13f2f5e9e922fee8032d04fce1d130
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2018
ms.locfileid: "27621865"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorowanie aktualizacji w stosie Azure przy użyciu uprzywilejowanych punktu końcowego

*Dotyczy: Azure stosu zintegrowane systemy*

Można monitorować postęp przebiegu aktualizacji stosu Azure uprzywilejowanych punktu końcowego, a wznowienie aktualizacji nie powiodło się, uruchom w ostatnim kroku pomyślne powinien stosu Azure portal staną się niedostępne.  Przy użyciu portalu Azure stos jest to zalecana metoda zarządzania aktualizacjami w stosie Azure.

Następujące nowe polecenia cmdlet programu PowerShell do zarządzania aktualizacjami są zawarte w aktualizacji 1710 stosu Azure zintegrowanych systemów.

| Polecenie cmdlet  | Opis  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Zwraca informacje o stanie aktualizacji aktualnie uruchomione, zakończone lub nie powiodło się. Zawiera ogólny stan operacji aktualizacji, a dokument XML, który opisuje zarówno bieżącego kroku, jak i odpowiadający mu stan. |
| `Get-AzureStackUpdateVerboseLog` | Zwraca pełne dzienniki, które zostały wygenerowane przez aktualizację. |
| `Resume-AzureStackUpdate` | Wznawia nieudana aktualizacja w momencie, w których nie powiodło się. W niektórych scenariuszach może być konieczne kroki zaradcze ukończenie wznowienie aktualizacji.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Sprawdź, czy dostępne są następujące polecenia cmdlet
Ponieważ polecenia cmdlet są nowe w pakiecie aktualizacji 1710 stosu Azure, proces aktualizacji 1710 musi pobrać pewnym przed udostępnieniem możliwość monitorowania. Zazwyczaj te polecenia cmdlet są dostępne, jeśli stan w portalu administratora wskazuje, że aktualizacja 1710 znajduje się w **ponowne uruchomienie magazynu hostów** kroku. W szczególności aktualizacji polecenia cmdlet występuje w ciągu **krok: uruchamiania kroku 2.6 - dozwolonych aktualizacji PrivilegedEndpoint**.

Można również określić, czy polecenia cmdlet są dostępne programowo badając listy poleceń z uprzywilejowanego punktu końcowego. Aby to zrobić, uruchom następujące polecenia z hosta cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej. Ponadto upewnij się, że zaufanego hosta jest uprzywilejowane punktu końcowego. Aby uzyskać więcej informacji, zobacz krok 1 [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Tworzenie sesji programu PowerShell na każdym ERCS maszyn wirtualnych w środowisku Azure stosu (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*-ERCS03). Zastąp *prefiks* z Ciąg prefiksu maszyny wirtualnej, które są specyficzne dla danego środowiska.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Po wyświetleniu monitu o poświadczenia, użyj &lt; *domeny stosu Azure*&gt;\cloudadmin konta lub konta, które jest członkiem grupy CloudAdmins. Dla konta CloudAdmin wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

2. Pobierz pełną listę poleceń, które są dostępne w punkcie końcowym uprzywilejowanych. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Ustal, jeśli uprzywilejowanych punkt końcowy został zaktualizowany.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Lista poleceń specyficznych dla modułu Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Na przykład:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Użyj polecenia cmdlet do zarządzania aktualizacji

> [!NOTE]
> Uruchom następujące polecenia z hosta cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej. Ponadto upewnij się, że zaufanego hosta jest uprzywilejowane punktu końcowego. Aby uzyskać więcej informacji, zobacz krok 1 [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Podłącz do punktu końcowego uprzywilejowanych i przypisz zmiennej sesji

Uruchom następujące polecenia, aby utworzyć sesję programu PowerShell na każdym ERCS maszyn wirtualnych w środowisku Azure stosu (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*-ERCS03) i przypisać zmiennej sesji.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Po wyświetleniu monitu o poświadczenia, użyj &lt; *domeny stosu Azure*&gt;\cloudadmin konta lub konta, które jest członkiem grupy CloudAdmins. Dla konta CloudAdmin wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Pobierz stan wysokiego poziomu bieżącego przebiegu aktualizacji 

Aby uzyskać ogólny stan bieżącego przebiegu aktualizacji, uruchom następujące polecenia: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Możliwe wartości obejmują:

- Działa
- Ukończono
- Błąd 
- Anulowane

Można uruchomić te polecenia, aby zobaczyć stan najbardziej aktualne. Nie trzeba ponownie ustanowić połączenia, aby ponownie sprawdzić.

### <a name="get-the-full-update-run-status-with-details"></a>Pobierz aktualizację pełny stan uruchomienia przy użyciu szczegółów 

Możesz uzyskać pełnej aktualizacji uruchom podsumowanie jako ciągu XML. Możesz zapisać ciąg do pliku w celu zbadania, lub przekonwertować go do dokumentu XML i przeanalizować go przy użyciu programu PowerShell. Polecenie analizuje XML w celu uzyskania listy hierarchiczne aktualnie uruchomionych kroków.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

W poniższym przykładzie kroku najwyższego poziomu (aktualizacja chmury) ma planu podrzędnych zaktualizować i uruchomić ponownie hostów magazynu. Pokazuje, że plan ponownego uruchomienia hostów magazynu jest aktualizacji usługi magazynu obiektów Blob na jednym z hostów.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Pobierz dziennik pełne postępu

Dziennik może zapisać do pliku w celu zbadania. Może to pomóc w diagnozowaniu Niepowodzenie aktualizacji.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Aktywnie wyświetlić pełne rejestrowanie

Aby aktywnie widoku pełnego dziennika podczas aktualizacji uruchom i przejść do najnowsze wpisy uruchom następujące polecenia wprowadzenia sesji w trybie interakcyjnym i wyświetlania dziennika:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
Dziennik aktualizuje co 60 sekund i nową zawartość (jeśli jest dostępny) jest wyświetlony w konsoli. 

Podczas długotrwałych procesów w tle dane wyjściowe konsoli może nie można zapisać konsolę przez pewien czas. Aby anulować interakcyjne dane wyjściowe, naciśnij klawisze Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Operacja aktualizacji nie powiodło się wznowienie

Jeśli aktualizacja nie powiedzie się, można wznowić przebiegu, którym przerwał aktualizacji.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Uprzywilejowane punkt końcowy jest dostępny na wszystkich maszynach wirtualnych ERCS w środowisku Azure stosu. Ponieważ połączenie nie jest możliwe do punktu końcowego wysokiej dostępności, mogą wystąpić przerwy okazjonalne, ostrzeżenia lub komunikaty o błędach. Te komunikaty może wskazywać, że sesja została rozłączona lub wystąpił błąd podczas komunikacji z usługą NZ. To zachowanie jest oczekiwane. Można ponowić próbę za kilka minut lub utworzyć nową sesję uprzywilejowanych punktu końcowego na jednym z innych maszyn wirtualnych ERCS. 

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w stosie Azure](azure-stack-updates.md) 


