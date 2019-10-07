---
title: Przykładowe zaakceptowane obrazy maszyn wirtualnych
description: Ta przykładowa definicja zasad wymaga, aby w danym środowisku zostały wdrożone tylko zatwierdzone obrazy niestandardowe.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 34a3cf54879dcf6fc25fab50bcbabde28fff653a
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980840"
---
# <a name="sample---approved-virtual-machine-images"></a>Przykładowe zaakceptowane obrazy maszyn wirtualnych

Te zasady wymagają, aby w środowisku wdrożono tylko zatwierdzone obrazy niestandardowe. Należy określić tablicę zatwierdzonych identyfikatorów obrazu.

Te przykładowe zasady można wdrożyć przy użyciu:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [INTERFEJS API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Przykładowe zasady

### <a name="policy-definition"></a>Definicja zasad

Kompletna definicja zasad JSON, używana przez interfejs API REST, przyciski "wdróż na platformie Azure" i ręcznie w portalu.

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> W przypadku ręcznego tworzenia zasad w portalu Użyj części **właściwości. Parameters** i **Properties. Klasa policyrule** powyższych elementów. Zawiń dwie sekcje razem z nawiasami klamrowymi `{}`, aby uczynić prawidłowym kodem JSON.

### <a name="policy-rules"></a>Reguły zasad

KOD JSON definiujący reguły zasad używany przez interfejs wiersza polecenia platformy Azure i Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zasad

KOD JSON definiujący parametry zasad używany przez interfejs wiersza polecenia platformy Azure i Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametry

|Nazwa |Typ |Pole |Opis |
|---|---|---|---|
|imageIds |Macierzy |Microsoft. COMPUTE/imageIds |Lista zatwierdzonych obrazów maszyn wirtualnych|

Podczas tworzenia przypisania za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure wartości parametrów mogą być przekazane jako dane JSON w ciągu lub za pośrednictwem pliku przy użyciu `-PolicyParameter` (PowerShell) lub `--params` (interfejs wiersza polecenia platformy Azure).
Program PowerShell obsługuje również `-PolicyParameterObject`, co wymaga przeprowadzenia przechodzenia do polecenia cmdlet nazwa/wartość Hashtable, gdzie **name** to nazwa parametru, a **wartość** jest pojedynczą wartością lub tablicą wartości przekazywanych podczas przypisywania.

W tym przykładowym parametrze dozwolony będzie tylko _ContosoStdImage_ w grupie zasobów _YourResourceGroup_ lub wersja obrazu 2018 systemu Windows Server 2016 centrum danych znajdująca się w lokalizacji "środkowe stany USA".

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Deploy przykładową zasadą do platformy azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[@no__t 4Deploy zasad na platformie Azure gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie za pomocą Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Istnieje kilka narzędzi, których można użyć do współpracy z interfejsem API REST Menedżer zasobów, na przykład [ARMClient](https://github.com/projectkudu/ARMClient) lub PowerShell. Przykład wywoływania interfejsu API REST z programu PowerShell można znaleźć w sekcji **aliasy** [struktury definicji zasad](../concepts/definition-structure.md#aliases).

### <a name="deploy-with-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

- Utwórz definicję zasad (zakres subskrypcji). Użyj formatu JSON [definicji zasad](#policy-definition) dla treści żądania.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Tworzenie przypisania zasad (zakres grupy zasobów)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  W treści żądania należy użyć następującego przykładu JSON:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Usuwanie przy użyciu interfejsu API REST

- Usuń przypisanie zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Usuń definicję zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
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
- Zobacz więcej Azure Policy przykładów Virtual Machines w temacie [stosowanie zasad do maszyn wirtualnych z systemem Windows](../../../virtual-machines/windows/policy.md)