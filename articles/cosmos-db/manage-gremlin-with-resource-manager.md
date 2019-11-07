---
title: Szablony Azure Resource Manager dla interfejsu API usługi Azure Cosmos DB Gremlin
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować interfejs API usługi Azure Cosmos DB Gremlin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f50a6980cb26d6a89e2564b492a8be900ad57736
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606788"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB Gremlin przy użyciu szablonów Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami Azure Cosmos DB, bazami danych i kontenerami przy użyciu szablonów Azure Resource Manager. W tym artykule przedstawiono przykłady tylko dla kont interfejsu API Gremlin, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: korzystanie z szablonów Menedżer zasobów z interfejsem API Azure Cosmos DB dla [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)i [tabel](manage-table-with-resource-manager.md) .

## Tworzenie Azure Cosmos DB interfejsu API dla konta MongoDB, bazy danych i kolekcji<a id="create-resource"></a>

Tworzenie Azure Cosmos DB zasobów przy użyciu szablonu Azure Resource Manager. Ten szablon utworzy konto usługi Azure Cosmos dla interfejsu API Gremlin z dwoma wykresami, które współdzielą przepływność na 400 RU/s na poziomie bazy danych. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

> [!NOTE]
> Nazwy kont muszą zawierać małe litery i < 31 znaków.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, **Skopiuj** skrypt i wybierz opcję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Polecenie `az cosmosdb show` wyświetla nowo utworzone konto usługi Azure Cosmos po jego aprowizacji. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z programu CloudShell, zobacz artykuł [interfejs wiersza poleceń platformy Azure](/cli/azure/) .

## Aktualizowanie przepływności (RU/s) w bazie danych<a id="database-ru-update"></a>

Następujący szablon zaktualizuje przepływność bazy danych. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Wdrażanie szablonu bazy danych za pomocą interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualizowanie przepływności (RU/s) na grafie<a id="graph-ru-update"></a>

Następujący szablon zaktualizuje przepływność grafu. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Wdróż szablon wykresu za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName graphName=$graphName throughput=$throughput
```

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)