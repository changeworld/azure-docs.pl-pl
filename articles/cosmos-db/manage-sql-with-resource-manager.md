---
title: Tworzenie i zarządzanie nimi w usłudze Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager
description: Szablony usługi Azure Resource Manager umożliwiają tworzenie i konfigurowanie usługi Azure Cosmos DB dla programu SQL (rdzenie) interfejsu API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mjbrown
ms.openlocfilehash: 5683fd072961c7793d8f4bbeb9ecc16a93dd7373
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242592"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API SQL usługi Azure Cosmos DB (rdzenie) przy użyciu szablonów usługi Azure Resource Manager

## Tworzenie konta usługi Azure Cosmos, bazy danych i kontenerów <a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konta usługi Azure Cosmos z dwóch kontenerów, które współużytkują 400 jednostek RU/s przepływności w poziomie bazy danych. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

> [!NOTE]
> Obecnie nie można wdrożyć dla użytkownika zdefiniowane Functions(UDFs), procedury składowane i wyzwalacze przy użyciu szablonów usługi Resource Manager. 

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Aby wdrożyć szablon usługi Resource Manager przy użyciu programu PowerShell, **kopii** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji programu PowerShell, a nie usługa Azure Cloud shell, musisz [zainstalować](/powershell/azure/install-az-ps) modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana.

### <a name="deploy-via-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzonego konta usługi Azure Cosmos po udostępnieniu mu. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji wiersza polecenia platformy Azure zamiast CloudShell, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/) artykułu.

## Aktualizuj przepływność (RU/s) w bazie danych <a id="database-ru-update"></a>

Następujący szablon zaktualizuje przepływności bazy danych. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Wdrażanie szablonu bazy danych za pomocą programu PowerShell

Aby wdrożyć szablon usługi Resource Manager przy użyciu programu PowerShell, **kopii** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Wdrażanie szablonu bazy danych za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualizuj przepływność (RU/s) w kontenerze <a id="container-ru-update"></a>

Następujący szablon zaktualizuje przepływności kontenera. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Wdrażanie szablonu kontenera za pomocą programu PowerShell

Aby wdrożyć szablon usługi Resource Manager przy użyciu programu PowerShell, **kopii** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Wdrażanie szablonu kontenera za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe zasoby:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów w usłudze Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony usługi Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych problemów z wdrożenia usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)