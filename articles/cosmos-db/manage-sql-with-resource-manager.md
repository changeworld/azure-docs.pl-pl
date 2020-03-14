---
title: Tworzenie Azure Cosmos DB z szablonami Menedżer zasobów i zarządzanie nimi
description: Używanie szablonów Azure Resource Manager do tworzenia i konfigurowania interfejsu API Azure Cosmos DB for SQL (rdzeń)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251845"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API Azure Cosmos DB SQL (rdzeń) przy użyciu szablonów Azure Resource Manager

W tym artykule dowiesz się, jak używać szablonów usługi Resource Manager, aby ułatwić automatyzację zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB.

W tym artykule przedstawiono tylko przykłady Azure Resource Manager szablonów dla kont interfejsu API SQL. Możesz również znaleźć przykłady szablonów dla interfejsów API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)i [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Tworzenie konta, bazy danych i kontenera usługi Azure Cosmos

Poniższy szablon Azure Resource Manager tworzy konto usługi Azure Cosmos przy użyciu:

* Dwa kontenery, które współużytkują 400 żądaną liczbę jednostek na sekundę (RU/s) na poziomie bazy danych.
* Jeden kontener z dedykowaną przepływność 400 RU/s.

Aby utworzyć zasoby Azure Cosmos DB, Skopiuj poniższy przykładowy szablon i Wdróż go zgodnie z opisem przy użyciu [programu PowerShell](#deploy-via-powershell) lub [interfejsu wiersza polecenia platformy Azure](#deploy-via-azure-cli).

* Opcjonalnie możesz odwiedzić [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) i wdrożyć szablon z poziomu Azure Portal.
* Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

> [!IMPORTANT]
>
> * Po dodaniu lub usunięciu lokalizacji na koncie usługi Azure Cosmos nie można jednocześnie modyfikować innych właściwości. Te operacje należy wykonać oddzielnie.
> * Nazwy kont są ograniczone do 44 znaków, wszystkie małe litery.
> * Aby zmienić wartości przepływności, prześlij ponownie szablon z zaktualizowanymi jednostkami RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Aby utworzyć kontener z dużym kluczem partycji, zmodyfikuj poprzedni szablon w celu uwzględnienia właściwości `"version":2` w obiekcie `partitionKey`.

### <a name="deploy-via-powershell"></a>Wdrażanie za pomocą programu PowerShell

Aby wdrożyć szablon Azure Resource Manager przy użyciu programu PowerShell:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy w oknie Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Możesz wybrać wdrożenie szablonu z zainstalowaną lokalnie wersją programu PowerShell, a nie Azure Cloud Shell. Należy [zainstalować moduł Azure PowerShell](/powershell/azure/install-az-ps). Uruchom `Get-Module -ListAvailable Az`, aby znaleźć wymaganą wersję.

### <a name="deploy-via-azure-cli"></a>Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy w oknie Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Polecenie `az cosmosdb show` wyświetla nowo utworzone konto usługi Azure Cosmos po zainicjowaniu obsługi administracyjnej. Zamiast Azure Cloud Shell można wdrożyć szablon z zainstalowaną lokalnie wersją interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [interfejsu wiersza polecenia (CLI) platformy Azure](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Tworzenie kontenera Azure Cosmos DB przy użyciu funkcji po stronie serwera

Za pomocą szablonu Azure Resource Manager można utworzyć kontener Azure Cosmos DB z procedurą składowaną, wyzwalaczem i funkcją zdefiniowaną przez użytkownika.

Skopiuj poniższy przykładowy szablon i Wdróż go zgodnie z opisem przy użyciu [programu PowerShell](#deploy-with-powershell) lub [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli).

* Opcjonalnie możesz odwiedzić [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) i wdrożyć szablon z poziomu Azure Portal.
* Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Aby wdrożyć szablon Azure Resource Manager przy użyciu programu PowerShell:

1. **Skopiuj** skrypt.
1. Wybierz pozycję **Wypróbuj,** aby otworzyć Azure Cloud Shell.
1. Kliknij prawym przyciskiem myszy okno Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Możesz wybrać wdrożenie szablonu z zainstalowaną lokalnie wersją programu PowerShell, a nie Azure Cloud Shell. Należy [zainstalować moduł Azure PowerShell](/powershell/azure/install-az-ps). Uruchom `Get-Module -ListAvailable Az`, aby znaleźć wymaganą wersję.

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy w oknie Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

* [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/)
* [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
