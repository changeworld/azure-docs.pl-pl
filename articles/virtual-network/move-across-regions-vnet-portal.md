---
title: Przenieś Virtual Network platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal.
description: Użyj szablonu Azure Resource Manager, aby przenieść Virtual Network platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 2a1ee358a6c97b721ec6f0da3eb70269239b0737
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077674"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Przenoszenie Virtual Network platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść istniejące sieci wirtualne platformy Azure (sieci wirtualnych) z jednego regionu do innego. Można na przykład utworzyć sieć wirtualną o tej samej konfiguracji na potrzeby testowania i dostępności istniejącej sieci wirtualnej. Możesz również przenieść produkcyjną sieć wirtualną do innego regionu w ramach planowania odzyskiwania po awarii.

Możesz użyć szablonu Azure Resource Manager, aby zakończyć przenoszenie sieci wirtualnej do innego regionu. W tym celu należy wyeksportować sieć wirtualną do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, [zobacz Szybki Start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że Virtual Network platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Aby wyeksportować sieć wirtualną i wdrożyć szablon w celu utworzenia sieci wirtualnej w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Komunikacja równorzędna sieci wirtualnych nie zostanie ponownie utworzona i zakończy się niepowodzeniem, jeśli nadal znajdują się one w szablonie.  Przed wyeksportowaniem szablonu należy usunąć wszystkie elementy równorzędne sieci wirtualnej, a następnie ponownie ustanowić elementy równorzędne po przeniesieniu sieci wirtualnej.
    
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i publicznych adresów IP.

- Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie sieci wirtualnych w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie sieci wirtualnych dla tego procesu.  Zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki przedstawiają sposób przygotowania sieci wirtualnej do przenoszenia przy użyciu szablonu Menedżer zasobów i przenoszenia sieci wirtualnej do regionu docelowego przy użyciu portalu.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Eksportowanie szablonu i wdrażanie go z portalu

1. Zaloguj się do**grup zasobów** [Azure Portal](http://portal.azure.com) > .
2. Znajdź grupę zasobów zawierającą źródłową sieć wirtualną i kliknij ją.
3. Wybierz pozycję > **Ustawienia** > **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon** > **Edytuj parametry** , aby otworzyć plik **Parameters. JSON** w edytorze online.
6. Aby edytować parametr nazwy sieci wirtualnej, Zmień właściwość **Value** w obszarze **Parametry**:
    
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
7. Zmień wartość Nazwa źródłowej sieci wirtualnej w edytorze na nazwę wybraną dla docelowej platformy wirtualnej. Upewnij się, że nazwa została ujęta w cudzysłów.

8.  Kliknij przycisk **Zapisz** w edytorze.

9.  Kliknij pozycję **szablon** > **Edytuj szablon** , aby otworzyć plik **Template. JSON** w edytorze online. 

10. Aby edytować region docelowy, w którym zostanie przeniesiona Sieć wirtualna, Zmień właściwość **Location** w obszarze **zasoby** w edytorze online:

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
 
11. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA** =  **.**
 
12. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:
    
    * **Przestrzeń adresowa** — przed zapisaniem można zmienić przestrzeń adresową sieci wirtualnej, modyfikując sekcję**addressSpace** **zasobów** > i zmieniając właściwość **addressPrefixes** w pliku **Template. JSON** :

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

    * **Podsieć** — nazwę podsieci i przestrzeń adresową podsieci można zmienić lub dodać do niej, modyfikując sekcję **podsieci** pliku **Template. JSON** . Nazwę podsieci można zmienić, zmieniając właściwość **name** . Przestrzeń adresową podsieci można zmienić, zmieniając właściwość **addressPrefix** w pliku **Template. JSON** :

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

         W pliku **Template. JSON** zmiana prefiksu adresu musi być edytowana w dwóch miejscach, w sekcji wymienionej powyżej i w sekcji **Type** wymienionej poniżej.  Zmień właściwość **addressPrefix** tak, aby pasowała do powyższego poziomu:

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

13. Kliknij przycisk **Zapisz** w edytorze online.

14. Kliknij pozycję**subskrypcja** **podstawy** > , aby wybrać subskrypcję, w której zostanie wdrożona docelowa sieć wirtualna.

15. Kliknij pozycję **podstawowe** > **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożona docelowa sieć wirtualna.  Możesz kliknąć przycisk **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowej sieci wirtualnej.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącej sieci wirtualnej. 

16. Sprawdź, czy**Lokalizacja** **podstawy** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożona Sieć wirtualna.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelową sieć wirtualną.

## <a name="discard"></a>Odrzuć 

Jeśli chcesz odrzucić docelową sieć wirtualną, Usuń grupę zasobów, która zawiera docelową sieć wirtualną.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, Usuń źródłową sieć wirtualną lub grupę zasobów. Aby to zrobić, wybierz sieć wirtualną lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono Virtual Network platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
