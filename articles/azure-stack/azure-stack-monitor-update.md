---
title: Monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować stan aktualizacji dla usługi Azure Stack, zintegrowanych systemów za pomocą uprzywilejowanych punktu końcowego.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: c62ed7c0fe556c50d3171045321596536d8b7098
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427977"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Możesz użyć [uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md) do monitorowania postępu w usłudze Azure Stack przebieg aktualizacji, a do wznowienia aktualizacji nie powiodło się, uruchom w ostatnim kroku pomyślnie powinien portalu usługi Azure Stack staną się niedostępne.  Za pomocą portalu usługi Azure Stack jest zalecaną metodą do zarządzania aktualizacjami w usłudze Azure Stack.

Następujące nowe polecenia cmdlet programu PowerShell do zarządzania aktualizacjami są zawarte w aktualizacji 1710 systemów zintegrowanych w usłudze Azure Stack.

| Polecenie cmdlet  | Opis  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Zwraca stan aktualizacji aktualnie uruchomione, ukończone lub nie powiodło się. Zapewnia ogólny stan operacji aktualizacji i dokumentu XML, który opisuje bieżący krok i odpowiadający mu stan. |
| `Resume-AzureStackUpdate` | Wznawia aktualizacji zakończonych niepowodzeniem w punkcie, w którym nie powiodło się. W niektórych scenariuszach może być konieczne kroki zaradcze ukończenie wznowienia aktualizacji.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Upewnij się, że polecenia cmdlet są dostępne
Ponieważ polecenia cmdlet są nowe w pakiecie aktualizacji 1710 usługi Azure Stack, proces aktualizacji 1710 serwer musi pobrać do pewnego momentu przed funkcją monitorowania jest dostępna. Zazwyczaj polecenia cmdlet są dostępne, jeśli jego stan w portalu administratora wskazuje, że aktualizacji 1710 znajduje się na **ponownie uruchomić Magazyn hosty** kroku. W szczególności aktualizacji polecenie cmdlet występuje w ciągu **krok: Uruchamianie kroku 2.6 — dozwolonych aktualizacji PrivilegedEndpoint**.

Można również określić, czy polecenia cmdlet są dostępne programowo, badając lista poleceń z uprzywilejowanym punktu końcowego. Aby to zrobić, uruchom następujące polecenia z hosta cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem. Ponadto upewnij się, że uprzywilejowanych punkt końcowy jest zaufanego hosta. Aby uzyskać więcej informacji, zobacz krok 1 [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Tworzenie sesji programu PowerShell na każdym ERCS maszyn wirtualnych w środowisku usługi Azure Stack (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*-ERCS03). Zastąp *prefiks* z Ciąg prefiksu maszyn wirtualnych, które są specyficzne dla danego środowiska.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Gdy zostanie wyświetlony monit o poświadczenia, &lt; *domeny usługi Azure Stack*&gt;\cloudadmin konto lub konta które jest członkiem grupy CloudAdmins. Dla konta CloudAdmin wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

2. Pobierz pełną listę poleceń, które są dostępne w punkcie końcowym uprzywilejowanych. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Określ, jeśli uprzywilejowanych punkt końcowy został zaktualizowany.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Utwórz listę poleceń, które są specyficzne dla modułu Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Na przykład:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Użyj poleceń cmdlet do zarządzania aktualizacji

> [!NOTE]
> Uruchom następujące polecenia z hosta cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem. Ponadto upewnij się, że uprzywilejowanych punkt końcowy jest zaufanego hosta. Aby uzyskać więcej informacji, zobacz krok 1 [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Podłącz do endpoint uprzywilejowanych i przypisz zmiennej sesji

Uruchom następujące polecenia, aby utworzyć sesję programu PowerShell na każdym ERCS maszyn wirtualnych w środowisku usługi Azure Stack (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*-ERCS03), a także przypisać zmiennej sesji.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Gdy zostanie wyświetlony monit o poświadczenia, &lt; *domeny usługi Azure Stack*&gt;\cloudadmin konto lub konta które jest członkiem grupy CloudAdmins. Dla konta CloudAdmin wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Uzyskaj ogólny stan bieżący przebieg aktualizacji 

Aby uzyskać ogólny stan bieżący przebieg aktualizacji, uruchom następujące polecenia: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Możliwe wartości obejmują:

- Działanie
- Zakończone
- Niepowodzenie 
- Anulowane

Można uruchomić te polecenia wielokrotnie, aby wyświetlić najbardziej aktualny stan. Nie trzeba ponownie ustanowić połączenia, aby ponownie sprawdzić.

### <a name="get-the-full-update-run-status-with-details"></a>Uzyskaj pełną aktualizację stan uruchomienia ze szczegółowymi informacjami 

Możesz uzyskać pełną aktualizację Uruchom podsumowanie jako ciąg znaków XML. Możesz zapisać ciąg do pliku w celu badania, lub przekonwertować go do dokumentu XML i analizy przy użyciu programu PowerShell. Następujące polecenie analizuje XML hierarchiczną listę aktualnie uruchomionych kroki.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

W poniższym przykładzie kroku najwyższego poziomu (aktualizacja chmury) ma plan podrzędnych do aktualizacji i ponownie uruchom hosty magazynu. Pokazuje, czy plan hostów ponownie uruchomić Magazyn aktualizuje usługi Blob Storage na jednym z hostów.

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

### <a name="resume-a-failed-update-operation"></a>Wznów operacja aktualizacji nie powiodło się

W przypadku niepowodzenia aktualizacji można wznowić przebiegu, tam, gdzie ją przerwaliśmy aktualizacji.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Uprzywilejowane końcowy jest dostępny na wszystkich maszynach wirtualnych ERCS w środowisku usługi Azure Stack. Ponieważ nie nawiązujesz połączenie z punktu końcowego o wysokiej dostępności, mogą występować sporadycznie przerw w działaniu, ostrzeżenia lub komunikaty o błędach. Te komunikaty może wskazywać, że sesja została rozłączona lub wystąpił błąd podczas komunikacji z usługą ONZ. Takie zachowanie jest oczekiwane. Można ponowić próbę wykonania operacji za kilka minut lub utworzyć nową sesję uprzywilejowanych punktu końcowego na jednym z innych maszyn wirtualnych ERCS. 

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md) 


