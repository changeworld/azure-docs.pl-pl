---
title: Szablony usługi Azure Resource Manager dla interfejsu API tabeli usługi Azure Cosmos DB
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i Konfigurowanie interfejsu API tabeli usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968829"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API tabeli usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager

## Tworzenie konta usługi Azure Cosmos i tabeli <a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konta usługi Azure Cosmos dla interfejsu API tabel z jednej tabeli na 400 RU/s przepływności. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Aby wdrożyć szablon usługi Resource Manager przy użyciu programu PowerShell, **kopii** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji programu PowerShell, a nie usługa Azure Cloud shell, musisz [zainstalować](/powershell/azure/install-az-ps) modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana.

### <a name="deploy-via-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Do wdrożenia szablonu usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, **kopiowania** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzonego konta usługi Azure Cosmos po udostępnieniu mu. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji wiersza polecenia platformy Azure zamiast CloudShell, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/) artykułu.

## Aktualizuj przepływność (RU/s) w tabeli <a id="table-ru-update"></a>

Następujący szablon zaktualizuje przepływność tabeli. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Wdrażanie przepływności tabeli za pomocą programu PowerShell

Aby wdrożyć szablon usługi Resource Manager przy użyciu programu PowerShell, **kopii** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Wdrażanie szablonu tabeli za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe zasoby:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów w usłudze Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony usługi Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych problemów z wdrożenia usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)