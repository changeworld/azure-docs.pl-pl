---
title: Przenoszenie zewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu platformy Azure przy użyciu usługi Azure PowerShell
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść zewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu platformy Azure do drugiego przy użyciu programu Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644821"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Przenoszenie zewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu przy użyciu programu Azure PowerShell

Istnieją różne scenariusze, w których chcesz przenieść istniejący moduł równoważenia obciążenia zewnętrznego z jednego regionu do drugiego. Na przykład można utworzyć zewnętrznego modułu równoważenia obciążenia z tą samą konfiguracją do testowania. Można również przenieść zewnętrznego modułu równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenosić zewnętrznych modułów równoważenia obciążenia platformy Azure z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager do wyeksportowania istniejącej konfiguracji i publicznego adresu IP zewnętrznego modułu równoważenia obciążenia.  Następnie można zorganizować zasób w innym regionie, eksportując moduł równoważenia obciążenia i publiczny adres IP do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że moduł równoważenia obciążenia zewnętrznego platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić zewnętrznych modułów równoważenia obciążenia platformy Azure między regionami.  Musisz skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację zewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia zewnętrznego modułu równoważenia obciążenia w innym regionie, należy potrzebować roli Współautor sieci lub wyższy.
   
- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, publiczne adresy IP i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie zewnętrznych modułów równoważenia obciążenia w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania modułów równoważenia obciążenia dla tego procesu.  Zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować zewnętrznego modułu równoważenia obciążenia do przeniesienia przy użyciu szablonu Menedżera zasobów i przenieść konfigurację zewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu programu Azure PowerShell.  W ramach tego procesu należy uwzględnić publiczną konfigurację IP zewnętrznego modułu równoważenia obciążenia i wykonać najpierw przed przeniesieniem zewnętrznego modułu równoważenia obciążenia.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Eksportowanie publicznego szablonu ip i wdrażanie z programu Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Uzyskaj identyfikator zasobu publicznego adresu IP, który chcesz przenieść do regionu docelowego, i umieść go w zmiennej przy użyciu [adresu Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportuj publiczny adres IP źródłowy do pliku json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
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

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Eksportowanie szablonu zewnętrznego modułu równoważenia obciążenia i wdrażanie z programu Azure PowerShell

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Uzyskaj identyfikator zasobu zewnętrznego modułu równoważenia obciążenia, który chcesz przenieść do regionu docelowego, i umieść go w zmiennej za pomocą [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Wyeksportowanie źródłowej konfiguracji zewnętrznego modułu równoważenia obciążenia do pliku json do katalogu, w którym wykona się polecenie [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Pobrany plik zostanie nazwany na cześć grupy zasobów, z którego został wyeksportowany.  Znajdź plik, który został wyeksportowany z polecenia o nazwie ** \<nazwa grupy zasobów>.json** i otwórz go w wybranym edytorze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Aby edytować parametr nazwy zewnętrznego modułu równoważenia obciążenia, zmień **właściwość defaultValue** nazwy źródłowego zewnętrznego modułu równoważenia obciążenia na nazwę docelowego zewnętrznego modułu równoważenia obciążenia, upewnij się, że nazwa znajduje się w cudzysłowie:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Aby edytować wartość docelowego publicznego adresu IP, który został przeniesiony powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować i wkleić go do ** \<pliku>.json.**  Aby uzyskać identyfikator, użyj [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Wpisz zmienną i naciśnij enter, aby wyświetlić identyfikator zasobu.  Wyróżnij ścieżkę identyfikatora i skopiuj ją do schowka:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  W pliku ** \<>.json name resource-group** wklej **identyfikator zasobu** ze zmiennej zamiast **domyślnejWartość** w drugim parametrze dla publicznego identyfikatora zewnętrznego IP, upewnij się, że ścieżka została ujęta w cudzysłów:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Jeśli skonfigurowano wychodzące reguły TRANSLATORa i wychodzące dla modułu równoważenia obciążenia, w tym pliku będzie obecny trzeci wpis dla zewnętrznego identyfikatora wychodzącego publicznego adresu IP.  Powtórz powyższe kroki w **regionie docelowym,** aby uzyskać identyfikator wychodzącego publicznego iP i wkleić ten wpis do ** \<pliku>.json z nazwą grupy zasobów:**

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Aby edytować region docelowy, w którym zostanie przeniesiona konfiguracja zewnętrznego modułu równoważenia obciążenia, zmień właściwość **lokalizacji** w obszarze **zasoby** w pliku ** \<>.json nazwa grupy zasobów:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **Sku** - Można zmienić sku zewnętrznego modułu równoważenia obciążenia w konfiguracji ze standardowego na podstawowy lub podstawowy na standard, zmieniając właściwość **sku** > **name** w pliku ** \<>.json name resource-group:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **Reguły ruchu wychodzącego** — reguły ruchu wychodzącego można dodawać lub usuwać w konfiguracji, edytując właściwość **outboundRules** w pliku ** \<>.json name grupy zasobów:**

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Aby uzyskać więcej informacji na temat reguł ruchu [wychodzącego, zobacz Reguły ruchu wychodzącego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Zapisz ** \<plik>.json nazwa grupy zasobów.**
    
10. Utwórz lub grupę zasobów w regionie docelowym dla docelowego zewnętrznego modułu równoważenia obciążenia, który ma zostać wdrożony przy użyciu [programu New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Istniejąca grupa zasobów z góry może być również ponownie życiówka w ramach tego procesu:
    
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

Po wdrożeniu, jeśli chcesz rozpocząć od nowa lub odrzucić publiczny adres IP i moduł równoważenia obciążenia w miejscu docelowym, usuń grupę zasobów, która została utworzona w miejscu docelowym, a przeniesiony publiczny adres IP i moduł równoważenia obciążenia zostaną usunięte.  Aby usunąć grupę zasobów, użyj [usuń azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy sieciowej, usuń źródłową grupę sieciowej grupy zasobów sieciowych lub zasobów, użyj [usuń-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) lub [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) i [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
