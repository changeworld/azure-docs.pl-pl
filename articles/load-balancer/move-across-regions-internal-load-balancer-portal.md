---
title: Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu platformy Azure przy użyciu portalu Azure
description: Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure z jednego regionu platformy Azure do innego za pomocą witryny Azure portal
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638812"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Przenoszenie wewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu przy użyciu portalu Azure

Istnieją różne scenariusze, w których chcesz przenieść istniejący wewnętrzny moduł równoważenia obciążenia z jednego regionu do drugiego. Na przykład można utworzyć wewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Można również przenieść wewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenosić wewnętrznych modułów równoważenia obciążenia platformy Azure z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager, aby wyeksportować istniejącą konfigurację i sieć wirtualną wewnętrznego modułu równoważenia obciążenia.  Następnie można zorganizować zasób w innym regionie, eksportując moduł równoważenia obciążenia i sieć wirtualną do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić wewnętrznych modułów równoważenia obciążenia platformy Azure między regionami.  Musisz skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować wewnętrzną konfigurację modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia wewnętrznego modułu równoważenia obciążenia w innym regionie, należy potrzebować roli Współautor sieci lub wyższy.

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, maszyny wirtualne i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie wewnętrznych modułów równoważenia obciążenia w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania modułów równoważenia obciążenia dla tego procesu.  Zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować wewnętrzny moduł równoważenia obciążenia do przeniesienia przy użyciu szablonu Menedżera zasobów i przenieść konfigurację wewnętrznego modułu równoważenia obciążenia do regionu docelowego za pomocą witryny Azure portal.  W ramach tego procesu należy uwzględnić konfigurację sieci wirtualnej modułu wewnętrznego równoważenia obciążenia i wykonać najpierw przed przeniesieniem wewnętrznego modułu równoważenia obciążenia.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Eksportowanie szablonu sieci wirtualnej i wdrażanie z witryny Azure portal

1. Zaloguj się do**grup zasobów** [portalu](https://portal.azure.com) > Azure .
2. Znajdź grupę zasobów zawierającą źródłową sieć wirtualną i kliknij ją.
3. Wybierz**szablon eksportu** **> Ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w bloku **Szablon eksportu.**
5. Kliknij **pozycję SZABLON** > **Edytuj parametry,** aby otworzyć plik **parameters.json** w edytorze online.
6. Aby edytować parametr nazwy sieci wirtualnej, zmień właściwość **value** pod **parametrami:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Zmień wartość nazwy sieci wirtualnej źródła w edytorze na nazwę wybranej dla docelowej sieci wirtualnej. Upewnij się, że nazwa jest ujęta w cudzysłowie.

8. Kliknij **pozycję Zapisz** w edytorze.

9. Kliknij **pozycję Szablon** > **Edytuj szablon,** aby otworzyć plik **template.json** w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiona sieci wirtualnej, zmień właściwość **lokalizacji** w obszarze zasoby:

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

11. Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod dla regionu to nazwa regionu bez spacji, **Central US** = **centralus**.

12. Można również zmienić inne parametry w pliku **template.json,** jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Przestrzeń** adresowa — przestrzeń adresowa sieci wirtualnej można zmienić przed zapisaniem, modyfikując sekcję**addressSpace** **zasobów** > i zmieniając właściwość **addressPrefixes** w pliku **template.json:**

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

    * **Podsieć** — nazwę podsieci i przestrzeń adresową podsieci można zmienić lub dodać, modyfikując sekcję **podsieci** pliku **template.json.** Nazwę podsieci można zmienić, zmieniając właściwość **name.** Przestrzeń adresowa podsieci można zmienić, zmieniając właściwość **addressPrefix** w pliku **template.json:**

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

         W pliku **template.json,** aby zmienić prefiks adresu, musi być edytowany w dwóch miejscach, sekcji wymienionej powyżej i sekcji **typu** wymienionej poniżej.  Zmień **właściwość addressPrefix,** aby dopasować się do powyższej:

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

13. Kliknij **pozycję Zapisz** w edytorze online.

14. Kliknij **przycisk Basics** > **Subscription,** aby wybrać subskrypcję, w której zostanie wdrożona docelowa sieci wirtualnej.

15. Kliknij pozycję Grupa**zasobów** **BASICS,** > aby wybrać grupę zasobów, w której zostanie wdrożona docelowa sieci wirtualne.  Można kliknąć **przycisk Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowej sieci wirtualnej.  Upewnij się, że nazwa nie jest taka sama jak źródłowej grupy zasobów istniejącej sieci wirtualnej.

16. Sprawdź,**czy lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której chcesz wdrożyć wirtualną siecię wirtualną.

17. Sprawdź w obszarze **USTAWIENIA,** czy nazwa jest zgodna z nazwą wprowadzona w edytorze parametrów powyżej.

18. Zaznacz pole w **regulaminie**.

19. Kliknij przycisk **Zakup,** aby wdrożyć docelową sieć wirtualną.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Eksportowanie szablonu wewnętrznego modułu równoważenia obciążenia i wdrażanie z programu Azure PowerShell

1. Zaloguj się do**grup zasobów** [portalu](https://portal.azure.com) > Azure .
2. Znajdź grupę zasobów zawierającą źródłowy wewnętrzny moduł równoważenia obciążenia i kliknij ją.
3. Wybierz**szablon eksportu** **> Ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w bloku **Szablon eksportu.**
5. Kliknij **pozycję SZABLON** > **Edytuj parametry,** aby otworzyć plik **parameters.json** w edytorze online.

6. Aby edytować parametr nazwy wewnętrznego modułu równoważenia obciążenia, zmień **właściwość defaultValue** nazwy źródłowego wewnętrznego modułu równoważenia obciążenia na nazwę docelowego wewnętrznego modułu równoważenia obciążenia, upewnij się, że nazwa znajduje się w cudzysłowie:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Aby edytować wartość docelowej sieci wirtualnej, która została przeniesiona powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować i wkleić go do pliku **parameters.json.** Aby uzyskać identyfikator:

    1. Zaloguj się do**grup zasobów witryny** [Azure portal](https://portal.azure.com) > w innej karcie lub oknie przeglądarki.
    2. Znajdź docelową grupę zasobów zawierającą przeniesioną sieć wirtualną z powyższych kroków i kliknij ją.
    3. Wybierz >**właściwości** **ustawień** > .
    4. W bloku po prawej stronie **wyróżnij identyfikator zasobu** i skopiuj go do schowka.  Alternatywnie można kliknąć przycisk **kopiowania do schowka** po prawej stronie ścieżki **identyfikatora zasobu.**
    5. Wklej identyfikator zasobu do **właściwości defaultValue** do edytora Edytuj **parametry** otwartego w innym oknie lub karcie przeglądarki:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Kliknij **pozycję Zapisz** w edytorze online.

7. Kliknij **pozycję Szablon** > **Edytuj szablon,** aby otworzyć plik **template.json** w edytorze online.
8. Aby edytować region docelowy, w którym zostanie przeniesiona wewnętrzna konfiguracja modułu równoważenia obciążenia, zmień właściwość **lokalizacji** w obszarze **zasoby** w pliku **template.json:**

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

9.  Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod dla regionu to nazwa regionu bez spacji, **Central US** = **centralus**.

10. Można również zmienić inne parametry w szablonie, jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Sku** - Można zmienić sku wewnętrznego modułu równoważenia obciążenia w konfiguracji ze standardowego na podstawowy lub podstawowy na standard, zmieniając właściwość **sku** > **name** w pliku **template.json:**

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

    * **Reguły równoważenia obciążenia** — można dodawać lub usuwać reguły równoważenia obciążenia w konfiguracji, dodając lub usuwając wpisy do sekcji **loadBalancingRules** pliku **template.json:**

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

    * **Sondy** — można dodać lub usunąć sondę dla modułu równoważenia obciążenia w konfiguracji, dodając lub usuwając wpisy do sekcji **sondy** pliku **template.json:**

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

    * **Przychodzące reguły NAT** — można dodawać lub usuwać przychodzące reguły NAT dla modułu równoważenia obciążenia, dodając lub usuwając wpisy do sekcji **przychodzące ZasadyNatrules** pliku **template.json:**

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
        Aby zakończyć dodawanie lub usuwanie przychodzącej reguły TRANSLATORA, reguła musi być obecna lub usuwana jako właściwość **typu** na końcu pliku **template.json:**

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

12. Kliknij **pozycję Zapisz** w edytorze online.

13. Kliknij **przycisk Basics** > **Subscription,** aby wybrać subskrypcję, w której zostanie wdrożony docelowy wewnętrzny moduł równoważenia obciążenia.

15. Kliknij **pozycję BASICS** > **Resource group to** choose the resource group where the target load balance balanceer will be deployed.  Można kliknąć **przycisk Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowego wewnętrznego modułu równoważenia obciążenia lub wybrać istniejącą grupę zasobów utworzoną powyżej dla sieci wirtualnej.  Upewnij się, że nazwa nie jest taka sama jak grupa zasobów źródłowych istniejącego źródłowego modułu równoważenia obciążenia wewnętrznego.

16. Sprawdź,**czy lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której chcesz wdrożyć wewnętrzny moduł równoważenia obciążenia.

17. Sprawdź w obszarze **USTAWIENIA,** czy nazwa jest zgodna z nazwą wprowadzona w edytorze parametrów powyżej.  Sprawdź, czy identyfikatory zasobów są wypełniane dla wszystkich sieci wirtualnych w konfiguracji.

18. Zaznacz pole w **regulaminie**.

19. Kliknij przycisk **Zakup,** aby wdrożyć docelową sieć wirtualną.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelową sieć wirtualną i wewnętrzny moduł równoważenia obciążenia, usuń grupę zasobów zawierającą docelową sieć wirtualną i wewnętrzny moduł równoważenia obciążenia.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** u góry strony przeglądu.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej i wewnętrznego modułu równoważenia obciążenia, usuń źródłową sieć wirtualną i wewnętrzny moduł równoważenia obciążenia lub grupę zasobów. Aby to zrobić, wybierz sieć wirtualną i wewnętrzny moduł równoważenia obciążenia lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz **pozycję Usuń** u góry każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono wewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
