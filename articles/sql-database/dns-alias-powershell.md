---
title: PowerShell dla aliasu DNS
description: Polecenia cmdlet programu PowerShell, takie jak New-AzSqlServerDNSAlias umożliwiają przekierowanie nowych połączeń klientów do innego serwera bazy danych SQL platformy Azure bez konieczności dotykania konfiguracji klienta.
keywords: dns sql baza danych
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420403"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell dla aliasu DNS do bazy danych SQL usługi Azure

Ten artykuł zawiera skrypt programu PowerShell, który pokazuje, jak można zarządzać aliasem DNS dla usługi Azure SQL Database.

> [!NOTE]
> Ten artykuł został zaktualizowany w celu użycia modułu Az programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.
>
> Aby dowiedzieć się więcej o module Az i zgodności usługi AzureRM, zobacz [Przedstawianie modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az) Aby uzyskać instrukcje dotyczące instalacji, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps) lub [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias DNS w ciągu połączenia

Aby połączyć określony serwer bazy danych SQL Azure, klient, taki jak SQL Server Management Studio (SSMS), może podać nazwę aliasu DNS zamiast nazwy prawdziwego serwera. W poniższym przykładowym ciągu serwera alias *any-unique-alias-name* zastępuje pierwszy węzeł rozdzielany kropką w ciągu serwera czterech węzłów:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić skrypt demo programu PowerShell podany w tym artykule, obowiązują następujące wymagania wstępne:

- Bezpłatna wersja próbna subskrypcji i konta platformy Azure, aby uzyskać bezpłatną wersję [próbną,](https://azure.microsoft.com/free/) zobacz Wersje próbne platformy Azure
- Dwa serwery bazy danych SQL platformy Azure

## <a name="example"></a>Przykład

Poniższy przykład kodu rozpoczyna się od przypisania wartości literału do kilku zmiennych.

Aby uruchomić kod, edytuj wartości zastępcze, aby dopasować rzeczywiste wartości w systemie.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Używane polecenia cmdlet są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy alias DNS w systemie usługi Azure SQL Database. Alias odnosi się do serwera bazy danych 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i wyświetl wszystkie aliasy przypisane do serwera bazy danych SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modyfikuje nazwę serwera, do których odnosi się alias, z serwera 1 na serwer 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń alias z serwera bazy danych 2, używając nazwy aliasu.

Aby przeprowadzić instalację lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Użyj `Get-Module -ListAvailable Az` w *programie\_powershell ise.exe*, aby znaleźć wersję.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Używane polecenia są następujące:

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy alias DNS w systemie usługi Azure SQL Database. Alias odnosi się do serwera bazy danych 1.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i wyekslizuj wszystkie aliasy przypisane do serwera BAZY DANYCH SQL 1.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modyfikuje nazwę serwera, do których odnosi się alias, z serwera 1 do serwera 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń alias z serwera bazy danych 2, używając nazwy aliasu.

Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne wyjaśnienie funkcji aliasu DNS dla bazy danych SQL, zobacz [alias DNS bazy danych SQL platformy Azure](dns-alias-overview.md).
