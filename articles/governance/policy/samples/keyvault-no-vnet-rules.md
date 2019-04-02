---
title: Przykład — Przeprowadzanie inspekcji magazynów usługi Key Vault pod kątem braku punktów końcowych sieci wirtualnej
description: Te przykładowe definicje zasad przeprowadzają inspekcję magazynów usługi Key Vault w celu wykrycia wystąpień, które nie mają punktów końcowych usługi sieci wirtualnej.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: f2f6e5c60553a55fd1a65e45cd38aef0b883020e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802262"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Przykład — Magazyny usługi Key Vault niemające punktów końcowych sieci wirtualnej

Te zasady umożliwiają przeprowadzenie inspekcji magazynów usługi Key Vault, które nie mają punktów końcowych sieci wirtualnej. Należy ich używać w celu wymuszenia wymagań dotyczących zabezpieczeń. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [punktów końcowych usługi sieci wirtualnej w usłudze Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

Te przykładowe zasady możesz wdrożyć przy użyciu następujących narzędzi:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Interfejs API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Przykładowe zasady

### <a name="policy-definition"></a>Definicja zasad

W pełni gotowa definicja zasad JSON używana przez interfejs API REST, przyciski „Wdróż na platformie Azure” i ręcznie w witrynie Azure Portal.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> W przypadku ręcznego tworzenia zasad w witrynie Azure Portal użyj powyższych elementów **properties.parameters** i **properties.policyRule**. Połącz dwie sekcje za pomocą nawiasów klamrowych `{}`, aby utworzyć poprawny kod JSON.

### <a name="policy-rules"></a>Reguły zasad

Kod JSON definiujący reguły zasad używany przez interfejs wiersza polecenia platformy Azure i program Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zasad

Ta przykładowa definicja zasad nie ma zdefiniowanych parametrów.

## <a name="azure-portal"></a>Azure Portal

[![Wdrażanie przykładu zasad w usłudze Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![wdrażanie przykładu zasad do klientów rządowych platformy Azure](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Usuwanie przy użyciu programu Azure PowerShell

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i poprzednią definicję:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Objaśnienie dla programu Azure PowerShell

Skrypty służące do wdrażania i usuwania korzystają z następujących poleceń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Tworzy nową definicję zasad Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Pobiera pojedynczą grupę zasobów. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Tworzy nowe przypisanie zasad Azure Policy. W tym przykładzie udostępniliśmy dla niego definicję, ale może także przyjąć inicjatywę. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Usuwa istniejące przypisanie zasad Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Usuwa istniejącą definicję zasad Azure Policy. |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Usuwanie za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i poprzednią definicję:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Objaśnienie dla interfejsu wiersza polecenia platformy Azure

| Polecenie | Uwagi |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Tworzy nową definicję zasad Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Pobiera pojedynczą grupę zasobów. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Tworzy nowe przypisanie zasad Azure Policy. W tym przykładzie udostępniliśmy dla niego definicję, ale może także przyjąć inicjatywę. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Usuwa istniejące przypisanie zasad Azure Policy. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Usuwa istniejącą definicję zasad Azure Policy. |

## <a name="rest-api"></a>Interfejs API REST

Istnieje kilka narzędzi, które mogą służyć do interakcji z interfejsem API REST usługi Resource Manager, np. klient [ARMClient](https://github.com/projectkudu/ARMClient) lub program PowerShell.

### <a name="deploy-with-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

- Utwórz definicję zasad (zakres subskrypcji). Użyj kodu JSON [definicji zasad](#policy-definition) w treści żądania.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Tworzenie przypisania zasad (zakres grupy zasobów)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Skorzystaj z następującego przykładowego kodu JSON w treści żądania:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Usuwanie za pomocą interfejsu API REST

- Usuwanie przypisania zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Usuwanie definicji zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Objaśnienie dla interfejsu API REST

| Usługa | Grupa | Operacja | Uwagi |
|---|---|---|---|
| Zarządzanie zasobami | Definicje zasad | [Tworzenie](/rest/api/resources/policydefinitions/createorupdate) | Tworzy nową definicję zasad Azure Policy w ramach subskrypcji. Alternatywne rozwiązanie: [Tworzenie w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Zarządzanie zasobami | Przypisania zasad | [Tworzenie](/rest/api/resources/policyassignments/create) | Tworzy nowe przypisanie zasad Azure Policy. W tym przykładzie udostępniliśmy dla niego definicję, ale może także przyjąć inicjatywę. |
| Zarządzanie zasobami | Przypisania zasad | [Usuwanie](/rest/api/resources/policyassignments/delete) | Usuwa istniejące przypisanie zasad Azure Policy. |
| Zarządzanie zasobami | Definicje zasad | [Usuwanie](/rest/api/resources/policydefinitions/delete) | Usuwa istniejącą definicję zasad Azure Policy. Alternatywne rozwiązanie: [Usuwanie w grupie zarządzania](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj dodatkowe [przykłady zasad Azure Policy](index.md)
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md)