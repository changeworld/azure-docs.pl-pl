---
title: Programowe tworzenie zasad i wyświetlać dane zgodności z zasadami Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób programowe tworzenie i zarządzanie zasadami dla zasad usługi Azure.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: bd0dbb1b6b44b34fc86b8c73fa586b1b4cf880f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programowe tworzenie zasad i wyświetlać dane zgodności

W tym artykule przedstawiono sposób programowe tworzenie zasad i zarządzaniem nimi. Także przedstawiono sposób wyświetlania zasobów stanów zgodności i zasady. Definicje zasad wymusza różnych zasad i akcje za pośrednictwem zasobów. Wymuszanie upewnia się, że zasoby pozostają zgodne ze standardami firmy, a umów dotyczących poziomu usług.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że są spełnione następujące wymagania wstępne:

1. Jeśli nie jest jeszcze zainstalować [ARMClient](https://github.com/projectkudu/ARMClient). To narzędzie, które wysyła żądania HTTP do usługi Azure Resource Manager na podstawie interfejsów API.
2. Zaktualizuj moduł AzureRM PowerShell do najnowszej wersji. Aby uzyskać więcej informacji na temat najnowszej wersji, zobacz programu Azure PowerShell https://github.com/Azure/azure-powershell/releases.
3. Zarejestruj dostawcę zasobów szczegółowych zasad informacji przy użyciu programu Azure PowerShell, aby upewnić się, że subskrypcja współpracuje z dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do wykonywania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
  ```

  Aby uzyskać więcej informacji o rejestracji i wyświetlanie dostawców zasobów, zobacz [dostawców zasobów i typów](../azure-resource-manager/resource-manager-supported-services.md).
4. Jeśli nie jest jeszcze zainstalować wiersza polecenia platformy Azure. Możesz pobrać najnowszą wersję na [zainstalować Azure CLI 2.0 w systemie Windows](/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-and-assign-a-policy-definition"></a>Utwórz i przypisz definicji zasad

Pierwszym krokiem procesu lepszą widoczność zasobów jest utworzenie i przypisanie zasad za pośrednictwem zasobów. Następnym krokiem jest, aby dowiedzieć się, jak programowo utworzyć i przypisać zasady. Przykład zasad inspekcji kont magazynu, które są otwarte dla wszystkich sieci publicznych, przy użyciu programu PowerShell, interfejsu wiersza polecenia Azure i żądań HTTP.

Następujące polecenia tworzenia definicji zasad dla warstwy standardowa. Warstwy standardowa pomaga osiągnąć zarządzania na dużą skalę, ocenę zgodności i korygowania. Aby uzyskać więcej informacji na temat warstw cenowych, zobacz [cennik zasadami Azure](https://azure.microsoft.com/pricing/details/azure-policy).

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Utwórz i przypisz definicję zasad przy użyciu programu PowerShell

1. Poniższy fragment kodu JSON umożliwia utworzenie pliku JSON o nazwie AuditStorageAccounts.json.

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

  Aby uzyskać więcej informacji o tworzeniu definicji zasad, zobacz [struktura definicji zasad Azure](policy-definition.md).
2. Uruchom następujące polecenie, aby utworzyć definicję zasad przy użyciu pliku AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy AuditStorageAccounts.json
  ```

  Polecenie tworzy definicję zasad o nazwie _inspekcji magazynu kont Otwórz do sieci publicznych_. Aby uzyskać więcej informacji na temat innych parametrów, których można użyć, zobacz [AzureRmPolicyDefinition nowy](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Po utworzeniu definicję zasad, można utworzyć przypisania zasad, uruchamiając następujące polecenia:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'

  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'

  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
  ```

  Zastąp _ContosoRG_ z nazwą grupy zasobów zamierzone.

Aby uzyskać więcej informacji o zarządzaniu zasad zasobów za pomocą modułu środowiska PowerShell usługi Azure Resource Manager, zobacz [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Utwórz i przypisz definicję zasad przy użyciu ARMClient

Poniższa procedura umożliwia utworzenie definicji zasad.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik JSON. Będzie wywoływać pliku w następnym kroku.

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

2. Utwórz definicję zasad przy użyciu następujące wywołanie:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Zastąp preceding_ &lt;subscriptionId&gt; z Identyfikatorem subskrypcji zamierzone.

Aby uzyskać więcej informacji o strukturze zapytania, zobacz [definicje zasad — Tworzenie lub aktualizacja](/rest/api/resources/policydefinitions/createorupdate).

Poniższa procedura umożliwia utworzenie przypisania zasad i przypisanie definicji zasad na poziomie grupy zasobów.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik JSON przypisania zasad. Zastąp przykładowe informacje przedstawione w &lt; &gt; symbole z własne wartości.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      },
      "sku": {
          "name": "A1",
          "tier": "Standard"
      }
  }
  ```

2. Utwórz przypisanie zasad przy użyciu następujące wywołanie:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Zastąp przykładowe informacje przedstawione w &lt; &gt; symbole z własne wartości.

  Aby uzyskać więcej informacji o wprowadzaniu HTTP wywołania interfejsu API REST, zobacz [zasobów interfejsu API REST Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Utwórz i przypisz definicję zasad z wiersza polecenia platformy Azure

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

2. Uruchom następujące polecenie, aby utworzyć definicję zasad:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Użyj następującego polecenia, aby utworzyć przypisanie zasad. Zastąp przykładowe informacje przedstawione w &lt; &gt; symbole z własne wartości.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
  ```

Identyfikator definicji zasad można uzyskać za pomocą następującego polecenia programu PowerShell:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Identyfikator definicji zasad dla definicji zasad, które utworzono powinien wyglądać następująco:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Aby uzyskać więcej informacji dotyczących sposobu zarządzania zasad zasobów z wiersza polecenia platformy Azure, zobacz [zasad zasobów Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

W przypisaniu zasobu nie jest zgodny, jeśli nie będzie zgodna z zasadami lub inicjatywy reguły. W poniższej tabeli przedstawiono, jak różne akcje zasady działają z programem oceny warunku dla Wynikowy stan zgodności:

| **Stan zasobu** | **Akcja** | **Ocena zasad** | **Stan zgodności** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Zgodne |
| Nowa | Audit, AuditIfNotExist\* | True | Niezgodne |
| Nowa | Audit, AuditIfNotExist\* | False | Zgodne |

\* Akcje Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE. Ponadto akcje wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Aby lepiej zrozumieć, jak zasoby są oznaczone jako niezgodna, Użyjmy przykład przypisanie zasady utworzone powyżej.

Załóżmy na przykład, ma grupa zasobów — ContsoRG, z niektórych kont magazynu (wyróżnionych kolorem czerwonym), które są dostępne do sieci publicznych.

![Konta magazynu uwidaczniany w sieciach publicznych](./media/policy-insights/resource-group01.png)

W tym przykładzie należy zachować ostrożność przy zagrożenia bezpieczeństwa. Teraz, po utworzeniu przypisania zasad jest obliczane dla wszystkich kont magazynu w grupie zasobów ContosoRG. Inspekcja on trzy konta magazynu niezgodnych, w związku z tym zmienianie ich stany do **niezgodnych.**

![Przeprowadzono inspekcję kont magazynu niezgodnych](./media/policy-insights/resource-group03.png)

Poniższa procedura umożliwia zidentyfikowanie zasoby w grupie zasobów, które nie są zgodne z przypisania zasad. W tym przykładzie zasoby są kont magazynu w grupie zasobów ContosoRG.

1. Pobierz identyfikator przypisania zasad, uruchamiając następujące polecenia:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object {$_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks'}

  $policyAssignment.PolicyAssignmentId
  ```

  Aby uzyskać więcej informacji na temat uzyskiwania Identyfikatora przypisanie zasad, zobacz [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Uruchom następujące polecenie, aby mieć identyfikatory zasobu niezgodne zasoby skopiowane do pliku JSON:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Wyniki powinny być podobne do następujących:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Wyniki są równoważne co zwykle zobaczysz kategorii **niezgodne zasoby** w [widok portalu Azure](assign-policy-definition.md#identify-non-compliant-resources).

Obecnie niezgodnych zasobów są tylko określone przy użyciu portalu Azure i żądań HTTP. Aby uzyskać więcej informacji o zapytaniach dotyczących stanów zasad, zobacz [stan zasad](/rest/api/policy-insights/policystates) artykule informacyjnym na temat interfejsu API.

## <a name="view-policy-events"></a>Wyświetl zdarzenia dotyczące zasad

Wynik oceny zasad jest generowany, gdy zasób jest tworzony lub aktualizowany. Wyniki są nazywane _zdarzenia dotyczące zasad_. Uruchom następującą kwerendę, aby wyświetlić wszystkie zdarzenia dotyczące zasad skojarzonych z przypisania zasad.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Takie jak Państwa zasad można wyświetlić tylko zdarzenia dotyczące zasad żądań HTTP. Aby uzyskać więcej informacji o zapytaniach dotyczących zdarzenia dotyczące zasad, zobacz [zdarzenia dotyczące zasad](/rest/api/policy-insights/policyevents) artykule.

## <a name="change-a-policy-assignments-pricing-tier"></a>Warstwa cenowa zmiany przypisania zasad

Można użyć *AzureRmPolicyAssignment zestaw* polecenia cmdlet programu PowerShell do aktualizacji cen warstwy Standard lub wolne dla istniejącego przypisania zasad. Na przykład:

```azurepowershell-interactive
Set-AzureRmPolicyAssignment -Id '/subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID>' -Sku @{Name='A1';Tier='Standard'}
```

Aby uzyskać więcej informacji na temat polecenia cmdlet, zobacz [AzureRmPolicyAssignment zestawu](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment).

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj następujące artykuły, aby uzyskać więcej informacji na temat poleceń i zapytań w tym artykule.

- [Zasoby interfejsu API Azure REST](/rest/api/resources/)
- [Moduły programu PowerShell Menedżera zasobów Azure](/powershell/module/azurerm.resources/#policies)
- [Azure CLI zasady poleceń](/cli/azure/policy?view=azure-cli-latest)
- [Dostawca zasobów Insights zasad dokumentacji interfejsu API REST](/rest/api/policy-insights)
