---
title: Szablony Menedżera zasobów dla interfejsu API bazy danych usługi Azure Cosmos DB dla usługi MongoDB
description: Tworzenie i konfigurowanie interfejsu API usługi Azure Cosmos DB dla usługi MongoDB za pomocą szablonów usługi Azure Resource Manager.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063635"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB mongodb przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager. W tym artykule przedstawiono przykłady interfejsu API usługi Azure Cosmos DB tylko dla mongodb, aby znaleźć przykłady dla innych kont typu interfejsu API zobacz: użyj szablonów usługi Azure Resource Manager z interfejsem API usługi Azure Cosmos DB dla [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [artykuły tabeli.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Tworzenie interfejsu API bazy danych usługi Azure Cosmos dla konta, bazy danych i kolekcji usługi MongoDB<a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konto usługi Azure Cosmos dla interfejsu API usługi MongoDB z dwiema kolekcjami, które współużytkują przepływność 400 RU/s na poziomie bazy danych. Skopiuj szablon i wdrażaj go w sposób pokazany poniżej lub odwiedź [galerię Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) i wdrażaj go w witrynie Azure Portal. Można również pobrać szablon na komputer lokalny lub utworzyć nowy szablon `--template-file` i określić ścieżkę lokalną z parametrem.

> [!NOTE]
> Nazwy kont muszą być małe i 44 lub mniej znaków.
> Aby zaktualizować program RU/s, należy ponownie przesłać szablon ze zaktualizowanymi wartościami właściwości przepływności.
>
> Obecnie można utworzyć tylko wersję 3.2 (czyli konta przy użyciu `*.documents.azure.com`punktu końcowego w formacie) interfejsu API usługi Azure Cosmos DB dla kont MongoDB przy użyciu programu PowerShell i interfejsu wiersza polecenia. Aby utworzyć 3.6 wersji kont, należy użyć szablonów Menedżera zasobów (poniżej) lub witryny Azure portal zamiast.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Wdrażanie za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, **skopiuj** skrypt i wybierz pozycję **Spróbuj go** otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Polecenie `az cosmosdb show` pokazuje nowo utworzone konto usługi Azure Cosmos po jego zainicjowaniu obsługi administracyjnej. Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z usługi Cloud Shell, zobacz artykuł [interfejsu wiersza polecenia platformy Azure.](/cli/azure/)

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony szybkiego startu usługi Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania usługi Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
