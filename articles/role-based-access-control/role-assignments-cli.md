---
title: Dodawanie lub usuwanie przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usług lub tożsamościom zarządzanym przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245670"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Dodawanie lub usuwanie przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)
- [Bash w usłudze Azure Cloud Shell](/azure/cloud-shell/overview) lub [azure cli](/cli/azure)

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby dodać lub usunąć przypisania ról, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111`. Identyfikator można uzyskać za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator obiektu dla użytkownika usługi Azure AD, można użyć [programu AZ Ad User Show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupa

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, możesz użyć [programu AZ Ad Group Show](/cli/azure/ad/group#az-ad-group-show) lub [az ad group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator obiektu dla jednostki usługi Azure AD (tożsamość używana przez aplikację), można użyć [listy az ad sp](/cli/azure/ad/sp#az-ad-sp-list). Dla jednostki usługi należy użyć identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W RBAC, aby udzielić dostępu, należy dodać przypisanie roli.

### <a name="user-at-a-resource-group-scope"></a>Użytkownik w zakresie grupy zasobów

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zasobów, użyj [przydziału roli az create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautora maszyny wirtualnej* do *\@patlong contoso.com* użytkownika w zakresie grupy zasobów *sprzedaży farmaceutycznej:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Używanie unikatowego identyfikatora roli

Istnieje kilka razy, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- Używasz roli podglądu, która ma **(Podgląd)** w nazwie. Po zwolnieniu roli rola zostanie zmieniona.

> [!IMPORTANT]
> Wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet jeśli nazwa roli zostanie zmieniona, identyfikator roli nie ulegnie zmianie. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym jeśli rola zostanie zmieniona, skrypty są bardziej prawdopodobne, aby działać.

Aby dodać przypisanie roli przy użyciu unikatowego identyfikatora roli zamiast nazwy roli, użyj [programu AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

W poniższym przykładzie przypisuje rolę [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do *użytkownika\@patlong contoso.com* w zakresie grupy zasobów sprzedaży *farmaceutycznej.* Aby uzyskać unikatowy identyfikator roli, można użyć [listy definicji roli az](/cli/azure/role/definition#az-role-definition-list) lub zobacz [Wbudowane role zasobów platformy Azure.](built-in-roles.md)

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupa w zakresie subskrypcji

Aby dodać przypisanie roli dla grupy, użyj [programu AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu grupy, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje *rolę czytelnika* do grupy *Ann Mack Team* o identyfikatorze 22222222-2222-2222-2222-2222222222222222222222 w zakresie subskrypcji.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Grupowanie w zakresie zasobu

Aby dodać przypisanie roli dla grupy, użyj [programu AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu grupy, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

W poniższym przykładzie przypisuje rolę *współautora maszyny wirtualnej* do grupy *Zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-22222222222222222222222 w zakresie zasobów sieci wirtualnej o nazwie *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplikacja w zakresie grupy zasobów

Aby dodać przypisanie roli dla aplikacji, użyj [programu AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu aplikacji, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautora maszyny wirtualnej* do aplikacji o identyfikatorze obiektu 44444444-4444-4444-4444-44444444444 w zakresie grupy zasobów *sprzedaży farmaceutycznej.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Użytkownik w zakresie subskrypcji

Aby dodać przypisanie roli dla użytkownika w zakresie subskrypcji, użyj [programu AZ Przypisanie roli create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator subskrypcji, można go znaleźć w bloku **Subskrypcje** w witrynie Azure portal lub można użyć [listy kont AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Poniższy przykład przypisuje rolę *czytelnika* do *annm\@example.com* użytkownika w zakresie subskrypcji.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Użytkownik w zakresie grupy zarządzania

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zarządzania, użyj [programu AZ Przypisanie roli create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **Grupy zarządzania** w witrynie Azure portal lub można użyć [listy grup zarządzania kontem AZ](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Poniższy przykład przypisuje rolę *czytnika rozliczeń* do *\@alain example.com* użytkownika w zakresie grupy zarządzania.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nowy dyrektor serwisu

Jeśli utworzysz nową jednostkę usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, to przypisanie roli może w niektórych przypadkach zakończyć się niepowodzeniem. Na przykład jeśli używasz skryptu do tworzenia nowej tożsamości zarządzanej, a następnie spróbuj przypisać rolę do tego podmiotu usługi, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Podmiot usługi jest tworzony w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który nie replikował jeszcze jednostki usługi. Aby rozwiązać ten scenariusz, należy określić typ główny podczas tworzenia przypisania roli.

Aby dodać przypisanie roli, użyj [programu AZ przypisania roli create](/cli/azure/role/assignment#az-role-assignment-create), określ wartość dla `--assignee-object-id`programu , a następnie ustaw `--assignee-principal-type` wartość `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje rolę *współautora maszyny wirtualnej* do tożsamości zarządzanej *testu msi* w zakresie grupy zasobów *pharma-sales:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W RBAC, aby usunąć dostęp, należy usunąć przypisanie roli za pomocą [az przypisania roli delete:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

W poniższym przykładzie usuwa przypisanie roli *współautora maszyny wirtualnej* z *\@patlong contoso.com* użytkownika w grupie zasobów *sprzedaży farmaceutycznej:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Poniższy przykład usuwa *rolę czytelnika* z grupy *Ann Mack Team* o identyfikatorze 22222222-2222-2222-2222-2222222222222222222222 w zakresie subskrypcji. Aby uzyskać informacje o tym, jak uzyskać identyfikator obiektu grupy, zobacz [Pobierz identyfikatory obiektów](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Poniższy przykład usuwa rolę *czytnika rozliczeń* z *\@alain example.com* użytkownika w zakresie grupy zarządzania. Aby uzyskać identyfikator grupy zarządzania, można użyć [listy grup zarządzania kontem AZ](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-list-cli.md)
- [Zarządzanie zasobami i grupami zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/cli-azure-resource-manager.md)
