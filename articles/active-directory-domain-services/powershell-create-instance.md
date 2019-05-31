---
title: Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: ergreenl
ms.openlocfilehash: f2c4f73af00e0093ce98f2de37e9c3a0ba381eda
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246857"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell
W tym artykule przedstawiono sposób włączania usług domenowych Azure Active Directory (AD) przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Zadanie 1. Zainstaluj wymagane moduły programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure AD PowerShell i łączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Zadanie 2. Tworzenie jednostki usługi wymagane w katalogu usługi Azure AD
Wpisz następujące polecenie programu PowerShell, aby utworzyć jednostkę usługi, wymagane dla usług domenowych Azure AD w katalogu usługi Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Zadanie 3. Tworzenie i Konfigurowanie grupy "Administratorzy usługi AAD DC"
Kolejnym zadaniem jest tworzenie grupy administratorów, która będzie służyć do delegowania zadań administracyjnych w domenie zarządzanej.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Teraz, po utworzeniu grupy, należy dodać kilka użytkowników do grupy.
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

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Zadanie 4. Zarejestruj dostawcę zasobów usługi Azure AD Domain Services
Wpisz następujące polecenie programu PowerShell można zarejestrować dostawcy zasobów dla usług domenowych Azure AD:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Zadanie 5. Tworzenie grupy zasobów
Wpisz następujące polecenie programu PowerShell, aby utworzyć grupę zasobów:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

W tej grupie zasobów, można utworzyć sieć wirtualną i domeny zarządzanej usług domenowych Azure AD.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Zadanie 6. Tworzenie i konfigurowanie sieci wirtualnej
Teraz Utwórz sieć wirtualną, w którym są włączone usługi domenowe Azure AD. Upewnij się, Utwórz dedykowane podsieć dla usługi Azure AD Domain Services. Nie należy wdrażać obciążenia maszyn wirtualnych w tej podsieci dedykowanych.

Wpisz następujące polecenia programu PowerShell, aby utworzyć sieć wirtualną za pomocą dedykowanej podsieci dla usługi Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Zadanie 7. Aprowizowanie domeny zarządzanej usług domenowych Azure AD
Wpisz następujące polecenie programu PowerShell, aby włączyć usług domenowych Azure AD dla katalogu:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nie zapomnij dodatkowe kroki konfiguracji po zainicjowaniu obsługi administracyjnej Twojej domeny zarządzanej.**
> Po zaaprowizowaniu domeny zarządzanej, musisz wykonać następujące zadania:
> * **[Aktualizowanie ustawień DNS](active-directory-ds-getting-started-dns.md)**  dla sieci wirtualnej, dzięki czemu maszyny wirtualne można znaleźć domeny zarządzanej dla przyłączanie do domeny lub uwierzytelniania.
> * **[Włączanie synchronizacji haseł w usłudze Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)** , dzięki czemu użytkownicy końcowi zalogować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.


## <a name="powershell-script"></a>Skrypt programu PowerShell
Skrypt programu PowerShell umożliwia wykonywanie wszystkich zadań wymienione w tym artykule znajduje się poniżej. Skopiuj skrypt i zapisz go jako plik z rozszerzeniem ".ps1". Uruchom skrypt w programie PowerShell lub przy użyciu programu PowerShell zintegrowane Scripting Environment (ISE).

> [!NOTE]
> **Uprawnienia wymagane do uruchamiania tego skryptu** Aby włączyć usługi domenowe Azure AD, musisz być administratorem globalnym katalogu usługi Azure AD. Ponadto należy co najmniej uprawnienia "Współautor" w sieci wirtualnej, w którym Włączanie usług domenowych Azure AD.
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
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

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
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nie zapomnij dodatkowe kroki konfiguracji po zainicjowaniu obsługi administracyjnej Twojej domeny zarządzanej.**
> Po zaaprowizowaniu domeny zarządzanej, musisz wykonać następujące zadania:
> * Aktualizowanie ustawień DNS dla sieci wirtualnej, dzięki czemu maszyny wirtualne można znaleźć domeny zarządzanej dla przyłączanie do domeny lub uwierzytelniania.
> * Włączanie synchronizacji haseł usługi Azure AD Domain Services, dzięki czemu użytkownicy końcowi zalogować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu Twojej domeny zarządzanej, należy wykonać poniższe zadania konfiguracji, aby można było używać domeny zarządzanej:

* [Zaktualizuj ustawienia serwera DNS dla sieci wirtualnej, aby wskazywały do domeny zarządzanej](active-directory-ds-getting-started-dns.md)
* [Włączanie synchronizacji haseł do domeny zarządzanej](active-directory-ds-getting-started-password-sync.md)
