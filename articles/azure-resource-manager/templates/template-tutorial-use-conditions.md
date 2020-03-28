---
title: Użyj warunku w szablonach
description: Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków. Pokazuje, jak wdrożyć nowy zasób lub użyć istniejącego zasobu.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f88f141257e8e614f62c7441c313002b5735116d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239190"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Samouczek: Użyj warunku w szablonach ARM

Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków w szablonie usługi Azure Resource Manager (ARM).

W samouczku [Ustawianie kolejności wdrażania zasobów](./template-tutorial-create-templates-with-dependent-resources.md) tworzysz maszynę wirtualną, sieć wirtualną i kilka innych zasobów zależnych, w tym konto magazynu. Zamiast za każdym razem tworzyć nowe konto magazynu, zezwalasz użytkownikom na utworzenie nowego konta magazynu lub użycie istniejącego. Aby osiągnąć ten cel, definiujesz dodatkowy parametr. Jeśli wartość parametru to „new”, jest tworzone nowe konto magazynu. W przeciwnym razie używane jest istniejące konto magazynu o podanej nazwie.

![Diagram warunków użycia szablonu Menedżera zasobów](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Modyfikowanie szablonu
> * Wdrożenie szablonu
> * Oczyszczanie zasobów

Ten samouczek obejmuje tylko podstawowy scenariusz korzystania z warunków. Aby uzyskać więcej informacji, zobacz:

* [Struktura pliku szablonu: Warunek](conditional-resource-deployment.md).
* [Warunkowe wdrożenie zasobu w szablonie ARM](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Funkcja szablonu: Jeśli](./template-functions-logical.md#if).
* [Funkcje porównywania szablonów ARM](./template-functions-comparison.md)

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybki start platformy Azure to repozytorium szablonów ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik**>**otwórz plik**.
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
5. Wybierz **opcję Zapisz plik,**>**Save As** aby zapisać kopię pliku na komputerze lokalnym o nazwie **azuredeploy.json**.

## <a name="modify-the-template"></a>Modyfikowanie szablonu

Wprowadź dwie zmiany do istniejącego szablonu:

* Dodaj parametr nazwy konta magazynu. Użytkownicy mogą określić nazwę nowego lub istniejącego konta magazynu.
* Dodaj nowy parametr o nazwie **newOrExisting**. Wdrożenie używa tego parametru do określenia, czy utworzyć nowe konto magazynu lub użyć istniejącego konta magazynu.

Poniżej przedstawiono procedurę wprowadzania zmian:

1. Otwórz plik **azuredeploy.json** w programie Visual Studio Code.
2. Zastąp trzy **zmienne('storageAccountName')** **parametrami('storageAccountName')** w całym szablonie.
3. Usuń następującą definicję zmiennej:

    ![Diagram warunków użycia szablonu Menedżera zasobów](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

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

    ![Warunek użycia w usłudze Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Dodaj następujący wiersz na początku definicji konta magazynu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Warunek sprawdza wartość parametru o nazwie **newOrExisting**. Jeśli wartość parametru to **new**, przy wdrażaniu tworzone jest konto magazynu.

    Zaktualizowana definicja konta magazynu wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Zaktualizuj właściwość **storageUri** definicji zasobu maszyny wirtualnej o następującą wartość:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ta zmiana jest niezbędna, jeśli używasz istniejącego konta magazynu w innej grupie zasobów.

7. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami w [Deploy szablon,](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) aby otworzyć powłokę chmury i przekazać poprawiony szablon, a następnie uruchom następujący skrypt programu PowerShell, aby wdrożyć szablon.

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

Spróbuj wykonać inne wdrożenie z **newOrExisting** ustawiony na "istniejące" i określić istniejące konto magazynu. Aby wcześniej utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Aby usunąć grupę zasobów, wybierz pozycję **Spróbuj,** aby otworzyć powłokę chmury. Aby wkleić skrypt programu PowerShell, kliknij prawym przyciskiem myszy okienko powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono szablon, który pozwala użytkownikom wybrać między utworzeniem nowego konta magazynu a użyciem istniejącego konta magazynu. Aby dowiedzieć się, jak pobierać wpisy tajne z usługi Azure Key Vault i używać ich jako haseł przy wdrożeniach na podstawie szablonu, zobacz:

> [!div class="nextstepaction"]
> [Integrowanie usługi Key Vault z wdrożeniem z użyciem szablonu](./template-tutorial-use-key-vault.md)
