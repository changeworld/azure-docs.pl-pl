---
title: Wdrażanie zasobów w dzierżawie
description: W tym artykule opisano sposób wdrażania zasobów w zakresie dzierżawy w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460266"
---
# <a name="create-resources-at-the-tenant-level"></a>Tworzenie zasobów na poziomie dzierżawy

W miarę dojrzewania organizacji może być konieczne zdefiniowanie i przypisanie [zasad](../../governance/policy/overview.md) lub [kontroli dostępu opartych na rolach](../../role-based-access-control/overview.md) w dzierżawie usługi Azure AD. Za pomocą szablonów na poziomie dzierżawy można deklaratywnie stosować zasady i przypisywać role na poziomie globalnym.

## <a name="supported-resources"></a>Obsługiwane zasoby

Można wdrożyć następujące typy zasobów na poziomie dzierżawy:

* [wdrożenia](/azure/templates/microsoft.resources/deployments) — dla szablonów zagnieżdżonych, które wdrażają w grupach zarządzania lub subskrypcjach.
* [zmiany zasadAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [zasadySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definicje ról](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schemat

Schemat używany dla wdrożeń dzierżawy różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrażania. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Wymagany dostęp

Główny wdrażający szablon musi mieć uprawnienia do tworzenia zasobów w zakresie dzierżawy. Podmiot zabezpieczeń musi mieć uprawnienia do`Microsoft.Resources/deployments/*`wykonywania akcji wdrażania ( ) i do tworzenia zasobów zdefiniowanych w szablonie. Na przykład, aby utworzyć grupę zarządzania, podmiot zabezpieczeń musi mieć uprawnienie współautora w zakresie dzierżawy. Aby utworzyć przypisania ról, podmiot zabezpieczeń musi mieć uprawnienie Owner.

Administrator globalny usługi Azure Active Directory nie ma automatycznie uprawnień do przypisywania ról. Aby włączyć wdrożenia szablonów w zakresie dzierżawy, administrator globalny musi wykonać następujące kroki:

1. Zwiększ poziom dostępu do konta, aby administrator globalny mógł przypisywać role. Aby uzyskać więcej informacji, zobacz [Podnoszenie poziomu dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania](../../role-based-access-control/elevate-access-global-admin.md).

1. Przypisz właściciela lub współautora do podmiotu zabezpieczeń, który musi wdrożyć szablony.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Podmiot zabezpieczeń ma teraz wymagane uprawnienia do wdrożenia szablonu.

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń dzierżawy różnią się od poleceń dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure należy użyć [utworzenia dzierżawy wdrożenia az:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

W przypadku programu Azure PowerShell należy użyć [funkcji New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

W przypadku interfejsu API REST należy użyć [wdrożeń — tworzenie lub aktualizowanie w zakresie dzierżawy](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie dzierżawy należy podać lokalizację dla wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wydrążenych zasobów. Lokalizacja wdrożenia określa, gdzie mają być przechowywane dane wdrożenia.

Można podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.json** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje istniejące wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony `InvalidDeploymentLocation`kod błędu, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń dzierżawy istnieją pewne ważne zagadnienia podczas korzystania z funkcji szablonu:

* Funkcja [resourceGroup()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [subscription()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [reference()](template-functions-resource.md#reference) i [list().](template-functions-resource.md#list)
* Użyj funkcji [tenantResourceId(),](template-functions-resource.md#tenantresourceid) aby uzyskać identyfikator zasobu dla zasobów, które są wdrażane na poziomie dzierżawy.

  Na przykład, aby uzyskać identyfikator zasobu dla definicji zasad, należy użyć:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Identyfikator zwróconego zasobu ma następujący format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Tworzenie grupy zarządzania

Poniższy [szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) tworzy grupę zarządzania.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Przypisywanie roli

Poniższy [szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) przypisuje rolę w zakresie dzierżawy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Szablony można również wdrażać na [poziomie subskrypcji](deploy-to-subscription.md) lub [grupy zarządzania](deploy-to-management-group.md).
