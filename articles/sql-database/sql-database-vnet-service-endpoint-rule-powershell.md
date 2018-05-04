---
title: Środowiska PowerShell dla punktów końcowych usługi sieci wirtualnej i zasady w programie SQL | Dokumentacja firmy Microsoft
description: Udostępnia skrypty programu PowerShell do tworzenia i zarządzania punktami końcowymi usług wirtualnych dla Twojej bazy danych SQL Azure.
services: sql-database
author: MightyPen
manager: jhubbard
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 5fcf33d3d54b118c1c25f1467a496da3644a2345
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database"></a>Utworzyć regułę i punkt końcowy usługi wirtualne w bazie danych SQL Azure przy użyciu programu PowerShell

Ten artykuł zawiera i objaśnienie skrypt programu PowerShell, który wykonuje następujące akcje:

1. Tworzy Microsoft Azure *punktu końcowego usługi wirtualnego* na podsieci.
2. Dodaje punkt końcowy do zapory serwera bazy danych SQL Azure, aby utworzyć *reguły sieci wirtualnej*.

Twoje motywacji dotyczących tworzenia reguły opisano szczegółowo w: [punktów końcowych usługi wirtualne w bazie danych SQL Azure][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Jeśli wszystkie informacje wymagane do oceny lub Dodaj punkt końcowy usługi wirtualnych *nazwy typu* dla bazy danych SQL do podsieci, możesz przejść od razu do naszej więcej [bezpośrednie skrypt programu PowerShell](#a-verify-subnet-is-endpoint-ps-100).

#### <a name="major-cmdlets"></a>Polecenia cmdlet główna

W tym artykule jest kładzie nacisk polecenia cmdlet, o nazwie **AzureRmSqlServerVirtualNetworkRule nowy**, która dodaje punkt końcowy podsieci do listy kontroli dostępu (ACL) serwera bazy danych SQL Azure, tworząc regułę.

Na poniższej liście przedstawiono sekwencję innych *głównych* poleceń cmdlet, które należy uruchomić, aby przygotować się do wywołania do **AzureRmSqlServerVirtualNetworkRule nowy**. W tym artykule występować w tych wywołań [skryptu 3 "zasady sieci wirtualnej"](#a-script-30):

1. [Nowy AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): tworzy obiekt podsieci.

2. [Nowy-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): tworzy sieci wirtualnej, podając go w podsieci.

3. [Zestaw AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): przypisuje punkt końcowy usługi wirtualnych do podsieci.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): będzie się powtarzał aktualizacje wprowadzone w Twojej sieci wirtualnej.

5. [Nowy AzureRmSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlservervirtualnetworkrule): po podsieć jest punktem końcowym, dodaje podsieć zasadzie sieci wirtualnej do listy kontroli dostępu serwera bazy danych SQL Azure.
    - Oferuje parametr **- IgnoreMissingVnetServiceEndpoint**, rozpoczynając modułu programu PowerShell Menedżera zasobów Azure wersji 5.1.1.

#### <a name="prerequisites-for-running-powershell"></a>Wymagania wstępne dotyczące uruchamiania programu PowerShell

- Możesz już zalogować na platformie Azure, takich jak za pomocą [portalu Azure][http-azure-portal-link-ref-477t].
- Już może uruchamiać skrypty programu PowerShell.

> [!NOTE]
> Upewnij się, że punkty końcowe usługi są włączone dla sieci wirtualnej/podsieci, w której mają zostać dodane do serwera w przeciwnym razie tworzenie reguły zapory w sieci wirtualnej zakończą się niepowodzeniem.

#### <a name="one-script-divided-into-four-chunks"></a>Skrypt jest podzielony na cztery fragmentów

Nasze pokaz skryptu programu PowerShell jest podzielona na sekwencję mniejszych skryptów. Podział ułatwia uczenie i zapewnia elastyczność. Skrypty można uruchomić w ich sekwencyjny. Jeśli nie masz teraz czas uruchamiania skryptów, wyjście rzeczywiste testu jest wyświetlany po skryptu 4.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Skrypt 1: zmiennych

Ten pierwszy skrypt programu PowerShell przypisuje wartości zmiennych. Kolejne skrypty są zależne od tych zmiennych.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu można edytować wartości, jeśli chcesz. Na przykład jeśli masz już grupę zasobów, można edytować nazwę grupy zasobów jako przypisaną wartość.
>
>  Należy zmodyfikować nazwę subskrypcji do skryptu.

#### <a name="powershell-script-1-source-code"></a>Kod źródłowy skryptu 1 programu PowerShell

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

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

## <a name="script-2-prerequisites"></a>Skrypt 2: wymagania wstępne

Skrypt ten przygotowuje się do dalej skryptu, gdzie jest akcja punktu końcowego. Ten skrypt tworzy następujące wymienionych elementów, ale tylko wtedy, jeśli jeszcze nie istnieje. Skrypt 2 można pominąć, jeśli masz pewność, że te elementy już istnieje:

- Grupa zasobów platformy Azure
- Serwer bazy danych SQL Azure

#### <a name="powershell-script-2-source-code"></a>Kod źródłowy 2 skryptu PowerShell

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
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

$sqlDbServer = Get-AzureRmSqlServer `
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

    $sqlDbServer = New-AzureRmSqlServer `
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

Ten skrypt tworzy sieć wirtualną z podsiecią. A następnie przypisuje skrypt **Microsoft.Sql** typ punktu końcowego do podsieci. Na koniec skrypt ten dodaje podsieć do listy kontroli dostępu (ACL) serwera bazy danych SQL, tworząc regułę.

#### <a name="powershell-script-3-source-code"></a>Kod źródłowy skryptu 3 programu PowerShell

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skrypt 4: Wyczyść

Ten skrypt końcowy usuwa zasoby utworzone przez wcześniejsze skrypty do pokazu. Jednak skrypt wyświetlona prośba o potwierdzenie przed usunięciem następujące czynności:

- Serwer bazy danych SQL Azure
- Grupy zasobów platformy Azure

Skrypt 4 można uruchomić w dowolnej chwili po ukończeniu działania skryptu 1.

#### <a name="powershell-script-4-source-code"></a>Kod źródłowy 4 skrypt programu PowerShell

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
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
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
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

## <a name="actual-output-from-scripts-1-through-4"></a>Rzeczywiste dane wyjściowe skryptów od 1 do 4

Dane wyjściowe z naszych uruchomienia testu jest wyświetlany obok, w formacie skróconym. Dane wyjściowe mogą być przydatne w przypadku, gdy nie chcesz faktycznie teraz uruchamiać skrypty programu PowerShell.

```
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

Jest to koniec naszym głównym skrypt programu PowerShell.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Sprawdź, czy podsieć jest punkt końcowy

Może być podsieć, która została już przypisana **Microsoft.Sql** nazwę typu, co oznacza, jest on już punkt końcowy usługi wirtualnego. Można użyć [portalu Azure] [ http-azure-portal-link-ref-477t] do utworzenia reguły sieci wirtualnej z punktu końcowego.

Lub może się nie wiesz, czy ma podsieć **Microsoft.Sql** nazwy typu. Można uruchomić poniższy skrypt programu PowerShell, aby wykonać następujące akcje:

1. Upewnić się, czy podsieć ma **Microsoft.Sql** nazwy typu.
2. Opcjonalnie można przypisać nazwy typu, jeśli jest nieobecny.
    - Skrypt prośba o *potwierdzić*, przed zastosowaniem nieobecne typu nazwa.

#### <a name="phases-of-the-script"></a>Etapy skryptu

Oto etapy skrypt programu PowerShell:

1. ZALOGUJ się do konta platformy Azure, potrzebne tylko raz na sesję PS.  Przypisz zmiennych.
2. Wyszukaj dla sieci wirtualnej, a następnie do podsieci.
3. Podsieć jest oznaczone jako **Microsoft.Sql** typ punktu końcowego serwera?
4. Dodaj punkt końcowy usługi wirtualnej nazwy typu **Microsoft.Sql**, na podsieci.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu, należy edytować wartości przypisane do $zmiennych w górnej części skryptu.

#### <a name="direct-powershell-source-code"></a>Bezpośrednie kodu źródłowego programu PowerShell

Jest to środowiska PowerShell skryptu nie żadnych aktualizacji, chyba że odpowiedź tak, jeśli zostanie wyświetlona prośba o potwierdzenie. Skryptu można dodać nazwę typu **Microsoft.Sql** do podsieci. Ale skrypt próbuje Dodaj tylko wtedy, gdy podsieć nie ma nazwy typu.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
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

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Rzeczywiste dane wyjściowe

Poniższy blok Wyświetla naszych rzeczywiste Reaguj (bardzo drobny zmiany).

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

