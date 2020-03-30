---
title: Programowe tworzenie zasad
description: W tym artykule oszukujesz programowo tworzenie zasad dla platformy Azure i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell i interfejsu API REST.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264546"
---
# <a name="programmatically-create-policies"></a>Programowe tworzenie zasad

W tym artykule otrzymasz od ciebie programowe tworzenie zasad i zarządzanie nimi. Definicje zasad platformy Azure wymuszają różne reguły i efekty w stosunku do zasobów. Wymuszanie zapewnia zgodność zasobów ze standardami firmowymi i umowami o poziomie usług.

Aby uzyskać informacje na temat zgodności, zobacz [uzyskiwanie danych dotyczących zgodności](get-compliance-data.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełnione są następujące wymagania wstępne:

1. Jeśli ta czynność nie została jeszcze wykonana, zainstaluj klienta [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API opartych na usłudze Azure Resource Manager.

1. Zaktualizuj moduł programu Azure PowerShell do najnowszej wersji. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje. Aby uzyskać więcej informacji na temat najnowszej wersji, zobacz [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Zarejestruj dostawcę zasobów usługi Azure Policy Insights przy użyciu programu Azure PowerShell, aby sprawdzić, czy subskrypcja współpracuje z dostawcą zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do uruchamiania operacji akcji rejestru dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Aby uzyskać więcej informacji na temat rejestrowania i wyświetlania dostawców zasobów, zobacz [Dostawcy i typy zasobów](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure. Najnowszą wersję można uzyskać w [instalacji interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Tworzenie i przypisywanie definicji zasad

Pierwszym krokiem w kierunku lepszej widoczności zasobów jest tworzenie i przypisywanie zasad nad zasobami. Następnym krokiem jest, aby dowiedzieć się, jak programowo tworzyć i przypisywać zasady. Przykładowa zasada przeprowadza inspekcję kont magazynu, które są otwarte dla wszystkich sieci publicznych przy użyciu żądań programu PowerShell, interfejsu wiersza polecenia platformy Azure i HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Tworzenie i przypisywanie definicji zasad za pomocą programu PowerShell

1. Użyj następującego fragmentu kodu JSON, aby utworzyć plik JSON o nazwie AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad przy użyciu pliku AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Polecenie tworzy definicję zasad o nazwie _Konta magazynu inspekcji otwarte dla sieci publicznych_.
   Aby uzyskać więcej informacji o innych parametrach, których można użyć, zobacz [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Po wywołaniu bez `New-AzPolicyDefinition` parametrów lokalizacji domyślnie zapisuje definicję zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję w innej lokalizacji, należy użyć następujących parametrów:

   - **Identyfikator subskrypcji** — zapisz w innej subskrypcji. Wymaga wartości _GUID._
   - **ManagementGroupName** — zapisywanie w grupie zarządzania. Wymaga wartości _ciągu._

1. Po utworzeniu definicji zasad można utworzyć przypisanie zasad, uruchamiając następujące polecenia:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Zastąp _ContosoRG_ nazwą zamierzonej grupy zasobów.

   Parametr **Zakres** `New-AzPolicyAssignment` dotyczący działa z grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem. Parametr używa pełnej ścieżki zasobu, która `Get-AzResourceGroup` **ResourceId** właściwość na zwraca. Wzorzec **dla zakresu** dla każdego kontenera jest następujący. Zastąp `{rName}` `{rgName}`, `{subId}`odpowiednio, `{mgName}` nazwą zasobu, nazwą grupy zasobów, identyfikatorem subskrypcji i nazwą grupy zarządzania.
   `{rType}`zostanie zastąpiony **typem zasobu,** na `Microsoft.Compute/virtualMachines` przykład dla maszyny Wirtualnej.

   - Zasób -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów -`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Subskrypcja -`/subscriptions/{subId}/`
   - Grupa zarządzająca -`/providers/Microsoft.Management/managementGroups/{mgName}`

Aby uzyskać więcej informacji na temat zarządzania zasadami zasobów przy użyciu modułu Programu PowerShell usługi Azure Resource Manager, zobacz [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Tworzenie i przypisywanie definicji zasad przy użyciu funkcji ARMClient

Aby utworzyć definicję zasad, użyj poniższej procedury.

1. Skopiuj następujący fragment kodu JSON, aby utworzyć plik JSON. Plik zostanie wywołany w następnym kroku.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Utwórz definicję zasad przy użyciu jednego z następujących wywołań:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Zastąp poprzednią {subscriptionId} identyfikatorem subskrypcji lub {managementGroupId} identyfikatorem [grupy zarządzania](../../management-groups/overview.md).

   Aby uzyskać więcej informacji na temat struktury kwerendy, zobacz [Definicje zasad platformy Azure — tworzenie lub aktualizowanie](/rest/api/resources/policydefinitions/createorupdate) i [definicje zasad — tworzenie lub aktualizowanie w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Poniższa procedura służy do tworzenia przypisania zasad i przypisywania definicji zasad na poziomie grupy zasobów.

1. Skopiuj następujący fragment kodu JSON, aby utworzyć plik przypisania zasad JSON. Zastąp &lt; &gt; przykładowe informacje w symbolach własnymi wartościami.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Utwórz przypisanie zasad przy użyciu następującego połączenia:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Zastąp &lt; &gt; przykładowe informacje w symbolach własnymi wartościami.

   Aby uzyskać więcej informacji na temat nawiązywać wywołania HTTP do interfejsu API REST, zobacz [Zasoby interfejsu API rest platformy Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Tworzenie i przypisywanie definicji zasad za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć definicję zasad, należy użyć następującej procedury:

1. Skopiuj następujący fragment kodu JSON, aby utworzyć plik przypisania zasad JSON.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Polecenie tworzy definicję zasad o nazwie _Konta magazynu inspekcji otwarte dla sieci publicznych_.
   Aby uzyskać więcej informacji o innych parametrach, których można użyć, zobacz [definicja zasad az create](/cli/azure/policy/definition#az-policy-definition-create).

   Po wywołaniu bez `az policy definition creation` parametrów lokalizacji domyślnie zapisuje definicję zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję w innej lokalizacji, należy użyć następujących parametrów:

   - **--subscription** - Zapisz do innej subskrypcji. Wymaga wartości _identyfikatora GUID_ dla identyfikatora subskrypcji lub wartości _ciągu_ dla nazwy subskrypcji.
   - **--management-group** — zapisz w grupie zarządzania. Wymaga wartości _ciągu._

1. Użyj następującego polecenia, aby utworzyć przypisanie zasad. Zastąp &lt; &gt; przykładowe informacje w symbolach własnymi wartościami.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametr **--scope** `az policy assignment create` w pracy z grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem. Parametr używa pełnej ścieżki zasobów. Wzorzec **dla --scope** dla każdego kontenera jest następujący. Zastąp `{rName}` `{rgName}`, `{subId}`odpowiednio, `{mgName}` nazwą zasobu, nazwą grupy zasobów, identyfikatorem subskrypcji i nazwą grupy zarządzania. `{rType}`zostanie zastąpiony **typem zasobu,** na `Microsoft.Compute/virtualMachines` przykład dla maszyny Wirtualnej.

   - Zasób -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów -`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Subskrypcja -`/subscriptions/{subID}`
   - Grupa zarządzająca -`/providers/Microsoft.Management/managementGroups/{mgName}`

Identyfikator definicji zasad platformy Azure można uzyskać przy użyciu programu PowerShell za pomocą następującego polecenia:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Identyfikator definicji zasad dla utworzonej definicji zasad powinien przypominać następujący przykład:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Aby uzyskać więcej informacji na temat zarządzania zasadami zasobów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zasady zasobów interfejsu wiersza polecenia platformy Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższymi artykułami, aby uzyskać więcej informacji na temat poleceń i zapytań w tym artykule.

- [Zasoby interfejsu API usługi Azure REST](/rest/api/resources/)
- [Moduły programu Azure PowerShell](/powershell/module/az.resources/#policies)
- [Polecenia zasad interfejsu wiersza polecenia platformy Azure](/cli/azure/policy?view=azure-cli-latest)
- [Odwołanie do interfejsu API interfejsu REST dostawcy zasobów usługi Azure Policy Insights](/rest/api/policy-insights)
- [Organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).