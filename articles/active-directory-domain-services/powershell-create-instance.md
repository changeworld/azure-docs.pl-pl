---
title: Włączanie usług domenowych usług domenowych usług Firmy Azure DS przy użyciu programu PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować i włączyć usługi domenowe Active Directory platformy Azure przy użyciu programu Azure AD PowerShell i programu Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613238"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Włączanie usług domenowych active directory platformy Azure przy użyciu programu PowerShell

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Te usługi domeny są używane bez samodzielnego wdrażania, zarządzania i instalowania poprawek kontrolerów domeny. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych i można użyć istniejących grup i kont użytkowników, aby zabezpieczyć dostęp do zasobów.

W tym artykule pokazano, jak włączyć usługi Azure AD DS przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące zasoby:

* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure PowerShell i połączyć się z subskrypcją platformy Azure.](/powershell/azure/install-az-ps)
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instalowanie i konfigurowanie programu Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure AD PowerShell i połączyć się z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Aby włączyć usługi Azure AD DS, potrzebujesz uprawnień *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usług Azure AD DS, potrzebujesz uprawnień *współautora* w ramach subskrypcji platformy Azure.

## <a name="create-required-azure-ad-resources"></a>Tworzenie wymaganych zasobów usługi Azure AD

Usługa Azure AD DS wymaga jednostki usługi i grupy usługi Azure AD. Te zasoby umożliwiają domenie zarządzanej usług Azure AD DS synchronizowanie danych i definiowanie, którzy użytkownicy mają uprawnienia administracyjne w domenie zarządzanej.

Najpierw utwórz jednostkę usługi Azure AD dla usług Azure AD DS, aby komunikować się i uwierzytelniać. Identyfikator określonej aplikacji jest używany o nazwie *Domain Controller Services* o identyfikatorze *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Nie zmieniaj tego identyfikatora aplikacji.

Utwórz jednostkę usługi Azure AD przy użyciu polecenia cmdlet [New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teraz utwórz grupę usługi Azure AD o nazwie *Administratorzy kontrolera domeny usługi AAD*. Użytkownicy dodani do tej grupy otrzymują uprawnienia do wykonywania zadań administracyjnych w domenie zarządzanej usług Azure AD DS.

Utwórz grupę *Administratorzy kontrolera domeny usługi AAD* przy użyciu polecenia cmdlet [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Po utworzeniu grupy *Administratorzy kontrolera domeny usługi AAD* dodaj użytkownika do grupy przy użyciu polecenia cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Najpierw otrzymasz identyfikator obiektu grupy *administratorów kontrolera domeny usługi AAD* przy użyciu polecenia cmdlet [Get-AzureADGroup,][Get-AzureADGroup] a następnie identyfikator obiektu żądanego użytkownika przy użyciu polecenia cmdlet [Get-AzureADUser.][Get-AzureADUser]

W poniższym przykładzie identyfikator obiektu użytkownika dla konta `admin@aaddscontoso.onmicrosoft.com`z omdyną UPN . Zastąp to konto użytkownika numerem UPN użytkownika, którego chcesz dodać do grupy *Administratorzy kontrolera domeny usługi AAD:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Tworzenie zasobów platformy Azure

Najpierw zarejestruj dostawcę zasobów usług domenowych usługi Azure AD przy użyciu polecenia cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Następnie utwórz grupę zasobów przy użyciu polecenia cmdlet [New-AzResourceGroup.][New-AzResourceGroup] W poniższym przykładzie grupa zasobów nosi nazwę *myResourceGroup* i jest tworzona w regionie *westus.* Użyj własnej nazwy i żądanego regionu:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Utwórz sieć wirtualną i podsieci dla usług domenowych usługi Azure AD. Two subsieci są tworzone - jedna dla *DomainServices*i jedna dla *obciążeń*. Usługi Azure AD DS jest wdrażany w dedykowanej *podsieci DomainServices.* Nie wdrażaj innych aplikacji lub obciążeń w tej podsieci. Użyj *oddzielnych obciążeń* lub innych podsieci dla pozostałych maszyn wirtualnych.

Utwórz podsieci przy użyciu polecenia cmdlet [New-AzVirtualNetworkSubnetConfig,][New-AzVirtualNetworkSubnetConfig] a następnie utwórz sieć wirtualną przy użyciu polecenia cmdlet [New-AzVirtualNetwork.][New-AzVirtualNetwork]

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Tworzenie domeny zarządzanej usług Azure AD DS

Teraz utwórzmy domenę zarządzaną usługą Azure AD DS. Ustaw identyfikator subskrypcji platformy Azure, a następnie podaj nazwę domeny zarządzanej, takiej jak *aaddscontoso.com*. Identyfikator subskrypcji można uzyskać za pomocą polecenia cmdlet [Get-AzSubscription.][Get-AzSubscription]

Jeśli wybierzesz region, który obsługuje strefy dostępności, zasoby usługi Azure AD DS są dystrybuowane między strefami w celu uzyskania dodatkowej redundancji.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

Nie ma nic do skonfigurowania dla usługi Azure AD DS, które mają być dystrybuowane w różnych strefach. Platforma Azure automatycznie obsługuje dystrybucji strefy zasobów. Aby uzyskać więcej informacji i wyświetlić dostępność regionu, zobacz [Co to są strefy dostępności na platformie Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Utworzenie zasobu i zwrócenie kontroli do monitu programu PowerShell zajmuje kilka minut. Domena zarządzana usługą Azure AD DS nadal jest aprowizowana w tle i może potrwać do godziny, aby zakończyć wdrożenie. W witrynie Azure portal na stronie **Przegląd** domeny zarządzanej usług Azure AD DS jest wyświetlany bieżący stan na tym etapie wdrażania.

Gdy w witrynie Azure Portal zostanie wykazanie, że domena zarządzana usługą Azure AD DS zakończyła inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzana dla dołączania do domeny lub uwierzytelniania.
    * Aby skonfigurować usługę DNS, wybierz domenę zarządzana usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną usługą Azure AD DS w regionie obsługującym strefy dostępności, utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej usług Azure AD DS. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga umieszczenia tych reguł. Ta sieciowa grupa zabezpieczeń zabezpiecza usługi Azure AD DS i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć sieciową grupę zabezpieczeń i wymagane reguły, wybierz domenę zarządzaną usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł z usługami domenowymi usługi Azure AD, aby](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

## <a name="complete-powershell-script"></a>Ukończ skrypt programu PowerShell

Poniższy kompletny skrypt programu PowerShell łączy wszystkie zadania pokazane w tym artykule. Skopiuj skrypt i zapisz `.ps1` go w pliku z rozszerzeniem. Uruchom skrypt w lokalnej konsoli programu PowerShell lub [w usłudze Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Aby włączyć usługi Azure AD DS, musisz być administratorem globalnym dla dzierżawy usługi Azure AD. Potrzebujesz również co najmniej uprawnień *współautora* w subskrypcji platformy Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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
  -Force -Verbose
```

Utworzenie zasobu i zwrócenie kontroli do monitu programu PowerShell zajmuje kilka minut. Domena zarządzana usługą Azure AD DS nadal jest aprowizowana w tle i może potrwać do godziny, aby zakończyć wdrożenie. W witrynie Azure portal na stronie **Przegląd** domeny zarządzanej usług Azure AD DS jest wyświetlany bieżący stan na tym etapie wdrażania.

Gdy w witrynie Azure Portal zostanie wykazanie, że domena zarządzana usługą Azure AD DS zakończyła inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzana dla dołączania do domeny lub uwierzytelniania.
    * Aby skonfigurować usługę DNS, wybierz domenę zarządzana usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną usługą Azure AD DS w regionie obsługującym strefy dostępności, utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej usług Azure AD DS. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga umieszczenia tych reguł. Ta sieciowa grupa zabezpieczeń zabezpiecza usługi Azure AD DS i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć sieciową grupę zabezpieczeń i wymagane reguły, wybierz domenę zarządzaną usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł z usługami domenowymi usługi Azure AD, aby](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić domenę zarządzaną usługą Azure AD DS w akcji, możesz [dołączyć do maszyny Wirtualnej systemu Windows,][windows-join] [skonfigurować bezpieczną usługę LDAP][tutorial-ldaps]i [skonfigurować synchronizację skrótów haseł][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
