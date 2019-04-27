---
title: Utwórz maszynę wirtualną w usłudze DevTest Labs przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać maszynami wirtualnymi przy użyciu programu Azure PowerShell za pomocą usługi Azure DevTest Labs.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622443"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Utwórz maszynę wirtualną z usługą DevTest Labs przy użyciu programu Azure PowerShell
W tym artykule pokazano, jak utworzyć maszynę wirtualną w usłudze Azure DevTest Labs przy użyciu programu Azure PowerShell. Można użyć skryptów programu PowerShell, aby zautomatyzować tworzenie maszyn wirtualnych w laboratorium Azure DevTest Labs. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

- [Tworzenie laboratorium](devtest-lab-create-lab.md) Jeśli nie chcesz używać istniejącego laboratorium do testowania skryptów lub poleceń, w tym artykule. 
- [Zainstaluj program Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) lub za pomocą usługi Azure Cloud Shell, która jest zintegrowana w witrynie Azure portal. 

## <a name="powershell-script"></a>Skrypt programu PowerShell
Przykładowy skrypt w tej sekcji używa [Invoke AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) polecenia cmdlet.  To polecenie cmdlet przyjmuje identyfikator zasobu w laboratorium, Nazwa akcji do wykonania (`createEnvironment`), a niezbędne parametry wykonania tej akcji. Parametry są w tabeli wyznaczania wartości skrótu, który zawiera wszystkie właściwości Opis maszyny wirtualnej. 

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

Właściwości maszyny wirtualnej w skrypcie powyżej umożliwiają tworzenie maszyny wirtualnej z systemem Windows Server 2016 DataCenter jako systemu operacyjnego. Dla każdego typu maszyny wirtualnej te właściwości może się nieco różnić. [Definicja maszyny wirtualnej](#define-virtual-machine) sekcji dowiesz się, jak ustalić, które właściwości mają zostać użyte w tym skrypcie.

Następujące polecenie stanowi przykład uruchomienie skryptu zapisane w nazwie pliku: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definicja maszyny wirtualnej
W tej sekcji dowiesz się, jak uzyskać właściwości, które są specyficzne dla typu maszyny wirtualnej, która ma zostać utworzona. 

### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia maszyny Wirtualnej w witrynie Azure portal, można wygenerować szablonu usługi Azure Resource Manager. Nie potrzebujesz ukończyć proces tworzenia maszyny Wirtualnej. Możesz tylko postępuj zgodnie z instrukcjami aż do wyświetlenia tego szablonu. Jest to najlepszy sposób pozyskania niezbędne opisu JSON, jeśli nie masz już utworzonych maszyn wirtualnych laboratorium. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w menu nawigacji po lewej stronie.
3. Wyszukaj i wybierz pozycję **DevTest Labs** z listy usług. 
4. Na **DevTest Labs** wybierz laboratorium na liście laboratoriów.
5. Na stronie głównej dla swojego laboratorium wybierz **+ Dodaj** na pasku narzędzi. 
6. Wybierz **obrazu podstawowego** dla maszyny Wirtualnej. 
7. Wybierz **opcje automatyzacji** w dolnej części strony powyżej **przesyłania** przycisku. 
8. Zostanie wyświetlony **szablonu usługi Azure Resource Manager** podczas tworzenia maszyny wirtualnej. 
9. JSON segment **zasobów** sekcja zawiera definicję dla typu obrazu wybrano wcześniej. 

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

W tym przykładzie widać sposób uzyskiwania definicji obrazu platformę handlową platformy Azure. Definicja niestandardowego obrazu, formuły lub w środowisku można rozpocząć w taki sam sposób. Dodaj wszelkie artefakty potrzebne dla maszyny wirtualnej i ustaw żądane ustawienia zaawansowane wymagane. Po podaniu wartości dla pola wymagane i opcjonalne pola, wybierając przed **opcje automatyzacji** przycisku.

### <a name="use-azure-rest-api"></a>Za pomocą interfejsu API REST platformy Azure
Poniższa procedura zawiera kroki, aby pobrać właściwości obrazu za pomocą interfejsu API REST: Następujące kroki działają tylko w przypadku istniejącej maszyny Wirtualnej w laboratorium. 

1. Przejdź do [listy maszyn wirtualnych —](/rest/api/dtl/virtualmachines/list) wybierz opcję **wypróbuj** przycisku. 
2. Wybierz swoją **subskrypcję** platformy Azure.
3. Wprowadź **grupę zasobów dla laboratorium**.
4. Wprowadź **Nazwa laboratorium**. 
5. Wybierz pozycję **Uruchom**.
6. Zostanie wyświetlony **właściwości obrazu** oparte na którym utworzono maszynę Wirtualną. 



## <a name="next-steps"></a>Kolejne kroki
Zobacz następującą zawartość: [Dokumentacja usługi Azure PowerShell dla usługi Azure DevTest Labs](/powershell/module/az.devtestlabs/)
