---
title: Szablony usługi Azure Resource Manager dla interfejsu API usługi Azure Cosmos DB Cassandra
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i Konfigurowanie interfejsu API usługi Azure Cosmos DB Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 9878939a461f3ba35d2b6c270de2a965f66204cc
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077548"
---
# <a name="create-azure-cosmos-db-cassandra-api-resources-from-a-resource-manager-template"></a>Tworzenie interfejsu API usługi Azure Cosmos DB Cassandra zasobów na podstawie szablonu usługi Resource Manager

Dowiedz się, jak utworzyć konto usługi Azure Cosmos dla interfejsu API rozwiązania Cassandra przy użyciu szablonu usługi Azure Resource Manager. Poniższy przykład tworzy konto interfejsu API usługi Azure Cosmos DB Cassandra od [szablonu szybkiego startu platformy Azure](https://aka.ms/cassandra-arm-qs). Ten szablon utworzy konta usługi Azure Cosmos dla interfejsu API rozwiązania Cassandra z dwóch tabel, które współużytkują 400 jednostek RU/s przepływności w poziomie przestrzeń kluczy. 

Oto kopię szablonu:

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

W poprzednim przykładzie ma odwołania do szablonu, który jest przechowywany w usłudze GitHub. Możesz również pobrać szablon na komputerze lokalnym lub utworzyć nowy szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe zasoby:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów w usłudze Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony usługi Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych problemów z wdrożenia usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)