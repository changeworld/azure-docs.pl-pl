---
title: Tworzenie maszyny wirtualnej w laboratoriach devtest za pomocą programu Azure PowerShell
description: Dowiedz się, jak używać laboratoriów DevTest Labs platformy Azure do tworzenia maszyn wirtualnych i zarządzania nimi za pomocą programu Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167108"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Tworzenie maszyny wirtualnej w laboratoriach DevTest przy użyciu programu Azure PowerShell
W tym artykule pokazano, jak utworzyć maszynę wirtualną w laboratoriach devtest platformy Azure przy użyciu programu Azure PowerShell. Skrypty programu PowerShell umożliwiają automatyzację tworzenia maszyn wirtualnych w laboratorium w laboratoriach DevTest. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

- [Utwórz laboratorium,](devtest-lab-create-lab.md) jeśli nie chcesz używać istniejącego laboratorium do testowania skryptu lub poleceń w tym artykule. 
- [Zainstaluj usługę Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) lub użyj usługi Azure Cloud Shell zintegrowanej z witryną Azure portal. 

## <a name="powershell-script"></a>Skrypt programu PowerShell
Przykładowy skrypt w tej sekcji używa polecenia cmdlet [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  To polecenie cmdlet przyjmuje identyfikator zasobu laboratorium, nazwę`createEnvironment`akcji do wykonania ( ) i parametry niezbędne do wykonania tej akcji. Parametry znajdują się w tabeli mieszania, która zawiera wszystkie właściwości opisu maszyny wirtualnej. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Właściwości maszyny wirtualnej w powyższym skrypcie umożliwiają nam utworzenie maszyny wirtualnej z centrum danych systemu Windows Server 2016 jako systemem operacyjnym. Dla każdego typu maszyny wirtualnej te właściwości będą nieco inne. W sekcji [Definiuj maszynę wirtualną](#define-virtual-machine) pokazano, jak określić właściwości, które mają być używane w tym skrypcie.

Następujące polecenie zawiera przykład uruchomienia skryptu zapisanego w nazwie pliku: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definiowanie maszyny wirtualnej
W tej sekcji pokazano, jak uzyskać właściwości, które są specyficzne dla typu maszyny wirtualnej, który chcesz utworzyć. 

### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia maszyny wirtualnej w witrynie Azure portal można wygenerować szablon usługi Azure Resource Manager. Nie trzeba ukończyć proces tworzenia maszyny Wirtualnej. Postępuj zgodnie z instrukcjami, dopóki nie zobaczysz szablonu. Jest to najlepszy sposób, aby uzyskać opis JSON niezbędne, jeśli nie masz jeszcze laboratorium maszyny Wirtualnej utworzone. 

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi** w lewym menu nawigacyjnym.
3. Wyszukaj i wybierz **DevTest Labs** z listy usług. 
4. Na stronie **DevTest Labs** wybierz laboratorium na liście laboratoriów.
5. Na stronie głównej laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 
6. Wybierz **obraz bazowy** maszyny Wirtualnej. 
7. Wybierz **opcje automatyzacji** u dołu strony powyżej przycisku **Prześlij.** 
8. Zostanie wyświetlony **szablon usługi Azure Resource Manager** do tworzenia maszyny wirtualnej. 
9. Segment JSON w sekcji **zasoby** zawiera definicję typu obrazu wybranego wcześniej. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

W tym przykładzie można zobaczyć, jak uzyskać definicję obrazu usługi Azure Market Place. Definicję niestandardowego obrazu, formuły lub środowiska można uzyskać w ten sam sposób. Dodaj wszystkie artefakty potrzebne dla maszyny wirtualnej i ustaw wszystkie wymagane ustawienia zaawansowane. Po podaniu wartości dla wymaganych pól i wszystkich pól opcjonalnych, przed wybraniem przycisku **Opcje automatyzacji.**

### <a name="use-azure-rest-api"></a>Korzystanie z interfejsu API usługi Azure REST
Poniższa procedura zawiera kroki, aby uzyskać właściwości obrazu przy użyciu interfejsu API REST: Te kroki działają tylko dla istniejącej maszyny Wirtualnej w laboratorium. 

1. Przejdź do strony [listy Maszyny wirtualne ,](/rest/api/dtl/virtualmachines/list) wybierz przycisk **Wypróbuj.** 
2. Wybierz swoją **subskrypcję platformy Azure**.
3. Wprowadź **grupę zasobów dla laboratorium**.
4. Wprowadź **nazwę laboratorium**. 
5. Wybierz pozycję **Uruchom**.
6. Widoczne **są właściwości obrazu,** na podstawie którego utworzono maszynę wirtualną. 

## <a name="set-expiration-date"></a>Ustawianie daty wygaśnięcia
W scenariuszach, takich jak szkolenia, pokazy i wersje próbne, można utworzyć maszyny wirtualne i usunąć je automatycznie po stałym czasie trwania, tak aby nie ponosić niepotrzebnych kosztów. Można ustawić datę wygaśnięcia maszyny Wirtualnej podczas tworzenia go przy użyciu programu PowerShell, jak pokazano w przykładzie sekcji [skryptu programu PowerShell.](#powershell-script)

Oto przykładowy skrypt programu PowerShell, który ustawia datę wygaśnięcia dla wszystkich istniejących maszyn wirtualnych w laboratorium:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Następne kroki
Zobacz następującą zawartość: [Dokumentacja programu Azure PowerShell dla laboratoriów devtest platformy Azure](/powershell/module/az.devtestlabs/)
