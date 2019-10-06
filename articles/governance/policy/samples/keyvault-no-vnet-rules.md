---
title: Przykładowe inspekcje magazynów kluczy dla punktów końcowych sieci wirtualnych
description: Ta przykładowa definicja zasad przeprowadza inspekcję Key Vault magazynów w celu wykrywania wystąpień, które nie mają punktów końcowych usługi sieci wirtualnej.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: ccfba0eae8d3c51cf153f58d554dc09b574d3873
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976962"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Przykładowe magazyny Key Vault bez punktów końcowych sieci wirtualnej

Ta zasada przeprowadza inspekcję Key Vault magazynów, które nie mają punktów końcowych sieci wirtualnej. Użyj, aby wymusić wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej w Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

Te przykładowe zasady można wdrożyć przy użyciu:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [INTERFEJS API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Przykładowe zasady

### <a name="policy-definition"></a>Definicja zasad

Kompletna definicja zasad JSON, używana przez interfejs API REST, przyciski "wdróż na platformie Azure" i ręcznie w portalu.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> W przypadku ręcznego tworzenia zasad w portalu Użyj części **właściwości. Parameters** i **Properties. Klasa policyrule** powyższych elementów. Zawiń dwie sekcje razem z nawiasami klamrowymi `{}`, aby uczynić prawidłowym kodem JSON.

### <a name="policy-rules"></a>Reguły zasad

KOD JSON definiujący reguły zasad używany przez interfejs wiersza polecenia platformy Azure i Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zasad

Ta przykładowa definicja zasad nie ma zdefiniowanych parametrów.

## <a name="azure-portal"></a>Azure Portal

[![Deploy przykładową zasadą do platformy azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[@no__t 4Deploy zasad na platformie Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie za pomocą Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Usuń z Azure PowerShell

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i definicję:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell Wyjaśnij

Skrypty wdrażania i usuwania używają następujących poleceń. Każde polecenie w poniższej tabeli zawiera linki do dokumentacji dotyczącej poleceń:

| Polecenie | Uwagi |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Tworzy nową definicję Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Pobiera pojedynczą grupę zasobów. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Tworzy nowe przypisanie Azure Policy. W tym przykładzie udostępniamy definicję, ale może ona również podejmować inicjatywy. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Usuwa istniejące przypisanie Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Usuwa istniejącą definicję Azure Policy. |

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

### <a name="remove-with-azure-cli"></a>Usuń za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenia, aby usunąć poprzednie przypisanie i definicję:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Wyjaśnienie interfejsu wiersza polecenia platformy Azure

| Polecenie | Uwagi |
|---|---|
| [AZ Policy Definition Create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Tworzy nową definicję Azure Policy. |
| [AZ Group Show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Pobiera pojedynczą grupę zasobów. |
| [AZ Policy przypisanie Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Tworzy nowe przypisanie Azure Policy. W tym przykładzie udostępniamy definicję, ale może ona również podejmować inicjatywy. |
| [AZ Policy przypisanie Delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Usuwa istniejące przypisanie Azure Policy. |
| [AZ Policy Definition Delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Usuwa istniejącą definicję Azure Policy. |

## <a name="rest-api"></a>INTERFEJS API REST

Istnieje kilka narzędzi, których można użyć do współpracy z interfejsem API REST Menedżer zasobów, na przykład [ARMClient](https://github.com/projectkudu/ARMClient) lub PowerShell.

### <a name="deploy-with-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

- Utwórz definicję zasad (zakres subskrypcji). Użyj formatu JSON [definicji zasad](#policy-definition) dla treści żądania.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Tworzenie przypisania zasad (zakres grupy zasobów)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  W treści żądania należy użyć następującego przykładu JSON:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Usuwanie przy użyciu interfejsu API REST

- Usuń przypisanie zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Usuń definicję zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Wyjaśnienie interfejsu API REST

| Usługa | Grupa | Operacja | Uwagi |
|---|---|---|---|
| Zarządzanie zasobami | Definicje zasad | [Create](/rest/api/resources/policydefinitions/createorupdate) | Tworzy nową definicję Azure Policy w ramach subskrypcji. Alternatywa: [Utwórz w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Zarządzanie zasobami | Przypisania zasad | [Create](/rest/api/resources/policyassignments/create) | Tworzy nowe przypisanie Azure Policy. W tym przykładzie udostępniamy definicję, ale może ona również podejmować inicjatywy. |
| Zarządzanie zasobami | Przypisania zasad | [Delete](/rest/api/resources/policyassignments/delete) | Usuwa istniejące przypisanie Azure Policy. |
| Zarządzanie zasobami | Definicje zasad | [Delete](/rest/api/resources/policydefinitions/delete) | Usuwa istniejącą definicję Azure Policy. Alternatywa: [usuwanie w grupie zarządzania](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z dodatkowymi [przykładami Azure Policy](index.md)
- Przegląd [struktury definicji Azure Policy](../concepts/definition-structure.md)