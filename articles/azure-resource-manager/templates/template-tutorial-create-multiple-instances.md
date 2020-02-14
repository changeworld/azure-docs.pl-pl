---
title: Tworzenie wielu wystąpień zasobu
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager w celu utworzenia wielu wystąpień zasobów platformy Azure.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 91a37178f8dc8ecc3c61ca16f193e2e52c309d46
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209481"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager

Dowiedz się, jak wykonywać iteracje w Twoim szablonie usługi Azure Resource Manager w celu utworzenia wielu wystąpień zasobu platformy Azure. W tym samouczku zmodyfikujesz szablon w celu utworzenia trzech wystąpień konta magazynu.

![Azure Resource Manager tworzy diagram wielu wystąpień](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Visual Studio Code z rozszerzeniem Menedżer zasobów Tools. [Aby utworzyć szablony Azure Resource Manager, zobacz temat używanie Visual Studio Code](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik.
4. W szablonie zdefiniowano zasób „Microsoft.Storage/storageAccounts”. Porównaj szablon z [dokumentacją dotyczącą szablonów](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
5. Wybierz pozycję **File (Plik)** >**Save As (Zapisz jako)** , aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

Istniejący szablon tworzy jedno konto magazynu. Możesz dostosować szablon, aby utworzyć trzy konta magazynu.

Z poziomu programu Visual Studio Code wprowadź następujące cztery zmiany:

![Usługa Azure Resource Manager tworzy wiele wystąpień](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Dodaj element `copy` do definicji zasobu konta magazynu. W elemencie copy określ liczbę iteracji i zmienną dla tej pętli. Wartość licznika musi być dodatnią liczbą całkowitą i nie może przekraczać 800.
2. Funkcja `copyIndex()` zwraca bieżącą iterację w pętli. Jako prefiksu nazwy należy użyć indeksu. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Aby przesunąć wartość indeksu, możesz przekazać wartość do funkcji copyIndex(). Na przykład *copyIndex(1)* .
3. Usuń element **variables**, ponieważ nie jest już używany.
4. Usuń element **outputs**. Nie jest już potrzebny.

Ukończony szablon wygląda następująco:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Aby uzyskać więcej informacji na temat tworzenia wielu wystąpień, zobacz [Wdrażanie wielu wystąpień zasobu lub właściwości w szablonach usługi Azure Resource Manager](./copy-resources.md)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się z sekcją [Wdrażanie szablonu](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) przewodnika Szybki Start programu Visual Studio Code, aby uzyskać procedurę wdrażania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić wszystkie trzy konta magazynu, pomiń parametr --name:

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Porównaj nazwy kont magazynu z definicją nazwy w szablonie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia wielu wystąpień konta magazynu.  W następnym samouczku utworzysz szablon z wieloma zasobami i wieloma typami zasobów. Niektóre zasoby zawierają zasoby zależne.

> [!div class="nextstepaction"]
> [Tworzenie zasobów zależnych](./template-tutorial-create-templates-with-dependent-resources.md)
