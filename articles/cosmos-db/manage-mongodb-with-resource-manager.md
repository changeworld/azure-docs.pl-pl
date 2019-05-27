---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB interfejs API systemu MongoDB
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i Konfigurowanie interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968870"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsem API MongoDB usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager

## Tworzenie interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB konto, bazę danych i kolekcji <a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konta usługi Azure Cosmos dla interfejsu API usługi MongoDB z dwie kolekcje, które współużytkują 400 jednostek RU/s przepływności w poziomie bazy danych. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Do wdrożenia szablonu usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, **kopiowania** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzonego konta usługi Azure Cosmos po udostępnieniu mu. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji wiersza polecenia platformy Azure zamiast CloudShell, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/) artykułu.

## Aktualizuj przepływność (RU/s) w bazie danych <a id="database-ru-update"></a>

Następujący szablon zaktualizuje przepływności bazy danych. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Wdrażanie szablonu bazy danych za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualizuj przepływność (RU/s) w kolekcji <a id="collection-ru-update"></a>

Następujący szablon zaktualizuje przepływność kolekcji. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Wdrażanie szablonu kolekcji za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe zasoby:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów w usłudze Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony usługi Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych problemów z wdrożenia usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)