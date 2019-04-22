---
title: Programowe tworzenie zasad i wyświetlić dane na temat zgodności
description: W tym artykule opisano proces programowego tworzenia i zarządzanie zasadami dotyczącymi usługi Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/31/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ade5d55833f1d63a8d70b6eedb3c3e4bdffe590b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276494"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programowe tworzenie zasad i wyświetlić dane na temat zgodności

W tym artykule opisano za pośrednictwem programowe tworzenie zasad i zarządzanie nimi. Definicje zasad wymuszają różne reguły i efekty dotyczące zasobów. Wymuszania gwarantuje, że zasoby pozostają zgodne ze standardami firmy i umów dotyczących poziomu usług.

Aby uzyskać informacje o zgodności, zobacz [pobierania danych zgodności](getting-compliance-data.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że są spełnione następujące wymagania wstępne:

1. Jeśli ta czynność nie została jeszcze wykonana, zainstaluj klienta [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API opartych na usłudze Azure Resource Manager.

1. Aktualizowanie modułu Azure PowerShell do najnowszej wersji. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje. Aby uzyskać więcej informacji na temat najnowszej wersji, zobacz [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Zarejestruj dostawcę zasobów usługi Policy Insights przy użyciu programu Azure PowerShell w celu zweryfikowania, że Twoja subskrypcja współpracuje z dostawcą zasobów. Aby zarejestrować dostawcę zasobów, musi mieć uprawnienia do uruchamiania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [dostawcy zasobów i ich typy](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Jeśli jeszcze nie, zainstaluj wiersza polecenia platformy Azure. Można uzyskać najnowszą wersję w [zainstalować interfejs wiersza polecenia w usłudze Azure na Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Tworzenie i przypisywanie definicji zasad

Pierwszym krokiem procesu lepszą widoczność zasobów jest tworzenie i przypisywanie zasad za pośrednictwem zasobów. Następnym krokiem jest, aby dowiedzieć się, jak programowo utworzyć i przypisać zasady. Przykładowe zasady inspekcji kont magazynu, które są otwarte dla wszystkich sieci publicznych, przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure i żądań HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Tworzenie i przypisywanie definicji zasad przy użyciu programu PowerShell

1. Poniższy fragment kodu JSON umożliwia Utwórz plik JSON o nazwie AuditStorageAccounts.json.

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

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [struktura definicji zasad platformy Azure](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad przy użyciu pliku AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Polecenie tworzy definicję zasad o nazwie _inspekcji magazynu kont otwarte do sieci publicznych_.
   Aby uzyskać więcej informacji na temat innych parametrów, których można wyświetlić [New AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Gdy zostanie wywołana bez parametrów lokalizacji `New-AzPolicyDefinition` wartość domyślna to zapisanie definicji zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję do innej lokalizacji, należy użyć następujących parametrów:

   - **SubscriptionId** — Zapisz się do innej subskrypcji. Wymaga _GUID_ wartość.
   - **ManagementGroupName** — zapisywanie do grupy zarządzania. Wymaga _ciąg_ wartość.

1. Po utworzeniu definicji zasad, można utworzyć przypisanie zasad, uruchamiając następujące polecenia:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Zastąp _ContosoRG_ nazwą grupy zasobów przeznaczone.

   **Zakres** parametru `New-AzPolicyAssignment` współpracuje z grupy zarządzania, subskrypcji, grupy zasobów lub pojedynczy zasób. Parametr używa ścieżki wszystkich zasobów, które **ResourceId** właściwość `Get-AzResourceGroup` zwraca. Wzorzec **zakres** dla każdego kontenera jest w następujący sposób. Zastąp `{rName}`, `{rgName}`, `{subId}`, i `{mgName}` nazwą zasobu, grupa zasobów nazwa, identyfikator subskrypcji i nazwę grupy zarządzania, odpowiednio.
   `{rType}` zostanie zamienione **typ zasobu** z zasobów, takich jak `Microsoft.Compute/virtualMachines` dla maszyny Wirtualnej.

   - Zasób — `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów- `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Subskrypcja — `/subscriptions/{subId}/`
   - Grupa zarządzania- `/providers/Microsoft.Management/managementGroups/{mgName}`

Aby uzyskać więcej informacji o zarządzaniu zasadami zasobów za pomocą modułu Azure PowerShell Resource Manager, zobacz [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Tworzenie i przypisywanie definicji zasad za pomocą ARMClient

Poniższa procedura umożliwia utworzenie definicji zasad.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik w formacie JSON. Wywołasz pliku w następnym kroku.

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

1. Utwórz definicję zasad, przy użyciu jednej z następujących połączeń:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Zastąp kroku {subscriptionId} o identyfikatorze subskrypcji lub {managementGroupId} o identyfikatorze usługi [grupy zarządzania](../../management-groups/overview.md).

   Aby uzyskać więcej informacji na temat struktury kwerendy zobacz [definicji zasad — tworzenia lub aktualizacji](/rest/api/resources/policydefinitions/createorupdate) i [definicji zasad — tworzenia lub aktualizacji w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Poniższa procedura umożliwia tworzenie przypisania zasad i przypisywanie definicji zasad na poziomie grupy zasobów.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik JSON przypisania zasad. Zastąp przykładowe informacje przedstawione w &lt; &gt; symboli z własnymi wartościami.

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

1. Tworzenie przypisania zasad za pomocą następującego wywołania:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Zastąp przykładowe informacje przedstawione w &lt; &gt; symboli z własnymi wartościami.

   Aby uzyskać więcej informacji na temat nawiązywania połączeń HTTP do interfejsu API REST, zobacz [zasobów interfejsu API REST usługi Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Tworzenie i przypisywanie definicji zasad przy użyciu wiersza polecenia platformy Azure

Aby utworzyć definicję zasad, użyj następującej procedury:

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik JSON przypisania zasad.

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

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [struktura definicji zasad platformy Azure](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Polecenie tworzy definicję zasad o nazwie _inspekcji magazynu kont otwarte do sieci publicznych_.
   Aby uzyskać więcej informacji na temat innych parametrów, których można wyświetlić [utworzenia definicji zasad az](/cli/azure/policy/definition#az-policy-definition-create).

   Gdy zostanie wywołana bez parametrów lokalizacji `az policy definition creation` wartość domyślna to zapisanie definicji zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję do innej lokalizacji, należy użyć następujących parametrów:

   - **--subskrypcji** — Zapisz się do innej subskrypcji. Wymaga _GUID_ wartość ID subskrypcji lub _ciąg_ wartość dla nazwy subskrypcji.
   - **--grupy zarządzania** — zapisywanie do grupy zarządzania. Wymaga _ciąg_ wartość.

1. Użyj następującego polecenia, aby utworzyć przypisanie zasad. Zastąp przykładowe informacje przedstawione w &lt; &gt; symboli z własnymi wartościami.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   **--Zakres** parametru `az policy assignment create` współpracuje z grupy zarządzania, subskrypcji, grupy zasobów lub pojedynczy zasób. Parametr używa ścieżki wszystkich zasobów. Wzorzec **--zakres** dla każdego kontenera jest w następujący sposób. Zastąp `{rName}`, `{rgName}`, `{subId}`, i `{mgName}` nazwą zasobu, grupa zasobów nazwa, identyfikator subskrypcji i nazwę grupy zarządzania, odpowiednio. `{rType}` zostanie zamienione **typ zasobu** z zasobów, takich jak `Microsoft.Compute/virtualMachines` dla maszyny Wirtualnej.

   - Zasób — `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów- `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Subskrypcja — `/subscriptions/{subID}`
   - Grupa zarządzania- `/providers/Microsoft.Management/managementGroups/{mgName}`

Możesz uzyskać identyfikator definicji zasad przy użyciu programu PowerShell za pomocą następującego polecenia:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Identyfikator definicji zasad w definicji zasad, który został utworzony powinien wyglądać następująco:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Aby uzyskać więcej informacji na temat sposobu zarządzania zasad zasobów przy użyciu wiersza polecenia platformy Azure, zobacz [zasad zasobów interfejsu wiersza polecenia platformy Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Kolejne kroki

Sprawdź następujące artykuły, aby uzyskać więcej informacji na temat polecenia i zapytania w tym artykule.

- [Zasoby interfejsu API REST platformy Azure](/rest/api/resources/)
- [Azure PowerShell Modules](/powershell/module/az.resources/#policies)
- [Polecenia zasad wiersza polecenia platformy Azure](/cli/azure/policy?view=azure-cli-latest)
- [Dostawcę zasobów szczegółowych informacji o zasadach dokumentacja interfejsu API REST](/rest/api/policy-insights)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/overview.md)