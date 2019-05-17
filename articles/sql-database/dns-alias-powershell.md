---
title: Program PowerShell w systemie DNS alias usługi Azure SQL | Dokumentacja firmy Microsoft
description: Polecenia cmdlet programu PowerShell, takie jak nowy AzSqlServerDNSAlias umożliwiają przekierowywanie nowe połączenia klientów na inny serwer usługi Azure SQL Database bez konieczności touch żadnej konfiguracji klienta.
keywords: Baza danych sql DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja, jrasnick
manager: craigg
ms.date: 05/14/2019
ms.openlocfilehash: 4318e6557dc72dff7200beb8783575131659b77f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797700"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Program PowerShell dla aliasu DNS do usługi Azure SQL Database

Ten artykuł zawiera skrypt programu PowerShell, który demonstruje, jak zarządzać DNS alias dla usługi Azure SQL Database. Skrypt jest uruchamiany z następujących poleceń cmdlet, które wykonuje następujące akcje:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenia cmdlet używanych w przykładzie kodu są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy nowy alias DNS w systemie usługi Azure SQL Database. Alias odwołuje się do serwera Azure SQL Database 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobieranie i wyświetlanie wszystkie aliasy DNS, które są przypisane do serwera bazy danych SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Zmienia nazwę serwera, którą skonfigurowano aliasu dotyczą z serwera 1 serwer bazy danych SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń DNS alias z serwera bazy danych SQL 2, przy użyciu nazwy aliasu.

## <a name="dns-alias-in-connection-string"></a>Alias systemu DNS w parametrach połączenia

Aby połączyć z określonym serwerem Azure SQL Database, klientowi, takie jak SQL Server Management Studio (SSMS) podać nazwę DNS alias zamiast nazwy serwera. W poniższym przykładzie serwera ciągu, alias *dowolny unikatowa nazwa--alias* zastępuje pierwszego węzła rozdzielanego kropką w ciągu czterech węzłów serwera:

- Przykład serwera ciąg: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić pokaz skryptu programu PowerShell, w tym artykule mają zastosowanie następujące wymagania wstępne:

- Subskrypcja platformy Azure i konto. Bezpłatnej wersji próbnej, kliknij przycisk [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].
- Moduł programu Azure PowerShell, za pomocą polecenia cmdlet **New AzSqlServerDNSAlias**.
  - Aby zainstalować lub uaktualnić, zobacz [Instalowanie modułu Azure PowerShell][install-Az-ps-84p].
  - Uruchom `Get-Module -ListAvailable Az;` w programie powershell\_ise.exe, aby znaleźć wersję.
- Dwa serwery usługi Azure SQL Database.

## <a name="code-example"></a>Przykładowy kod

Następujące polecenie programu PowerShell który przykładowy kod, który rozpoczyna się od Przypisz wartości literału do kilku zmiennych. Aby uruchomić kod, możesz edytować wartości symboli zastępczych do dopasowania wartości rzeczywistych w Twoim systemie. Lub po prostu mogą badać kodu. I dane wyjściowe konsoli kodu jest również udostępniany.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Dane wyjściowe konsoli rzeczywisty przykładowy programu PowerShell

Następujące dane wyjściowe konsoli z został skopiowany, a następnie wkleić z rzeczywistego uruchamiania.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełne wyjaśnienie funkcji DNS Alias dla usługi SQL Database, zobacz [alias DNS dla usługi Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
