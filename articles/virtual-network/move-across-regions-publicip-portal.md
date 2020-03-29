---
title: Przenoszenie publicznego adresu IP platformy Azure do innego regionu platformy Azure przy użyciu portalu Azure
description: Użyj szablonu usługi Azure Resource Manager, aby przenieść publiczny adres IP platformy Azure z jednego regionu platformy Azure do drugiego przy użyciu witryny Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641405"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Przenoszenie publicznego adresu IP platformy Azure do innego regionu przy użyciu witryny Azure portal

Istnieją różne scenariusze, w których chcesz przenieść istniejące publiczne usługi Azure IP z jednego regionu do drugiego. Na przykład można utworzyć publiczny adres IP z tą samą konfiguracją i sku do testowania. Można również przenieść publiczny adres IP do innego regionu w ramach planowania odzyskiwania po awarii.

Publiczne usługi IP platformy Azure są specyficzne dla regionu i nie można ich przenosić z jednego regionu do drugiego. Można jednak użyć szablonu usługi Azure Resource Manager do wyeksportowania istniejącej konfiguracji publicznego adresu IP.  Następnie można zorganizować zasób w innym regionie, eksportując publiczny adres IP do szablonu, modyfikując parametry zgodne z regionem docelowym, a następnie wdrażając szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżera zasobów i szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że publiczny adres IP platformy Azure znajduje się w regionie platformy Azure, z którego chcesz przenieść.

- Nie można przenosić publicznych obiektów IP platformy Azure między regionami.  Musisz skojarzyć nowe adresy ip publiczne z zasobami w regionie docelowym.

- Aby wyeksportować publiczną konfigurację adresu IP i wdrożyć szablon w celu utworzenia publicznego adresu IP w innym regionie, musisz mieć rolę Współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie zasoby, których aktualnie używasz. Ten układ obejmuje między innymi moduły równoważenia obciążenia, sieciowe grupy zabezpieczeń i sieci wirtualne.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie publicznych witryn IP w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że subskrypcja ma wystarczającą ilość zasobów do obsługi dodawania publicznych usług IP dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie
Poniższe kroki pokazują, jak przygotować publiczny adres IP do przeniesienia konfiguracji przy użyciu szablonu Menedżera zasobów i przenieść publiczną konfigurację IP do regionu docelowego przy użyciu witryny Azure portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie ze skryptu

1. Zaloguj się do**grup zasobów** [portalu](https://portal.azure.com) > Azure .
2. Znajdź grupę zasobów zawierającą publiczny adres IP źródła i kliknij ją.
3. Wybierz**szablon eksportu** **> Ustawienia** > .
4. Wybierz **pozycję Wdrażanie** w bloku **Szablon eksportu.**
5. Kliknij **pozycję SZABLON** > **Edytuj parametry,** aby otworzyć plik **parameters.json** w edytorze online.
8. Aby edytować parametr publicznej nazwy IP, zmień właściwość w**obszarze wartości** **parametrów** > ze źródłowej publicznej nazwy IP na nazwę docelowego publicznego adresu IP, upewnij się, że nazwa znajduje się w cudzysłowie:

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
8.  Kliknij **pozycję Zapisz** w edytorze.

9.  Kliknij **pozycję Szablon** > **Edytuj szablon,** aby otworzyć plik **template.json** w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiony publiczny adres IP, zmień **właściwość lokalizacji** w obszarze **zasoby:**

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

11. Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod dla regionu to nazwa regionu bez spacji, **Central US** = **centralus**.

12. Można również zmienić inne parametry w szablonie, jeśli wybierzesz i są opcjonalne w zależności od wymagań:

    * **Sku** - Możesz zmienić sku publicznego IP w konfiguracji ze standardowej na podstawową lub podstawową na standardową, zmieniając właściwość **sku** > **name** w pliku **template.json:**

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

        Aby uzyskać więcej informacji na temat różnic między podstawowymi i standardowymi identyfikatorami publicznymi sku, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP:](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

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

        ```

        Aby uzyskać więcej informacji na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Kliknij **pozycję Zapisz** w edytorze online.

14. Kliknij **przycisk Basics** > **Subscription,** aby wybrać subskrypcję, w której zostanie wdrożony docelowy publiczny adres IP.

15. Kliknij **pozycję BASICS** > **Resource group to** choose the resource group where the target public IP will be deployed.  Można kliknąć przycisk **Utwórz nowy,** aby utworzyć nową grupę zasobów dla docelowego publicznego adresu IP.  Upewnij się, że nazwa nie jest taka sama jak grupa zasobów źródłowych istniejącego źródłowego publicznego adresu IP.

16. Sprawdź, czy**lokalizacja** **BASICS** > jest ustawiona na lokalizację docelową, w której chcesz wdrożyć publiczny adres IP.

17. Sprawdź w obszarze **USTAWIENIA,** czy nazwa jest zgodna z nazwą wprowadzona w edytorze parametrów powyżej.

18. Zaznacz pole w **regulaminie**.

19. Kliknij przycisk **Zakup,** aby wdrożyć docelowy publiczny adres IP.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelowy publiczny adres IP, usuń grupę zasobów zawierającą docelowy publiczny adres IP.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** u góry strony przeglądu.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie publicznego adresu IP, usuń źródłowy publiczny adres IP lub grupę zasobów. Aby to zrobić, wybierz publiczny adres IP lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz **pozycję Usuń** u góry każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono publiczny adres IP platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
