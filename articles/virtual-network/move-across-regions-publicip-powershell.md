---
title: Przenoszenie publicznego adresu IP platformy Azure do innego regionu platformy Azure przy użyciu programu Azure PowerShell
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść publiczny adres IP platformy Azure z jednego regionu platformy Azure do drugiego przy użyciu programu Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641557"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Przenoszenie publicznego adresu IP platformy Azure do innego regionu przy użyciu programu Azure PowerShell

Istnieją różne scenariusze, w których chcesz przenieść istniejące publiczne usługi Azure IP z jednego regionu do drugiego. Na przykład można utworzyć publiczny adres IP z tą samą konfiguracją i sku do testowania. Można również przenieść publiczny adres IP do innego regionu w ramach planowania odzyskiwania po awarii.

Publiczne usługi IP platformy Azure są specyficzne dla regionu i nie można ich przenosić z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager do wyeksportowania istniejącej konfiguracji publicznego adresu IP.  Następnie można zorganizować zasób w innym regionie, eksportując publiczny adres IP do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że publiczny adres IP platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Publiczne usługi IP platformy Azure nie mogą być przenoszone między regionami.  Musisz skojarzyć nowe adresy ip publiczne z zasobami w regionie docelowym.

- Aby wyeksportować publiczną konfigurację adresu IP i wdrożyć szablon w celu utworzenia publicznego adresu IP w innym regionie, musisz mieć rolę Współautor sieci lub wyższą.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie publicznych witryn IP w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania publicznych usług IP dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować publiczny adres IP do przeniesienia konfiguracji przy użyciu szablonu Menedżera zasobów i przenieść publiczną konfigurację IP do regionu docelowego przy użyciu programu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie ze skryptu

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu publicznego adresu IP, który chcesz przenieść do regionu docelowego, i umieść go w zmiennej przy użyciu [adresu Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportowanie źródłowej sieci wirtualnej do pliku json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Aby edytować parametr publicznej nazwy IP, zmień **właściwość defaultValue** nazwy źródłowego publicznego adresu IP na nazwę docelowego publicznego adresu IP, upewnij się, że nazwa znajduje się w cudzysłowie:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Aby edytować region docelowy, w którym zostanie przeniesiony publiczny adres IP, zmień właściwość **lokalizacji** w obszarze zasoby:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Aby uzyskać kody lokalizacji regionu, można użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Można również zmienić inne parametry w szablonie, jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Sku** - Można zmienić sku publicznego adresu IP w konfiguracji ze standardowego na podstawowy lub podstawowy na standard, zmieniając właściwość **sku** > **name** w pliku ** \<>.json name grupy zasobów:**

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Aby uzyskać więcej informacji na temat różnic między podstawowymi i standardowymi identyfikatorami publicznymi sku, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Publicznej metody alokacji ip** i **limitu czasu bezczynności** — można zmienić obie te opcje w szablonie, zmieniając **właściwość publicIPAllocationMethod** z **dynamicznego** na **statyczny** lub **statyczny** na **dynamiczny**. Limit czasu bezczynność można zmienić, zmieniając **idleTimeoutInMinutes** właściwości do żądanej kwoty.  Wartość domyślna to **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Aby uzyskać więcej informacji na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Zapisz ** \<plik>.json nazwa grupy zasobów.**

10. Utwórz grupę zasobów w regionie docelowym dla docelowego publicznego adresu IP, który ma zostać wdrożony przy użyciu [programu New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Wdrażanie edytowanego ** \<pliku>.json w** grupie zasobów utworzonej w poprzednim kroku przy użyciu [funkcji New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, należy użyć [adresów Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Odrzuć 

Po wdrożeniu, jeśli chcesz rozpocząć od nowa lub odrzucić publiczne ip w miejscu docelowym, usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiony publiczny adres IP zostanie usunięty.  Aby usunąć grupę zasobów, użyj [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, usuń źródło sieci wirtualnej lub grupę zasobów, użyj [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono publiczny adres IP platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
