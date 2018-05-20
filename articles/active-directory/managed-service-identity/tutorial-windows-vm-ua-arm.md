---
title: Umożliwia dostęp do usługi Azure Resource Manager przypisane MSI użytkownika maszyny Wirtualnej systemu Windows
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu użytkownika przypisane zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Windows, można uzyskać dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Samouczek: Użyj użytkownika przypisane zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Windows, można uzyskać dostępu do usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku wyjaśniono, jak utworzyć przypisane tożsamości użytkownika, przypisz je do maszyny wirtualnej systemu Windows (VM), a następnie użyj tej tożsamości można uzyskać dostępu do interfejsu API Azure Resource Manager. Azure automatycznie zarządza zarządzanych tożsamości usługi. Umożliwiają one uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności osadzania poświadczeń w kodzie. 

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows 
> * Utwórz użytkownika przypisane tożsamości
> * Przypisz użytkownika przypisane tożsamości do maszyny Wirtualnej systemu Windows
> * Udziel dostępu do tożsamości użytkownika przypisanego do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskaj token dostępu przy użyciu użytkownika z przypisanym tożsamości i użyć go do wywołania usługi Azure Resource Manager 
> * Odczytywania właściwości grupy zasobów

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś doświadczenia w pracy z zarządzanych tożsamość usługi zapoznaj się z [omówienie](overview.md) sekcji. **Należy przejrzeć [różnice między systemu i użytkownika przypisane tożsamości](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do przeprowadzenia tworzenia wymaganych zasobów oraz czynności administracyjne roli w ramach tego samouczka, Twoje konto wymaga uprawnień "Właściciela" w zakresie odpowiedniego (Twojej subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy przy przypisania roli, zobacz [Use Role-Based kontroli dostępu, aby zarządzać dostępem do zasobów subskrypcji platformy Azure](/azure/role-based-access-control/role-assignments-portal).

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu wersji 5.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

W poniższym przykładzie, grupy zasobów o nazwie *myResourceGroupVM* jest tworzony w *EastUS* regionu.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Po utworzeniu grupy zasobów, należy utworzyć Maszynę wirtualną systemu Windows.

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Utwórz użytkownika przypisane tożsamości

Użytkownik, któremu przypisano tożsamości jest tworzony jako autonomiczny zasobów platformy Azure. Przy użyciu [AzureRmUserAssignedIdentity nowy](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, który można przypisać do co najmniej jedno wystąpienie usługi Azure.

> [!IMPORTANT]
> Tworzenie tożsamości użytkowników przypisanych obsługuje tylko alfanumeryczne i łączniki (0-9 lub a-z lub A-Z lub -) znaków. Ponadto nazwa powinna być ograniczona do 24 znaków do przypisania do maszyny Wirtualnej/VMSS działała poprawnie. Sprawdzanie dostępności aktualizacji. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Odpowiedź zawiera szczegóły dotyczące użytkownika z przypisanym tożsamości utworzone, podobnie do poniższego przykładu. Uwaga `Id` wartość dla tożsamości użytkownika przypisane przez użytkownika, jak będzie on używany w następnym kroku:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Przypisz użytkownika z przypisanym tożsamości do maszyny Wirtualnej systemu Windows

Użytkownik, któremu przypisano tożsamości można przez klientów na wielu zasobów platformy Azure. Użyj następujących poleceń, aby przypisać tożsamość użytkownika przypisane do jednej maszyny Wirtualnej. Użyj `Id` właściwości zwracane w poprzednim kroku dla `-IdentityID` parametru.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Przyznaj użytkownikowi przypisane MSI dostępu do grupy zasobów w usłudze Azure Resource Manager 

Tożsamość usługi zarządzanej (MSI) zawiera tożsamości, które kod służy do żądania tokenów dostępu do uwierzytelniania zasobu interfejsy API uwierzytelniania pomocy technicznej usługi Azure AD. W tym samouczku kodzie będą uzyskiwać dostęp do interfejsu API Azure Resource Manager. 

Przed kodu dostępu do interfejsu API, musisz przyznać dostęp tożsamości do zasobu w usłudze Azure Resource Manager. W takim przypadku grupę zasobów, w którym znajduje się maszyna wirtualna. Zaktualizuj wartość `<SUBSCRIPTION ID>` jako odpowiednie dla danego środowiska.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Odpowiedź zawiera szczegóły dotyczące przypisania roli utworzone, podobnie do poniższego przykładu:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Resource Manager 

W pozostałej części tego samouczka będzie działać z maszyny Wirtualnej, utworzony wcześniej.

1. Zaloguj się do portalu Azure pod adresem [https://portal.azure.com](https://portal.azure.com)

2. W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej systemu Windows i w **omówienie**, kliknij przycisk **Connect**.

3. Wprowadź **Username** i **hasło** używane podczas tworzenia maszyny Wirtualnej systemu Windows.

4. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, otwórz **PowerShell** w sesji zdalnej.

5. Przy użyciu programu PowerShell w `Invoke-WebRequest`, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu usługi Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Odczytywania właściwości grupy zasobów

Użyj pobrać token dostępu w poprzednim kroku dostępu do usługi Azure Resource Manager i odczytywania właściwości zasobu grupy przyznaje dostęp tożsamości przypisane przez użytkownika. Zastąp <SUBSCRIPTION ID> z identyfikatorem subskrypcji danego środowiska.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Odpowiedź zawiera określone informacje grupy zasobów, podobnie do poniższego przykładu:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Kolejne kroki

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
