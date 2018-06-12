---
title: Zarządzanie kontrolą dostępu na podstawie ról (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania opartego na rolach kontrola dostępu (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą listę ról i akcje ról i przypisywania ról do zakresów subskrypcji i aplikacji.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8b50d04bcbd067059bf4816468585e5d56a63d41
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266741"
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Zarządzanie oparte na rolach kontrola dostępu przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](role-assignments-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](role-assignments-cli.md)
> * [Interfejs API REST](role-assignments-rest.md)


Przy użyciu kontroli dostępu opartej na rolach (RBAC) należy zdefiniować dostęp dla użytkowników, grup i nazwy główne usług poprzez przypisywanie ról w określonym zakresie. W tym artykule opisano sposób zarządzania przypisań ról przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarządzać przypisań ról przy użyciu interfejsu wiersza polecenia Azure, musi mieć następujące wymagania wstępne:

* [Interfejs wiersza polecenia platformy Azure 2.0](/cli/azure). Możesz używać go w przeglądarce dzięki usłudze [Azure Cloud Shell](../cloud-shell/overview.md), albo [zainstalować](/cli/azure/install-azure-cli) w systemach macOS, Linux i Windows, a następnie uruchamiać z wiersza polecenia.

## <a name="list-role-definitions"></a>Definicje ról listy

Aby wyświetlić listę wszystkich dostępnych definicji ról, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Poniższy przykład zawiera nazwę i opis wszystkich dostępnych definicji ról:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Poniższy przykład zawiera listę wszystkich definicji wbudowanej roli:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Akcje listy definicji roli

Aby wyświetlić listę akcji definicji roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Na poniższych listach przykład *współautora* definicji roli:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Na poniższych listach przykład *akcje* i *notActions* z *współautora* roli:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Poniższy przykład zawiera listę działań *Współautor·maszyny·wirtualnej* roli:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-role-assignments"></a>Lista przypisań ról

### <a name="list-role-assignments-for-a-user"></a>Lista przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról określonego użytkownika, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania ograniczone do subskrypcji. Aby wyświetlić przypisania ograniczone w zależności od zasobów w grupie, należy użyć `--all`.

Poniższy przykład zawiera listę przypisań ról, które są przypisane bezpośrednio do *patlong@contoso.com* użytkownika:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Lista przypisań ról dla grupy zasobów.

Aby wyświetlić listę przypisań ról, które istnieją dla grupy zasobów, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład zawiera listę przypisań ról *pharma sprzedaży projectforecast* grupy zasobów:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="create-role-assignments"></a>Tworzenie przypisań ról

### <a name="create-a-role-assignment-for-a-user"></a>Tworzy przypisanie roli użytkownika

Aby utworzyć przypisanie roli użytkownika w zakresie grupy zasobów, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę *patlong@contoso.com* użytkownika na *pharma sprzedaży projectforecast* zakres grupy zasobów:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Tworzy przypisanie roli w grupie

Aby utworzyć przypisanie roli, grupy, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *czytnika* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *projektu sieci, pharma sprzedaży w-*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Tworzenie przypisania roli dla aplikacji

Aby utworzyć rolę dla aplikacji, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *Współautor·maszyny·wirtualnej* roli do aplikacji z 44444444-4444-4444-4444-444444444444 Identyfikatora obiektu w *pharma sprzedaży projectforecast* grupy zasobów zakres. Aby uzyskać identyfikator aplikacji, można użyć [listy aplikacji usługi ad az](/cli/azure/ad/app#az-ad-app-list) lub [Pokaż aplikacji ad az](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>Usuń przypisanie roli

Aby usunąć przypisanie roli, należy użyć [usunąć przypisanie roli az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

Poniższy przykład umożliwia usunięcie *Współautor·maszyny·wirtualnej* przypisania roli z *patlong@contoso.com* użytkownika na *pharma sprzedaży projectforecast* zasobów Grupa:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

Poniższy przykład umożliwia usunięcie *czytnika* rolę z *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az-ad-group-list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Role niestandardowe

### <a name="list-custom-roles"></a>Lista ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

Oba poniższe przykłady listy role niestandardowe w bieżącej subskrypcji:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń

Aby utworzyć niestandardową rolę, użyj [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create). Definicja roli może być opisu JSON lub ścieżka do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

W poniższym przykładzie tworzona rola niestandardowa o nazwie *Operator maszyny wirtualnej*. Tę rolę niestandardową przypisuje dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* dostęp do dostawcy i przypisuje zasobów Aby rozpocząć, uruchom ponownie i monitorowania maszyn wirtualnych. Tę rolę niestandardową można w dwóch subskrypcji. W tym przykładzie używany jest plik JSON jako danych wejściowych.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń

Aby zaktualizować niestandardowej roli zabezpieczeń, należy najpierw użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list) można pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [aktualizacji definicji roli az](/cli/azure/role/definition#az-role-definition-update) można zapisać definicji roli zaktualizowane.

```azurecli
az role definition update --role-definition <role_definition>
```

W poniższym przykładzie dodano *Microsoft.Insights/diagnosticSettings/* operacji *akcje* z *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń

Aby usunąć niestandardową rolę, użyj [usunąć definicji roli az](/cli/azure/role/definition#az-role-definition-delete). Aby określić roli do usunięcia, użyj nazwy roli lub identyfikator roli. Aby określić identyfikator roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

W następującym przykładzie *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

