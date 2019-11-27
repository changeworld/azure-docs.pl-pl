---
title: Przykład — stosowanie tagu i jego wartości domyślnej
description: Ta przykładowa definicja zasad dołącza określoną nazwę tagu i wartość zdefiniowaną w parametrze, jeśli ten tag nie jest podany.
ms.date: 01/26/2019
ms.topic: sample
ms.openlocfilehash: 33d0580d2f6c231c4cd7e73abdaab6cb14c363d9
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463679"
---
# <a name="sample---apply-tag-and-its-default-value"></a>Przykład — stosowanie tagu i jego wartości domyślnej

Ta zasada dołącza określoną nazwę i wartość tagu, jeśli nie podano tagu. Należy określić nazwę i wartość tagu do zastosowania.

Te przykładowe zasady możesz wdrożyć przy użyciu następujących narzędzi:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Interfejs API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Przykładowe zasady

### <a name="policy-definition"></a>Definicja zasad

W pełni gotowa definicja zasad JSON używana przez interfejs API REST, przyciski „Wdróż na platformie Azure” i ręcznie w witrynie Azure Portal.

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> W przypadku ręcznego tworzenia zasad w witrynie Azure Portal użyj powyższych elementów **properties.parameters** i **properties.policyRule**. Połącz dwie sekcje za pomocą nawiasów klamrowych `{}`, aby utworzyć poprawny kod JSON.

### <a name="policy-rules"></a>Reguły zasad

Kod JSON definiujący reguły zasad używany przez interfejs wiersza polecenia platformy Azure i program Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zasad

Kod JSON definiujący parametry zasad używany przez interfejs wiersza polecenia platformy Azure i program Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Nazwa |Typ |Pole |Opis |
|---|---|---|---|
|tagName |Ciąg |tagów |Nazwa tagu, na przykład costCenter|
|tagValue |Ciąg |tagów |Wartość tagu, na przykład headquarter|

Podczas tworzenia przypisania za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure wartości parametrów można przekazać jako kod JSON w ramach ciągu lub pliku przy użyciu opcji `-PolicyParameter` (program PowerShell) lub opcji `--params` (interfejs wiersza polecenia platformy Azure).
Program PowerShell obsługuje także opcję `-PolicyParameterObject`, która wymaga przekazania do polecenia cmdlet tabeli skrótów Name/Value (Nazwa/Wartość), gdzie parametr **Name** oznacza nazwę parametru, a parametr **Value** oznacza pojedynczą wartość lub tablicę wartości przekazywaną podczas przypisywania.

W tym przykładowym parametrze jest zdefiniowany tag _tagName_ o wartości **costCenter** i tag _tagValue_ o wartości **headquarters**.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![wdrożyć przykład zasad na platformie azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![wdrożyć przykład zasad na platformie Azure gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Istnieje kilka narzędzi, które mogą służyć do interakcji z interfejsem API REST usługi Resource Manager, np. klient [ARMClient](https://github.com/projectkudu/ARMClient) lub program PowerShell. Przykład wywołania interfejsu API REST z poziomu programu PowerShell można znaleźć w sekcji **Aliasy** w temacie [Struktura definicji zasad](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>Interfejs API REST

### <a name="deploy-with-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

- Utwórz definicję zasad (zakres subskrypcji). Użyj kodu JSON [definicji zasad](#policy-definition) w treści żądania.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- Tworzenie przypisania zasad (zakres grupy zasobów)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  Skorzystaj z następującego przykładowego kodu JSON w treści żądania:

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Usuwanie za pomocą interfejsu API REST

- Usuwanie przypisania zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- Usuwanie definicji zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Objaśnienie dla interfejsu API REST

| Usługa | Grupa | Operacja | Uwagi |
|---|---|---|---|
| Zarządzanie zasobami | Definicje zasad | [Tworzenie](/rest/api/resources/policydefinitions/createorupdate) | Tworzy nową definicję zasad Azure Policy w ramach subskrypcji. Alternatywne rozwiązanie: [Tworzenie w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Zarządzanie zasobami | Przypisania zasad | [Tworzenie](/rest/api/resources/policyassignments/create) | Tworzy nowe przypisanie zasad Azure Policy. W tym przykładzie udostępniliśmy dla niego definicję, ale może także przyjąć inicjatywę. |
| Zarządzanie zasobami | Przypisania zasad | [Usuwanie](/rest/api/resources/policyassignments/delete) | Usuwa istniejące przypisanie zasad Azure Policy. |
| Zarządzanie zasobami | Definicje zasad | [Usuwanie](/rest/api/resources/policydefinitions/delete) | Usuwa istniejącą definicję zasad Azure Policy. Alternatywne rozwiązanie: [Usuwanie w grupie zarządzania](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Następne kroki

- Przejrzyj dodatkowe [przykłady zasad Azure Policy](index.md)
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md)