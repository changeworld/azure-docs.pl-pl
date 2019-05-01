---
title: Monitorowania bram sieci VPN za pomocą usługi Azure Network Watcher Rozwiązywanie problemów z | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowanie połączeń lokalnych za pomocą usługi Azure Automation i Usługa Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c26aef777df3ef5c7df6575b8d939a329740a97e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719802"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorowania bram sieci VPN z rozwiązywaniem problemów usługi Network Watcher

Uzyskiwanie szczegółowych informacji o wydajności sieci ma krytyczne znaczenie, aby zapewnić klientom usług reliable services. W związku z tym jest niezwykle szybko wykryć warunki awarii sieci i podejmij działania naprawcze, aby uniknąć awarii warunku. Usługa Azure Automation pozwala na implementowanie i uruchomić zadanie w sposób programowy za pomocą elementów runbook. Za pomocą usługi Azure Automation tworzy doskonałe przepis na wykonywanie sieci ciągłe i aktywnego monitorowania i alertów.

## <a name="scenario"></a>Scenariusz

Scenariusz na poniższej ilustracji włączeniu wielowarstwowych aplikacji przy użyciu połączenia nawiązane za pomocą bramy sieci VPN i tunelowania. Zapewnienie, że brama sieci VPN jest to kluczowe znaczenie dla wydajności aplikacji.

Element runbook jest tworzony za pomocą skryptu, aby sprawdzić stan połączenia tunelu VPN, aby sprawdzić stan tunel połączenia przy użyciu interfejsu API Rozwiązywanie problemów z zasobów. Jeśli nie jest w dobrej kondycji, wyzwalacz poczty e-mail są wysyłane do administratorów.

![Przykładowy scenariusz][scenario]

W tym scenariuszu wykonują następujące czynności:

- Utwórz wywołanie elementu runbook `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenia cmdlet, aby rozwiązać stan połączenia
- Powiązanie harmonogramu do elementu runbook

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tego scenariusza, musisz mieć następujące wymagania wstępne:

- Konto usługi Azure automation na platformie Azure. Upewnij się, że konto usługi automation ma najnowsze moduły i ma również moduł AzureRM.Network. Moduł AzureRM.Network jest dostępna w galerię modułów, jeśli chcesz dodać go do konta usługi automation.
- Konieczne jest posiadanie zestawu poświadczeń, skonfiguruj w usłudze Azure Automation. Dowiedz się więcej o [zabezpieczeń usługi Azure Automation](../automation/automation-security-overview.md)
- Prawidłowy adres serwera SMTP (Office 365, Twój adres e-mail w środowisku lokalnym lub innym) i poświadczeń określonych w usłudze Azure Automation
- Skonfigurowanej bramy sieci wirtualnej na platformie Azure.
- Istniejące konto magazynu przy użyciu istniejący kontener do przechowywania dzienników w.

> [!NOTE]
> Infrastruktura przedstawiony w powyższej ilustracji jest dla celów ilustracyjnych i nie są tworzone za pomocą kroki zawarte w tym artykule.

### <a name="create-the-runbook"></a>Tworzenie elementu runbook

Pierwszym krokiem do konfigurowania w przykładzie jest utworzyć element runbook. W tym przykładzie użyto konta Uruchom jako. Aby dowiedzieć się więcej o kontach Uruchom jako, odwiedź stronę [uwierzytelniać elementy Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Przejdź do usługi Azure Automation w [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **elementów Runbook**

![Przegląd konta usługi Automation][1]

### <a name="step-2"></a>Krok 2

Kliknij przycisk **Dodaj element runbook** można uruchomić procesu tworzenia elementu runbook.

![Blok elementów runbook][2]

### <a name="step-3"></a>Krok 3

W obszarze **szybkie tworzenie**, kliknij przycisk **Utwórz nowy element runbook** do tworzenia elementu runbook.

![Dodaj blok elementu runbook][3]

### <a name="step-4"></a>Krok 4

W tym kroku będziemy nadaj elementowi runbook nazwę, w tym przykładzie jest wywoływana **Get VPNGatewayStatus**. Jest ważne, aby nadaj elementowi runbook nazwę opisową i zalecana, nadając mu nazwę zgodną z standardowego środowiska PowerShell standardami nazewnictwa. Typ elementu runbook w tym przykładzie to **PowerShell**, inne opcje są graficzny, w przypadku przepływu pracy programu PowerShell i PowerShell graficzny przepływ pracy.

![Blok elementu runbook][4]

### <a name="step-5"></a>Krok 5

W tym kroku zostanie utworzony element runbook poniższy kod zawiera cały kod, które są wymagane dla przykładu. Elementy w kodzie, które zawierają \<wartość\> należy zastąpić wartościami z Twojej subskrypcji.

Użyj poniższego kodu, kliknij przycisk **Zapisz**

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

Po zapisaniu elementu runbook zgodnie z harmonogramem muszą być połączone do niego zautomatyzować uruchamiania elementu runbook. Aby uruchomić proces, kliknij przycisk **harmonogram**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Powiązanie harmonogramu do elementu runbook

Należy utworzyć nowy harmonogram. Kliknij przycisk **powiązania harmonogramu z elementem runbook**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

Na **harmonogram** bloku kliknij **Utwórz nowy harmonogram**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

Na **nowy harmonogram** bloku Wypełnij informacje o harmonogramie. Wartości, które mogą być ustawiane są na poniższej liście:

- **Nazwa** — przyjazna nazwa harmonogramu.
- **Opis** — opis harmonogramu.
- **Uruchamia** — ta wartość jest kombinacją daty, godziny i strefy czasowej, wchodzące w skład czas wyzwalaczach harmonogramu.
- **Cykl** — ta wartość określa powtórzenie harmonogramów.  Prawidłowe wartości to **raz** lub **cyklicznie**.
- **Powtarzaj co** — interwał cyklu harmonogramu w godzinach, dniach, tygodniach lub miesiącach.
- **Ustawienia okresu ważności** — wartość określa, jeśli harmonogram czas wygaśnięcia, czy nie. Można ustawić **tak** lub **nie**. Prawidłową datę i godzinę są dostarczane, jeśli tak jest wybierany.

> [!NOTE]
> Jeśli potrzebujesz częściej niż co godzinę uruchomienia elementu runbook, należy utworzyć wiele harmonogramów w różnych interwałach (oznacza to, 15, 30, 45 minut po pełnej godzinie)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknij przycisk Zapisz, aby zapisać harmonogram do elementu runbook.

![Krok 10][10]

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiedzę na temat usługi Network Watcher Rozwiązywanie problemów z usługą Azure Automation, Dowiedz się, jak wyzwalać Przechwytywanie pakietów dotyczących alertów maszyny Wirtualnej, odwiedzając [tworzenie przechwytywania pakietów wyzwolonych alertów za pomocą usługi Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

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
