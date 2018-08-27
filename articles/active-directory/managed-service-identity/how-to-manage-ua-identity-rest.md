---
title: Jak zarządzać użytkownika usługi Azure przypisane zarządzanych tożsamości przy użyciu usługi REST
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania użytkownika przypisać tożsamość zarządzaną wykonywania wywołań interfejsu API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: a6241c105019f04df09080a89e8fe3b77b5f9385
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888767"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Tworzenie listy i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu wywołań interfejsu API REST

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość zarządzana umożliwia usług platformy Azure do usługi uwierzytelniania to uwierzytelnianie pomocy technicznej usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć listę i Usuń użytkownika z przypisaną tożsamości zarządzanej przy użyciu programu CURL wykonywania wywołań interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Jeśli używasz Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal.
- Jeśli używasz [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [dystrybucji systemu Linux, OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instalacji lokalnej konsoli wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Jeśli używasz lokalnej konsoli wiersza polecenia platformy Azure, zaloguj się do platformy Azure za pomocą `az login` przy użyciu konta, który jest skojarzony z subskrypcją platformy Azure, o których chcesz wdrożyć lub pobrać użytkownik przypisany informacji o zarządzanych tożsamości.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości przypisanych przez użytkownika (lista).
- Pobieranie elementu nośnego dostęp do tokenu przy użyciu `az account get-access-token` przeprowadzić następujący użytkownik przypisany operacje tożsamość zarządzaną.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika 

Aby utworzyć zarządzanych tożsamości przypisanych przez użytkownika, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, i `<ACCESS TOKEN>` wartości wybranymi samodzielnie wartościami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Zarządzanych tożsamości przypisanych przez użytkownika z listy

Aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<ACCESS TOKEN>` wartości wybranymi samodzielnie wartościami:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie zarządzanych tożsamości przypisanych przez użytkownika

Aby usunąć zarządzanych tożsamości przypisanych przez użytkownika, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<ACCESS TOKEN>` wartości parametrów własnymi wartościami:

> [!NOTE]
> Usuwanie tożsamości przypisanych przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, który został przypisany do. Aby usunąć użytkownika z przypisaną zarządzanych z maszyny Wirtualnej przy użyciu programu CURL, zobacz [usuwanie tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości przypisanych przez użytkownika do usługi Azure VM/VMSS przy użyciu programu CURL, zobacz [konfigurowania tożsamości zarządzanej na Maszynie wirtualnej platformy Azure przy użyciu programu CURL](qs-configure-rest-vm.md#user-assigned-identity) i [Konfigurowanie zarządzanych tożsamości maszyny wirtualnej zestawu skalowania przy użyciu programu CURL ](qs-configure-rest-vmss.md#user-assigned-identity).


