---
title: Szablony usługi Azure Resource Manager dla interfejsu API usługi Azure Cosmos DB Cassandra
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i Konfigurowanie interfejsu API usługi Azure Cosmos DB Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: db754adbe60acfa155400910c47de556db793eef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968911"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB Cassandra przy użyciu szablonów usługi Azure Resource Manager

## Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli <a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konta usługi Azure Cosmos dla interfejsu API rozwiązania Cassandra z dwóch tabel, które współużytkują 400 jednostek RU/s przepływności w poziomie przestrzeń kluczy. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Do wdrożenia szablonu usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, **kopiowania** skrypt, a następnie wybierz pozycję **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Polecenie wyświetla nowo utworzonego konta usługi Azure Cosmos po udostępnieniu mu. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji wiersza polecenia platformy Azure zamiast CloudShell, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/) artykułu.

## Aktualizuj przepływność (RU/s) na przestrzeń kluczy <a id="keyspace-ru-update"></a>

Następujący szablon zaktualizuje przepływność przestrzeń kluczy. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Wdrażanie szablonu przestrzeń kluczy przy użyciu wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Aktualizuj przepływność (RU/s) w tabeli <a id="table-ru-update"></a>

Następujący szablon zaktualizuje przepływność tabeli. Kopiowanie szablonu i wdrażanie, jak pokazano poniżej lub odwiedź [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) i wdrożyć w witrynie Azure portal. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Wdrażanie szablonu tabeli za pomocą wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Resource Manager przy użyciu wiersza polecenia platformy Azure, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe zasoby:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów w usłudze Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony usługi Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych problemów z wdrożenia usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)