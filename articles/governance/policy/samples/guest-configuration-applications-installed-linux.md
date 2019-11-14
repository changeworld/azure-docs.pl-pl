---
title: Przykład — Inspekcja brakujących aplikacji na maszynach wirtualnych z systemem Linux
description: Ta przykładowa inicjatywa konfiguracji gościa i definicje inspekcji, jeśli określone aplikacje nie są zainstalowane na maszynach wirtualnych z systemem Linux.
ms.date: 05/02/2019
ms.topic: sample
ms.openlocfilehash: 0789b7f408c1f3eea000bfb2fc21ddf5feff790c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076224"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Próbkowanie — Inspekcja, jeśli określone aplikacje nie są zainstalowane na maszynach wirtualnych z systemem Linux

Ta inicjatywa konfiguracji gościa tworzy zdarzenie inspekcji, gdy określone aplikacje nie są zainstalowane na maszynach wirtualnych z systemem Linux. Identyfikator tej inicjatywy wbudowanej to `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Wszystkie inicjatywy konfiguracji gościa składają się z definicji zasad **inspekcji** i **deployIfNotExists** . Przypisanie tylko jednej z definicji zasad powoduje, że konfiguracja gościa nie będzie działała poprawnie.

Możesz przypisać ten przykład przy użyciu:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Składniki inicjatywy

Ta inicjatywa [konfiguracji gościa](../concepts/guest-configuration.md) składa się z następujących zasad:

- [Inspekcja](#audit-definition) — Inspekcja, gdy aplikacje nie są zainstalowane na maszynach wirtualnych z systemem Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) — Wdróż rozszerzenie maszyny wirtualnej w celu przeprowadzenia inspekcji, gdy aplikacje nie są zainstalowane na maszynach wirtualnych systemu Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definicja inicjatywy

Inicjatywa jest tworzona przez dołączenie definicji **audytu** i **DeployIfNotExists** razem oraz [parametrów inicjatywy](#initiative-parameters). Jest to kod JSON definicji.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parametry inicjatywy

|Nazwa |Typ |Opis |
|---|---|---|
|applicationName |Ciąg |Nazwy aplikacji. Przykład: "Python", "PowerShell" lub listę rozdzieloną przecinkami, taką jak "Python, PowerShell". Użyj \* do dopasowania symboli wieloznacznych, takich jak "moc\*". |

Podczas tworzenia przypisania za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure wartości parametrów można przekazać jako kod JSON w ramach ciągu lub pliku przy użyciu opcji `-PolicyParameter` (program PowerShell) lub opcji `--params` (interfejs wiersza polecenia platformy Azure).
Program PowerShell obsługuje także opcję `-PolicyParameterObject`, która wymaga przekazania do polecenia cmdlet tabeli skrótów Name/Value (Nazwa/Wartość), gdzie parametr **Name** oznacza nazwę parametru, a parametr **Value** oznacza pojedynczą wartość lub tablicę wartości przekazywaną podczas przypisywania.

W tym przykładowym parametrze instalacja aplikacji _Python_ i _PowerShell_ jest przeprowadzana inspekcją.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Tylko definicja zasad **deployIfNotExists** korzysta z parametrów inicjatywy.

### <a name="audit-definition"></a>Definicja inspekcji

KOD JSON definiujący reguły definicji zasad **inspekcji** .

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>Definicja deployIfNotExists

KOD JSON definiujący reguły definicji zasad **deployIfNotExists** .

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

Definicja zasad **deployIfNotExists** definiuje obrazy platformy Azure, na których zasady zostały zweryfikowane:

|Wydawca |Oferta |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Wszystkie z wyjątkiem 6\* |
|RedHat |RHEL |Wszystkie z wyjątkiem 6\* |
|RedHat |OEM | Wszystkie |
|credativ |Debian | Wszystkie z wyjątkiem 7\* |
|SUSE |SLES\* |Wszystkie z wyjątkiem 11\* |
|Canonical| UbuntuServer |Wszystkie z wyjątkiem 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Wszystkie |
|microsoft-dsvm |połączone |Wszystkie |
|cloudera |Cloudera — CentOS — OS |Wszystkie z wyjątkiem 6\* |
|cloudera |cloudera-altus-centos-os |Wszystkie |
|microsoft-ads |linux\* |Wszystkie |
|microsoft-aks |Wszystkie |Wszystkie |
|AzureDatabricks |Wszystkie |Wszystkie |
|qubole-inc |Wszystkie |Wszystkie |
|DataStax |Wszystkie |Wszystkie |
|Couchbase |Wszystkie |Wszystkie |
|scalegrid |Wszystkie |Wszystkie |
|elementu |Wszystkie |Wszystkie |
|paloaltonetworks |Wszystkie |Wszystkie |

Część **wdrożenia** reguły przekazuje parametr _InstalledApplication_ do agenta konfiguracji gościa na maszynie wirtualnej. Ta konfiguracja umożliwia agentowi wykonywanie walidacji i zgłaszanie zgodności z powrotem przez definicję zasad **inspekcji** .

## <a name="azure-portal"></a>Azure Portal

Po utworzeniu definicji **inspekcji** i **deployIfNotExists** w portalu zaleca się ich grupowanie do [inicjatywy](../concepts/definition-structure.md#initiatives) w celu przypisania.

### <a name="create-copy-of-audit-definition"></a>Utwórz kopię definicji inspekcji

[![wdrożyć przykład zasad na platformie azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![wdrożyć przykład zasad na platformie Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Użycie tych przycisków do wdrożenia za pośrednictwem portalu spowoduje utworzenie kopii definicji zasad **inspekcji** .
Bez sparowanej definicji zasad **DeployIfNotExists** konfiguracja gościa nie będzie działała poprawnie.

### <a name="create-copy-of-deployifnotexists-definition"></a>Utwórz kopię definicji deployIfNotExists

[![wdrożyć przykład zasad na platformie azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![wdrożyć przykład zasad na platformie Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Użycie tych przycisków do wdrożenia za pośrednictwem portalu tworzy kopię definicji zasad **deployIfNotExists** . Bez sparowanej definicji zasad **inspekcji** konfiguracja gościa nie będzie działała prawidłowo.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Kopiuj i przypisz inicjatywę

Te kroki umożliwiają utworzenie kopii inicjatywy zawierającej wbudowane zasady zarówno **inspekcji** , jak i **deployIfNotExists** , a także przypisuje inicjatywę do grupy zasobów.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i poprzednią definicję:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Kopiowanie i przypisywanie definicji inspekcji

Te kroki umożliwiają utworzenie kopii definicji **inspekcji** i przypisanie jej do grupy zasobów. Ta definicja nie będzie działała poprawnie bez przypisywania również skojarzonej definicji **deployIfNotExists** .

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i poprzednią definicję:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Kopiowanie i przypisywanie definicji deployIfNotExists

Te kroki umożliwiają utworzenie kopii definicji **deployIfNotExists** i przypisanie jej do grupy zasobów.
Ta definicja nie będzie działała poprawnie bez przypisanej także sparowanej definicji **inspekcji** .

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i poprzednią definicję:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Objaśnienie dla programu Azure PowerShell

Skrypty służące do wdrażania i usuwania korzystają z następujących poleceń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Tworzy inicjatywę Azure Policy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Tworzy definicję Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Pobiera pojedynczą grupę zasobów. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Tworzy nowe przypisanie Azure Policy dla inicjatywy lub definicji. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Nadaje istniejące przypisanie roli do określonego podmiotu zabezpieczeń. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Usuwa istniejące przypisanie zasad Azure Policy. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Usuwa inicjatywę. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Usuwa definicję. |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z dodatkowymi [przykładami Azure Policy](index.md).
- Dowiedz się więcej o [konfigurowaniu Azure Policy gościa](../concepts/guest-configuration.md).
- Przejrzyj [strukturę Azure Policy definicji](../concepts/definition-structure.md).
