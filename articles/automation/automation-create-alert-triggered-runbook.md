---
title: Użyj alertów do wyzwalania elementu runbook usługi Azure Automation
description: Dowiedz się, jak można wyzwolić elementu runbook do uruchamiania, gdy zostanie zgłoszony alert platformy Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d8e7bba6d43ba1daa3173ce5d7e043e2310a482
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229984"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Użyj alertów do wyzwalania elementu runbook usługi Azure Automation

Możesz użyć [usługi Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) monitorować metryki na poziomie podstawowym i dzienników dla większości usług platformy Azure. Elementy runbook usługi Azure Automation można wywoływać za pomocą [grup akcji](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) lub za pomocą alertów klasycznych do automatyzowania zadań na podstawie alertów. W tym artykule przedstawiono sposób konfigurowania i uruchamiania elementu runbook za pomocą alertów.

## <a name="alert-types"></a>Typy alertów

Za pomocą elementów runbook usługi automation cztery typy alertów:

* Najczęstsze alerty
* Alerty dziennika aktywności
* Niemal w czasie rzeczywistym alertów dotyczących metryk

> [!NOTE]
> Wspólny schemat alertu standaryzuje środowisko wykorzystania powiadomień o alertach na platformie Azure już dziś. W przeszłości trzy typy alertów na platformie Azure już dziś (metryk, dzienników i dziennika aktywności) miały własne szablony wiadomości e-mail, schematy elementu webhook, itp. Aby dowiedzieć się więcej, zobacz [wspólny schemat alertu](../azure-monitor/platform/alerts-common-schema.md)

Jeśli alert wywołuje element runbook, to rzeczywiste wywołanie jest wysłanie żądania HTTP POST do elementu webhook. Treść żądania POST zawiera sformatowane JSON obiekt, który ma użytecznych właściwości, które są powiązane z alertem. Poniższa lista zawiera łącza do schematu ładunku dla każdego typu alertu:

|Alert  |Opis|Ładunek schematu  |
|---------|---------|---------|
|[Typowe alertu](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Wspólny schemat alertu, który standaryzuje środowisko wykorzystania powiadomień o alertach na platformie Azure już dziś.|[Wspólny schemat ładunku alertu](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Alert dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy wszystkie nowe zdarzenie w dzienniku aktywności platformy Azure jest zgodna z określonych warunków. Na przykład, gdy `Delete VM` operacja odbywa się w **myProductionResourceGroup** lub gdy nowe zdarzenie kondycji usługi platformy Azure za pomocą **Active** zostanie wyświetlony stan.| [Schemat ładunku alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Niemal w czasie rzeczywistym alertu metryki](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie szybciej niż alertów dotyczących metryk, gdy co najmniej jedną metrykę poziom platformy spełniają określone warunki. Na przykład, gdy wartość **procent użycia procesora CPU** na maszynie Wirtualnej jest większa niż **90**, a następnie jego wartość **sieci w** jest większa niż **500 MB** w ciągu ostatnich 5 minuty.| [Niemal w czasie rzeczywistym ładunku alertu metryki schematu](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Ponieważ dane, które są dostarczane przez każdy typ alertu jest inny, każdego typu alertu odbywa się inaczej. W następnej sekcji dowiesz się, jak utworzyć element runbook w celu obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie elementu runbook do obsługi alertów

Aby korzystać z automatyzacji przy użyciu alertów usługi należy elementu runbook, który zawiera logikę, która zarządza ładunek w formacie JSON alertu, który jest przekazywany do elementu runbook. Następującego przykładowego elementu runbook musi zostać wywołany z poziomu alertu platformy Azure.

Zgodnie z opisem w poprzedniej sekcji, każdy rodzaj alert ma innego schematu. Skrypt pobiera dane elementu webhook w `WebhookData` parametr wejściowy elementu runbook z poziomu alertu. Następnie skrypt oblicza ładunek JSON, aby ustalić, jakiego typu alertu został użyty.

W tym przykładzie użyto alert z maszyny Wirtualnej. Pobiera dane maszyny Wirtualnej z ładunku, a następnie używa tych informacji, aby zatrzymać maszynę Wirtualną. Połączenie musi skonfigurować na koncie usługi Automation gdzie element runbook jest uruchomiony. Korzystanie z alertów, aby wyzwalać elementy runbook, jest ważne, aby sprawdzić stan alertu w elemencie runbook, który zostanie wywołany. Element runbook spowoduje wyzwolenie każdej zmianie alert stanu. Alerty mają różne stany, są dwa najpopularniejsze stany `Activated` i `Resolved`. Sprawdź, czy ten stan w logice elementu runbook, aby upewnić się, że dany element runbook nie działa więcej niż jeden raz. W przykładzie w tym artykule przedstawiono sposób wyszukiwania `Activated` tylko alerty.

Element runbook używa **AzureRunAsConnection** [konta Uruchom jako](automation-create-runas-account.md) do uwierzytelniania za pomocą platformy Azure w celu wykonania akcji zarządzania względem maszyny Wirtualnej.

Użyj tego przykładu, aby utworzyć element runbook o nazwie **Stop AzureVmInResponsetoVMAlert**. Możesz zmodyfikować skrypt programu PowerShell i korzystania z wielu różnych zasobów.

1. Przejdź do swojego konta usługi Azure Automation.
2. W obszarze **automatyzacji procesów**, wybierz opcję **elementów Runbook**.
3. W górnej części listy elementów runbook, zaznacz **+ Utwórz element runbook**.
4. Na **Dodawanie elementu Runbook** wpisz **Stop AzureVmInResponsetoVMAlert** dla nazwy elementu runbook. Wybierz typ elementu runbook **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).  
5. Skopiuj poniższy przykład programu PowerShell do **Edytuj** strony.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Wybierz **Publikuj** zapisywanie i publikowanie elementu runbook.

## <a name="create-the-alert"></a>Utwórz alert

Alerty korzystanie z grup akcji, które są kolekcjami akcje, które są wyzwalane przez alert. Elementy Runbook to tylko jeden z wielu akcji, które można używać z grup akcji.

1. Na koncie usługi Automation wybierz **alerty** w obszarze **monitorowanie**.
1. Wybierz **+ Nowa reguła alertu**.
1. Kliknij przycisk **wybierz** w obszarze **zasobów**. Na **wybierz zasób** strony, wybierz maszynę Wirtualną z alertem wylogować się z, a następnie kliknij przycisk **gotowe**.
1. Kliknij przycisk **Dodaj warunek** w obszarze **warunek**. Wybierz sygnał, którego chcesz użyć, na przykład **procentowe użycie procesora CPU** i kliknij przycisk **gotowe**.
1. Na **konfigurowanie logiki sygnału** wpisz swoje **wartość progowa** w obszarze **Alert logic**i kliknij przycisk **gotowe**.
1. W obszarze **Grupy akcji** wybierz pozycję **Utwórz nową**.
1. Na **Dodaj grupę akcji** strony, nadać grupy akcji, nazwy i krótkie nazwy.
1. Nazwij akcji. Typ akcji wybierz **elementu Runbook usługi Automation**.
1. Wybierz **Edytuj szczegóły**. Na **skonfigurować element Runbook** w obszarze **źródło elementu Runbook**, wybierz opcję **użytkownika**.  
1. Wybierz swoje **subskrypcji** i **konta usługi Automation**, a następnie wybierz pozycję **Stop AzureVmInResponsetoVMAlert** elementu runbook.  
1. Wybierz **tak** dla **Włącz wspólny schemat alertu**.
1. Aby utworzyć grupę akcji, wybierz **OK**.

    ![Dodaj stronę grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Można użyć tej grupie akcji w [alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) i [alerty w czasie rzeczywistym w pobliżu](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) utworzony.

1. W obszarze **szczegóły alertu**, Dodaj regułę alertu nazwę i opis i kliknij przycisk **Utwórz regułę alertu**.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat uruchamiania elementu runbook usługi Automation przy użyciu elementu webhook, zobacz [uruchamiania elementu runbook z poziomu elementu webhook](automation-webhooks.md).
* Aby uzyskać szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu runbook, zobacz [uruchamianie elementu runbook](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak utworzyć alertu dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Aby dowiedzieć się, jak utworzyć alert dotyczący niemal w czasie rzeczywistym, zobacz [Tworzenie reguły alertu w witrynie Azure portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
