---
title: Tworzenie połączonych szablonów usługi Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak tworzyć połączone szablony usługi Azure Resource Manager na potrzeby tworzenia maszyny wirtualnej
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dfdad89d628fda476ecef1c43246ce3927927555
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863503"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Samouczek: tworzenie połączonych szablonów usługi Azure Resource Manager

Dowiedz się, jak tworzyć połączone szablony usługi Azure Resource Manager. Użycie połączonych szablonów umożliwia wywołanie szablonu przez inny szablon. Jest to idealne narzędzie do podziału szablonów na moduły. W tym samouczku użyjesz tego samego szablonu, który był używany w sekcji [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md) i który tworzy maszynę wirtualną, sieć wirtualną oraz inne zasoby zależne, takie jak konto magazynu. Zasób konta magazynu jest wydzielany do połączonego szablonu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Tworzenie połączonego szablonu
> * Przekazywanie połączonego szablonu
> * Łączenie z połączonym szablonem
> * Konfigurowanie zależności
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). To jest ten sam szablon, który był używany w sekcji [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Zapisz dwie kopie tego samego szablonu, który ma być używany w następujących celach:

* **Główny szablon**: tworzenie wszystkich zasobów, z wyjątkiem konta magazynu.
* **Połączony szablon**: tworzenie konta magazynu.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Istnieje pięć zasobów definiowanych przez szablon:

    * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). 
    * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses). 
    * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks). 
    * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces). 
    * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
5. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.
6. Wybierz pozycję **Plik**>**Zapisz jako**, aby utworzyć kolejną kopię pliku o nazwie **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Tworzenie połączonego szablonu

Połączony szablon tworzy konto magazynu. Połączony szablon jest niemal taki sam, jak szablon autonomiczny, który tworzy konto magazynu. W tym samouczku połączony szablon musi przekazać wartość z powrotem do głównego szablonu. Ta wartość jest definiowana w elemencie `outputs`.

1. Otwórz plik linkedTemplate.json w programie Visual Studio Code, jeśli nie jest jeszcze otwarty.
2. Wprowadź następujące zmiany:

    * Usuń wszystkie zasoby z wyjątkiem konta magazynu. Łącznie należy usunąć cztery zasoby.
    * Zaktualizuj element **outputs**, aby wyglądał następująco:

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        Element **storageUri** jest wymagany przez definicję zasobu maszyny wirtualnej w głównym szablonie.  Wartość jest przekazywana z powrotem do głównego szablonu jako wartość wyjściowa.
    * Usuń parametry, które nigdy nie są używane. Parametry te są podkreślone zieloną linią falistą. Powinien zostać tylko jeden parametr o nazwie **location**.
    * Usuń element **variables**. Nie jest on potrzebny w tym samouczku.
    * Dodaj parametr o nazwie **storageAccountName**. Nazwa konta magazynu jest przekazywana z głównego szablonu do połączonego szablonu jako parametr.

    Gdy wszystko będzie gotowe, szablon powinien wyglądać następująco:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Zapisz zmiany.

## <a name="upload-the-linked-template"></a>Przekazywanie połączonego szablonu

Szablony muszą być dostępne z miejsca, gdzie jest uruchamiane wdrożenie. Tą lokalizacją może być konto magazynu platformy Azure albo usługa GitHub lub Dropbox. Jeśli Twoje szablony zawierają poufne informacje, upewnij się, że dostęp do nich jest chroniony. W tym samouczku używasz metody wdrażania za pomocą usługi Cloud Shell, która była używana w sekcji [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Główny szablon (azuredeploy.json) jest przekazywany do powłoki. Połączony szablon (linkedTemplate.json) musi być gdzieś udostępniony.  Aby zmniejszyć liczbę zadań w tym samouczku, połączony szablon zdefiniowany w poprzedniej sekcji został przekazany do [konta magazynu platformy Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Wywoływanie połączonego szablonu

Główny szablonu ma nazwę azuredeploy.json.

1. Otwórz plik azuredeploy.json w programie Visual Studio Code, jeśli nie jest otwarty.
2. Usuń z szablonu definicję zasobu konta magazynu.
3. Dodaj poniższy fragment kodu json w miejscu, gdzie była definicja konta magazynu:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Zwróć uwagę na następujące szczegóły:

    * Zasób `Microsoft.Resources/deployments` w głównym szablonie służy do łączenia się z innym szablonem.
    * Zasób `deployments` ma nazwę `linkedTemplate`. Ta nazwa jest używana do [konfigurowania zależności](#configure-dependency).  
    * W przypadku wywoływania połączonych szablonów możesz używać tylko trybu wdrożenia [Incremental](./deployment-modes.md).
    * Parametr `templateLink/uri` zawiera identyfikator URI połączonego szablonu. Połączony szablon został przekazany do udostępnionego konta magazynu. Jeśli przekażesz szablon do innej lokalizacji w Internecie, możesz zaktualizować identyfikator URI.
    * Za pomocą właściwości `parameters` możesz przekazywać wartości z głównego szablonu do połączonego szablonu.
4. Zapisz zmiany.

## <a name="configure-dependency"></a>Konfigurowanie zależności

Jak omówiono w artykule [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonu usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), zasób maszyny wirtualnej zależy od konta magazynu:

![Diagram zależności szablonów usługi Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ponieważ konto magazynu jest teraz zdefiniowane w połączonym szablonie, musisz zaktualizować następujące dwa elementy zasobu `Microsoft.Compute/virtualMachines`.

* Skonfiguruj ponownie element `dependOn`. Definicja konta magazynu została przeniesiona do połączonego szablonu.
* Skonfiguruj ponownie element `properties/diagnosticsProfile/bootDiagnostics/storageUri`. W sekcji [Tworzenie połączonego szablonu](#create-the-linked-template) została dodana wartość wyjściowa:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Ta wartość jest wymagana przez główny szablon.

1. Otwórz plik azuredeploy.json w programie Visual Studio Code, jeśli nie jest otwarty.
2. Rozwiń definicję zasobu maszyny wirtualnej i zaktualizuj element **dependsOn** w sposób pokazany na poniższym zrzucie ekranu:

    ![Konfigurowanie zależności połączonych szablonów usługi Azure Resource Manager ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* to nazwa zasobu wdrożeń.  
3. Zaktualizuj wartość **properties/diagnosticsProfile/bootDiagnostics/storageUri** w sposób pokazany na poprzednim zrzucie ekranu.

Aby uzyskać więcej informacji, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i wdrożono dołączony szablon. Aby dowiedzieć się, jak wykonać zadania po wdrożeniu przy użyciu rozszerzenia maszyny wirtualnej, zobacz

> [!div class="nextstepaction"]
> [Wdrażanie rozszerzeń maszyny wirtualnej](./deployment-manager-tutorial.md)
