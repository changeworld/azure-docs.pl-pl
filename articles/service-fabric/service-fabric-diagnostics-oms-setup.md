---
title: Azure Service Fabric — skonfiguruj monitorowanie za pomocą analizy dzienników OMS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Operations Management Suite do wizualizacji i analizowania zdarzeń do monitorowania klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/30/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 2589efa1808a394f2e32b842efa2ee70809da232
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-operations-management-suite-log-analytics-for-a-cluster"></a>Konfigurowanie Operations Management Suite Log Analytics dla klastra

Obszar roboczy Operations Management Suite (OMS) za pośrednictwem usługi Azure Resource Manager, programu PowerShell lub portalu Azure Marketplace można skonfigurować. Jeśli obsługa zaktualizowany szablon usługi Resource Manager wdrożenia do użycia w przyszłości, należy użyć tego samego szablonu do konfigurowania środowiska OMS. Wdrożenia za pośrednictwem portalu Marketplace jest łatwiejsze, jeśli masz już wdrożone z włączoną diagnostykę klastra. Jeśli nie masz dostępu na poziomie subskrypcji w ramach konta, w której wdrażasz OMS, wdrożyć przy użyciu programu PowerShell lub szablonu usługi Resource Manager.

> [!NOTE]
> Można skonfigurować OMS, aby monitorować klastra, musisz mieć włączone, aby wyświetlić zdarzenia klastra platformy poziomie lub diagnostyki.

## <a name="deploy-oms-by-using-azure-marketplace"></a>Wdrażanie pakietu OMS przy użyciu portalu Azure Marketplace

Jeśli chcesz dodać obszar roboczy OMS po wdrożeniu klastra, przejdź do portalu Azure Marketplace w portalu i poszukaj **usługi sieć szkieletowa Analytics**:

1. Wybierz **nowy** w menu nawigacji po lewej stronie. 

2. Wyszukaj **usługi Analytics sieci szkieletowej**. Wybierz zasób, który jest wyświetlany.

3. Wybierz pozycję **Utwórz**.

    ![OMS rz Analytics w portalu Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. W oknie tworzenia analiz sieci szkieletowej usług wybierz **wybierz obszar roboczy** dla **obszarem roboczym pakietu OMS** pola, a następnie **Utwórz nowy obszar roboczy**. Wypełnij odpowiednie wpisy. Jedynym wymaganiem jest subskrypcji dla klastra usługi sieć szkieletowa usług i obszar roboczy OMS jest taka sama. Po sprawdzeniu poprawności wpisy obszar roboczy OMS rozpocznie wdrażanie. Wdrożenie zajmuje tylko kilka minut.

5. Po zakończeniu wybierz **Utwórz** ponownie w dolnej części okna Tworzenie usługi Analytics sieci szkieletowej. Upewnij się, że nowy obszar roboczy zostaną wyświetlone w obszarze **obszarem roboczym pakietu OMS**. Ta akcja dodaje rozwiązania do obszaru roboczego, który został utworzony.

Jeśli korzystasz z systemu Windows, wykonaj następujące kroki, aby nawiązać połączenia z usługą OMS konta magazynu przechowywania zdarzeń klastra. 

>[!NOTE]
>Włączenie tej czynności w przypadku klastrów systemu Linux nie jest jeszcze dostępna. 

### <a name="connect-the-oms-workspace-to-your-cluster"></a>Obszar roboczy OMS nawiązać połączenia z klastrem 

1. Obszar roboczy musi być połączona z danych diagnostycznych z klastra. Przejdź do grupy zasobów, w której utworzono rozwiązania analizy sieci szkieletowej usług. Wybierz **ServiceFabric\<nameOfOMSWorkspace\>**  i przejdź do strony Przegląd. Z tego miejsca można zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i uzyskać dostęp do portalu OMS.

2. W menu nawigacji po lewej stronie w obszarze **źródeł danych obszaru roboczego**, wybierz pozycję **dzienników kont magazynu**.

3. Na **dzienniki konta magazynu** wybierz pozycję **Dodaj** u góry, aby dodać dzienników z klastra do obszaru roboczego.

4. Wybierz **konta magazynu** można dodać odpowiednie konta tworzone w klastrze. Jeśli zostanie użyta domyślna nazwa, konto magazynu jest **sfdg\<resourceGroupName\>**. Można również potwierdzić to przy użyciu szablonu usługi Azure Resource Manager używane do wdrażania klastra, sprawdzając wartość używaną do **applicationDiagnosticsStorageAccountName**. Jeśli nazwa nie jest wyświetlany, przewiń w dół i wybierz **załadować więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw ją na **zdarzenia sieci szkieletowej usług**.

6. Upewnij się, że źródło jest automatycznie ustawiana **WADServiceFabric\*EventTable**.

7. Wybierz **OK** nawiązać obszaru roboczego dzienników z klastra.

    ![Dodaj dzienniki konta magazynu z usługą OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto teraz wyświetlone jako część konta magazynu dzienniki w źródłach danych obszaru roboczego.

W obszarze roboczym analizy dzienników OMS, który jest teraz prawidłowo podłączone do sieci klastra platformy oraz tabeli dziennika aplikacji dodaniu rozwiązania analizy sieci szkieletowej usług. Możesz dodać dodatkowe źródła do obszaru roboczego w taki sam sposób.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Wdrażanie pakietu OMS przy użyciu szablonu usługi Resource Manager

W przypadku wdrażania klastra przy użyciu szablonu usługi Resource Manager szablon tworzy nowy obszar roboczy OMS, dodaje rozwiązania sieci szkieletowej usług do obszaru roboczego i skonfiguruje je odczytać danych z tabel odpowiedniego magazynu.

Można użyć i modyfikować [ten przykładowy szablon](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms) zgodnie z wymaganiami.

Wprowadź następujące zmiany:
1. Dodaj `omsWorkspaceName` i `omsRegion` do parametry, dodając następujący fragment kodu do parametrów zdefiniowanych w Twojej *template.json* pliku. Możesz zmodyfikować wartości domyślne, zgodnie z własnymi potrzebami. Ponadto Dodaj dwa nowe parametry w Twojej *parameters.JSON następującym kodem* pliku, aby określić ich wartości dla wdrażania zasobów:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` Wartości muszą być zgodne z określonego zestawu wartości. Wybierz jedną, która jest najbardziej zbliżony do wdrożenia klastra.

2. Jeśli wszystkie dzienniki aplikacji zostanie wysłana do OMS, najpierw upewnij się, że `applicationDiagnosticsStorageAccountType` i `applicationDiagnosticsStorageAccountName` są dołączone jako parametry w szablonie. Jeśli nie są uwzględnione, dodaj je do sekcji zmiennych i Edytuj ich wartości zgodnie z potrzebami. Można również uwzględnić je jako parametry, postępując w poprzednim formacie.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Dodaj rozwiązanie usługi sieć szkieletowa OMS do zmiennych do szablonu:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Dodaj następującą wartość do końca sekcji zasobów, po którym jest zadeklarowany jako zasób klastra usługi sieć szkieletowa usług:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Jeśli dodano `applicationDiagnosticsStorageAccountName` jako zmienną, upewnij się, że każde odwołanie do jej modyfikowania `variables('applicationDiagnosticsStorageAccountName')` zamiast `parameters('applicationDiagnosticsStorageAccountName')`.

5. Wdrażanie szablonu jako uaktualniania programu Menedżer zasobów do klastra przy użyciu `New-AzureRmResourceGroupDeployment` interfejsu API w module środowiska AzureRM PowerShell. Należy na przykład polecenie:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Usługa Azure Resource Manager wykryje, że to polecenie aktualizacji do istniejącego zasobu. Przetwarza tylko zmiany między szablonu zwiększają istniejącego wdrożenia i nowy szablon, pod warunkiem.

## <a name="deploy-oms-by-using-azure-powershell"></a>Wdrażanie pakietu OMS przy użyciu programu Azure PowerShell

Można także wdrożyć zasobu OMS Log Analytics za pomocą programu PowerShell przy użyciu `New-AzureRmOperationalInsightsWorkspace` polecenia. Aby użyć tej metody, upewnij się, zainstalowano [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Użyj tego skryptu do tworzenia nowego obszaru roboczego analizy dzienników OMS i Dodaj do niej rozwiązania sieci szkieletowej usług: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Gdy wszystko będzie gotowe, wykonaj kroki opisane w poprzedniej sekcji do nawiązania połączenia z kontem magazynu odpowiednie OMS analizy dzienników.

Można także dodać inne rozwiązania lub wprowadzić inne zmiany na obszar roboczy OMS przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [analizy dzienników zarządzania przy użyciu programu PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie agenta pakietu OMS](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk docker i dzienniki dla kontenerów
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników
* [Projektant widoków umożliwia tworzenie niestandardowych widoków w analizy dzienników](../log-analytics/log-analytics-view-designer.md)
