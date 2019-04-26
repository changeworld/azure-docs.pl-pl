---
title: Przykład — inspekcji, jeśli aplikacje nie są zainstalowane wewnątrz maszyn wirtualnych systemu Linux
description: Tej przykładowej konfiguracji gościa zasad inicjatywy i definicje inspekcji, jeśli określone aplikacje nie są zainstalowane wewnątrz maszyn wirtualnych systemu Linux.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: b432d8557c4244d58c23e7b068874dd747f6249f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545099"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Przykład — inspekcja określonych aplikacji nie są zainstalowane wewnątrz maszyn wirtualnych systemu Linux

Tej inicjatywy konfiguracji gościa zasad inspekcji, zainstalowanie określonej aplikacji wewnątrz maszyny wirtualnej z systemem Linux. Identyfikator tej inicjatywy wbudowanych `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Wszystkich konfiguracji gościa inicjatywy składają się z **inspekcji** i **deployIfNotExists** definicji zasad. Przypisywanie tylko do jednej z definicji zasad spowodować, że konfiguracja gościa nie będą działać poprawnie.

Można przypisać za pomocą tej przykładowej:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Składniki inicjatywy

To [konfiguracji gościa](../concepts/guest-configuration.md) inicjatywy składają się następujące zasady:

- [Inspekcja](#audit-definition) -inspekcji, że aplikacja jest zainstalowana wewnątrz maszyn wirtualnych systemu Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) — wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji, że aplikacja jest zainstalowana wewnątrz maszyn wirtualnych systemu Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definicja inicjatywy

Inicjatywy jest tworzony przez dołączenie **inspekcji** i **deployIfNotExists** definicje razem i [parametry inicjatywy](#initiative-parameters). Jest to definicji za pomocą pliku JSON.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parametry inicjatywy

|Name |Type ||Description | |---|---||---| |applicationName |String |Application names. Przykład: "python", "powershell" lub rozdzielaną przecinkami listę takich jak "python, programu powershell". Użyj \* dla dopasowanie z symbolami wieloznacznymi, takie jak "power\*". |

Podczas tworzenia przypisania za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure wartości parametrów można przekazać jako kod JSON w ramach ciągu lub pliku przy użyciu opcji `-PolicyParameter` (program PowerShell) lub opcji `--params` (interfejs wiersza polecenia platformy Azure).
Program PowerShell obsługuje także opcję `-PolicyParameterObject`, która wymaga przekazania do polecenia cmdlet tabeli skrótów Name/Value (Nazwa/Wartość), gdzie parametr **Name** oznacza nazwę parametru, a parametr **Value** oznacza pojedynczą wartość lub tablicę wartości przekazywaną podczas przypisywania.

W tym przykładzie parametrze instalację aplikacji _python_ i _powershell_ podlega inspekcji.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Tylko **deployIfNotExists** definicji zasad sprawia, że użycie parametrów inicjatywy.

### <a name="audit-definition"></a>Definicja danych inspekcji

Za pomocą pliku JSON definiowania reguły **inspekcji** definicji zasad.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>Definicja deployIfNotExists

Za pomocą pliku JSON definiowania reguły **deployIfNotExists** definicji zasad.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**DeployIfNotExists** definicji zasad definiuje zasady została zweryfikowana w obrazy platformy Azure:

|Wydawca |Oferta |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Wszystkie regiony z wyjątkiem 6\* |
|RedHat |RHEL |Wszystkie regiony z wyjątkiem 6\* |
|RedHat |osa | Wszyscy |
|credativ |Debian | Wszystkie regiony z wyjątkiem 7\* |
|SUSE |SLES\* |Wszystkie regiony z wyjątkiem 11\* |
|Canonical| UbuntuServer |Wszystkie regiony z wyjątkiem 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Wszyscy |
|microsoft-dsvm |azureml |Wszyscy |
|cloudera |cloudera-centos-os |Wszystkie regiony z wyjątkiem 6\* |
|cloudera |cloudera-altus-centos-os |Wszyscy |
|microsoft-ads |linux\* |Wszyscy |
|microsoft-aks |Wszyscy |Wszyscy |
|AzureDatabricks |Wszyscy |Wszyscy |
|qubole-inc |Wszyscy |Wszyscy |
|datastax |Wszyscy |Wszyscy |
|couchbase |Wszyscy |Wszyscy |
|scalegrid |Wszyscy |Wszyscy |
|Punkt kontrolny |Wszyscy |Wszyscy |
|paloaltonetworks |Wszyscy |Wszyscy |

**Wdrożenia** część reguła przekazuje _installedApplication_ parametru, aby agent Configuration gościa na maszynie wirtualnej. Ta konfiguracja umożliwia agenta Aby wykonywać operacje sprawdzania poprawności i raportowanie zgodności z powrotem przez **inspekcji** definicji zasad.

## <a name="azure-portal"></a>Azure Portal

Po **inspekcji** i **deployIfNotExists** definicje są tworzone w portalu, zaleca się ich do grupy [inicjatywy](../concepts/definition-structure.md#initiatives) przypisania.

### <a name="create-copy-of-audit-definition"></a>Utwórz kopię definicji inspekcji

[![Wdrażanie przykładu zasad w usłudze Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![wdrażanie przykładu zasad do klientów rządowych platformy Azure](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Za pomocą tych przycisków do wdrożenia w portalu jest tworzona kopia **inspekcji** definicji zasad.
Bez sparowane **deployIfNotExists** definicji zasad konfiguracji gościa nie będzie działać poprawnie.

### <a name="create-copy-of-deployifnotexists-definition"></a>Utwórz kopie deployIfNotExists definicji

[![Wdrażanie przykładu zasad w usłudze Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![wdrażanie przykładu zasad do klientów rządowych platformy Azure](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Za pomocą tych przycisków do wdrożenia w portalu jest tworzona kopia **deployIfNotExists** definicji zasad. Bez sparowane **inspekcji** definicji zasad konfiguracji gościa nie będzie działać poprawnie.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Kopiuj i przypisz inicjatywę

Następujące kroki, Utwórz kopię tej inicjatywy, która zawiera wbudowane zasady dla obu **inspekcji** i **deployIfNotExists** i przypisanie inicjatywy do grupy zasobów.

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

#### <a name="copy-and-assign-the-audit-definition"></a>Skopiuj i przypisywanie definicji inspekcji

Te kroki służą utworzeniu kopię **inspekcji** definicji i przypisz je do grupy zasobów. Ta definicja nie będzie działać poprawnie bez sparowane **deployIfNotExists** definicji również przypisane.

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

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Skopiuj i przypisywanie definicji deployIfNotExists

Te kroki służą utworzeniu kopię **deployIfNotExists** definicji i przypisz je do grupy zasobów.
Ta definicja nie będzie działać poprawnie bez sparowane **inspekcji** definicji również przypisane.

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
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Tworzy inicjatywy usługi Azure Policy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Tworzy definicję zasad platformy Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Pobiera pojedynczą grupę zasobów. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Tworzy nowe przypisanie usługi Azure Policy, inicjatywy lub definicji. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Zapewnia istniejącego przypisania roli do określonej jednostki. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Usuwa istniejące przypisanie zasad Azure Policy. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Usuwa inicjatywy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Usuwa definicję. |

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj dodatkowe [przykładów usługi Azure Policy](index.md).
- Dowiedz się więcej o [konfiguracji gościa zasad platformy Azure](../concepts/guest-configuration.md).
- Przegląd [struktura definicji usługi Azure Policy](../concepts/definition-structure.md).
