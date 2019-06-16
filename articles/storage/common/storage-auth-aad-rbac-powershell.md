---
title: Zarządzania prawami dostępu do usługi Azure AD do danych obiektów blob i kolejek za pomocą funkcji RBAC — usługi Azure Storage za pomocą programu Azure PowerShell
description: Przypisywanie dostępu do kontenerów i kolejki przy użyciu kontroli dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell. Usługa Azure Storage obsługuje niestandardowe i wbudowane role RBAC do uwierzytelniania za pomocą usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e850b915cd01b6bacd70d6df7752eeb83f7101d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153860"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu kontroli RBAC przy użyciu programu PowerShell

Azure Active Directory (Azure AD) autoryzuje praw dostępu do zabezpieczonych zasobów przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowane role kontroli RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do kontenerów i kolejki. 

Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń usługi Azure AD, Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Może należeć do poziomu subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki zakresu dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, nazwy głównej usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano, jak za pomocą programu Azure PowerShell listy wbudowane role kontroli RBAC i przypisać je do użytkowników. Aby uzyskać więcej informacji o używaniu programu Azure PowerShell, zobacz [Omówienie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla kolejek i obiektów blob

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określenie zakresu zasobów 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Listę dostępnych ról RBAC

Aby wyświetlić listę dostępnych wbudowane role kontroli RBAC przy użyciu programu Azure PowerShell, użyj [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) polecenia:

```powershell
Get-AzRoleDefinition | FT Name, Description
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

Aby przypisać rolę RBAC do użytkownika, należy użyć [New AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) polecenia. Format polecenia mogą się różnić w zależności od zakresu przypisania. Poniższe przykłady pokazują, jak przypisać rolę użytkownikowi w różnych zakresach.

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę ograniczone do kontenera, należy określić ciąg zawierający zakres kontener dla `--scope` parametru. Zakres dla kontenera ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Poniższy przykład przypisuje **Współautor danych obiektu Blob magazynu** roli do użytkownika o określonym zakresie do kontenera o nazwie *przykładowy kontener*. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę ograniczone do kolejki, należy określić ciąg zawierający zakres kolejki `--scope` parametru. Zakres dla kolejki ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Poniższy przykład przypisuje **Współautor danych kolejki magazynu** roli do użytkownika o określonym zakresie do kolejki o nazwie *kolejki przykładowe*. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę ograniczone do konta magazynu, należy określić zakres zasób konta magazynu dla `--scope` parametru. Zakres dla konta magazynu ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

W poniższym przykładzie przedstawiono sposób zakresu **czytnik danych obiektu Blob magazynu** roli do użytkownika na poziomie konta magazynu. Koniecznie Zastąp przykładowe wartości wybranymi samodzielnie wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę do zakresu grupy zasobów, określ nazwę grupy zasobów lub identyfikator `--resource-group` parametru. Poniższy przykład przypisuje **czytnik danych kolejki magazynu** roli do użytkownika na poziomie grupy zasobów. Koniecznie Zastąp przykładowe wartości i wartości zastępcze w nawiasach własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę do subskrypcji zakresu, określić zakres dla subskrypcji `--scope` parametru. Zakres subskrypcji ma postać:

```
/subscriptions/<subscription>
```

Poniższy przykład pokazuje, jak przypisać **czytnik danych obiektu Blob magazynu** roli do użytkownika na poziomie konta magazynu. Koniecznie Zastąp przykładowe wartości wybranymi samodzielnie wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [Udzielanie dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
