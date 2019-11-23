---
title: 'Quickstart: Create a blueprint with PowerShell'
description: In this quickstart, you use Azure Blueprints to create, define, and deploy artifacts using the PowerShell.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: ed337fa040804159dae3194de1befc43ff2b698f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327465"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Quickstart: Define and Assign an Azure Blueprint with PowerShell

Znajomość sposobu tworzenia i przypisywania strategii umożliwia definiowanie typowych wzorców tworzenia konfiguracji wielokrotnego użytku, które można szybko wdrażać, w oparciu o szablony usługi Resource Manager, zasady, zabezpieczenia itd. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

If it isn't already installed, follow the instructions in [Add the Az.Blueprint module](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) to install and validate the **Az.Blueprint** module from the PowerShell Gallery.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. Utworzymy strategię o nazwie „MyBlueprint” służącą do konfigurowania przypisań ról i zasad dla subskrypcji. Następnie dodamy grupę zasobów, szablon usługi Resource Manager i przypisanie roli w grupie zasobów.

> [!NOTE]
> When using PowerShell, the _blueprint_ object is created first. Dla każdego _artefaktu_ zawierającego parametry, który ma zostać dodany, parametry _strategii_ początkowej muszą zostać zdefiniowane wcześniej.

1. Utwórz obiekt _strategii_ początkowej. The **BlueprintFile** parameter takes a JSON file which includes properties about the blueprint, any resource groups to create, and all of the blueprint level parameters. Parametry są określane podczas przypisywania i używane przez artefakty dodane w kolejnych krokach.

   - JSON file - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Use the filename _blueprint.json_ when creating your blueprint definitions programmatically.
     > This file name is used when calling [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     The blueprint object is created in the default subscription by default. To specify the management group, use parameter **ManagementGroupId**. To specify the subscription, use parameter **SubscriptionId**.

1. Dodaj przypisanie roli w subskrypcji. The **ArtifactFile** defines the _kind_ of artifact, the properties align to the role definition identifier, and the principal identities are passed as an array of values. W poniższym przykładzie tożsamości podmiotu zabezpieczeń, którym przyznano określoną rolę, są konfigurowane za pomocą parametru określonego podczas przypisywania strategii. W tym przykładzie użyto wbudowanej roli _Współautor_ o identyfikatorze GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - JSON file - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Dodaj przypisanie zasad w subskrypcji. The **ArtifactFile** defines the _kind_ of artifact, the properties that align to a policy or initiative definition, and configures the policy assignment to use the defined blueprint parameters to configure during blueprint assignment. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Dodaj kolejne przypisanie zasad dla tagu magazynu (używając ponownie parametru _storageAccountType_) w subskrypcji. Ten dodatkowy artefakt przypisania zasad pokazuje, że parametr zdefiniowany w strategii może być używany przez więcej niż jeden artefakt. W tym przykładzie parametr **storageAccountType** służy do określania tagu w grupie zasobów. Ta wartość zawiera informacje o koncie magazynu, które zostanie tworzone w następnym kroku. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Dodaj szablon w grupie zasobów. The **TemplateFile** for a Resource Manager template includes the normal JSON component of the template. Szablon używa również wielokrotnie parametrów strategii **storageAccountType**, **tagName** i **tagValue**, przekazując każdy z nich do szablonu. The blueprint parameters are available to the template by using parameter **TemplateParameterFile** and inside the template JSON that key-value pair is used to inject the value. The blueprint and template parameter names could be the same.

   - JSON Azure Resource Manager template file - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON Azure Resource Manager template parameter file - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Dodaj przypisanie roli w grupie zasobów. Podobnie jak w poprzednim wpisie przypisania roli, w poniższym przykładzie użyto identyfikatora definicji dla roli **Właściciel** i podano mu inny parametr ze strategii. W tym przykładzie użyto wbudowanej roli _Właściciel_ o identyfikatorze GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - JSON file - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu artefaktów do strategii czas na jej opublikowanie. Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Wartość zmiennej `{BlueprintVersion}` jest ciągiem liter, cyfr i łączników (bez spacji czy innych znaków specjalnych) o maksymalnej długości 20 znaków. Używaj wartości, które są unikatowe i informacyjne, na przykład **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Once a blueprint is published using PowerShell, it's assignable to a subscription. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji. The **Blueprint** parameter specifies the blueprint to assign. To provide name, location, identity, lock, and blueprint parameters, use the matching PowerShell parameters on the `New-AzBlueprintAssignment` cmdlet or provide them in the **AssignmentFile** parameter JSON file.

1. Uruchom wdrażanie strategii, przypisując ją do subskrypcji. As the **contributors** and **owners** parameters require an array of objectIds of the principals to be granted the role assignment, use [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) for gathering the objectIds for use in the **AssignmentFile** for your own users, groups, or service principals.

   - JSON file - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Tożsamość zarządzana przypisana przez użytkownika

     W przypisaniu strategii można również użyć [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md).
     In this case, the **identity** portion of the JSON assignment file changes as follows. Replace `{tenantId}`, `{subscriptionId}`, `{yourRG}`, and `{userIdentity}` with your tenantId, subscriptionId, resource group name, and the name of your user-assigned managed identity, respectively.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **Tożsamość zarządzana przypisana przez użytkownika** może należeć do dowolnej subskrypcji i grupy zasobów, do której użytkownik przypisujący strategię ma uprawnienia.

     > [!IMPORTANT]
     > Strategie nie zarządzają tożsamością przypisaną przez użytkownika. Użytkownicy są odpowiedzialni za przypisywanie wystarczających ról i uprawnień — w przeciwnym razie przypisanie strategii kończy się niepowodzeniem.

## <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Strategię można usunąć z subskrypcji. Usunięcie często przeprowadza się, gdy zasoby artefaktu przestają być potrzebne. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. To remove a blueprint assignment, use the `Remove-AzBlueprintAssignment` cmdlet:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Następne kroki

In this quickstart, you've created, assigned, and removed a blueprint with PowerShell. To learn more about Azure Blueprints, continue to the blueprint lifecycle article.

> [!div class="nextstepaction"]
> [Learn about the blueprint lifecycle](./concepts/lifecycle.md)