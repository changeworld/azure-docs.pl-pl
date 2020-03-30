---
title: Przenoszenie sieciowej grupy zabezpieczeń platformy Azure do innego regionu platformy Azure przy użyciu programu Azure PowerShell
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść grupę zabezpieczeń sieci platformy Azure z jednego regionu platformy Azure do drugiego przy użyciu programu Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641472"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Przenoszenie sieciowej grupy zabezpieczeń platformy Azure do innego regionu przy użyciu programu Azure PowerShell

Istnieją różne scenariusze, w których chcesz przenieść istniejące cele zabezpieczeń z jednego regionu do drugiego. Na przykład można utworzyć sieciowej grupy zabezpieczeń z tej samej konfiguracji i reguł zabezpieczeń do testowania. Można również przenieść nsg do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenosić grup zabezpieczeń platformy Azure z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager do wyeksportowania istniejących reguł konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń.  Następnie można zorganizować zasób w innym regionie, eksportując grupę zabezpieczeń do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że grupa zabezpieczeń sieci platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić grup zabezpieczeń sieci platformy Azure między regionami.  Musisz skojarzyć nową szkoliwę grupy ndsg z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację sieciowej grupy zabezpieczeń i wdrożyć szablon w celu utworzenia sieciowej grupy zabezpieczeń w innym regionie, musisz mieć rolę współautora sieci lub wyższą.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, publiczne adresy IP i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie grup zabezpieczeń w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodatku nsgs dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować grupę zabezpieczeń sieci do przenoszenia reguły konfiguracji i zabezpieczeń przy użyciu szablonu Menedżera zasobów i przenieść reguły konfiguracji sieciowej grupy zabezpieczeń i zabezpieczeń do regionu docelowego przy użyciu programu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie ze skryptu

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu sieciowej grupy zabezpieczeń, którą chcesz przenieść do regionu docelowego, i umieść ją w zmiennej przy użyciu [get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj źródłową grupę sieciową sieciowej do pliku .json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy grupy nsg, zmień właściwość **defaultValue** nazwy źródłowej grupy nsg na nazwę docelowej grupy ndg, upewnij się, że nazwa znajduje się w cudzysłowie:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Aby edytować region docelowy, w którym zostaną przeniesione reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń, zmień właściwość **lokalizacji** w obszarze **zasoby:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Aby uzyskać kody lokalizacji regionu, można użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Można również zmienić inne parametry w ** \<nazwie grupy zasobów>.json,** jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Reguły zabezpieczeń** — można edytować reguły wdrożone w docelowej grupie zabezpieczeń, dodając lub usuwając reguły do sekcji **securityRules** w pliku ** \<>.json nazwa grupy zasobów:**

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Aby zakończyć dodawanie lub usuwanie reguł w docelowej grupie sieciowej sieciowej, należy również edytować typy reguł niestandardowych na końcu ** \<pliku>.json** w formacie poniższego przykładu:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Zapisz ** \<plik>.json nazwa grupy zasobów.**

10. Utwórz grupę zasobów w regionie docelowym dla docelowej sieciowej grupy roboczej, która ma zostać wdrożona przy użyciu [new-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Wdrażanie edytowanego ** \<pliku>.json w** grupie zasobów utworzonej w poprzednim kroku przy użyciu [funkcji New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, należy użyć [get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Odrzuć 

Po wdrożeniu, jeśli chcesz rozpocząć od nowa lub odrzucić grupę sieciowej w miejscu docelowym, usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiona grupa sieciowej grupy sieciowej zostanie usunięta.  Aby usunąć grupę zasobów, użyj [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy zabezpieczeń, usuń źródłową grupę sieciowej grupy zabezpieczeń lub zasobów, użyj [usuń-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
