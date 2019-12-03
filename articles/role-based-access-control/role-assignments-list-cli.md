---
title: Wyświetlanie listy przypisań ról przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane są dostępne do korzystania z kontroli dostępu opartej na rolach (RBAC) platformy Azure i interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710440"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Wyświetlanie listy przypisań ról przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] w tym artykule opisano sposób wyświetlania przypisań ról przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Bash w Azure Cloud Shell](/azure/cloud-shell/overview) lub [interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról dla określonego użytkownika, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania bezpośrednie mające zakres do subskrypcji. Aby wyświetlić przydziały należące do zakresu zasobów lub grup, użyj `--all` i aby wyświetlić dziedziczone przydziały, użyj `--include-inherited`.

Poniższy przykład zawiera listę przypisań ról przypisanych bezpośrednio do *patlong\@contoso.com* użytkownika:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Tworzenie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę przypisań ról istniejących w zakresie grupy zasobów, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład zawiera listę przypisań ról dla grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Wyświetlanie listy przypisań ról dla grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, użyj [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu usług Azure RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md)
