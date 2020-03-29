---
title: Rozwiązywanie problemów i monitorowanie bram sieci VPN — usługa Azure Automation
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób diagnozowania łączności lokalnej za pomocą usługi Azure Automation i Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845036"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorowanie bram sieci VPN za pomocą rozwiązywania problemów z obserwatorem sieci

Uzyskanie szczegółowych informacji na temat wydajności sieci ma kluczowe znaczenie dla zapewnienia niezawodnych usług dla klientów. Dlatego ważne jest szybkie wykrycie warunków awarii sieci i podjęcie działań naprawczych w celu złagodzenia stanu awarii. Usługa Azure Automation umożliwia implementowanie i uruchamianie zadania w sposób programowy za pomocą śmięty. Korzystanie z usługi Azure Automation tworzy doskonałą receptę na wykonywanie ciągłego i proaktywnego monitorowania sieci i alertów.

## <a name="scenario"></a>Scenariusz

Scenariusz na poniższej ilustracji jest aplikacją wielowarstwową, z łącznością lokalną ustanowioną przy użyciu bramy sieci VPN i tunelu. Zapewnienie, że brama sieci VPN jest uruchomiona i działa, ma kluczowe znaczenie dla wydajności aplikacji.

Projekt runbook jest tworzony za pomocą skryptu w celu sprawdzenia stanu połączenia tunelu sieci VPN, przy użyciu interfejsu API rozwiązywania problemów z zasobami w celu sprawdzenia stanu tunelu połączenia. Jeśli stan nie jest w dobrej kondycji, wyzwalacz wiadomości e-mail jest wysyłany do administratorów.

![Przykładowy scenariusz][scenario]

W tym scenariuszu:

- Tworzenie zestawu runbooka wywołującego polecenie cmdlet w `Start-AzureRmNetworkWatcherResourceTroubleshooting` celu rozwiązywania problemów ze stanem połączenia
- Łączenie harmonogramu z harmonogramem

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tego scenariusza należy mieć następujące wymagania wstępne:

- Konto automatyzacji platformy Azure na platformie Azure. Upewnij się, że konto automatyzacji ma najnowsze moduły, a także moduł AzureRM.Network. Moduł AzureRM.Network jest dostępny w galerii modułów, jeśli trzeba dodać go do konta automatyzacji.
- W usłudze Azure Automation musi być skonfigurowany zestaw poświadczeń. Dowiedz się więcej na [temat zabezpieczeń usługi Azure Automation](../automation/automation-security-overview.md)
- Prawidłowy serwer SMTP (Office 365, lokalny adres e-mail lub inny) i poświadczenia zdefiniowane w usłudze Azure Automation
- Skonfigurowana brama sieci wirtualnej na platformie Azure.
- Istniejące konto magazynu z istniejącym kontenerem do przechowywania logowań.

> [!NOTE]
> Infrastruktura przedstawiona na poprzednim obrazie służy do celów ilustracyjnych i nie jest tworzona za pomocą kroków zawartych w tym artykule.

### <a name="create-the-runbook"></a>Tworzenie śmiętu

Pierwszym krokiem do skonfigurowania przykładu jest utworzenie śmiętu runbook. W tym przykładzie użyto konta run-as. Aby dowiedzieć się więcej o kontach uruchamianych, odwiedź stronę [Uwierzytelnij łańszeń ekscesowych za pomocą konta Azure Run As](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Przejdź do usługi Azure Automation w [portalu Azure](https://portal.azure.com) i kliknij pozycję **Księgi funkcjonowania**

![omówienie konta automatyzacji][1]

### <a name="step-2"></a>Krok 2

Kliknij **pozycję Dodaj elementu runbook,** aby rozpocząć proces tworzenia elementu runbook.

![blok runbooks][2]

### <a name="step-3"></a>Krok 3

W obszarze **Szybkie tworzenie**kliknij pozycję **Utwórz nowy projekt runbook,** aby utworzyć projekt runbook.

![dodawanie bloku eksletu][3]

### <a name="step-4"></a>Krok 4

W tym kroku nadajemy runbook nazwę, w przykładzie nazywa **get-VPNGatewayStatus**. Ważne jest, aby nadać emuńek opisową nazwę i zalecić nadanie mu nazwy zgodnej ze standardowymi standardami nazewnictwa programu PowerShell. Typ ekscesu w tym przykładzie to **Program PowerShell**, inne opcje to Graficzny, Przepływ pracy programu PowerShell i graficzny przepływ pracy programu PowerShell.

![blok runbooka][4]

### <a name="step-5"></a>Krok 5

W tym kroku tworzony jest projekt runbook, poniższy przykład kodu zawiera cały kod potrzebny do przykładu. Elementy w kodzie, \<\> które zawierają wartość muszą zostać zastąpione wartościami z subskrypcji.

Użyj następującego kodu, klikając przycisk **Zapisz**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Krok 6

Po zapisaniu element runbooka należy połączyć z nim harmonogram, aby zautomatyzować uruchamianie śmiętu. Aby rozpocząć proces, kliknij przycisk **Zaplanuj**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Łączenie harmonogramu z harmonogramem

Należy utworzyć nowy harmonogram. Kliknij **pozycję Połącz harmonogram z schematem .**

![Krok 7][7]

### <a name="step-1"></a>Krok 1

W bloku **Harmonogram** kliknij pozycję **Utwórz nowy harmonogram**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

Na **bloku Nowy harmonogram** wypełnij informacje o harmonogramie. Wartości, które można ustawić, znajdują się na następującej liście:

- **Nazwa** — przyjazna nazwa harmonogramu.
- **Opis** — opis harmonogramu.
- **Rozpoczyna —** ta wartość jest kombinacją daty, godziny i strefy czasowej, które tworzą czas wyzwalania harmonogramu.
- **Cykl —** ta wartość określa powtórzenia harmonogramów.  Prawidłowe wartości to **Once** lub **Recurring**.
- **Powtarzaj co** — interwał cyklu harmonogramu w godzinach, dniach, tygodniach lub miesiącach.
- **Ustaw wygaśnięcia** — wartość określa, czy harmonogram powinien wygasnąć, czy nie. Można ustawić na **Tak** lub **Nie**. W przypadku wybrania "tak" należy podać prawidłową datę i godzinę.

> [!NOTE]
> Jeśli musisz mieć runbook uruchomić częściej niż co godzinę, wiele harmonogramów muszą być tworzone w różnych odstępach czasu (czyli 15, 30, 45 minut po godzinie)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknij przycisk Zapisz, aby zapisać harmonogram w bieśmie.

![Krok 10][10]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wiedzę na temat integracji rozwiązywania problemów z kontrolami funkcji Kontrola sieci z usługą Azure Automation, dowiedz się, jak wyzwolić przechwytywanie pakietów w alertach maszyn wirtualnych, odwiedzając pozycję [Tworzenie wyzwalanego alertu przechwytywania pakietów za pomocą usługi Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
