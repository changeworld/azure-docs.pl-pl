---
title: Przenoszenie sieci wirtualnej platformy Azure do innego regionu platformy Azure przy użyciu witryny Azure portal.
description: Przenoszenie sieci wirtualnej platformy Azure z jednego regionu platformy Azure do innego przy użyciu szablonu Usługi Resource Manager i witryny Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640792"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Przenoszenie sieci wirtualnej platformy Azure do innego regionu przy użyciu portalu Azure

Istnieją różne scenariusze przenoszenia istniejącej sieci wirtualnej platformy Azure z jednego regionu do drugiego. Na przykład można utworzyć sieć wirtualną z taką samą konfiguracją do testowania i dostępności jak istniejąca sieć wirtualna. Lub można przenieść produkcyjną sieć wirtualną do innego regionu w ramach planowania odzyskiwania po awarii.

Za pomocą szablonu usługi Azure Resource Manager można wykonać przeniesienie sieci wirtualnej do innego regionu. Można to zrobić, eksportując sieć wirtualną do szablonu, modyfikując parametry, aby dopasować go do regionu docelowego, a następnie wdrażając szablon w nowym regionie. Aby uzyskać więcej informacji na temat szablonów Usługi Resource Manager, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że sieć wirtualna znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Aby wyeksportować sieć wirtualną i wdrożyć szablon w celu utworzenia sieci wirtualnej w innym regionie, musisz mieć rolę współautora sieci lub wyższą.

- Komunikacja równorzędna sieci wirtualnej nie zostanie ponownie utworzona i zakończy się niepowodzeniem, jeśli nadal są obecne w szablonie. Przed wyeksportowaniem szablonu należy usunąć wszystkie elementy równorzędne sieci wirtualnej. Następnie można przywrócić je po przeniesieniu sieci wirtualnej.

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i publiczne pliki IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieci wirtualnych w regionie docelowym. Aby włączyć wymagany przydział, skontaktuj się z pomocą techniczną.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania sieci wirtualnych dla tego procesu. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Przygotuj się do przeprowadzki
W tej sekcji należy przygotować sieć wirtualną do przeniesienia przy użyciu szablonu Menedżera zasobów. Następnie przenieść sieć wirtualną do regionu docelowego przy użyciu witryny Azure portal.

Aby wyeksportować sieć wirtualną i wdrożyć docelową sieć wirtualną przy użyciu witryny Azure portal, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz pozycję **Grupy zasobów**.
1. Znajdź grupę zasobów zawierającą źródłową sieć wirtualną, a następnie wybierz ją.
1. Wybierz**szablon Eksportuj** **ustawienia** > .
1. W okienku **Szablon eksportu** wybierz pozycję **Wdrażanie**.
1. Aby otworzyć plik *parameters.json* w edytorze online, wybierz polecenie > **Parametry edycji** **szablonu**.
1. Aby edytować parametr nazwy sieci wirtualnej, zmień właściwość **value** pod **parametrami:**

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

1. W edytorze zmień wartość nazwy sieci wirtualnej źródła w edytorze na nazwę, która ma być kierować na docelową sieć wirtualną. Należy ująć nazwę w cudzysłów.

1. Wybierz **pozycję Zapisz** w edytorze.

1. Aby otworzyć plik *template.json* w edytorze online, wybierz szablon**Edycji** **szablonu szablonu** > .

1. W edytorze online, aby edytować region docelowy, w którym sieć wirtualna zostanie przeniesiona, zmień **właściwość lokalizacji** w obszarze **zasoby:**

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

1. Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod dla regionu jest nazwą regionu, bez spacji (na przykład **Centralus** = **Centralus).**

1. (Opcjonalnie) Można również zmienić inne parametry w szablonie, w zależności od wymagań:

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

    * **Podsieć**: Można zmienić lub dodać do nazwy podsieci i przestrzeni adresowej podsieci, zmieniając sekcję **podsieci** szablonu. Nazwę podsieci można zmienić, zmieniając właściwość **name.** I można zmienić przestrzeń adresową podsieci, zmieniając **właściwość addressPrefix:**

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

        Aby zmienić prefiks adresu w pliku *template.json,* edytuj go w dwóch miejscach: w kodzie w poprzedniej sekcji i w sekcji **typu** następującego kodu. Zmień **właściwość addressPrefix** w poniższym kodzie, aby dopasować właściwość **addressPrefix** w kodzie w poprzedniej sekcji.

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

1. W edytorze online wybierz pozycję **Zapisz**.

1. Aby wybrać subskrypcję, w której zostanie wdrożona docelowa sieć wirtualna, wybierz opcję**Subskrypcja** **podstawowe** > .

1. Aby wybrać grupę zasobów, w której zostanie wdrożona docelowa sieć wirtualna, wybierz**grupę Zasobów** **podstawowe** > . 

    Jeśli chcesz utworzyć nową grupę zasobów dla docelowej sieci wirtualnej, wybierz pozycję **Utwórz nowy**. Upewnij się, że nazwa nie jest taka sama jak nazwa źródłowej grupy zasobów w istniejącej sieci wirtualnej.

1. Sprawdź, czy**lokalizacja** **podstawy** > jest ustawiona na lokalizację docelową, w której ma zostać wdrożona sieć wirtualna.

1. W obszarze **Ustawienia**sprawdź, czy nazwa jest zgodna z wcześniej wprowadzona nazwa w edytorze parametrów.

1. Zaznacz pole wyboru **Warunki i postanowienia.**

1. Aby wdrożyć docelową sieć wirtualną, wybierz pozycję **Zakup**.

## <a name="delete-the-target-virtual-network"></a>Usuwanie docelowej sieci wirtualnej

Aby odrzucić docelową sieć wirtualną, należy usunąć grupę zasobów zawierającą docelową sieć wirtualną. W tym celu:
1. Na pulpicie nawigacyjnym portalu azure wybierz grupę zasobów.
1. U góry okienka **Przegląd** wybierz pozycję **Usuń**.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, należy usunąć źródłową sieć wirtualną lub grupę zasobów. W tym celu:
1. Na pulpicie nawigacyjnym portalu Azure wybierz sieć wirtualną lub grupę zasobów.
1. U góry każdego okienka wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono sieć wirtualną platformy Azure z jednego regionu do drugiego przy użyciu witryny Azure portal, a następnie oczyściłeś niepotrzebne zasoby źródłowe. Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
