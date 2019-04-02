---
title: Przykład — zatwierdzone obrazy maszyn wirtualnych
description: Ta przykładowa definicja zasad wymaga, aby w środowisku były wdrażane tylko zatwierdzone niestandardowe obrazy.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 1fc7b9f49b806c4b740f4f85330dc9e37f0f61f2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805622"
---
# <a name="sample---approved-virtual-machine-images"></a>Przykład — obrazów zatwierdzonych maszyn wirtualnych

Te zasady wymagają, aby w środowisku były wdrażane tylko zatwierdzone niestandardowe obrazy. Należy określić tablicę identyfikatorów zatwierdzonych obrazów.

Te przykładowe zasady możesz wdrożyć przy użyciu następujących narzędzi:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Interfejs API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Przykładowe zasady

### <a name="policy-definition"></a>Definicja zasad

W pełni gotowa definicja zasad JSON używana przez interfejs API REST, przyciski „Wdróż na platformie Azure” i ręcznie w witrynie Azure Portal.

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> W przypadku ręcznego tworzenia zasad w witrynie Azure Portal użyj powyższych elementów **properties.parameters** i **properties.policyRule**. Połącz dwie sekcje za pomocą nawiasów klamrowych `{}`, aby utworzyć poprawny kod JSON.

### <a name="policy-rules"></a>Reguły zasad

Kod JSON definiujący reguły zasad używany przez interfejs wiersza polecenia platformy Azure i program Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametry zasad

Kod JSON definiujący parametry zasad używany przez interfejs wiersza polecenia platformy Azure i program Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametry

|Name (Nazwa) |Type |Pole |Opis |
|---|---|---|---|
|imageIds |Tablica |Microsoft.Compute/imageIds |Lista zatwierdzonych obrazów maszyn wirtualnych|

Podczas tworzenia przypisania za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure wartości parametrów można przekazać jako kod JSON w ramach ciągu lub pliku przy użyciu opcji `-PolicyParameter` (program PowerShell) lub opcji `--params` (interfejs wiersza polecenia platformy Azure).
Program PowerShell obsługuje także opcję `-PolicyParameterObject`, która wymaga przekazania do polecenia cmdlet tabeli skrótów Name/Value (Nazwa/Wartość), gdzie parametr **Name** oznacza nazwę parametru, a parametr **Value** oznacza pojedynczą wartość lub tablicę wartości przekazywaną podczas przypisywania.

W tym przykładowym parametrze dozwolony będzie tylko element _ContosoStdImage_ w grupie zasobów _YourResourceGroup_ lub wersja obrazu systemu Windows Server 2016 Datacenter z maja 2018 roku znajdująca się w regionie „Środkowe stany USA”.

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

[![Wdrażanie przykładu zasad w usłudze Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![wdrażanie przykładu zasad do klientów rządowych platformy Azure](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

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
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Istnieje kilka narzędzi, które mogą służyć do interakcji z interfejsem API REST usługi Resource Manager, np. klient [ARMClient](https://github.com/projectkudu/ARMClient) lub program PowerShell. Przykład wywołania interfejsu API REST z poziomu programu PowerShell można znaleźć w sekcji **Aliasy** w temacie [Struktura definicji zasad](../concepts/definition-structure.md#aliases).

### <a name="deploy-with-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

- Utwórz definicję zasad (zakres subskrypcji). Użyj kodu JSON [definicji zasad](#policy-definition) w treści żądania.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Tworzenie przypisania zasad (zakres grupy zasobów)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  Skorzystaj z następującego przykładowego kodu JSON w treści żądania:

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

### <a name="remove-with-rest-api"></a>Usuwanie za pomocą interfejsu API REST

- Usuwanie przypisania zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Usuwanie definicji zasad

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
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
- Zobacz więcej przykładów usługi Azure Policy dla maszyn wirtualnych na stronie [Apply policies to Windows VMs](../../../virtual-machines/windows/policy.md) (Stosowanie zasad na maszynach wirtualnych z systemem Windows)