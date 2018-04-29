---
title: Monitorowanie bramy sieci VPN z rozwiązywaniem problemów obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowania lokalnymi łączności z usługi Automatyzacja Azure i obserwatora sieciowego
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a102916bb0626f5b110fb134a8a25c902cfaefe7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorowanie bramy sieci VPN z rozwiązywaniem problemów obserwatora sieciowego

Bardzo ważne klientom niezawodne usługi jest uzyskania szczegółowych informacji na wydajność sieci. Dlatego bardzo ważne jest szybkie wykrywanie warunków awarii sieci i podjąć działania naprawcze w celu złagodzenia warunku awarii. Automatyzacja Azure umożliwia wdrożenie i uruchom zadanie w sposób programowy za pomocą elementów runbook. Przy użyciu automatyzacji Azure tworzy doskonałe przepisu sieci ciągły i aktywnego monitorowania i alertów.

## <a name="scenario"></a>Scenariusz

Scenariusz na poniższej ilustracji jest aplikacją wielowarstwowych z na połączenie lokalne za pomocą bramy sieci VPN i tunelowania. Zapewnienie, że brama sieci VPN i uruchomiona jest krytyczne znaczenie dla wydajności aplikacji.

Element runbook jest tworzony przy użyciu skryptu, aby sprawdzić stan połączenia tunelu VPN, aby sprawdzić stan tunelu połączenia przy użyciu interfejsu API Rozwiązywanie problemów z zasobów. Jeśli stan nie jest w dobrej kondycji, wyzwalacz poczty e-mail są wysyłane do administratorów.

![Przykładowy scenariusz][scenario]

W tym scenariuszu obejmują:

- Tworzenie wywołania elementu runbook `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenia cmdlet, aby rozwiązać stan połączenia
- Połącz harmonogram do elementu runbook

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tego scenariusza, musi mieć następujące wymagania wstępne:

- Konto usługi Automatyzacja Azure w systemie Azure. Sprawdź, czy konto automatyzacji ma najnowszą modułów i ma również modułu AzureRM.Network. Moduł AzureRM.Network jest dostępny w galerii modułu, jeśli konieczne jest dodanie go do Twojego konta automatyzacji.
- Musi mieć zestaw poświadczeń, skonfiguruj w automatyzacji Azure. Dowiedz się więcej o [zabezpieczeń usługi Automatyzacja Azure](../automation/automation-security-overview.md)
- Prawidłowy adres serwera SMTP (Office 365, poczty e-mail lokalnego lub innej) i poświadczeń określonych w automatyzacji Azure
- Skonfigurowana brama sieci wirtualnej na platformie Azure.
- Istniejące konto magazynu z istniejącego kontenera do przechowywania dzienników w.

> [!NOTE]
> Infrastruktura opisany w poprzednim obrazu jest w celach ilustracyjnych i nie są tworzone z kroki zawarte w tym artykule.

### <a name="create-the-runbook"></a>Tworzenie elementu runbook

Pierwszym krokiem do konfigurowania w przykładzie jest można utworzyć elementu runbook. W tym przykładzie użyto konta Uruchom jako. Więcej informacji o kontach Uruchom jako można znaleźć [uwierzytelniania elementy Runbook za pomocą konta Uruchom jako platformy Azure](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Krok 1

Przejdź do usługi Automatyzacja Azure w [portalu Azure](https://portal.azure.com) i kliknij przycisk **elementów Runbook**

![Przegląd konta automatyzacji][1]

### <a name="step-2"></a>Krok 2

Kliknij przycisk **Dodaj element runbook** do rozpoczęcia procesu tworzenia elementu runbook.

![elementy runbook bloku][2]

### <a name="step-3"></a>Krok 3

W obszarze **szybkie tworzenie**, kliknij przycisk **Utwórz nowy element runbook** można utworzyć elementu runbook.

![Dodawanie bloku elementu runbook][3]

### <a name="step-4"></a>Krok 4

W tym kroku będziemy nazwę elementu runbook, w tym przykładzie jest to **Get-VPNGatewayStatus**. Jest ważne, aby dać nazwę opisową elementu runbook i zalecane nadanie mu nazwę zgodną z standard standardy nazewnictwa programu PowerShell. Typ elementu runbook w tym przykładzie jest **PowerShell**, graficzne, przepływ pracy programu PowerShell, są inne opcje i przepływ pracy programu PowerShell graficznego.

![bloku elementu runbook][4]

### <a name="step-5"></a>Krok 5

W tym kroku tworzone elementu runbook w poniższym przykładzie kodu zawiera całego kodu, które są potrzebne w przykładzie. Elementy w kodzie, które zawierają \<wartość\> należy zastąpić wartościami z subskrypcją.

Użyj następującego kodu jako kliknij **Zapisz**

```PowerShell
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

Po zapisaniu elementu runbook harmonogramu musi być połączony na początku elementu runbook automatyzacji. Aby uruchomić proces, kliknij przycisk **harmonogram**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Połącz harmonogram do elementu runbook

Należy utworzyć nowy harmonogram. Kliknij przycisk **Połącz harmonogram z elementem runbook**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

Na **harmonogram** bloku, kliknij przycisk **Utwórz nowy harmonogram**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

Na **nowy harmonogram** bloku Wypełnij informacje tego harmonogramu. Są wartości, które można ustawić na poniższej liście:

- **Nazwa** -przyjazną nazwę harmonogramu.
- **Opis elementu** — opis harmonogramu.
- **Uruchamia** — ta wartość jest kombinacją datę, godzinę i strefę czasową, wchodzące w skład czas wyzwalaczy harmonogramu.
- **Cykl** — ta wartość określa powtarzania harmonogramów.  Prawidłowe wartości to **raz** lub **cykliczny**.
- **Powtarzanie co** — interwał cyklu harmonogramu w godziny, dni, tygodnie lub miesiące.
- **Ustawienia okresu ważności** — wartość określa, czy harmonogram wygaśnięcia lub nie. Można ustawić **tak** lub **nr**. Prawidłową datę i godzinę mają zostać podany, jeśli tak jest wybrany.

> [!NOTE]
> Jeśli potrzebujesz częściej niż co godzinę uruchomienia elementu runbook, wiele harmonogramów musi zostać utworzony w różnych odstępach czasu (to znaczy, 15, 30, 45 minut po pełnej godzinie)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknij przycisk Zapisz, aby zapisać harmonogram do elementu runbook.

![Krok 10][10]

## <a name="next-steps"></a>Kolejne kroki

Teraz wiedzę na temat integracji obserwatora sieciowego Rozwiązywanie problemów z usługi Automatyzacja Azure, Dowiedz się, jak można wyzwolić przechwytywania pakietów na alerty maszyny Wirtualnej po przejściu na stronę [utworzyć przechwytywania alertów wyzwalanych pakietów z obserwatora sieciowego Azure](network-watcher-alert-triggered-packet-capture.md).

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
