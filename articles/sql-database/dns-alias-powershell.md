---
title: Program PowerShell dla aliasu DNS Azure SQL | Microsoft Docs
description: Polecenia cmdlet programu PowerShell, takie jak New-AzSqlServerDNSAlias, umożliwiają przekierowywanie nowych połączeń klienckich do innego serwera Azure SQL Database, bez konieczności ingerencji żadnej konfiguracji klienta.
keywords: Baza danych SQL DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 47afd905b1fa28ce65163203b9d43781f434233d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058545"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Program PowerShell dla aliasu DNS do Azure SQL Database

Ten artykuł zawiera skrypt programu PowerShell, który demonstruje sposób zarządzania aliasem DNS dla Azure SQL Database. Skrypt uruchamia następujące polecenia cmdlet, które podejmują następujące czynności:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenia cmdlet używane w przykładzie kodu są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy nowy alias DNS w systemie usługi Azure SQL Database. Alias odnosi się do Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i Wyświetl listę wszystkich aliasów DNS przypisanych do serwera SQL DB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modyfikuje nazwę serwera, do którego odwołuje się alias, od serwera 1 do SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń alias DNS z serwera SQL DB 2 przy użyciu nazwy aliasu.

## <a name="dns-alias-in-connection-string"></a>Alias DNS w parametrach połączenia

Aby połączyć określony serwer Azure SQL Database, klient, taki jak SQL Server Management Studio (SSMS), może podać nazwę aliasu DNS zamiast prawdziwej nazwy serwera. W poniższym przykładowym ciągu serwera alias any- *Unique-alias-Name* zastępuje pierwszy węzeł rozdzielony kropką w ciągu serwera z czterema węzłami:

- Przykładowy ciąg serwera: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić pokazowy skrypt programu PowerShell przedstawiony w tym artykule, obowiązują następujące wymagania wstępne:

- Subskrypcja i konto platformy Azure. W przypadku bezpłatnej wersji próbnej kliknij pozycję [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].
- Moduł Azure PowerShell, za pomocą polecenia cmdlet **New-AzSqlServerDNSAlias**.
  - Aby przeprowadzić instalację lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell][install-Az-ps-84p].
  - Aby `Get-Module -ListAvailable Az;` znaleźć wersję\_, uruchom polecenie w programie PowerShell ISE. exe.
- Dwa Azure SQL Database serwery.

## <a name="code-example"></a>Przykładowy kod

Poniższy przykład kodu programu PowerShell jest uruchamiany przez przypisanie wartości literału do kilku zmiennych. Aby uruchomić kod, musisz najpierw edytować wszystkie wartości symboli zastępczych, aby odpowiadały rzeczywistym wartościom w systemie. Możesz też po prostu zbadać kod. I podano również dane wyjściowe konsoli kodu.

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

### <a name="actual-console-output-from-the-powershell-example"></a>Rzeczywiste dane wyjściowe konsoli z przykładu programu PowerShell

Następujące dane wyjściowe konsoli zostały skopiowane i wklejone z rzeczywistego przebiegu.

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne wyjaśnienie funkcji aliasu DNS dla SQL Database, zobacz [alias usługi DNS dla Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
