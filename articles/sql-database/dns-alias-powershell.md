---
title: PowerShell w systemie DNS alias Azure SQL | Dokumentacja firmy Microsoft
description: Polecenia cmdlet programu PowerShell, takie jak nowy AzureRMSqlServerDNSAlias umożliwiają przekierowanie nowe połączenia klientów na inny serwer bazy danych SQL Azure, bez konieczności touch żadnej konfiguracji klienta.
keywords: Baza danych sql DNS
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: 06c27a2fda79afe5a7f4a0e9efdbc51e171bcbda
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Środowiska PowerShell dla aliasu DNS do bazy danych Azure SQL

Ten artykuł zawiera skrypt programu PowerShell, który pokazuje, jak można zarządzać DNS alias bazy danych SQL Azure. Skrypt jest uruchamiany następujące polecenia cmdlet, które wykonuje następujące akcje:


Polecenia cmdlet używane w przykładzie kodu są następujące:
- [Nowy AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): tworzy nowy alias DNS w systemie usługi baza danych SQL Azure. Alias odwołuje się do serwera bazy danych SQL Azure 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): lista wszystkich aliasów DNS, które są przypisane do bazy danych SQL server 1 i Get.
- [Zestaw AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Modyfikuje nazwę serwera skonfigurowanego do aliasu dotyczą z serwera 1 serwer bazy danych SQL. 2.
- [Usuń AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Usuń DNS alias z serwera bazy danych SQL 2, przy użyciu nazwy aliasu.


Powyższe polecenia cmdlet środowiska PowerShell zostały dodane do **AzureRm.Sql** modułu, począwszy od wersji 5.1.1.




#### <a name="dns-alias-in-connection-string"></a>Alias systemu DNS w parametrach połączenia

Aby połączyć z określonym serwerze bazy danych SQL Azure, klienta takich jak SQL Server Management Studio (SSMS) można podać nazwę aliasu DNS zamiast nazwy serwera. W poniższym przykładzie serwera ciągu, alias *dowolny unikatowy alias nazwa-* zastępuje pierwszy węzeł rozdzielanych kropkami w ciągu czterech węzłów serwera:
- Przykład serwera ciąg: `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić pokaz skryptu PowerShell podane w tym artykule, zastosuj następujące wymagania wstępne:

- Subskrypcja platformy Azure i konta. Bezpłatnej wersji próbnej, kliknij przycisk [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].

- Modułu Azure PowerShell, za pomocą polecenia cmdlet **AzureRMSqlServerDNSAlias nowy**.
    - Aby zainstalować lub uaktualnić, zobacz [modułu instalacji programu Azure PowerShell][install-azurerm-ps-84p].
    - Uruchom `Get-Module -ListAvailable AzureRM;` w programie powershell\_ise.exe, aby znaleźć wersję.

- Dwa serwery bazy danych SQL Azure.

## <a name="code-example"></a>Przykładowy kod

Następujące środowiska PowerShell, który przykładowy kod uruchamia się za przypisać wartości literału kilku zmiennych. Aby uruchomić kod, należy najpierw zmodyfikować wszystkie wartości symbol zastępczy do dopasowania wartości rzeczywistych w systemie. Lub po prostu mogą badać kodu. I dane wyjściowe konsoli kodu jest również udostępniany.


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
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>Dane wyjściowe rzeczywistej konsoli w przykładzie programu PowerShell

Następujące dane wyjściowe konsoli było skopiować i wkleić z rzeczywistego uruchamiania.


```
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

Pełny opis funkcji DNS Alias dla bazy danych SQL, zobacz [alias systemu DNS dla usługi Azure SQL Database][dns-alias-overview-37v].



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

