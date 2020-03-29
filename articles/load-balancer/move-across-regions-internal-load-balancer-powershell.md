---
title: Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu platformy Azure przy użyciu usługi Azure PowerShell
description: Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure z jednego regionu platformy Azure do innego za pomocą usługi Azure PowerShell za pomocą szablonu usługi Azure Resource Manager
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644277"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu przy użyciu programu PowerShell

Istnieją różne scenariusze, w których chcesz przenieść istniejący wewnętrzny moduł równoważenia obciążenia z jednego regionu do drugiego. Na przykład można utworzyć wewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Można również przenieść wewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenosić wewnętrznych modułów równoważenia obciążenia platformy Azure z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager, aby wyeksportować istniejącą konfigurację i sieć wirtualną wewnętrznego modułu równoważenia obciążenia.  Następnie można zorganizować zasób w innym regionie, eksportując moduł równoważenia obciążenia i sieć wirtualną do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić wewnętrznych modułów równoważenia obciążenia platformy Azure między regionami.  Musisz skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować wewnętrzną konfigurację modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia wewnętrznego modułu równoważenia obciążenia w innym regionie, należy potrzebować roli Współautor sieci lub wyższy.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, maszyny wirtualne i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie wewnętrznych modułów równoważenia obciążenia w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania modułów równoważenia obciążenia dla tego procesu.  Zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować wewnętrzny moduł równoważenia obciążenia do przeniesienia przy użyciu szablonu Menedżera zasobów i przenieść konfigurację wewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu programu Azure PowerShell.  W ramach tego procesu należy uwzględnić konfigurację sieci wirtualnej modułu wewnętrznego równoważenia obciążenia i wykonać najpierw przed przeniesieniem wewnętrznego modułu równoważenia obciążenia.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Eksportowanie szablonu sieci wirtualnej i wdrażanie z programu Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Uzyskaj identyfikator zasobu sieci wirtualnej, którą chcesz przenieść do regionu docelowego, i umieść ją w zmiennej za pomocą [sieci Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportowanie źródłowej sieci wirtualnej do pliku json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy sieci wirtualnej, zmień **właściwość defaultValue** nazwy źródłowej sieci wirtualnej na nazwę docelowej sieci wirtualnej, upewnij się, że nazwa znajduje się w cudzysłowie:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Aby edytować region docelowy, w którym zostanie przeniesiona sieci wirtualnej, zmień właściwość **lokalizacji** w obszarze zasoby:

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
  
7. Aby uzyskać kody lokalizacji regionu, można użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Można również zmienić inne parametry w pliku ** \<>.json nazwa grupy zasobów,** w zależności od wymagań:

    * **Przestrzeń** adresowa — przestrzeń adresowa sieci wirtualnej można zmienić przed zapisaniem, modyfikując **sekcję** > **resource addressSpace** i zmieniając właściwość **addressPrefixes** w pliku ** \<>.json** name grupy zasobów:

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

    * **Podsieć** — nazwę podsieci i przestrzeń adresową **podsieci** można zmienić lub dodać, modyfikując sekcję podsieci ** \<pliku>.json.** Nazwę podsieci można zmienić, zmieniając właściwość **name.** Przestrzeń adresowa podsieci można zmienić, zmieniając właściwość **addressPrefix** w ** \<pliku>.json:**

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

         W pliku ** \<>.json nazwa grupy zasobów,** aby zmienić prefiks adresu, musi być edytowany w dwóch miejscach, sekcji wymienionej powyżej i sekcji **typu** wymienionej poniżej.  Zmień **właściwość addressPrefix,** aby dopasować się do powyższej:

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

9.  Zapisz ** \<plik>.json nazwa grupy zasobów.**

10. Tworzenie grupy zasobów w regionie docelowym dla docelowej sieci wirtualnej, która ma zostać wdrożona przy użyciu [grupy New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Wdrażanie edytowanego ** \<pliku>.json w** grupie zasobów utworzonej w poprzednim kroku przy użyciu [funkcji New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, należy użyć [polecenia Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Eksportowanie szablonu wewnętrznego modułu równoważenia obciążenia i wdrażanie z programu Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu wewnętrznego modułu równoważenia obciążenia, który chcesz przenieść do regionu docelowego, i umieść go w zmiennej za pomocą [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportowanie źródłowej wewnętrznej konfiguracji modułu równoważenia obciążenia do pliku json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy wewnętrznego modułu równoważenia obciążenia, zmień **właściwość defaultValue** nazwy źródłowego wewnętrznego modułu równoważenia obciążenia na nazwę docelowego wewnętrznego modułu równoważenia obciążenia, upewnij się, że nazwa znajduje się w cudzysłowie:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Aby edytować wartość docelowej sieci wirtualnej, która została przeniesiona powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować i wkleić go do ** \<pliku>.json.**  Aby uzyskać identyfikator, użyj [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Wpisz zmienną i naciśnij enter, aby wyświetlić identyfikator zasobu.  Wyróżnij ścieżkę identyfikatora i skopiuj ją do schowka:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  W pliku ** \<>.json name resource-group** wklej **identyfikator zasobu** ze zmiennej zamiast **domyślnejWartość** w drugim parametrze dla docelowego identyfikatora sieci wirtualnej, upewnij się, że ścieżka została ujęta w cudzysłowie:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Aby edytować region docelowy, w którym zostanie przeniesiona wewnętrzna konfiguracja modułu równoważenia obciążenia, zmień właściwość **lokalizacji** w obszarze **zasoby** w pliku ** \<>.json nazwa grupy zasobów:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Aby uzyskać kody lokalizacji regionu, można użyć polecenia cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) programu Azure PowerShell, uruchamiając następujące polecenie:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Można również zmienić inne parametry w szablonie, jeśli wybierzesz i są opcjonalne w zależności od wymagań:
    
    * **Sku** - Można zmienić sku wewnętrznego modułu równoważenia obciążenia w konfiguracji ze standardowego na podstawowy lub podstawowy na standard, zmieniając właściwość **sku** > **name** w pliku ** \<>.json name resource-group:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Aby uzyskać więcej informacji na temat różnic między podstawowymi i standardowymi modułami równoważenia obciążenia sku, zobacz [Omówienie równoważenia obciążenia standardowego usługi Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Reguły równoważenia obciążenia** — można dodawać lub usuwać reguły równoważenia obciążenia w konfiguracji, dodając lub usuwając wpisy do sekcji **loadBalancingRules** w pliku ** \<>.json nazwa grupy zasobów:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Aby uzyskać więcej informacji na temat reguł równoważenia obciążenia, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondy** — można dodać lub usunąć sondę dla modułu równoważenia obciążenia w konfiguracji, dodając lub usuwając **wpisy** do sekcji ** \<sondy pliku>.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Aby uzyskać więcej informacji na temat sond kondycji modułu równoważenia obciążenia platformy Azure, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Przychodzące reguły NAT** — dla modułu równoważenia obciążenia można dodawać lub usuwać przychodzące reguły NAT, dodając lub usuwając wpisy do sekcji **inboundNatRules** ** \<** Przychodzące UstawieniaNatrules pliku>.json:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Aby zakończyć dodawanie lub usuwanie przychodzącej reguły TRANSLATORA, reguła musi być obecna lub usuwana jako właściwość **typu** na końcu pliku ** \<>.json nazwa grupy zasobów:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Aby uzyskać więcej informacji na temat przychodzących reguł nat, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Zapisz ** \<plik>.json nazwa grupy zasobów.**
    
10. Utwórz lub grupę zasobów w regionie docelowym dla docelowego wewnętrznego modułu równoważenia obciążenia, który ma zostać wdrożony przy użyciu [programu New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Istniejąca grupa zasobów z góry może być również ponownie życiówka w ramach tego procesu:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Wdrażanie edytowanego ** \<pliku>.json w** grupie zasobów utworzonej w poprzednim kroku przy użyciu [funkcji New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Aby sprawdzić, czy zasoby zostały utworzone w regionie docelowym, należy użyć [get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) i [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Odrzuć 

Po wdrożeniu, jeśli chcesz rozpocząć od nowa lub odrzucić sieć wirtualną i moduł równoważenia obciążenia w miejscu docelowym, usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiona sieć wirtualna i moduł równoważenia obciążenia zostaną usunięte.  Aby usunąć grupę zasobów, użyj [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy sieciowej, usuń źródłową grupę sieciowej grupy zasobów sieciowych lub zasobów, użyj [usuń-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) i [Usuń-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono wewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
