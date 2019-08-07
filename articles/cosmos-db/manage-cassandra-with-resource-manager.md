---
title: Szablony Azure Resource Manager dla Azure Cosmos DB interfejs API Cassandra
description: Utwórz i skonfiguruj Azure Cosmos DB interfejs API Cassandra za pomocą szablonów Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: beae89b3f8e21e2f56a1dbf2090b7a612ed5cab0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815111"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejs API Cassandra Azure Cosmos DB przy użyciu szablonów Azure Resource Manager

## Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy i tabeli<a id="create-resource"></a>

Tworzenie Azure Cosmos DB zasobów przy użyciu szablonu Azure Resource Manager. Ten szablon spowoduje utworzenie konta usługi Azure Cosmos na potrzeby interfejs API Cassandra z dwiema tabelami, które współużytkują przepływność na poziomie między jednostkami 400 RU/s. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

> [!NOTE]
> Nazwy kont muszą zawierać małe litery i < 31 znaków.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, **Skopiuj** skrypt i wybierz opcję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

`az cosmosdb show` Polecenie wyświetla nowo utworzone konto usługi Azure Cosmos po jego aprowizacji. Jeśli zdecydujesz się używać lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z programu CloudShell, zobacz artykuł [interfejs wiersza poleceń platformy Azure](/cli/azure/) .

## Aktualizowanie przepływności (RU/s) na przestrzeni kluczy<a id="keyspace-ru-update"></a>

Następujący szablon zaktualizuje przepływność przestrzeni kluczy. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Wdróż szablon przestrzeni kluczy za pomocą interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Aktualizowanie przepływności (RU/s) w tabeli<a id="table-ru-update"></a>

Następujący szablon zaktualizuje przepływność tabeli. Skopiuj szablon i Wdróż, jak pokazano poniżej, lub odwiedź [galerię szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) i Wdróż ją z Azure Portal. Możesz również pobrać szablon na komputer lokalny lub utworzyć nowy szablon i określić ścieżkę lokalną za pomocą `--template-file` parametru.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Wdrażanie szablonu tabeli za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Oto kilka dodatkowych zasobów:

- [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
