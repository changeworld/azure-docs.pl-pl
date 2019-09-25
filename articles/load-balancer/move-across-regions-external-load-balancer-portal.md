---
title: Przenoszenie zewnętrznych Load Balancer platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal
description: Użyj szablonu Azure Resource Manager, aby przenieść zewnętrzny Load Balancer platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ad7e4c5aaa20722e6158973571fb95eb8d853f4d
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219792"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Przenoszenie zewnętrznych Load Balancer platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść istniejący zewnętrzny moduł równoważenia obciążenia z jednego regionu do innego. Na przykład możesz chcieć utworzyć zewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Możesz również przenieść zewnętrzny moduł równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

Zewnętrznych usług równoważenia obciążenia platformy Azure nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji i publicznego adresu IP zewnętrznego modułu równoważenia obciążenia.  Następnie można przemieścić zasób w innym regionie, eksportując moduł równoważenia obciążenia i publiczny adres IP do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrażając szablony w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić zewnętrznych usług równoważenia obciążenia platformy Azure między regionami.  Należy skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację zewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia zewnętrznego modułu równoważenia obciążenia w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń, publicznych adresów IP i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie zewnętrznych modułów równoważenia obciążenia w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie modułów równoważenia obciążenia dla tego procesu.  Zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować zewnętrzny moduł równoważenia obciążenia na potrzeby przenoszenia przy użyciu szablonu Menedżer zasobów i przenieść konfigurację zewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu Azure Portal.  W ramach tego procesu konfiguracja publicznego adresu IP zewnętrznego modułu równoważenia obciążenia musi być uwzględniona, a przed przeniesieniem zewnętrznego modułu równoważenia obciążenia należy najpierw wykonać te czynności.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>Eksportowanie szablonu publicznego adresu IP i wdrażanie go z portalu

1. Zaloguj się do**grup zasobów** [Azure Portal](https://portal.azure.com) > .
2. Znajdź grupę zasobów zawierającą źródłowy publiczny adres IP, a następnie kliknij ją.
3. Wybierz pozycję > **Ustawienia** > **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon** > **Edytuj parametry** , aby otworzyć plik **Parameters. JSON** w edytorze online.
8. Aby edytować parametr publicznej nazwy IP, Zmień właściwość w polu**wartość** **parametrów** > ze źródłowej publicznej nazwy IP na nazwę docelowego publicznego adresu IP, upewnij się, że nazwa jest cudzysłowem:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Kliknij przycisk **Zapisz** w edytorze.

9.  Kliknij pozycję **szablon** > **Edytuj szablon** , aby otworzyć plik **Template. JSON** w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiony publiczny adres IP, Zmień właściwość **lokalizacji** w obszarze **zasoby**:

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

11. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA** =  **.**

12. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Jednostka SKU** — można zmienić jednostkę SKU publicznego adresu IP w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na standardowa, zmieniając właściwość**Nazwa** **jednostki SKU** > w pliku **Template. JSON** :

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

        Aby uzyskać więcej informacji na temat różnic między podstawowymi a standardowymi adresami IP jednostki SKU, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Metoda alokacji publicznego adresu IP** i **limit czasu bezczynności** — można zmienić obie te opcje w szablonie, zmieniając właściwość **publicIPAllocationMethod** z **dynamicznego** na **static** lub **static** na **dynamiczną.** . Limit czasu bezczynności można zmienić, zmieniając właściwość **idleTimeoutInMinutes** na żądaną wartość.  Wartość domyślna to **4**:

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

        ```

        Aby uzyskać więcej informacji na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Kliknij przycisk **Zapisz** w edytorze online.

14. Kliknij pozycję**subskrypcja** **podstawy** > , aby wybrać subskrypcję, w której zostanie wdrożony docelowy publiczny adres IP.

15. Kliknij pozycję **podstawowe** > **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy publiczny adres IP.  Możesz kliknąć pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego publicznego adresu IP.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego publicznego adresu IP.

16. Sprawdź, czy**Lokalizacja** **podstawy** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożony publiczny adres IP.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelowy publiczny adres IP.
20. Jeśli masz inny publiczny adres IP, który jest używany dla ruchu wychodzącego NAT dla przenoszonego modułu równoważenia obciążenia, powtórz powyższe kroki w celu wyeksportowania i wdrożenia drugiego wychodzącego publicznego adresu IP w regionie docelowym.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>Wyeksportuj szablon zewnętrznego modułu równoważenia obciążenia i Wdróż go z Azure Portal

1. Zaloguj się do**grup zasobów** [Azure Portal](https://portal.azure.com) > .
2. Znajdź grupę zasobów zawierającą źródłowy zewnętrzny moduł równoważenia obciążenia i kliknij ją.
3. Wybierz pozycję > **Ustawienia** > **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon** > **Edytuj parametry** , aby otworzyć plik **Parameters. JSON** w edytorze online.

5. Aby edytować parametr nazwy zewnętrznego modułu równoważenia obciążenia, Zmień **wartość** właściwości Nazwa źródłowego zewnętrznego modułu równoważenia obciążenia na nazwę docelowego zewnętrznego modułu równoważenia obciążenia, a następnie upewnij się, że nazwa jest cudzysłowem:

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Aby edytować wartość docelowego publicznego adresu IP, który został przeniesiony powyżej, należy najpierw uzyskać identyfikator zasobu, a następnie skopiować go i wkleić do pliku **Parameters. JSON** . Aby uzyskać identyfikator:

    1. Zaloguj się do**grup zasobów** [Azure Portal](https://portal.azure.com) > w innej karcie lub oknie przeglądarki.
    2. Znajdź docelową grupę zasobów zawierającą przeniesiony publiczny adres IP z powyższych kroków i kliknij ją.
    3. Wybierz**Właściwości** **ustawień** > >.
    4. W bloku po prawej stronie zaznacz **Identyfikator zasobu** i skopiuj go do Schowka.  Alternatywnie możesz kliknąć przycisk **Kopiuj do schowka** z prawej strony ścieżki **identyfikatora zasobu** .
    5. Wklej identyfikator zasobu do właściwości **wartość** w edytorze **edycji parametrów** otwartym w innym oknie przeglądarki lub karcie:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Kliknij przycisk **Zapisz** w edytorze online.


7.  W przypadku skonfigurowania wychodzącego NAT i reguł ruchu wychodzącego dla modułu równoważenia obciążenia trzeci wpis będzie obecny w tym pliku dla zewnętrznego identyfikatora dla wychodzącego publicznego adresu IP.  Powtórz powyższe kroki w **regionie docelowym** , aby uzyskać identyfikator wychodzącego publicznego adresu IP i wkleić ten wpis do pliku **Parameters. JSON** :

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Kliknij pozycję **szablon** > **Edytuj szablon** , aby otworzyć plik **Template. JSON** w edytorze online.
9.  Aby edytować region docelowy, w którym zostanie przeniesiona konfiguracja zewnętrznego modułu równoważenia obciążenia, Zmień właściwość **Location** w obszarze **zasoby** w pliku **Template. JSON** :

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

10. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA** =  **.**

11. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Jednostka SKU** — można zmienić jednostkę SKU zewnętrznego modułu równoważenia obciążenia w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na standardowa, zmieniając właściwość**Nazwa** **jednostki SKU** > w pliku **Template. JSON** :

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
      Aby uzyskać więcej informacji na temat różnic między usługą równoważenia obciążenia Basic i standardowymi jednostkami SKU, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Reguły równoważenia obciążenia** — w konfiguracji można dodawać lub usuwać reguły równoważenia obciążenia, dodając lub usuwając wpisy do sekcji **loadBalancingRules** w pliku **Template. JSON** :

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
       Aby uzyskać więcej informacji o regułach równoważenia obciążenia, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondy** — można dodać lub usunąć sondę modułu równoważenia obciążenia w konfiguracji przez dodanie lub usunięcie wpisów do sekcji **sondy** w pliku **Template. JSON** :

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
       Aby uzyskać więcej informacji na Azure Load Balancer sond kondycji, zobacz [sondy kondycji Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Reguły NAT dla ruchu przychodzącego** — można dodać lub usunąć reguły NAT dla ruchu przychodzącego dla modułu równoważenia obciążenia przez dodanie lub usunięcie wpisów do sekcji **inboundNatRules** w pliku **Template. JSON** :

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
        Aby ukończyć Dodawanie lub usuwanie reguły NAT dla ruchu przychodzącego, reguła musi być obecna lub usunięta jako właściwość **typu** na końcu pliku **Template. JSON** :

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
        Aby uzyskać więcej informacji na temat reguł NAT dla ruchu przychodzącego, zobacz [co to jest Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Reguły ruchu wychodzącego** — można dodawać lub usuwać reguły wychodzące w konfiguracji, edytując Właściwość **outboundRules** w pliku **Template. JSON** :

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

         Aby uzyskać więcej informacji na temat reguł ruchu wychodzącego, zobacz [Load Balancer reguły wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

12. Kliknij przycisk **Zapisz** w edytorze online.

13. Kliknij pozycję**subskrypcja** **podstawy** > , aby wybrać subskrypcję, w której zostanie wdrożony docelowy zewnętrzny moduł równoważenia obciążenia.

15. Kliknij pozycję **podstawowe** > **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy moduł równoważenia obciążenia.  Możesz kliknąć przycisk **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego zewnętrznego modułu równoważenia obciążenia lub wybrać istniejącą grupę zasobów, która została utworzona powyżej dla publicznego adresu IP.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego źródłowego zewnętrznego modułu równoważenia obciążenia.

16. Sprawdź, czy**Lokalizacja** **podstawy** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożony zewnętrzny moduł równoważenia obciążenia.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.  Sprawdź, czy identyfikatory zasobów są wypełnione dla wszystkich publicznych adresów IP w konfiguracji.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelowy publiczny adres IP.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelowy publiczny adres IP i zewnętrzny moduł równoważenia obciążenia, Usuń grupę zasobów zawierającą docelowy publiczny adres IP i zewnętrzny moduł równoważenia obciążenia.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie publicznego adresu IP i zewnętrznego modułu równoważenia obciążenia, Usuń źródłowy publiczny adres IP i zewnętrzną usługę równoważenia obciążenia lub grupę zasobów. Aby to zrobić, wybierz publiczny adres IP i zewnętrzną usługę równoważenia obciążenia lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono zewnętrzny moduł równoważenia obciążenia platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
