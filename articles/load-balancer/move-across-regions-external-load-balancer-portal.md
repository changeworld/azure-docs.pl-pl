---
title: Przenoszenie zewnętrznego modułu równoważenia obciążenia platformy Azure do innego regionu platformy Azure przy użyciu witryny Azure portal
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść zewnętrznego modułu równoważenia obciążenia z jednego regionu platformy Azure do drugiego przy użyciu witryny Azure portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638531"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Przenoszenie zewnętrznego modułu równoważenia obciążenia do innego regionu przy użyciu witryny Azure portal

Istnieją różne scenariusze, w których chcesz przenieść zewnętrznego modułu równoważenia obciążenia z jednego regionu do drugiego. Na przykład można utworzyć inny zewnętrzny moduł równoważenia obciążenia z tą samą konfiguracją do testowania. Można również przenieść zewnętrznego modułu równoważenia obciążenia do innego regionu w ramach planowania odzyskiwania po awarii.

W sensie dosłownym nie można przenieść zewnętrznego modułu równoważenia obciążenia platformy Azure z jednego regionu do drugiego. Ale można użyć szablonu usługi Azure Resource Manager do eksportowania istniejącej konfiguracji i publicznego adresu IP zewnętrznego modułu równoważenia obciążenia. Następnie można zorganizować zasób w innym regionie, eksportując moduł równoważenia obciążenia i publiczny adres IP do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie. Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Eksportowanie grup zasobów do szablonów](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zewnętrzny moduł równoważenia obciążenia platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić zewnętrznych modułów równoważenia obciążenia platformy Azure między regionami. Musisz skojarzyć nowy moduł równoważenia obciążenia z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację zewnętrznego modułu równoważenia obciążenia i wdrożyć szablon w celu utworzenia zewnętrznego modułu równoważenia obciążenia w innym regionie, należy przypisać rolę Współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń, publiczne adresy IP i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie zewnętrznych modułów równoważenia obciążenia w regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodatku modułów równoważenia obciążenia. Zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe procedury pokazują, jak przygotować zewnętrznego modułu równoważenia obciążenia do przeniesienia przy użyciu szablonu Menedżera zasobów i przenieść konfigurację zewnętrznego modułu równoważenia obciążenia do regionu docelowego przy użyciu witryny Azure portal. Najpierw należy wyeksportować publiczną konfigurację IP zewnętrznego modułu równoważenia obciążenia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Eksportowanie publicznego szablonu ip i wdrażanie publicznego adresu IP z portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.
2. Znajdź grupę zasobów zawierającą źródłowy publiczny adres IP i wybierz ją.
3. Wybierz**szablon Eksportuj** **ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w obszarze **Szablon eksportu**.
5. Wybierz polecenie > **Szablonuj parametry edycji,** aby otworzyć plik parameters.json w edytorze online. **TEMPLATE**
8. Aby edytować parametr publicznej nazwy IP, zmień właściwość **value** w obszarze **parametry** ze źródłowej publicznej nazwy IP na nazwę docelowego publicznego adresu IP. Nazwa należy ująć w cudzysłów.

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

    Wybierz **pozycję Zapisz** w edytorze.

9.  Wybierz**szablon edycji** **szablonu szablonu szablonu,** > aby otworzyć plik template.json w edytorze online.

10. Aby edytować region docelowy, do którego zostanie przeniesiony publiczny adres IP, zmień **właściwość lokalizacji** w obszarze **zasoby:**

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
  
    Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji. Na przykład kod dla centralnych stanów USA to **centralus**.
    
12. Można również zmienić inne parametry w szablonie, jeśli chcesz lub trzeba, w zależności od wymagań:

    * **SKU**. Można zmienić jednostkę SKU publicznego adresu IP w konfiguracji ze standardowego na podstawowy lub podstawowy na standardowy, zmieniając właściwość **name** pod **sku** w pliku template.json:

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

        Aby uzyskać informacje na temat różnic między publicznymi adresami IP podstawowej i standardowej jednostki SKU, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Publicznej metody alokacji adresów IP** i **limitu czasu bezczynnia**. Publicznej metody alokacji adresów IP można zmienić, zmieniając właściwość **publicIPAllocationMethod** z **dynamiczna** na **statyczna** lub **statyczna** na **dynamiczna.** Limit czasu bezczynnego można zmienić, zmieniając właściwość **idleTimeoutInMinutes** na żądaną wartość. Wartość domyślna to **4**.

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

        Aby uzyskać informacje na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Wybierz **pozycję Zapisz** w edytorze online.

14. Wybierz**subskrypcję** **BASICS,** > aby wybrać subskrypcję, w której zostanie wdrożony docelowy publiczny adres IP.

15. Wybierz**grupę zasobów** **BASICS,** > aby wybrać grupę zasobów, w której zostanie wdrożony docelowy publiczny adres IP. Można wybrać **Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowego publicznego adresu IP. Upewnij się, że nazwa nie jest taka sama jak grupa zasobów źródłowych istniejącego źródłowego publicznego adresu IP.

16. Sprawdź, czy**lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożony publiczny adres IP.

17. W obszarze **USTAWIENIA**sprawdź, czy nazwa jest zgodna z nazwą wprowadzona wcześniej w edytorze parametrów.

18. Zaznacz pole wyboru **WARUNKI I POSTANOWIENIA.**

19. Wybierz **opcję Zakup,** aby wdrożyć docelowy publiczny adres IP.

20. Jeśli masz inny publiczny adres IP, który jest używany do wychodzącego translatora adresów sieciowych dla modułu równoważenia obciążenia jest przenoszony, powtórz poprzednie kroki, aby wyeksportować i wdrożyć drugi wychodzący publiczny adres IP do regionu docelowego.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Eksportowanie szablonu zewnętrznego modułu równoważenia obciążenia i wdrażanie modułu równoważenia obciążenia z witryny Azure portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.
2. Znajdź grupę zasobów zawierającą źródłowy zewnętrzny moduł równoważenia obciążenia i wybierz ją.
3. Wybierz**szablon Eksportuj** **ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w obszarze **Szablon eksportu**.
5. Wybierz polecenie > **Szablonuj parametry edycji,** aby otworzyć plik parameters.json w edytorze online. **TEMPLATE**

5. Aby edytować parametr nazwy zewnętrznego modułu równoważenia obciążenia, zmień właściwość **wartości** nazwy źródłowego zewnętrznego modułu równoważenia obciążenia na nazwę docelowego zewnętrznego modułu równoważenia obciążenia. Nazwa należy ująć w cudzysłów.

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

6.  Aby edytować wartość docelowego publicznego adresu IP przeniesionego w poprzednich krokach, należy najpierw uzyskać identyfikator zasobu, a następnie wkleić go do pliku parameters.json. Aby uzyskać identyfikator:

    1. W innym oknie przeglądarki zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.
    2. Znajdź docelową grupę zasobów zawierającą publiczny adres IP przeniesiony w poprzednich krokach. Wybierz ją.
    3. Wybierz **pozycję Właściwości ustawień** > **Properties**.
    4. W bloku po prawej stronie **wyróżnij identyfikator zasobu** i skopiuj go do schowka. Alternatywnie można wybrać **opcję Kopiuj do schowka** po prawej stronie ścieżki **identyfikatora zasobu.**
    5. Wklej identyfikator zasobu do właściwości **value** w edytorze **Edytuj parametry,** który jest otwarty w innym oknie przeglądarki lub na karcie:

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
    6. Wybierz **pozycję Zapisz** w edytorze online.


7.  Jeśli skonfigurowano wychodzące reguły TRANSLATORa i wychodzące dla modułu równoważenia obciążenia, zobaczysz trzeci wpis w tym pliku dla zewnętrznego identyfikatora wychodzącego publicznego adresu IP. Powtórz poprzednie kroki w **regionie docelowym,** aby uzyskać identyfikator wychodzącego publicznego adresu IP. Wklej ten identyfikator do pliku parameters.json:

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

8.  Wybierz**szablon edycji** **szablonu szablonu szablonu,** > aby otworzyć plik template.json w edytorze online.
9.  Aby edytować region docelowy, do którego zostanie przeniesiona zewnętrzna konfiguracja modułu równoważenia obciążenia, zmień właściwość **lokalizacji** w obszarze **zasoby** w pliku template.json:

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

10. Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji. Na przykład kod dla centralnych stanów USA to **centralus**.

11. Można również zmienić inne parametry w szablonie, jeśli chcesz lub trzeba, w zależności od wymagań:

    * **SKU**. Jednostkę SKU zewnętrznego modułu równoważenia obciążenia można zmienić w konfiguracji ze standardowego na podstawowy lub od poziomu podstawowego do standardowego, zmieniając właściwość **name** pod **sku** w pliku template.json:

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
      Aby uzyskać informacje na temat różnic między podstawowymi i standardowymi modułami równoważenia obciążenia jednostki SKU, zobacz [omówienie standardowego modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Reguły równoważenia obciążenia**. Reguły równoważenia obciążenia można dodawać lub usuwać w konfiguracji, dodając lub usuwając wpisy w sekcji **loadBalancingRules** pliku template.json:

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
       Aby uzyskać informacje na temat reguł równoważenia obciążenia, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sondy**. Można dodać lub usunąć sondę dla modułu równoważenia obciążenia w konfiguracji, dodając lub usuwając wpisy w sekcji **sondy** pliku template.json:

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
       Aby uzyskać więcej informacji, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Przychodzące reguły NAT**. Reguły przychodzącego translatora kluczy sieciowych dla modułu równoważenia obciążenia można dodawać lub usuwać, dodając lub usuwając wpisy w sekcji **przychodzące ZasadyNatrules** pliku template.json:

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
        Aby zakończyć dodawanie lub usuwanie przychodzącej reguły TRANSLATORA, reguła musi być obecna lub usuwana jako właściwość **typu** na końcu pliku template.json:

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
        Aby uzyskać informacje na temat przychodzących reguł nat, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Reguły ruchu wychodzącego**. Reguły ruchu wychodzącego można dodawać lub usuwać w konfiguracji, edytując właściwość **outboundRules** w pliku template.json:

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

         Aby uzyskać więcej informacji, zobacz [Reguły wychodzące modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Wybierz **pozycję Zapisz** w edytorze online.

13. Wybierz**opcję Subskrypcja** **PODSTAWOWE,** > aby wybrać subskrypcję, w której zostanie wdrożony docelowy moduł równoważenia obciążenia zewnętrznego.

15. Wybierz**grupę zasobów** **BASICS,** > aby wybrać grupę zasobów, w której zostanie wdrożony docelowy moduł równoważenia obciążenia. Można wybrać **Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowego zewnętrznego modułu równoważenia obciążenia. Można też wybrać istniejącą grupę zasobów utworzoną wcześniej dla publicznego adresu IP. Upewnij się, że nazwa nie jest taka sama jak grupa zasobów źródłowych istniejącego źródłowego zewnętrznego modułu równoważenia obciążenia.

16. Sprawdź, czy**lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożony zewnętrzny moduł równoważenia obciążenia.

17. W obszarze **USTAWIENIA**sprawdź, czy nazwa jest zgodna z nazwą wprowadzona wcześniej w edytorze parametrów. Sprawdź, czy identyfikatory zasobów są wypełniane dla wszystkich publicznych plików IP w konfiguracji.

18. Zaznacz pole wyboru **WARUNKI I POSTANOWIENIA.**

19. Wybierz **opcję Zakup,** aby wdrożyć docelowy publiczny adres IP.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelowy publiczny adres IP i zewnętrzny moduł równoważenia obciążenia, usuń grupę zasobów, która je zawiera. Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu, a następnie wybierz pozycję **Usuń** u góry strony przeglądu.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie publicznego adresu IP i zewnętrznego modułu równoważenia obciążenia, usuń źródłowy publiczny adres IP i zewnętrzny moduł równoważenia obciążenia lub grupę zasobów. Aby to zrobić, wybierz tę grupę zasobów z pulpitu nawigacyjnego w portalu, a następnie wybierz pozycję **Usuń** u góry każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono zewnętrznego modułu równoważenia obciążenia platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe. Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
