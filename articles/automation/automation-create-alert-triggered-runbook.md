---
title: Użyj alertu można wyzwolić elementu runbook usługi Automatyzacja Azure
description: Dowiedz się, jak można wyzwolić elementu runbook do uruchomienia, gdy zostanie zgłoszony alert platformy Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e791cddb07d3204f807dbeff98b7fc69419ae23c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Użyj alertu można wyzwolić elementu runbook usługi Automatyzacja Azure

Można użyć [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) do monitorowania dzienników dla większości usług platformy Azure i metryk poziomu base. Należy wywołać element runbook usługi Automatyzacja Azure za pomocą [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) lub przy użyciu klasycznego alerty do automatyzowania zadań na podstawie alertów. W tym artykule przedstawiono sposób konfigurowania i uruchamiania elementu runbook za pomocą alertów.

## <a name="alert-types"></a>Typy alertów

Elementy runbook automatyzacji można użyć z trzech typów alertów:
* Klasycznym metryki alertów
* Alerty dziennika aktywności
* Niemal w czasie rzeczywistym metryki alertów

Gdy alert wywołuje element runbook, to rzeczywiste wywołanie jest żądanie HTTP POST do elementu webhook. Treść żądania POST zawiera sformatowane JSON obiektu, który ma użyteczne właściwości, które są powiązane z danym alertem. Poniższa lista zawiera łącza do schematu ładunku dla każdego typu alertu:

|Alerty  |Opis|Schemat ładunku  |
|---------|---------|---------|
|[Klasycznym metryki alertu](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy wszystkie metryki platformy poziomu spełnia określony warunek. Na przykład, jeśli wartość **procesora CPU Wynoszące %** na maszynie Wirtualnej jest większa niż **90** ostatnich 5 minut.| [Klasa schematu metryki ładunku alertu](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alert dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy wszystkie nowe zdarzenie w dzienniku aktywności platformy Azure jest zgodna z określonych warunków. Na przykład, jeśli `Delete VM` operacja odbywa się w **myProductionResourceGroup** lub gdy jest to nowe zdarzenie kondycji usługi platformy Azure z **Active** stan jest wyświetlany.| [Schemat alertu ładunku dziennika aktywności](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[W czasie rzeczywistym metryki alert o zbliżającym się](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie szybciej niż metryki alerty, gdy co najmniej jedną metrykę platformy poziomu spełnia określone warunki. Na przykład, jeśli wartość **procesora CPU Wynoszące %** na maszynie Wirtualnej jest większa niż **90**, a wartością **sieci w** jest większa niż **500 MB** w ciągu ostatnich 5 minut.| [Niemal w czasie rzeczywistym metryki ładunku alertu schematu](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Różni się dane, które są dostarczane przez poszczególne typy alertów, inaczej obsługi każdego typu alertu. W następnej sekcji można dowiedzieć się, jak utworzyć element runbook do obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie elementu runbook do obsługi alertów

Aby korzystać z automatyzacji z alertami, należy elementu runbook, który zawiera logikę, która zarządza alertu ładunek JSON, który zostanie przekazany do elementu runbook. Następujący przykładowy element runbook musi zostać wywołany z poziomu alertu platformy Azure. 

Zgodnie z opisem w poprzedniej sekcji, poszczególne typy alertów ma inny schemat. Skrypt akceptuje danych elementu webhook w `WebhookData` parametr wejściowy elementu runbook z poziomu alertu. Następnie skrypt oblicza ładunek JSON, aby ustalić, jakiego typu alertu użyto. 

W tym przykładzie użyto alert z maszyny Wirtualnej. Pobiera dane dotyczące maszyny Wirtualnej z ładunku, a następnie używa tych informacji, aby zatrzymać maszynę Wirtualną. Połączenia należy zdefiniować w konta automatyzacji gdy element runbook jest uruchomiony.

Element runbook używa **AzureRunAsConnection** [konta Uruchom jako](automation-create-runas-account.md) do uwierzytelniania w usłudze Azure można wykonać akcji zarządzania dla maszyny Wirtualnej.

Użyj tego przykładu, aby utworzyć element runbook o nazwie **Stop-AzureVmInResponsetoVMAlert**. Możesz zmodyfikować skrypt programu PowerShell i używać go z wielu różnych zasobów.

1. Przejdź do swojego konta usługi Automatyzacja Azure.
1. W obszarze **AUTOMATYZACJI procesu**, wybierz pozycję **elementów Runbook**.
1. W górnej części listy elementów runbook, zaznacz **Dodaj element runbook**. 
1. Na stronie **Dodawanie elementu runbook** wybierz pozycję **Szybkie tworzenie**.
1. Wprowadź nazwę elementu runbook **Stop-AzureVmInResponsetoVMAlert**. Wybierz typ elementu runbook **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).  
1. W poniższym przykładzie programu PowerShell do kopiowania **Edytuj** okienka. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Wybierz **publikowania** Aby zapisać i opublikować elementu runbook.

## <a name="create-an-action-group"></a>Utwórz grupę

Grupy akcji to zbiór akcji, które są wyzwalane przez alert. Element Runbook jest tylko jeden z wielu akcji, które można używać z grupy akcji.

1. W portalu Azure wybierz **Monitor** > **ustawienia** > **grupy akcji**.
1. Wybierz **Dodaj grupę akcji**, a następnie wprowadź wymagane informacje:  
    1. W **nazwy grupy akcji** wprowadź nazwę.
    1. W **krótką nazwę** wprowadź nazwę. Krótka nazwa służy zamiast akcji Pełna nazwa grupy powiadomienia są wysyłane przy użyciu tej grupy działań.
    1. **Subskrypcji** pole zostanie wypełnione automatycznie w ramach bieżącej subskrypcji. Jest to subskrypcji, w którym jest zapisany grupy akcji.
    1. Wybierz grupę zasobów, w którym jest zapisany grupy akcji.

Na przykład utworzyć dwie akcje: akcję elementu runbook i akcji powiadamiania.

### <a name="runbook-action"></a>Działania elementu Runbook

Aby utworzyć akcję elementu runbook w grupie akcji:

1. W obszarze **akcje**, dla **Nazwa akcji**, wprowadź nazwę dla akcji. Dla **typ akcji**, wybierz pozycję **elementu Runbook automatyzacji**.
1. W obszarze **szczegóły**, wybierz pozycję **Edytuj szczegóły**.  
1. Na **skonfigurować elementu Runbook** w obszarze **źródło elementu Runbook**, wybierz pozycję **użytkownika**.  
1. Wybierz z **subskrypcji** i **konta automatyzacji**, a następnie wybierz **Stop-AzureVmInResponsetoVMAlert** elementu runbook.  
1. Gdy skończysz, wybierz **OK**.

### <a name="notification-action"></a>Akcja powiadomienia

Aby utworzyć akcję powiadomień w grupie akcji:

1. W obszarze **akcje**, dla **Nazwa akcji**, wprowadź nazwę dla akcji. Dla **typ akcji**, wybierz pozycję **E-mail**.  
1. W obszarze **szczegóły** wybrać, **Edytuj szczegóły**.  
1. Na **E-mail** wprowadź adres e-mail dla powiadomień, a następnie wybierz pozycję **OK**. Dodawanie adresu e-mail, oprócz elementu runbook jako akcja jest przydatne. Użytkownik jest powiadamiany o uruchomieniu elementu runbook.  

    Grupy akcji powinna wyglądać poniższej ilustracji:

   ![Dodaj stronę grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Aby utworzyć grupę działań, wybierz **OK**.

Korzystając z tej grupy działań w [alertów dotyczących działań w dzienniku](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) i [alertów w czasie rzeczywistym w pobliżu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) utworzony.

## <a name="classic-alert"></a>Alert klasycznego

Klasycznym alerty są oparte na metryki i nie należy używać grup akcji. Można jednak skonfigurować akcję elementu runbook, opartą na klasycznej alercie. 

Aby utworzyć klasycznego alertu:

1. Wybierz pozycję **Dodaj alert dotyczący metryki**.
1. Nazwa metryki alertu **myVMCPUAlert**. Podaj krótki opis alertu.
1. Metryki warunku alertu, wybierz **większa niż**. Aby uzyskać **próg** wartości, wybierz opcję **10**. Aby uzyskać **okres** wartości, wybierz opcję **w ciągu ostatnich pięciu minut**.
1. W obszarze **reakcję**, wybierz pozycję **uruchomienia elementu runbook z następującego alertu**.
1. Na **skonfigurować elementu Runbook** strony, dla **źródło elementu Runbook**, wybierz pozycję **użytkownika**. Wybierz konto automatyzacji, a następnie wybierz **Stop AzureVmInResponsetoVMAlert** elementu runbook. Kliknij przycisk **OK**.
1. Aby zapisać reguły alertów, wybierz **OK**.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat uruchamiania elementu runbook automatyzacji za pomocą elementu webhook, zobacz [uruchomienia elementu runbook z elementu webhook](automation-webhooks.md).
* Aby uzyskać szczegółowe informacje o różnych sposobach uruchamiania elementu runbook, zobacz [uruchamianie elementu runbook](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak utworzyć alert dziennika aktywności, zobacz [utworzyć alerty dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Aby dowiedzieć się, jak utworzyć near alertu w czasie rzeczywistym, zobacz [Tworzenie reguły alertu w portalu Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
