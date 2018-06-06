---
title: Programowe tworzenie zasad i wyświetlać dane zgodności z zasadami usługi Azure
description: W tym artykule przedstawiono sposób programowe tworzenie i zarządzanie zasadami dla zasad usługi Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a83402316854b23fe85bff813dc9f5665bccd1fb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794814"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programowe tworzenie zasad i wyświetlać dane zgodności

W tym artykule przedstawiono sposób programowe tworzenie zasad i zarządzaniem nimi. Także przedstawiono sposób wyświetlania zasobów stanów zgodności i zasady. Definicje zasad wymusić różne zasady i wpływ na zasoby. Wymuszanie upewnia się, że zasoby pozostają zgodne ze standardami firmy, a umów dotyczących poziomu usług.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że są spełnione następujące wymagania wstępne:

1. Jeśli nie jest jeszcze zainstalować [ARMClient](https://github.com/projectkudu/ARMClient). To narzędzie, które wysyła żądania HTTP do usługi Azure Resource Manager na podstawie interfejsów API.
2. Zaktualizuj moduł AzureRM PowerShell do najnowszej wersji. Aby uzyskać więcej informacji na temat najnowszej wersji, zobacz [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Zarejestruj dostawcę zasobów szczegółowych zasad informacji przy użyciu programu Azure PowerShell, aby upewnić się, że subskrypcja współpracuje z dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do wykonywania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Aby uzyskać więcej informacji o rejestracji i wyświetlanie dostawców zasobów, zobacz [dostawców zasobów i typów](../azure-resource-manager/resource-manager-supported-services.md).
4. Jeśli nie jest jeszcze zainstalować wiersza polecenia platformy Azure. Możesz pobrać najnowszą wersję na [zainstalować Azure CLI 2.0 w systemie Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Utwórz i przypisz definicji zasad

Pierwszym krokiem procesu lepszą widoczność zasobów jest utworzenie i przypisanie zasad za pośrednictwem zasobów. Następnym krokiem jest, aby dowiedzieć się, jak programowo utworzyć i przypisać zasady. Przykład zasad inspekcji kont magazynu, które są otwarte dla wszystkich sieci publicznych, przy użyciu programu PowerShell, interfejsu wiersza polecenia Azure i żądań HTTP.

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
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  Polecenie tworzy definicję zasad o nazwie _inspekcji magazynu kont Otwórz do sieci publicznych_. Aby uzyskać więcej informacji na temat innych parametrów, których można użyć, zobacz [AzureRmPolicyDefinition nowy](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Po utworzeniu definicję zasad, można utworzyć przypisania zasad, uruchamiając następujące polecenia:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
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

2. Tworzenie definicji zasad przy użyciu jednej z następujących połączeń:

  ```
  # For defining a policy in a subscription
  armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

  # For defining a policy in a management group
  armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Zastąp poprzedzających {subscriptionId} o identyfikatorze subskrypcji lub {managementGroupId} o identyfikatorze Twojej [grupy zarządzania](../azure-resource-manager/management-groups-overview.md).

  Aby uzyskać więcej informacji o strukturze zapytania, zobacz [definicje zasad — Tworzenie lub aktualizacja](/rest/api/resources/policydefinitions/createorupdate) i [definicje zasad — Tworzenie lub aktualizacja w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

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
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
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

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj następujące artykuły, aby uzyskać więcej informacji na temat poleceń i zapytań w tym artykule.

- [Zasoby interfejsu API Azure REST](/rest/api/resources/)
- [Moduły programu PowerShell Menedżera zasobów Azure](/powershell/module/azurerm.resources/#policies)
- [Azure CLI zasady poleceń](/cli/azure/policy?view=azure-cli-latest)
- [Dostawca zasobów Insights zasad dokumentacji interfejsu API REST](/rest/api/policy-insights)
- [Organizowanie zasobów z grupami zarządzania Azure](../azure-resource-manager/management-groups-overview.md)