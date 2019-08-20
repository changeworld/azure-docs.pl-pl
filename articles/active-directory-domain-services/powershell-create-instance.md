---
title: Włączanie Azure Active Directory Domain Services przy użyciu programu PowerShell | Microsoft Docs
description: Włączanie Azure Active Directory Domain Services przy użyciu programu PowerShell
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617212"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Włączanie Azure Active Directory Domain Services przy użyciu programu PowerShell
W tym artykule pokazano, jak włączyć usługi domenowe Azure Active Directory (AD) przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Zadanie 1. Instalowanie wymaganych modułów programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i Konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Zadanie 2. Utwórz wymaganą jednostkę usługi w katalogu usługi Azure AD
Wpisz następujące polecenie programu PowerShell, aby utworzyć nazwę główną usługi wymaganą dla Azure AD Domain Services w katalogu usługi Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Zadanie 3. Utwórz i skonfiguruj grupę "Administratorzy kontrolera domeny usługi AAD"
Następnym zadaniem jest utworzenie grupy administratorów, która będzie używana do delegowania zadań administracyjnych w domenie zarządzanej.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Po utworzeniu grupy Dodaj kilka użytkowników do tej grupy.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Zadanie 4. Rejestrowanie dostawcy zasobów Azure AD Domain Services
Wpisz następujące polecenie programu PowerShell, aby zarejestrować dostawcę zasobów dla Azure AD Domain Services:
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

W tej grupie zasobów można utworzyć sieć wirtualną i Azure AD Domain Services domenę zarządzaną.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Zadanie 6: Utwórz i skonfiguruj sieć wirtualną
Teraz Utwórz sieć wirtualną, w której zostanie włączona Azure AD Domain Services. Upewnij się, że tworzysz dedykowaną podsieć dla Azure AD Domain Services. Nie Wdrażaj maszyn wirtualnych obciążeń w tej dedykowanej podsieci.

Wpisz następujące polecenia programu PowerShell, aby utworzyć sieć wirtualną z dedykowaną podsiecią dla Azure AD Domain Services.

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


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Zadanie 7: Inicjowanie obsługi administracyjnej Azure AD Domain Services domeny zarządzanej
Wpisz następujące polecenie programu PowerShell, aby włączyć Azure AD Domain Services dla katalogu:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
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
> **Nie zapomnij dodatkowych kroków konfiguracyjnych po zainicjowaniu obsługi administracyjnej domeny zarządzanej.**
> Po zainicjowaniu obsługi administracyjnej domeny zarządzanej nadal trzeba wykonać następujące zadania:
> * Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania. Aby skonfigurować system DNS, wybierz domenę zarządzaną platformy Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
> * Utwórz wymagane reguły sieciowej grupy zabezpieczeń, aby ograniczyć ruch przychodzący dla domeny zarządzanej. Aby utworzyć reguły sieciowej grupy zabezpieczeń, wybierz domenę zarządzaną platformy Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie odpowiednich reguł sieciowej grupy zabezpieczeń.
> * **[Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="powershell-script"></a>Skrypt programu PowerShell
Skrypt programu PowerShell używany do wykonywania wszystkich zadań wymienionych w tym artykule znajduje się poniżej. Skopiuj skrypt i Zapisz go w pliku z rozszerzeniem ". ps1". Wykonaj skrypt w programie PowerShell lub za pomocą środowiska PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Uprawnienia wymagane do uruchomienia tego skryptu** Aby włączyć Azure AD Domain Services, musisz być administratorem globalnym katalogu usługi Azure AD. Ponadto musisz mieć co najmniej uprawnienia współautora w sieci wirtualnej, w której ma być włączona Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
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
> **Nie zapomnij dodatkowych kroków konfiguracyjnych po zainicjowaniu obsługi administracyjnej domeny zarządzanej.**
> Po zainicjowaniu obsługi administracyjnej domeny zarządzanej nadal trzeba wykonać następujące zadania:
> * Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania. Aby skonfigurować system DNS, wybierz domenę zarządzaną platformy Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
> * Utwórz wymagane reguły sieciowej grupy zabezpieczeń, aby ograniczyć ruch przychodzący dla domeny zarządzanej. Aby utworzyć reguły sieciowej grupy zabezpieczeń, wybierz domenę zarządzaną platformy Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie odpowiednich reguł sieciowej grupy zabezpieczeń.
> * **[Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu domeny zarządzanej wykonaj następujące zadania konfiguracji, aby można było używać domeny zarządzanej:

* [Zaktualizuj ustawienia serwera DNS dla sieci wirtualnej, aby wskazywały na domenę zarządzaną](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Włączanie synchronizacji haseł do domeny zarządzanej](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
