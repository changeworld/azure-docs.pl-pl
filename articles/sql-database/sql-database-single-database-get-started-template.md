---
title: 'Azure Resource Manager: Tworzenie pojedynczej bazy danych — usługa Azure SQL Database | Microsoft Docs'
description: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu szablonu usługi Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 01e14f86b16db6d998d60e74211ae5ad77381461
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373021"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Szybki start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu szablonu usługi Azure Resource Manager

Tworzenie [pojedynczej bazy danych](sql-database-single-database.md) to najszybsza i najprostsza opcja wdrażania w przypadku tworzenia bazy danych w usłudze Azure SQL Database. Ten przewodnik Szybki Start przedstawia sposób tworzenia pojedynczej bazy danych przy użyciu szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedyncza baza danych ma zdefiniowany zestaw zasobów obliczeniowych, pamięci, we/wy i magazynu przy użyciu jednego z dwóch [modeli zakupu](sql-database-purchase-models.md). Podczas tworzenia pojedynczej bazy danych definiowany jest również [serwer usługi SQL Database](sql-database-servers.md) służący do zarządzania tą bazą danych i umieszczania jej w ramach [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w określonym regionie.

Szablon używany w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/). Następujący plik JSON jest szablon, który jest używany w tym artykule. Więcej przykładów szablonu bazy danych Azure SQL można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database](~/quickstart-templates/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json)]

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-threat-detection-server-policy-optional-db%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.  

    ![Szablon usługi Resource Manager utworzyć bazę danych azure sql](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    O ile nie zostanie określony, należy użyć wartości domyślnych.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: Wybierz **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**. 
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Administrator**: Określ nazwę użytkownika administratora serwera bazy danych SQL.
    * **Hasło administratora**: Określ hasło administratora. 
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.
3. Wybierz pozycję **Kup**.

## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Aby wysłać zapytanie bazy danych, zobacz [w bazie danych](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zachowaj tę grupę zasobów, serwer bazy danych i pojedynczą bazę danych, jeśli chcesz przejść do [następnych kroków](#next-steps). W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów za pomocą wiersza polecenia platformy Azure lub programu Azure Powershell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Kolejne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](sql-database-connect-query.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby tworzyć pojedyncze bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Przykłady interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Aby tworzyć pojedyncze bazy danych przy użyciu usługi Azure PowerShell, zobacz [Przykłady programu Azure PowerShell](sql-database-powershell-samples.md).
