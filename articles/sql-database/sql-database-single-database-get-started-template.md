---
title: 'Usługa Azure Resource Manager: tworzenie pojedynczej bazy danych'
description: Utwórz pojedynczą bazę danych w usłudze Azure SQL Database przy użyciu szablonu Usługi Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 7c42ff7f42dea049752f9f879abffffd0e7b3902
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531332"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Szybki start: tworzenie pojedynczej bazy danych w bazie danych SQL usługi Azure przy użyciu szablonu usługi Azure Resource Manager

Tworzenie [pojedynczej bazy danych](sql-database-single-database.md) to najszybsza i najprostsza opcja wdrażania w przypadku tworzenia bazy danych w usłudze Azure SQL Database. Ten przewodnik Szybki start pokazuje, jak utworzyć pojedynczą bazę danych przy użyciu szablonu Usługi Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedyncza baza danych ma zdefiniowany zestaw zasobów obliczeniowych, pamięci, we/wy i magazynu przy użyciu jednego z dwóch [modeli zakupów](sql-database-purchase-models.md). Podczas tworzenia pojedynczej bazy danych definiowany jest również [serwer usługi SQL Database](sql-database-servers.md) służący do zarządzania tą bazą danych i umieszczania jej w ramach [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w określonym regionie.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Te zasoby są zdefiniowane w szablonie:

- [**Microsoft.Sql/serwery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servers/vulnerabilityAssessments Microsoft.Sql/servers/vulnerabilityAssessments Microsoft.Sql/servers/vulnerabilityAssessments Microsoft.**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/serwery/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageKondyje**](/azure/templates/microsoft.storage/storageaccounts)
- [**Magazyn/magazyn firmy MicrosoftKonstyty/dostawcy/rolaAsy**](/azure/templates/microsoft.authorization/roleassignments)

Więcej przykładów szablonów bazy danych SQL platformy Azure można znaleźć w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz **pozycję Wypróbuj z** następującego bloku kodu programu PowerShell, aby otworzyć usługę Azure Cloud Shell.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby zbadać bazę danych, zobacz [Kwerenda bazy danych](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zachowaj tę grupę zasobów, serwer bazy danych i pojedynczą bazę danych, jeśli chcesz przejść do [następnych kroków](#next-steps). W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Następne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](sql-database-connect-query.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby utworzyć pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Aby utworzyć pojedynczą bazę danych przy użyciu programu Azure PowerShell, zobacz [przykłady programu Azure PowerShell](sql-database-powershell-samples.md).
- Aby dowiedzieć się, jak tworzyć szablony Menedżera zasobów, zobacz [Tworzenie pierwszego szablonu](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
