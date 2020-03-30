---
title: PowerShell dla punktów końcowych sieci wirtualnej i reguł dla pojedynczych i pulowych baz danych
description: Udostępnia skrypty programu PowerShell do tworzenia punktów końcowych usługi wirtualnej i zarządzania nimi dla bazy danych SQL Azure i usługi Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124705"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>Program PowerShell: Tworzenie reguły punktu końcowego usługi wirtualnej i sieci wirtualnej dla języka SQL

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która określa, czy serwer bazy danych dla pojedynczych baz danych i puli elastycznej w [usłudze](sql-database-technical-overview.md) Azure SQL Database lub dla baz danych w [usłudze Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikację, która jest wysyłana z określonych podsieci w sieciach wirtualnych.

> [!IMPORTANT]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz bazy danych SQL i magazynu danych w usłudze Azure Synapse, które są tworzone na serwerze SQL platformy Azure. Dla uproszczenia bazy danych SQL jest używany podczas odwoływania się do bazy danych SQL i platformy Azure Synapse. Ten artykuł *nie* dotyczy wdrożenia **wystąpienia zarządzanego** w usłudze Azure SQL Database, ponieważ nie ma skojarzonego z nim punktu końcowego usługi.

Ten artykuł zawiera i wyjaśnia skrypt programu PowerShell, który wykonuje następujące akcje:

1. Tworzy punkt *końcowy usługi wirtualnej platformy* Microsoft Azure w podsieci.
2. Dodaje punkt końcowy do zapory serwera usługi Azure SQL Database, aby utworzyć *regułę sieci wirtualnej*.

Twoje motywacje do tworzenia reguły są wyjaśnione w: [Punkty końcowe usługi wirtualnej dla usługi Azure SQL Database.][sql-db-vnet-service-endpoint-rule-overview-735r]

> [!TIP]
> Jeśli wystarczy ocenić lub dodać *nazwę typu* punktu końcowego usługi wirtualnej dla bazy danych SQL do podsieci, możesz przejść do naszego bardziej [bezpośredniego skryptu programu PowerShell.](#a-verify-subnet-is-endpoint-ps-100)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

## <a name="major-cmdlets"></a>Główne polecenia cmdlet

W tym artykule podkreślono **new-AzSqlServerVirtualNetworkRule** cmdlet, który dodaje punkt końcowy podsieci do listy kontroli dostępu (ACL) serwera usługi Azure SQL Database, tworząc w ten sposób regułę.

Na poniższej liście przedstawiono sekwencję innych *głównych* poleceń cmdlet, które należy uruchomić, aby przygotować się do wywołania **do New-AzSqlServerVirtualNetworkRule**. W tym artykule te wywołania występują w [skrypcie 3 "Reguła sieci wirtualnej":](#a-script-30)

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Tworzy obiekt podsieci.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Tworzy sieć wirtualną, nadając jej podsieć.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Przypisuje punkt końcowy usługi wirtualnej do podsieci.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Utrzymuje aktualizacje wprowadzone do sieci wirtualnej.
5. [New-AzSqlServerVirtualNetworkRule:](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)Po podsieci jest punktem końcowym, dodaje podsieci jako regułę sieci wirtualnej, do listy ACL serwera bazy danych SQL Azure.
   - To polecenie cmdlet oferuje parametr **-IgnoreMissingVNetServiceEndpoint**, począwszy od modułu programu Azure RM PowerShell w wersji 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Wymagania wstępne dotyczące uruchamiania programu PowerShell

- Można już zalogować się na platformie Azure, na przykład za pośrednictwem [witryny Azure portal][http-azure-portal-link-ref-477t].
- Skrypty programu PowerShell można już uruchamiać.

> [!NOTE]
> Upewnij się, że punkty końcowe usługi są włączone dla sieci wirtualnej/podsieci, które chcesz dodać do serwera w przeciwnym razie utworzenie reguły zapory sieci wirtualnej zakończy się niepowodzeniem.

## <a name="one-script-divided-into-four-chunks"></a>Jeden skrypt podzielony na cztery kawałki

Nasz demonstracyjny skrypt programu PowerShell jest podzielony na sekwencję mniejszych skryptów. Podział ułatwia naukę i zapewnia elastyczność. Skrypty muszą być uruchamiane we wskazanej sekwencji. Jeśli nie masz teraz czasu na uruchomienie skryptów, nasze rzeczywiste dane wyjściowe testu są wyświetlane po skrypcie 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Skrypt 1: Zmienne

Ten pierwszy skrypt programu PowerShell przypisuje wartości do zmiennych. Kolejne skrypty zależą od tych zmiennych.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu można edytować wartości, jeśli chcesz. Na przykład jeśli masz już grupę zasobów, możesz chcieć edytować nazwę grupy zasobów jako przypisaną wartość.
>
> Nazwa subskrypcji powinna być edytowana w skrypcie.

### <a name="powershell-script-1-source-code"></a>Kod źródłowy skryptu programu PowerShell 1

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

Ten skrypt przygotowuje się do następnego skryptu, gdzie jest akcja punktu końcowego. Ten skrypt tworzy dla Ciebie następujące wymienione elementy, ale tylko wtedy, gdy jeszcze nie istnieją. Możesz pominąć skrypt 2, jeśli masz pewność, że te elementy już istnieją:

- Grupa zasobów platformy Azure
- Serwer bazy danych SQL platformy Azure

### <a name="powershell-script-2-source-code"></a>Kod źródłowy skryptu programu PowerShell 2

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

if ($null -eq $sqlDbServer) {
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

    if ($null -eq $sqlAdministratorCredentials) {
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
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skrypt 3: Tworzenie punktu końcowego i reguły

Ten skrypt tworzy sieć wirtualną z podsiecią. Następnie skrypt przypisuje typ punktu końcowego **Microsoft.Sql** do podsieci. Na koniec skrypt dodaje podsieci do listy kontroli dostępu (ACL) serwera bazy danych SQL, tworząc w ten sposób regułę.

### <a name="powershell-script-3-source-code"></a>Kod źródłowy skryptu programu PowerShell 3

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

## <a name="script-4-clean-up"></a>Skrypt 4: Oczyszczanie

Ten ostateczny skrypt usuwa zasoby utworzone przez poprzednie skrypty dla demonstracji. Jednak skrypt prosi o potwierdzenie, zanim usunie następujące:

- Serwer bazy danych SQL platformy Azure
- Grupa zasobów platformy Azure

Skrypt 4 można uruchomić w dowolnym momencie po zakończeniu skryptu 1.

### <a name="powershell-script-4-source-code"></a>Kod źródłowy skryptu programu PowerShell 4

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
if ('yes' -eq $yesno) {
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
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Sprawdź, czy podsieć jest punktem końcowym

Być może masz podsieć, która została już przypisana nazwa typu **Microsoft.Sql,** co oznacza, że jest już punktem końcowym usługi wirtualnej. Za pomocą [witryny Azure portal][http-azure-portal-link-ref-477t] można utworzyć regułę sieci wirtualnej z punktu końcowego.

Możesz też nie mieć pewności, czy podsieć ma nazwę typu **Microsoft.Sql.** Aby podjąć następujące działania, można uruchomić następujący skrypt programu PowerShell:

1. Sprawdź, czy podsieć ma nazwę typu **Microsoft.Sql.**
2. Opcjonalnie przypisz nazwę typu, jeśli jest nieobecny.
    - Skrypt prosi o *potwierdzenie*, zanim zastosuje nazwę typu nieobecnego.

### <a name="phases-of-the-script"></a>Fazy skryptu

Oto fazy skryptu programu PowerShell:

1. Zaloguj się do konta platformy Azure, potrzebne tylko raz na ps sesji.  Przypisz zmienne.
2. Wyszukaj sieć wirtualną, a następnie podsieć.
3. Czy podsieć jest oznaczona jako typ serwera punktu końcowego **microsoft.sql?**
4. Dodaj punkt końcowy usługi wirtualnej o nazwie typu **Microsoft.Sql**w podsieci.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu należy edytować wartości przypisane do zmiennych $, w górnej części skryptu.

### <a name="direct-powershell-source-code"></a>Bezpośredni kod źródłowy programu PowerShell

Ten skrypt programu PowerShell nie aktualizuje niczego, chyba że odpowiesz tak, jeśli zostanie wyświetlony prośba o potwierdzenie. Skrypt można dodać nazwę typu **Microsoft.Sql** do podsieci. Ale skrypt próbuje dodać tylko wtedy, gdy podsieć nie ma nazwy typu.

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

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
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

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
