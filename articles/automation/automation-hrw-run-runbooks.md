---
title: Uruchomienie elementów runbook na procesu roboczego Runbook hybrydowego automatyzacji Azure
description: Ten artykuł zawiera informacje dotyczące wykonywania elementów runbook na komputerach w lokalnym centrum danych lub dostawcy chmury z rolą hybrydowy proces roboczy elementu Runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a4cf32ea7b77db3fc78a404063b8a4d69ecebf58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Uruchomione elementy runbook na hybrydowy proces roboczy elementu Runbook

Nie ma różnic w strukturze elementów runbook, które są uruchamiane w automatyzacji Azure oraz te, które uruchamiane na hybrydowy proces roboczy elementu Runbook. Elementy Runbook korzystające z każdym najprawdopodobniej różnią się znacznie jednak ponieważ elementy runbook, elementów docelowych hybrydowy proces roboczy elementu Runbook, zwykle zarządzać zasobami na komputerze lokalnym lub w odniesieniu do zasobów w środowisku lokalnym, w których jest wdrożona, gdy elementy runbook automatyzacji Azure zazwyczaj zarządzać zasobami w chmurze Azure.

Podczas tworzenia elementów runbook do uruchamiania na hybrydowy proces roboczy elementu Runbook, należy edytować i testowania elementów runbook na maszynie, który jest hostem hybrydowy proces roboczy. Komputer hosta zawiera wszystkie moduły programu PowerShell i dostępu do sieci, które należy do zarządzania i dostęp do zasobów lokalnych. Gdy element runbook został edytować i przetestowane na maszynie hybrydowego procesu roboczego, można przekazać jej do środowiska usługi Automatyzacja Azure, w których jest dostępna do uruchamiania w hybrydowy proces roboczy. Ważne jest, aby dowiedzieć się, że zadania uruchomione w ramach lokalnego konta systemowego dla systemu windows lub specjalne konto użytkownika **nxautomation** w systemie Linux, które mogą stać się niewielkie różnice podczas tworzenia elementów runbook dla procesu roboczego elementu Runbook na hybrydowego powinna to być brana pod uwagę.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Uruchamianie elementu runbook na hybrydowy proces roboczy elementu Runbook

[Uruchamianie elementu Runbook automatyzacji Azure](automation-starting-a-runbook.md) opisano różne metody uruchamiania elementu runbook. Dodaje hybrydowy proces roboczy elementu Runbook **RunOn** opcja, w którym można określić nazwę grupy hybrydowego procesu roboczego elementu Runbook. Jeśli zostanie określona grupa, element runbook jest pobrać i uruchom przez jeden z procesów roboczych w tej grupie. Jeśli ta opcja nie jest określona, następnie jest uruchamiany w automatyzacji Azure normalnie.

Po uruchomieniu elementu runbook w portalu Azure, jest wyświetlana **Uruchom na** opcja, w którym można wybrać **Azure** lub **hybrydowy proces roboczy**. W przypadku wybrania **hybrydowy proces roboczy**, a następnie z listy rozwijanej można wybrać grupy.

Użyj **RunOn** parametru. Następujące polecenie służy do uruchomienia elementu runbook o nazwie Test-Runbook na hybrydowego grupy procesów roboczych elementu Runbook o nazwie MyHybridGroup przy użyciu programu Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> **RunOn** parametr został dodany do **Start AzureAutomationRunbook** polecenia cmdlet w wersji od 0.9.1 Microsoft Azure PowerShell. Należy [Pobierz najnowszą wersję](https://azure.microsoft.com/downloads/) Jeśli masz wcześniejszą zainstalowane. Musisz zainstalować tę wersję stacji roboczej, na którym jest uruchamiany element runbook z programu PowerShell. Nie trzeba zainstalować na komputerze pracownika, chyba że użytkownik zamierza uruchamiania elementów runbook z tego komputera"

## <a name="runbook-permissions"></a>Uprawnienia elementów Runbook

Elementów Runbook uruchomionych na hybrydowy proces roboczy elementu Runbook nie można użyć tej samej metody, która jest zwykle używana do elementów runbook uwierzytelniania do zasobów platformy Azure, ponieważ uzyskują dostęp do zasobów poza platformą Azure. Element runbook można ustalić uwierzytelniania do zasobów lokalnych, lub można określić konto Uruchom jako, aby zapewnić kontekstu użytkownika dla wszystkich elementów runbook.

### <a name="runbook-authentication"></a>Uwierzytelnianie elementu Runbook

Domyślnie, elementy runbook działają w kontekście konta systemu lokalnego dla systemu Windows oraz specjalne konto użytkownika **nxautomation** dla systemu Linux na komputerze lokalnym, więc użytkownik musi podać własne uwierzytelniania do zasobów, które będą uzyskiwać dostęp do .

Można użyć [poświadczeń](automation-credentials.md) i [certyfikatu](automation-certificates.md) zasoby w elemencie runbook za pomocą poleceń cmdlet, które pozwalają określić poświadczenia, można uwierzytelniać do różnych zasobów. Poniższy przykład przedstawia części elementu runbook, który powoduje ponowne uruchomienie komputera. Pobiera poświadczenia z zasobu poświadczeń i nazwę komputera z zasób zmiennej, a następnie używa tych wartości w poleceniu cmdlet Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można też skorzystać [InlineScript](automation-powershell-workflow.md#inlinescript), co pozwala uruchamiać bloków kodu na innym komputerze przy użyciu poświadczeń, określony przez [typowy parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Konto Uruchom jako

Domyślnie hybrydowy proces roboczy elementu Runbook używa lokalnego systemu dla systemu Windows i specjalne konto użytkownika **nxautomation** dla systemu Linux do wykonywania elementów runbook. Zamiast zapewnienia uwierzytelniania do zasobów lokalnych elementów runbook, można określić **RunAs** konto do grupy hybrydowych procesów roboczych. Należy określić [zasób poświadczeń](automation-credentials.md) mający dostęp do zasobów lokalnych i wszystkich elementów runbook działać w ramach tych poświadczeń, podczas uruchamiania na hybrydowy proces roboczy elementu Runbook w grupie.

Nazwa użytkownika dla poświadczenia musi być w jednym z następujących formatów:

* domena azwa_użytkownika
* username@domain
* Nazwa użytkownika (dla kont lokalnych dla komputera lokalnego)

Aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych, użyj poniższej procedury:

1. Utwórz [zasób poświadczeń](automation-credentials.md) z dostępem do zasobów lokalnych.
2. Otwórz konto usługi Automatyzacja w portalu Azure.
3. Wybierz **hybrydowego procesu roboczego grupy** Kafelek, a następnie wybierz grupę.
4. Wybierz **wszystkie ustawienia** , a następnie **ustawienia grupy hybrydowego procesu roboczego**.
5. Zmień **Uruchom jako** z **domyślne** do **niestandardowy**.
6. Wybierz poświadczenie, a następnie kliknij przycisk **zapisać**.

### <a name="automation-run-as-account"></a>Konto Uruchom jako automatyzacji

W ramach procesu kompilacji automatycznego wdrażania zasobów na platformie Azure mogą wymagać dostępu do lokalnego systemów do obsługi zadań lub zestaw kroków w sekwencji wdrożenia. Do obsługi uwierzytelniania przy użyciu konta Uruchom jako platformy Azure, należy zainstalować certyfikat konta Uruchom jako.

Następujący element runbook programu PowerShell *RunAsCertificateToHybridWorker eksportu*, umożliwia wyeksportowanie certyfikatu uruchom jako z konta usługi Automatyzacja Azure i pliki do pobrania i zaimportowanie go do magazynu certyfikatów komputera lokalnego na hybrydowy proces roboczy podłączony do tego samego konta. Po ukończeniu tego kroku sprawdza to, czy Proces roboczy może pomyślnie wykonać uwierzytelnienia na platformie Azure przy użyciu konta Uruchom jako.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

Zapisz *RunAsCertificateToHybridWorker eksportu* runbook na komputerze z `.ps1` rozszerzenia. Zaimportuj go do Twojego konta automatyzacji i edytować element runbook, zmiana wartości zmiennej `$Password` z własnego hasła. Publikowanie, a następnie uruchom korzystających z uwierzytelniania przy użyciu konta Uruchom jako elementy runbook, które grupy hybrydowego procesu roboczego elementu runbook. Strumień zadań zgłasza próba zaimportowania certyfikatu w magazynie komputera lokalnego i jest zgodny z wielu wierszy w zależności od liczby kont automatyzacji są zdefiniowane w ramach subskrypcji i jeśli uwierzytelnianie zakończy się pomyślnie.

## <a name="job-behavior"></a>Zachowanie zadania

Zadania są obsługiwane nieco inne w hybrydowych procesów roboczych elementu Runbook, niż uruchamianych na Azure obszarów izolowanych. Jednego klucza różnica polega na tym, że nie ma żadnego limitu na czas trwania zadania na hybrydowych procesów roboczych elementu Runbook. Jeśli element runbook długotrwałe chcesz zapewnić odporność na możliwe ponowne uruchomienie, na przykład jeśli ponowne uruchomienie komputera, który jest hostem hybrydowy proces roboczy. Jeśli komputer hosta hybrydowy proces roboczy zostanie uruchomiony ponownie, wszystkie uruchomione zadania elementu runbook uruchamia ponownie od samego początku, lub z ostatniego punktu kontrolnego dla elementów runbook przepływu pracy programu PowerShell. Jeśli zadanie elementu runbook zostanie ponownie uruchomiony więcej niż 3 razy, następnie jest wstrzymana.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Rozwiązywanie problemów z elementów runbook na hybrydowy proces roboczy elementu Runbook

Dzienniki są przechowywane lokalnie na każdym hybrydowy proces roboczy na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Hybrydowe procesy robocze również rejestrowania błędów i zdarzeń w dzienniku zdarzeń systemu Windows w obszarze **aplikacji i usług Logs\Microsoft-SMA\Operational**. Zdarzenia związane z elementami runbook wykonywane w procesie roboczym są zapisywane w **aplikacji i usług Logs\Microsoft-Automation\Operational**. **Microsoft SMA** dziennika zawiera wiele więcej zdarzeń związanych z zadania elementu runbook do elementu roboczego i przetwarzania elementu runbook. Gdy **automatyzacji Microsoft** dziennika zdarzeń nie ma wiele zdarzeń przy użyciu szczegółów pomoc w rozwiązywaniu problemów z wykonanie elementu runbook, zawiera wyniki zadań elementu runbook.

[Runbook dane wyjściowe i komunikaty](automation-runbook-output-and-messages.md) są wysyłane do usługi Automatyzacja Azure z hybrydowych procesów roboczych, podobnie jak zadania elementów runbook działają w chmurze. Można również włączyć pełne i postępu strumienie tak samo jak dla innych elementów runbook.

Jeśli elementy runbook nie są pomyślnie wykonywane zadanie podsumowania przedstawia stan **zawieszone**, przejrzyj artykuł dotyczący rozwiązywania problemów [hybrydowy proces roboczy elementu Runbook: kończy zadanie elementu runbook o stanie Suspended](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat różnych metod, które mogą służyć do uruchamiania elementu runbook, zobacz [uruchamianie elementu Runbook automatyzacji Azure](automation-starting-a-runbook.md).
* Aby poznać różne procedury dotyczące pracy z elementami runbook programu PowerShell i przepływ pracy programu PowerShell w automatyzacji Azure za pomocą edytor tekstowy, zobacz [edytowanie elementu Runbook automatyzacji Azure](automation-edit-textual-runbook.md)
