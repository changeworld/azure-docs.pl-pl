---
title: Użyj alertów do wyzwalania elementu runbook usługi Azure Automation
description: Dowiedz się, jak można wyzwolić elementu runbook do uruchamiania, gdy zostanie zgłoszony alert platformy Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073862"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Użyj alertów do wyzwalania elementu runbook usługi Azure Automation

Możesz użyć [usługi Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) monitorować metryki na poziomie podstawowym i dzienników dla większości usług platformy Azure. Elementy runbook usługi Azure Automation można wywoływać za pomocą [grup akcji](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) lub za pomocą alertów klasycznych do automatyzowania zadań na podstawie alertów. W tym artykule przedstawiono sposób konfigurowania i uruchamiania elementu runbook za pomocą alertów.

## <a name="alert-types"></a>Typy alertów

Za pomocą elementów runbook usługi automation trzy typy alertów:
* Klasyczne alertów dotyczących metryk
* Alerty dotyczące dziennika aktywności
* Niemal w czasie rzeczywistym alertów dotyczących metryk

Jeśli alert wywołuje element runbook, to rzeczywiste wywołanie jest wysłanie żądania HTTP POST do elementu webhook. Treść żądania POST zawiera sformatowane JSON obiekt, który ma użytecznych właściwości, które są powiązane z alertem. Poniższa lista zawiera łącza do schematu ładunku dla każdego typu alertu:

|Alerty  |Opis|Ładunek schematu  |
|---------|---------|---------|
|[Klasycznego alertu metryki](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy dowolnej metryce poziom platformy spełnia określony warunek. Na przykład, gdy wartość **procent użycia procesora CPU** na maszynie Wirtualnej jest większa niż **90** ostatnich 5 minut.| [Schemat ładunku alertu metryki klasy](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alert dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy wszystkie nowe zdarzenie w dzienniku aktywności platformy Azure jest zgodna z określonych warunków. Na przykład, gdy `Delete VM` operacja odbywa się w **myProductionResourceGroup** lub gdy nowe zdarzenie kondycji usługi platformy Azure za pomocą **Active** zostanie wyświetlony stan.| [Schemat ładunku alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Niemal w czasie rzeczywistym alertu metryki](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie szybciej niż alertów dotyczących metryk, gdy co najmniej jedną metrykę poziom platformy spełniają określone warunki. Na przykład, gdy wartość **procent użycia procesora CPU** na maszynie Wirtualnej jest większa niż **90**, a następnie jego wartość **sieci w** jest większa niż **500 MB** w ciągu ostatnich 5 minuty.| [Niemal w czasie rzeczywistym ładunku alertu metryki schematu](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Ponieważ dane, które są dostarczane przez każdy typ alertu jest inny, każdego typu alertu odbywa się inaczej. W następnej sekcji dowiesz się, jak utworzyć element runbook w celu obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie elementu runbook do obsługi alertów

Aby korzystać z automatyzacji przy użyciu alertów usługi należy elementu runbook, który zawiera logikę, która zarządza ładunek w formacie JSON alertu, który jest przekazywany do elementu runbook. Następującego przykładowego elementu runbook musi zostać wywołany z poziomu alertu platformy Azure.

Zgodnie z opisem w poprzedniej sekcji, każdy rodzaj alert ma innego schematu. Skrypt pobiera dane elementu webhook w `WebhookData` parametr wejściowy elementu runbook z poziomu alertu. Następnie skrypt oblicza ładunek JSON, aby ustalić, jakiego typu alertu został użyty.

W tym przykładzie użyto alert z maszyny Wirtualnej. Pobiera dane maszyny Wirtualnej z ładunku, a następnie używa tych informacji, aby zatrzymać maszynę Wirtualną. Połączenie musi skonfigurować na koncie usługi Automation gdzie element runbook jest uruchomiony. Korzystanie z alertów, aby wyzwalać elementy runbook, jest ważne, aby sprawdzić stan alertu w elemencie runbook, który zostanie wywołany. Element runbook spowoduje wyzwolenie każdej zmianie alert stanu. Alerty mają różne stany, są dwa najpopularniejsze stany `Activated` i `Resolved`. Sprawdź, czy ten stan w logice elementu runbook, aby upewnić się, że dany element runbook nie działa więcej niż jeden raz. W przykładzie w tym artykule przedstawiono sposób wyszukiwania `Activated` tylko alerty.

Element runbook używa **AzureRunAsConnection** [konta Uruchom jako](automation-create-runas-account.md) do uwierzytelniania za pomocą platformy Azure w celu wykonania akcji zarządzania względem maszyny Wirtualnej.

Użyj tego przykładu, aby utworzyć element runbook o nazwie **Stop AzureVmInResponsetoVMAlert**. Możesz zmodyfikować skrypt programu PowerShell i korzystania z wielu różnych zasobów.

1. Przejdź do swojego konta usługi Azure Automation.
1. W obszarze **AUTOMATYZACJI procesów**, wybierz opcję **elementów Runbook**.
1. W górnej części listy elementów runbook, zaznacz **Dodaj element runbook**. 
1. Na stronie **Dodawanie elementu runbook** wybierz pozycję **Szybkie tworzenie**.
1. Wprowadź nazwę elementu runbook **Stop AzureVmInResponsetoVMAlert**. Wybierz typ elementu runbook **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).  
1. Skopiuj poniższy przykład programu PowerShell do **Edytuj** okienka. 

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
1. Wybierz **Publikuj** zapisywanie i publikowanie elementu runbook.

## <a name="create-an-action-group"></a>Utwórz grupę akcji

Grupy akcji to kolekcja działań, które są wyzwalane za pomocą alertu. Elementy Runbook to tylko jeden z wielu akcji, które można używać z grup akcji.

1. W witrynie Azure portal wybierz **Monitor** > **ustawienia** > **grup akcji**.
1. Wybierz **Dodaj grupę akcji**, a następnie wprowadź wymagane informacje:  
    1. W **Nazwa grupy akcji** wprowadź nazwę.
    1. W **krótką nazwę** wprowadź nazwę. Krótka nazwa jest używana zamiast akcji pełną nazwę grupy, podczas powiadomienia są wysyłane przy użyciu tej grupie akcji.
    1. **Subskrypcji** pole zostanie wypełnione automatycznie, przy użyciu Twojej bieżącej subskrypcji. To jest subskrypcja, w którym jest zapisany do grupy akcji.
    1. Wybierz grupę zasobów, w którym jest zapisany do grupy akcji.

W tym przykładzie utworzysz dwie akcje: akcję elementu runbook i akcji powiadomienia.

### <a name="runbook-action"></a>Działania elementu Runbook

Aby utworzyć akcję elementu runbook do grupy akcji:

1. W obszarze **akcje**, aby uzyskać **nazwy akcji**, wprowadź nazwę dla akcji. Aby uzyskać **typ akcji**, wybierz opcję **elementu Runbook usługi Automation**.
1. W obszarze **szczegóły**, wybierz opcję **Edytuj szczegóły**.  
1. Na **skonfigurować element Runbook** w obszarze **źródło elementu Runbook**, wybierz opcję **użytkownika**.  
1. Wybierz swoje **subskrypcji** i **konta usługi Automation**, a następnie wybierz pozycję **Stop AzureVmInResponsetoVMAlert** elementu runbook.  
1. Gdy skończysz, wybierz pozycję **OK**.

### <a name="notification-action"></a>Akcja powiadomienia

Aby utworzyć akcji powiadomienia do grupy akcji:

1. W obszarze **akcje**, aby uzyskać **nazwy akcji**, wprowadź nazwę dla akcji. Aby uzyskać **typ akcji**, wybierz opcję **wiadomości E-mail**.  
1. W obszarze **szczegóły** wybierz **Edytuj szczegóły**.  
1. Na **E-mail** strony, wprowadź adres e-mail do wysłania powiadomienia, a następnie wybierz **OK**. Dodawanie adresu e-mail, oprócz element runbook jako akcja przydaje się. Otrzymasz powiadomienie, gdy element runbook jest uruchomiony.  

    Grupy akcji powinna wyglądać podobnie do następującego:

   ![Dodaj stronę grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Aby utworzyć grupę akcji, wybierz **OK**.

Można użyć tej grupie akcji w [alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) i [alerty w czasie rzeczywistym w pobliżu](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) utworzony.

## <a name="classic-alert"></a>Klasyczny alert

Alertów klasycznych są na podstawie metryk i nie należy używać grup akcji. Można jednak skonfigurować akcję elementu runbook, na podstawie klasycznego alertu. 

Można utworzyć klasycznego alertu:

1. Wybierz pozycję **Dodaj alert dotyczący metryki**.
1. Nazwa alertu metryki **myVMCPUAlert**. Wprowadź krótki opis alertu.
1. Metryki warunek alertu można wybrać **większa**. Aby uzyskać **próg** wartości, wybierz opcję **10**. Aby uzyskać **okres** wartości, wybierz opcję **w ciągu ostatnich pięciu minut**.
1. W obszarze **reakcję**, wybierz opcję **uruchomienia elementu runbook z tego alertu**.
1. Na **skonfigurować element Runbook** strony dla **źródło elementu Runbook**, wybierz opcję **użytkownika**. Wybierz swoje konto usługi automation, a następnie wybierz **Stop AzureVmInResponsetoVMAlert** elementu runbook. Kliknij przycisk **OK**.
1. Aby zapisać reguły alertu, zaznacz **OK**.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat uruchamiania elementu runbook usługi Automation przy użyciu elementu webhook, zobacz [uruchamiania elementu runbook z poziomu elementu webhook](automation-webhooks.md).
* Aby uzyskać szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu runbook, zobacz [uruchamianie elementu runbook](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak utworzyć alertu dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Aby dowiedzieć się, jak utworzyć alert dotyczący niemal w czasie rzeczywistym, zobacz [Tworzenie reguły alertu w witrynie Azure portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

