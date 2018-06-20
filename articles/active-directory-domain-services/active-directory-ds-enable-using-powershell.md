---
title: Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: acae942131903aa86601f023976b0715bf553d4d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215035"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell
W tym artykule przedstawiono sposób Włączanie usług domenowych Azure Active Directory (AD) przy użyciu programu PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Zadanie 1: Instalowanie wymaganych modułów programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i konfigurowanie programu Azure AD PowerShell.
Postępuj zgodnie z instrukcjami w artykule [zainstalować moduł Azure AD PowerShell i łączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule [zainstalować moduł Azure PowerShell i nawiązać połączenia z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Zadanie 2: Tworzenie wymagana usługa podmiotu zabezpieczeń w katalogu usługi Azure AD
Wpisz następujące polecenie programu PowerShell, aby zapisać nazwy głównej usługi wymagane dla usług domenowych Azure AD w katalogu usługi Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Zadanie 3: Tworzenie i Konfigurowanie grupy "Administratorzy kontrolera domeny usługi AAD"
Następne zadanie to można utworzyć grupy administratorów, która będzie służyć do delegowania zadań administracyjnych na domeny zarządzanej.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Teraz, po utworzeniu grupy, dodać kilka użytkowników do grupy.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Zadanie 4: Zarejestruj dostawcy zasobów usługi domenowe Azure AD
Wpisz następujące polecenie programu PowerShell, aby zarejestrować dostawcy zasobów dla usług domenowych Azure AD:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Zadanie 5: Tworzenie grupy zasobów
Wpisz następujące polecenie programu PowerShell, aby utworzyć grupę zasobów:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

W tej grupie zasobów, można utworzyć sieci wirtualnej i domeny zarządzanej usług domenowych Azure AD.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Zadanie 6: Tworzenie i konfigurowanie sieci wirtualnej
Teraz Utwórz sieć wirtualną, w którym można włączyć usługi domenowe Azure AD. Upewnij się, Utwórz dedykowane podsieć dla usług domenowych Azure AD. Nie należy wdrażać obciążenia maszyn wirtualnych w tej podsieci dedykowanego.

Wpisz następujące polecenia programu PowerShell, aby utworzyć sieć wirtualną z podsiecią dedykowane dla usług domenowych Azure AD.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Zadanie 7: Udostępnianie domeny zarządzanej usług domenowych Azure AD
Wpisz następujące polecenie programu PowerShell, aby włączyć usługi domenowe Azure AD dla katalogu:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nie zapomnij dodatkowe kroki konfiguracji po zainicjowaniu obsługi administracyjnej domeny zarządzanej.**
> Po udostępnieniu domeny zarządzanej, nadal należy wykonać następujące zadania:
> * **[Aktualizowanie ustawień DNS](active-directory-ds-getting-started-dns.md)**  dla sieci wirtualnej, maszyn wirtualnych można znaleźć domeny zarządzanej do przyłączania do domeny lub uwierzytelniania.
* **[Włączanie synchronizacji haseł w usługach domenowych Azure AD](active-directory-ds-getting-started-password-sync.md)**, przez co użytkownicy końcowi mogą Zaloguj się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.
>


## <a name="powershell-script"></a>Skrypt programu PowerShell
Skrypt programu PowerShell, używany do wykonywania wszystkich zadań wymienione w tym artykule jest niższa. Skopiuj skrypt i zapisz go jako plik z rozszerzeniem ".ps1". Uruchom skrypt programu PowerShell lub przy użyciu programu PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Uprawnienia wymagane, aby uruchomić ten skrypt** Aby włączyć usługi domenowe Azure AD, musisz być administratorem globalnym katalogu usługi Azure AD. Ponadto należy co najmniej uprawnienia "Współautora" sieci wirtualnej, w którym włączyć usługi domenowe Azure AD.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nie zapomnij dodatkowe kroki konfiguracji po zainicjowaniu obsługi administracyjnej domeny zarządzanej.**
> Po udostępnieniu domeny zarządzanej, nadal należy wykonać następujące zadania:
> * Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne można znaleźć domeny zarządzanej do przyłączania do domeny lub uwierzytelniania.
* Włączanie synchronizacji haseł w usługach domenowych Azure AD, użytkownicy końcowi mogą Zaloguj się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.
>

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu domeny zarządzanej, należy wykonać poniższe zadania konfiguracji, aby można było używać domeny zarządzanej:

* [Zaktualizuj ustawienia serwera DNS dla sieci wirtualnej, aby wskazywał domeny zarządzanej](active-directory-ds-getting-started-dns.md)
* [Włączanie synchronizacji haseł do domeny zarządzanej](active-directory-ds-getting-started-password-sync.md)
