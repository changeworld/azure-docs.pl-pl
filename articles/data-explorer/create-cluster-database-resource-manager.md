---
title: Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak utworzyć klaster Eksplorator danych i bazę danych platformy Azure przy użyciu szablonu Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 49c55b580abdaea6c876a0fac4e7dd4e73d496af
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643830"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu szablonu Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon usługi ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. 

W tym artykule opisano tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). W tym artykule przedstawiono sposób definiowania zasobów wdrożonych i sposobu definiowania parametrów, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać informacje na temat tworzenia szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Aby uzyskać składnię i właściwości JSON do użycia w szablonie, zobacz [typy zasobów Microsoft. Kusto](/azure/templates/microsoft.kusto/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Szablon Azure Resource Manager na potrzeby tworzenia klastrów i baz danych

W tym artykule używany jest [istniejący szablon szybkiego startu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
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
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-05-15",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-05-15",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Wdróż szablon i sprawdź wdrożenie szablonu

Szablon Azure Resource Manager można wdrożyć za [pomocą Azure Portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) lub [przy użyciu programu PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Użyj Azure Portal, aby wdrożyć szablon i zweryfikować wdrożenie szablonu

1. Aby utworzyć klaster i bazę danych, użyj następującego przycisku, aby rozpocząć wdrażanie. Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz w nowym oknie**, aby wykonać pozostałe kroki w tym artykule.

    [![Wdrażanie na platformie Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal w celu wypełnienia formularza wdrożenia.

    ![Wdrażanie na platformie Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Można [edytować i wdrażać szablon w Azure Portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) przy użyciu formularza.

1. Wypełnij sekcje **podstawowe** i **Ustawienia** . Wybierz pozycję unikatowy klaster i nazwy baz danych.
Utworzenie klastra usługi Azure Eksplorator danych i bazy danych może potrwać kilka minut.

1. Aby zweryfikować wdrożenie, należy otworzyć grupę zasobów w [Azure Portal](https://portal.azure.com) , aby znaleźć nowy klaster i bazę danych. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Wdróż szablon i sprawdź wdrożenie szablonu przy użyciu programu PowerShell

#### <a name="deploy-the-template-using-powershell"></a>Wdrażanie szablonu przy użyciu programu PowerShell

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do usługi Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
1. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.
Utworzenie klastra usługi Azure Eksplorator danych i bazy danych może potrwać kilka minut.

#### <a name="verify-the-deployment-using-powershell"></a>Weryfikowanie wdrożenia przy użyciu programu PowerShell

Aby zweryfikować wdrożenie, użyj następującego skryptu Azure PowerShell.  Jeśli Cloud Shell nadal jest otwarty, nie musisz kopiować/uruchamiać pierwszego wiersza (odczyt-Host). Aby uzyskać więcej informacji na temat zarządzania zasobami usługi Azure Eksplorator danych w programie PowerShell, Przeczytaj [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Czyszczenie zasobów przy użyciu Azure Portal

Usuń zasoby z Azure Portal, wykonując czynności opisane w sekcji [Oczyszczanie zasobów](create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Oczyszczanie zasobów przy użyciu programu PowerShell

Jeśli Cloud Shell nadal jest otwarty, nie musisz kopiować/uruchamiać pierwszego wiersza (odczyt-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

[Pozyskiwanie danych w klastrze i bazie danych usługi Azure Eksplorator danych](ingest-data-overview.md)
