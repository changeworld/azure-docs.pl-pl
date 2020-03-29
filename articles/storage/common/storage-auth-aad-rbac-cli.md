---
title: Przypisywanie roli RBAC w celu uzyskania dostępu do danych za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przypisywać uprawnienia do podmiotu zabezpieczeń usługi Azure Active Directory za pomocą kontroli dostępu opartej na rolach (RBAC). Usługa Azure Storage obsługuje wbudowane i niestandardowe role RBAC do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891972"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Przypisywanie roli RBAC w celu uzyskania dostępu do danych obiektów blob i kolejek za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/overview.md) Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych obiektów blob lub kolejki.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

W tym artykule opisano sposób używania interfejsu wiersza polecenia platformy Azure do listy wbudowanych ról RBAC i przypisywania ich do użytkowników. Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure, zobacz [Interfejs wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określanie zakresu zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista dostępnych ról RBAC

Aby wyświetlić listę dostępnych wbudowanych ról RBAC za pomocą interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, użyj polecenia [listy definicji roli az:](/cli/azure/role/definition#az-role-definition-list)

```azurecli-interactive
az role definition list --out table
```

Zobaczysz wbudowane role danych usługi Azure Storage wymienione wraz z innymi wbudowanymi rolami platformy Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Przypisywanie roli RBAC do podmiotu zabezpieczeń

Aby przypisać rolę RBAC do podmiotu zabezpieczeń, użyj polecenia [utwórz przypisanie roli az.](/cli/azure/role/assignment#az-role-assignment-create) Format polecenia może się różnić w zależności od zakresu przypisania. Poniższe przykłady pokazują, jak przypisać rolę do użytkownika w różnych zakresach, ale można użyć tego samego polecenia, aby przypisać rolę do dowolnego podmiotu zabezpieczeń.

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę o zakresie do kontenera, należy określić ciąg `--scope` zawierający zakres kontenera dla parametru. Zakres kontenera jest w formie:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Poniższy przykład przypisuje **roli współautora danych obiektu blob magazynu** do użytkownika, o zakresie do poziomu kontenera. Pamiętaj, aby zastąpić wartości przykładowe i zastępcze w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę o zakresie do kolejki, należy określić ciąg `--scope` zawierający zakres kolejki dla parametru. Zakres kolejki jest w formularzu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Poniższy przykład przypisuje rolę **współautora danych kolejki magazynu** do użytkownika, o zakresie do poziomu kolejki. Pamiętaj, aby zastąpić wartości przykładowe i zastępcze w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę o zakresie do konta magazynu, należy określić `--scope` zakres zasobu konta magazynu dla parametru. Zakres konta magazynu jest w formularzu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

W poniższym przykładzie pokazano, jak przypisać rolę **czytnika danych obiektów blob magazynu** do użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę o zakresie do grupy zasobów, należy określić `--resource-group` nazwę grupy zasobów lub identyfikator parametru. Poniższy przykład przypisuje rolę **czytnika danych kolejki magazynu** do użytkownika na poziomie grupy zasobów. Pamiętaj, aby zastąpić wartości przykładowe i zastępcze w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę o zakresie do subskrypcji, należy określić zakres subskrypcji dla parametru. `--scope` Zakres subskrypcji jest w formie:

```
/subscriptions/<subscription>
```

W poniższym przykładzie pokazano, jak przypisać rolę **czytnika danych obiektów blob magazynu** do użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić wartości przykładowe własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Udziel dostępu do danych obiektów blob i kolejek platformy Azure za pomocą funkcji RBAC przy użyciu programu Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach w witrynie Azure Portal](storage-auth-aad-rbac-portal.md)
