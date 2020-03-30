---
title: Przenoszenie sieciowej grupy zabezpieczeń platformy Azure do innego regionu platformy Azure przy użyciu witryny Azure portal
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść grupę zabezpieczeń sieci platformy Azure z jednego regionu platformy Azure do innego przy użyciu witryny Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647190"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Przenoszenie sieciowej grupy zabezpieczeń platformy Azure do innego regionu przy użyciu witryny Azure portal

Istnieją różne scenariusze, w których chcesz przenieść istniejące cele zabezpieczeń z jednego regionu do drugiego. Na przykład można utworzyć sieciowej grupy zabezpieczeń z tej samej konfiguracji i reguł zabezpieczeń do testowania. Można również przenieść nsg do innego regionu w ramach planowania odzyskiwania po awarii.

Nie można przenosić grup zabezpieczeń platformy Azure z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager do wyeksportowania istniejących reguł konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń.  Następnie można zorganizować zasób w innym regionie, eksportując grupę zabezpieczeń do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że grupa zabezpieczeń sieci platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić grup zabezpieczeń sieci platformy Azure między regionami.  Musisz skojarzyć nową szkoliwę grupy ndsg z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację sieciowej grupy zabezpieczeń i wdrożyć szablon w celu utworzenia sieciowej grupy zabezpieczeń w innym regionie, musisz mieć rolę współautora sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, publiczne adresy IP i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie grup zabezpieczeń w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodatku nsgs dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować grupę zabezpieczeń sieci do przenoszenia reguły konfiguracji i zabezpieczeń przy użyciu szablonu Menedżera zasobów i przenieść reguły konfiguracji sieciowej grupy zabezpieczeń i zabezpieczeń do regionu docelowego za pomocą portalu.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Eksportowanie szablonu i wdrażanie z portalu

1. Zaloguj się do**grup zasobów** [portalu](https://portal.azure.com) > Azure .
2. Znajdź grupę zasobów zawierającą źródłową grupę NSG i kliknij ją.
3. Wybierz**szablon eksportu** **> Ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w bloku **Szablon eksportu.**
5. Kliknij **pozycję SZABLON** > **Edytuj parametry,** aby otworzyć plik **parameters.json** w edytorze online.
6. Aby edytować parametr nazwy nsg, zmień właściwość **value** w obszarze **parametry:**

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Zmień wartość źródłowej grupy NSG w edytorze na wybraną nazwę dla docelowej grupy nsg. Upewnij się, że nazwa jest ujęta w cudzysłowie.

8.  Kliknij **pozycję Zapisz** w edytorze.

9.  Kliknij **pozycję Szablon** > **Edytuj szablon,** aby otworzyć plik **template.json** w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiona konfiguracja sieciowej grupy zabezpieczeń i reguły zabezpieczeń, zmień właściwość **lokalizacji** w obszarze **zasoby** w edytorze online:

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
           ]

    ```

11. Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod dla regionu to nazwa regionu bez spacji, **Central US** = **centralus**.

12. Można również zmienić inne parametry w szablonie, jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Reguły zabezpieczeń** — można edytować reguły wdrożone w docelowej sieciowej grupy zabezpieczeń, dodając lub usuwając reguły do sekcji **securityRules** w pliku **template.json:**

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
                    },
                ]
            }
        ```

      Aby zakończyć dodawanie lub usuwanie reguł w docelowej sieciowej grupy wlekajsz, należy również edytować typy reguł niestandardowych na końcu pliku **template.json** w formacie poniższego przykładu:

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

13. Kliknij **pozycję Zapisz** w edytorze online.

14. Kliknij **przycisk Basics** > **Subscription,** aby wybrać subskrypcję, w której zostanie wdrożona docelowa grupa nsg.

15. Kliknij pozycję Grupa**zasobów** **BASICS,** > aby wybrać grupę zasobów, w której zostanie wdrożona docelowa grupa zasobów płciowych.  Możesz kliknąć **przycisk Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowej grupy zasobów grupy roboczej.  Upewnij się, że nazwa nie jest taka sama jak grupa zasobów źródłowych istniejącej grupy zasobów nienasytekowych.

16. Sprawdź, czy**lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której chcesz wdrożyć pragę zunifikowanie.

17. Sprawdź w obszarze **USTAWIENIA,** czy nazwa jest zgodna z nazwą wprowadzona w edytorze parametrów powyżej.

18. Zaznacz pole w **regulaminie**.

19. Kliknij przycisk **Zakup,** aby wdrożyć docelową grupę zabezpieczeń sieci.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelową grupę ndsg, usuń grupę zasobów, która zawiera docelową grupę nsg.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** u góry strony przeglądu.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie grupy roboczej, usuń źródłową grupę nsg lub grupę zasobów. Aby to zrobić, wybierz grupę zabezpieczeń sieci lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz **pozycję Usuń** u góry każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
