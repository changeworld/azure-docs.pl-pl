---
title: Szablony Menedżera zasobów dla interfejsu API tabeli usługi Azure Cosmos DB
description: Tworzenie i konfigurowanie interfejsu API tabel usługi Azure Cosmos DB za pomocą szablonów usługi Azure Resource Manager.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246710"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API tabeli usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób wykonywania różnych operacji w celu zautomatyzowania zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB przy użyciu szablonów usługi Azure Resource Manager. W tym artykule przedstawiono przykłady tylko kont interfejsu API tabeli, aby znaleźć przykłady dla innych kont typu interfejsu API zobacz: użyj szablonów usługi Azure Resource Manager z interfejsem API usługi Azure Cosmos DB dla [cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), artykułów [SQL.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Tworzenie konta i tabeli usługi Azure Cosmos<a id="create-resource"></a>

Tworzenie zasobów usługi Azure Cosmos DB przy użyciu szablonu usługi Azure Resource Manager. Ten szablon utworzy konto usługi Azure Cosmos dla interfejsu API tabeli z jedną tabelą przy przepływności 400 RU/s. Skopiuj szablon i wdrażaj go w sposób pokazany poniżej lub odwiedź [galerię Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) i wdrażaj go w witrynie Azure Portal. Można również pobrać szablon na komputer lokalny lub utworzyć nowy szablon `--template-file` i określić ścieżkę lokalną z parametrem.

> [!NOTE]
> Nazwy kont muszą być małe i 44 lub mniej znaków.
> Aby zaktualizować program RU/s, należy ponownie przesłać szablon ze zaktualizowanymi wartościami właściwości przepływności.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Wdrażanie za pośrednictwem programu PowerShell

Aby wdrożyć szablon Menedżera zasobów przy użyciu programu PowerShell, **skopiuj** skrypt i wybierz pozycję **Spróbuj go** otworzyć w usłudze Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji programu PowerShell zamiast powłoki usługi Azure Cloud, musisz [zainstalować](/powershell/azure/install-az-ps) moduł programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana.

### <a name="deploy-via-the-azure-cli"></a>Wdrażanie za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon Usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, **skopiuj** skrypt i wybierz pozycję **Spróbuj go** otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Polecenie `az cosmosdb show` pokazuje nowo utworzone konto usługi Azure Cosmos po jego zainicjowaniu obsługi administracyjnej. Jeśli zdecydujesz się użyć lokalnie zainstalowanej wersji interfejsu wiersza polecenia platformy Azure zamiast korzystania z usługi Cloud Shell, zobacz artykuł [interfejsu wiersza polecenia platformy Azure.](/cli/azure/)

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
- [Schemat dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Szablony szybkiego startu usługi Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Rozwiązywanie typowych błędów wdrażania usługi Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)