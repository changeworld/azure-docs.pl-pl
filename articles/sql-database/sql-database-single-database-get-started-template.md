---
title: 'Azure Resource Manager: Tworzenie pojedynczej bazy danych — usługa Azure SQL Database | Microsoft Docs'
description: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu szablonu usługi Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
manager: craigg
ms.date: 06/28/2019
ms.openlocfilehash: 4ef0f9ff6f8620109f2ef6f6bd5f549281b4de54
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472156"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Szybki start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu szablonu usługi Azure Resource Manager

Tworzenie [pojedynczej bazy danych](sql-database-single-database.md) to najszybsza i najprostsza opcja wdrażania w przypadku tworzenia bazy danych w usłudze Azure SQL Database. Ten przewodnik Szybki Start przedstawia sposób tworzenia pojedynczej bazy danych przy użyciu szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedyncza baza danych ma zdefiniowany zestaw zasobów obliczeniowych, pamięci, we/wy i magazynu przy użyciu jednego z dwóch [modeli zakupów](sql-database-purchase-models.md). Podczas tworzenia pojedynczej bazy danych definiowany jest również [serwer usługi SQL Database](sql-database-servers.md) służący do zarządzania tą bazą danych i umieszczania jej w ramach [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w określonym regionie.

Następujący plik JSON jest szablon, który jest używany w tym artykule. Szablon znajduje się w [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Więcej przykładów szablonu bazy danych Azure SQL znajdują się w [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Wybierz **wypróbuj** z poniższy blok kodu programu PowerShell, aby otworzyć usługę Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Wybierz **kopiowania** skopiować skrypt programu PowerShell do Schowka.
1. Kliknij prawym przyciskiem myszy w okienku shell, a następnie wybierz **Wklej**.

    Może potrwać kilka chwil, aby utworzyć serwer bazy danych i bazy danych.

## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Aby wysłać zapytanie bazy danych, zobacz [w bazie danych](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zachowaj tę grupę zasobów, serwer bazy danych i pojedynczą bazę danych, jeśli chcesz przejść do [następnych kroków](#next-steps). W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów w programie Azure Powershell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Kolejne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](sql-database-connect-query.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby utworzyć pojedynczą bazę danych przy użyciu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Aby utworzyć pojedynczą bazę danych przy użyciu programu Azure PowerShell, zobacz [przykładów programu Azure PowerShell](sql-database-powershell-samples.md).
