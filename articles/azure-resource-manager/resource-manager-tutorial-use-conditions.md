---
title: Używanie warunków w szablonach usługi Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6a03707246f27bcba9cc46168ec04893b7bbc4c3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990800"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Samouczek: Używanie warunków w szablonach usługi Azure Resource Manager

Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków.

W samouczku [Ustawianie kolejności wdrażania zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md) tworzysz maszynę wirtualną, sieć wirtualną i kilka innych zasobów zależnych, w tym konto magazynu. Zamiast za każdym razem tworzyć nowe konto magazynu, zezwalasz użytkownikom na utworzenie nowego konta magazynu lub użycie istniejącego. Aby osiągnąć ten cel, definiujesz dodatkowy parametr. Jeśli wartość parametru to „new”, jest tworzone nowe konto magazynu. W przeciwnym razie służy istniejące konto magazynu o podanej nazwie.

![Diagram warunek użycia szablonu usługi Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Modyfikowanie szablonu
> * Wdrożenie szablonu
> * Oczyszczanie zasobów

Ten samouczek obejmuje tylko podstawowy scenariusz użycia warunków. Aby uzyskać więcej informacji, zobacz:

* [Struktura pliku szablonu: Warunek](./resource-group-authoring-templates.md#condition).
* [Warunkowe wdrażanie zasobu w szablonie usługi Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/conditional-deploy.md).
* [Funkcja szablonu: Jeśli](./resource-group-template-functions-logical.md#if).
* [Funkcje porównania dla szablonów usługi Azure Resource Manager](./resource-group-template-functions-comparison.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

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

## <a name="modify-the-template"></a>Modyfikowanie szablonu

Wprowadź dwie zmiany do istniejącego szablonu:

* Dodaj parametr nazwy konta magazynu. Użytkownicy mogą określić nazwę nowego lub istniejącego konta magazynu.
* Dodaj nowy parametr o nazwie **newOrExisting**. Ten parametr jest używany przy wdrożeniu do określenia, czy utworzyć nowe konto magazynu, czy użyć istniejącego.

Poniżej przedstawiono procedurę wprowadzania zmian:

1. Otwórz plik **azuredeploy.json** w programie Visual Studio Code.
2. Zastąp trzy **variables('storageAccountName')** z **parameters('storageAccountName')** całego szablonu.
3. Usuń następującą definicję zmiennej:

    ![Diagram warunek użycia szablonu usługi Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Dodaj następujące dwa parametry do szablonu:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Zaktualizowana definicja parametrów wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Dodaj następujący wiersz na początku definicji konta magazynu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Warunek sprawdza wartość parametru o nazwie **newOrExisting**. Jeśli wartość parametru to **new**, przy wdrażaniu tworzone jest konto magazynu.

    Zaktualizowana definicja konta magazynu wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Aktualizacja **storageUri** właściwości definicji zasobu maszyny wirtualnej przy użyciu następujących wartości:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ta zmiana jest niezbędna, jeśli używasz istniejącego konta magazynu w innej grupie zasobów.

7. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami w [wdrożenia szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) Otwórz usługę Cloud shell Przekaż szablon i następnie uruchom skrypt programu PowerShell, aby wdrożyć szablon postępuj zgodnie z.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> Wdrożenie zakończy się niepowodzeniem, jeśli parametr **newOrExisting** będzie mieć wartość **new**, ale konto magazynu o podanej nazwie będzie już istnieć.

Spróbuj przeprowadzić kolejne wdrożenie, tym razem z parametrem **newOrExisting** ustawionym na wartość „existing”, i podaj nazwę istniejącego konta magazynu. Aby wcześniej utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Aby usunąć grupę zasobów, wybierz **wypróbuj** do Otwórz usługę Cloud shell. Wklej skrypt programu PowerShell, kliknij prawym przyciskiem myszy w okienku shell, a następnie wybierz **Wklej**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzono szablon, który pozwala użytkownikom wybrać między utworzeniem nowego konta magazynu a użyciem istniejącego konta magazynu. Aby dowiedzieć się, jak pobierać wpisy tajne z usługi Azure Key Vault i używać ich jako haseł przy wdrożeniach na podstawie szablonu, zobacz:

> [!div class="nextstepaction"]
> [Integrowanie usługi Key Vault z wdrożeniem z użyciem szablonu](./resource-manager-tutorial-use-key-vault.md)
