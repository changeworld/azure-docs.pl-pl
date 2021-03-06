---
title: Przenoszenie sieci wirtualnej platformy Azure do innego regionu platformy Azure przy użyciu programu Azure PowerShell
description: Przenoszenie sieci wirtualnej platformy Azure z jednego regionu platformy Azure do innego przy użyciu szablonu usługi Resource Manager i programu Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646714"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Przenoszenie sieci wirtualnej platformy Azure do innego regionu przy użyciu programu Azure PowerShell

Istnieją różne scenariusze przenoszenia istniejącej sieci wirtualnej platformy Azure z jednego regionu do drugiego. Na przykład można utworzyć sieć wirtualną z taką samą konfiguracją do testowania i dostępności jak istniejąca sieć wirtualna. Lub można przenieść produkcyjną sieć wirtualną do innego regionu w ramach planowania odzyskiwania po awarii.

Za pomocą szablonu usługi Azure Resource Manager można wykonać przeniesienie sieci wirtualnej do innego regionu. Można to zrobić, eksportując sieć wirtualną do szablonu, modyfikując parametry, aby dopasować go do regionu docelowego, a następnie wdrażając szablon w nowym regionie. Aby uzyskać więcej informacji na temat szablonów Menedżera zasobów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że sieć wirtualna znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Aby wyeksportować sieć wirtualną i wdrożyć szablon w celu utworzenia sieci wirtualnej w innym regionie, musisz mieć rolę współautora sieci lub wyższą.

- Komunikacja równorzędna sieci wirtualnej nie zostanie ponownie utworzona i zakończy się niepowodzeniem, jeśli nadal są obecne w szablonie. Przed wyeksportowaniem szablonu należy usunąć wszystkie elementy równorzędne sieci wirtualnej. Następnie można przywrócić je po przeniesieniu sieci wirtualnej.
    
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publiczne pliki IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieci wirtualnych w regionie docelowym. Aby włączyć wymagany przydział, skontaktuj się z pomocą techniczną.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania sieci wirtualnych dla tego procesu. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Przygotuj się do przeprowadzki
W tej sekcji należy przygotować sieć wirtualną do przeniesienia przy użyciu szablonu Menedżera zasobów. Następnie przenieść sieć wirtualną do regionu docelowego przy użyciu poleceń programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wyeksportować sieć wirtualną i wdrożyć docelową sieć wirtualną przy użyciu programu PowerShell, wykonaj następujące czynności:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a następnie postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Uzyskaj identyfikator zasobu sieci wirtualnej, który chcesz przenieść do regionu docelowego, a następnie umieść go w zmiennej za pomocą [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Wyeksportowanie źródłowej sieci wirtualnej do pliku json w katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Pobrany plik ma taką samą nazwę jak grupa zasobów, z których wyeksportowano zasób. Znajdź * \<plik>.json nazwa grupy zasobów,* który został wyeksportowany za pomocą polecenia, a następnie otwórz go w edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Aby edytować parametr nazwy sieci wirtualnej, zmień **właściwość defaultValue** nazwy źródłowej sieci wirtualnej na nazwę docelowej sieci wirtualnej. Należy ująć nazwę w cudzysłów.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Aby edytować region docelowy, w którym sieć wirtualna zostanie przeniesiona, zmień właściwość **lokalizacji** w obszarze zasoby:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Aby uzyskać kody lokalizacji regionu, można użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Opcjonalnie) Można również zmienić inne parametry w pliku * \<>.json nazwa grupy zasobów,* w zależności od wymagań:

    * **Przestrzeń adresowa:** Przed zapisaniem pliku można zmienić przestrzeń adresową sieci wirtualnej, modyfikując **sekcję** > **resources addressSpace** i zmieniając właściwość **addressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Podsieć**: Można zmienić lub dodać do nazwy podsieci i przestrzeni adresowej podsieci, zmieniając sekcję **podsieci** pliku. Nazwę podsieci można zmienić, zmieniając właściwość **name.** I można zmienić przestrzeń adresową podsieci, zmieniając **właściwość addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Aby zmienić prefiks adresu, edytuj plik w dwóch miejscach: w kodzie w poprzedniej sekcji i w sekcji **typu** następującego kodu. Zmień **właściwość addressPrefix** w poniższym kodzie, aby dopasować właściwość **addressPrefix** w kodzie w poprzedniej sekcji.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Zapisz * \<plik>.json nazwa grupy zasobów.*

1. Utwórz grupę zasobów w regionie docelowym dla docelowej sieci wirtualnej, która ma zostać wdrożona przy użyciu [programu New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Wdrażanie edytowanego * \<pliku>.json w* grupie zasobów utworzonej w poprzednim kroku przy użyciu [funkcji New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, należy użyć [get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Usuwanie sieci wirtualnej lub grupy zasobów 

Po wdrożeniu sieci wirtualnej, aby rozpocząć od nowa lub odrzucić sieć wirtualną w regionie docelowym, usuń grupę zasobów utworzoną w regionie docelowym, a przeniesiona sieć wirtualna zostanie usunięta. 

Aby usunąć grupę zasobów, użyj [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, wykonaj jedną z następujących czynności:

* Usuń grupę zasobów przy użyciu [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Usuń źródło sieci wirtualnej za pomocą [Remove-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono sieć wirtualną z jednego regionu do drugiego przy użyciu programu PowerShell, a następnie oczyściłeś niepotrzebne zasoby źródłowe. Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:

- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
