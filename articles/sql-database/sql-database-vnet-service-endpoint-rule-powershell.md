---
title: Program PowerShell dla punktów końcowych sieci wirtualnej i reguł dla pojedynczych i puli baz danych w usłudze Azure SQL | Dokumentacja firmy Microsoft
description: Zawiera skrypty programu PowerShell, aby utworzyć i zarządzać punktami końcowymi usługi wirtualnej dla usługi Azure SQL Database i SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6713182003a280c1d53e904209159b55b4ad01c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331149"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>Program PowerShell:  Tworzenie punktu końcowego usługi wirtualnego i regułę sieci wirtualnej dla programu SQL

*Reguł sieci wirtualnej* są jednym zapory funkcja zabezpieczeń, która określa, czy serwer bazy danych dla pojedynczych baz danych i pul elastycznych w systemie Azure [bazy danych SQL](sql-database-technical-overview.md) lub baz danych w [danych serwera SQL Magazyn](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje łączności, które są wysyłane z określonej podsieci w sieciach wirtualnych.

> [!IMPORTANT]
> Ten artykuł dotyczy serwera Azure SQL i bazy danych SQL Database i SQL Data Warehouse baz danych, które są tworzone na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. W tym artykule jest *nie* dotyczą **wystąpienia zarządzanego** wdrożenia w usłudze Azure SQL Database, ponieważ nie ma punktu końcowego usługi skojarzono.

Ten artykuł zawiera oraz wyjaśniono skrypt programu PowerShell, który wykonuje następujące akcje:

1. Microsoft Azure tworzy *punktu końcowego usługi wirtualne* na podsieci.
2. Dodaje punkt końcowy do zapory serwera Azure SQL Database, aby utworzyć *reguły sieci wirtualnej*.

Twoje zresztą tworzenia reguły są wyjaśnione w: [Punkty końcowe usługi wirtualnej dla usługi Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Jeśli wszystko, czego potrzebujesz do oceny, lub Dodaj punkt końcowy usługi wirtualnych *nazwy typu* usługi SQL Database do podsieci, możesz przejść od razu do naszych więcej [bezpośrednie skrypt programu PowerShell](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

## <a name="major-cmdlets"></a>Polecenia cmdlet główna

W tym artykule kładzie nacisk **New AzSqlServerVirtualNetworkRule** polecenia cmdlet, który dodaje punkt końcowy podsieci do listy kontroli dostępu (ACL) serwera usługi Azure SQL Database, tworząc w ten sposób regułę.

Na poniższej liście przedstawiono sekwencję innych *głównych* poleceń cmdlet, które należy uruchomić, aby przygotować się do wywołania do **New AzSqlServerVirtualNetworkRule**. W tym artykule te wywołania występują w [skryptu 3 "reguły sieci wirtualnej"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Tworzy obiekt podsieci.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Powoduje utworzenie sieci wirtualnej, nadając mu podsieci.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Przypisuje wirtualne punktu końcowego do podsieci.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Utrzymuje aktualizacje wprowadzone w Twojej sieci wirtualnej.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Po podsieci jest punktem końcowym, dodanie podsieci jako regułę sieci wirtualnej, do listy kontroli dostępu serwera usługi Azure SQL Database.
   - To polecenie cmdlet oferuje parametr **- IgnoreMissingVNetServiceEndpoint**, począwszy od usługi Azure RM PowerShell modułu wersji 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Wymagania wstępne dotyczące uruchamiania programu PowerShell

- Możesz już zalogować się do platformy Azure, takich jak za pomocą [witryny Azure portal][http-azure-portal-link-ref-477t].
- Można już uruchomić skrypty programu PowerShell.

> [!NOTE]
> Upewnij się, że punkty końcowe usługi są włączone dla sieci wirtualnej/podsieci, który chcesz dodać do serwera w przeciwnym razie tworzenie reguły zapory sieci wirtualnej zakończy się niepowodzeniem.

## <a name="one-script-divided-into-four-chunks"></a>Skrypt jest podzielony na cztery fragmentów

Nasz pokaz skryptu programu PowerShell jest podzielony na sekwencję mniejszych skryptów. Podział learning upraszcza i zapewnia elastyczność. Skrypty muszą być uruchamiane w ich wskazanej kolejności. Jeśli nie masz teraz czas na uruchamianie skryptów, nasze dane wyjściowe testu rzeczywisty jest wyświetlany po skrypcie 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Skrypt 1: Zmienne

Ten pierwszy skrypt programu PowerShell przypisuje wartości do zmiennych. Kolejne skrypty są zależne od tych zmiennych.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu można edytować wartości, jeśli chcesz. Na przykład jeśli masz już grupę zasobów, możesz edytować Nazwa grupy zasobów jako przypisaną wartością.
>
> Nazwa subskrypcji usługi powinny być edytowane w skrypcie.

### <a name="powershell-script-1-source-code"></a>Kod źródłowy skryptu 1 programu PowerShell

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Skrypt 2: Wymagania wstępne

Ten skrypt przygotowuje się do następnego skrypt, gdzie jest akcja punktu końcowego. Ten skrypt tworzy następujące wymienionych elementów, ale tylko wtedy, jeśli jeszcze nie istnieje. Jeśli masz pewność, że te elementy już istnieje, możesz pominąć skryptu 2:

- Grupa zasobów platformy Azure
- Serwer usługi Azure SQL Database

### <a name="powershell-script-2-source-code"></a>Kod źródłowy 2 skrypt programu PowerShell

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skrypt 3: Tworzenie punktu końcowego i zasady

Ten skrypt tworzy sieć wirtualną z podsiecią. A następnie przypisuje skrypt **Microsoft.Sql** typ punktu końcowego do podsieci. Na koniec skrypt ten dodaje podsieć do listy kontroli dostępu (ACL) z serwerem usługi SQL Database, tworząc w ten sposób regułę.

### <a name="powershell-script-3-source-code"></a>Kod źródłowy skryptu 3 programu PowerShell

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skrypt 4: Wyczyść

Ten skrypt końcowy usuwa zasoby, utworzonych przez wcześniejsze skrypty demonstracyjne. Jednak skrypt prosi o potwierdzenie przed usunięciem następujące czynności:

- Serwer usługi Azure SQL Database
- Grupa zasobów platformy Azure

Możesz uruchomić skrypt 4 w dowolnej chwili po ukończeniu działania skryptu 1.

### <a name="powershell-script-4-source-code"></a>Kod źródłowy skryptu 4 programu PowerShell

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Rzeczywiste dane wyjściowe skryptów 1 do 4

Dane wyjściowe z naszych przebieg testu jest wyświetlany obok, w formie skróconej. Dane wyjściowe mogą być pomocne w przypadku, gdy nie chcesz teraz faktycznie uruchomić skrypty programu PowerShell.

```cmd
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Completed script 2, the "Prerequisites".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations
----------------- -------       ---------
Succeeded         Microsoft.Sql {westcentralus}

Verify that the rule is in the SQL DB ACL.

Completed script 3, the "Virtual-Network-Rule".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

To jest koniec naszym głównym skrypt programu PowerShell.

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Upewnij się, że Twoja podsieć jest punktem końcowym

Może być podsieci, który został już przypisany **Microsoft.Sql** nazwę typu, co oznacza, jest on już wirtualnego punktu końcowego. Można użyć [witryny Azure portal] [ http-azure-portal-link-ref-477t] można utworzyć regułę sieci wirtualnej z punktu końcowego.

Lub może być pewności, że podsieć ma **Microsoft.Sql** nazwy typu. Można uruchomić następujący skrypt programu PowerShell, aby wykonać następujące akcje:

1. Ustalenia, czy podsieć ma **Microsoft.Sql** nazwy typu.
2. Opcjonalnie można przypisać nazwy typu, jeśli jest nieobecny.
    - Skrypt wyświetli monit o *potwierdzić*, zanim dotyczyła nieobecny typ nazwy.

### <a name="phases-of-the-script"></a>Fazy skryptu

Oto etapy skrypt programu PowerShell:

1. ZALOGUJ się do konta platformy Azure potrzebne tylko raz na sesję PS.  Przypisz zmiennych.
2. Wyszukiwanie w sieci wirtualnej, a następnie dla podsieci.
3. Podsieć jest oznaczony jako **Microsoft.Sql** typ serwera punktu końcowego?
4. Dodaj punkt końcowy usługi wirtualnej nazwy typu **Microsoft.Sql**, na podsieci.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu musi Edytowanie wartości przypisane atrybutowi $— zmienne w górnej części skryptu.

### <a name="direct-powershell-source-code"></a>Bezpośrednie PowerShell kodu źródłowego

Ten program PowerShell, skrypt nie żadnych aktualizacji, chyba że użytkownik odpowie tak, jeśli jest monituje o potwierdzenie. Skrypt można dodać nazwę typu **Microsoft.Sql** do podsieci. Ale skrypt próbuje dodawania tylko wtedy, gdy podsieci nie posiada nazwę typu.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

### <a name="actual-output"></a>Rzeczywiste dane wyjściowe

Poniższy blok wyświetla swoją opinię rzeczywiste (z kosmetycznych zmian).

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```

<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/
