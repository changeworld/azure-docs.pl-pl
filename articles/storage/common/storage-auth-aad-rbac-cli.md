---
title: Używanie wiersza polecenia platformy Azure do zarządzania usługi Azure AD praw dostępu do danych obiektów blob i kolejek przy użyciu RBAC — usługi Azure Storage
description: Przypisz dostęp do kontenerów i kolejki przy użyciu kontroli dostępu opartej na rolach (RBAC) za pomocą wiersza polecenia platformy Azure. Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC do uwierzytelniania za pomocą usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: dc2beda1ae017b5e81fddf08d0c7e88c785bcdf5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153893"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu wiersza polecenia platformy Azure

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do danych obiektów blob i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano jak listy wbudowane role kontroli RBAC i przypisać je do użytkowników za pomocą wiersza polecenia platformy Azure. Aby uzyskać więcej informacji o korzystaniu z wiersza polecenia platformy Azure, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określenie zakresu zasobów 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Listę dostępnych ról RBAC

Aby wyświetlić listę dostępnych wbudowane role kontroli RBAC przy użyciu wiersza polecenia platformy Azure, użyj [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list) polecenia:

```azurecli-interactive
az role definition list --out table
```

Zostanie wyświetlony na liście, wraz z innych wbudowanych ról na platformie Azure wbudowane role danych usługi Azure Storage:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Przypisz rolę RBAC do użytkownika

Aby przypisać rolę RBAC do użytkownika, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create) polecenia. Format polecenia mogą się różnić w zależności od zakresu przypisania. Poniższe przykłady pokazują, jak przypisać rolę użytkownikowi w różnych zakresach.

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę ograniczone do kontenera, należy określić ciąg zawierający zakres kontener dla `--scope` parametru. Zakres dla kontenera ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Poniższy przykład przypisuje **Współautor danych obiektu Blob magazynu** roli do użytkownika o określonym zakresie do kontenera o nazwie *przykładowy kontener*. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę ograniczone do kolejki, należy określić ciąg zawierający zakres kolejki `--scope` parametru. Zakres dla kolejki ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Poniższy przykład przypisuje **Współautor danych kolejki magazynu** roli do użytkownika o określonym zakresie do kolejki o nazwie *kolejki przykładowe*. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę ograniczone do konta magazynu, należy określić zakres zasób konta magazynu dla `--scope` parametru. Zakres dla konta magazynu ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Poniższy przykład pokazuje, jak przypisać **czytnik danych obiektu Blob magazynu** roli do użytkownika na poziomie konta magazynu. Koniecznie Zastąp przykładowe wartości wybranymi samodzielnie wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę do zakresu grupy zasobów, określ nazwę grupy zasobów lub identyfikator `--resource-group` parametru. Poniższy przykład przypisuje **czytnik danych kolejki magazynu** roli do użytkownika na poziomie grupy zasobów. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę do subskrypcji zakresu, określić zakres dla subskrypcji `--scope` parametru. Zakres subskrypcji ma postać:

```
/subscriptions/<subscription>
```

Poniższy przykład pokazuje, jak przypisać **czytnik danych obiektu Blob magazynu** roli do użytkownika na poziomie konta magazynu. Koniecznie Zastąp przykładowe wartości wybranymi samodzielnie wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu programu Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Udzielanie dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
