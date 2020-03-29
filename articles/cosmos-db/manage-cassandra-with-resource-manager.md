---
title: Szablony Menedżera zasobów dla interfejsu API Cassandra usługi Azure Cosmos DB
description: Tworzenie i konfigurowanie interfejsu API Cassandra usługi Azure Cosmos DB Cassandra za pomocą szablonów usługi Azure Resource Manager.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251897"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB Cassandra przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager. W tym artykule przedstawiono przykłady tylko kont interfejsu API SQL, aby znaleźć przykłady dla innych kont typu interfejsu API zobacz: użyj szablonów usługi Azure Resource Manager z interfejsem API usługi Azure Cosmos DB dla [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [artykułów tabeli.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Tworzenie konta, przestrzeni kluczy i tabeli usługi Azure Cosmos<a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konto usługi Azure Cosmos dla interfejsu API Cassandra z dwiema tabelami, które współużytkują przepływność 400 RU/s na poziomie przestrzeni kluczy. Skopiuj szablon i wdrażaj go w sposób pokazany poniżej lub odwiedź [galerię Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) i wdrażaj go w witrynie Azure Portal. Można również pobrać szablon na komputer lokalny lub utworzyć nowy szablon `--template-file` i określić ścieżkę lokalną z parametrem.

> [!NOTE]
> Nazwy kont muszą być małe i 44 lub mniej znaków.
> Aby zaktualizować program RU/s, należy ponownie przesłać szablon ze zaktualizowanymi wartościami właściwości przepływności.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, **skopiuj** skrypt i wybierz pozycję **Spróbuj go** otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Polecenie `az cosmosdb show` pokazuje nowo utworzone konto usługi Azure Cosmos po jego zainicjowaniu obsługi administracyjnej. Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z usługi Cloud Shell, zobacz artykuł [interfejsu wiersza polecenia platformy Azure.](/cli/azure/)


## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony szybkiego startu usługi Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania usługi Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
