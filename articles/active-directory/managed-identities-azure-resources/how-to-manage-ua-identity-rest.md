---
title: Jak zarządzać Azure tożsamości przypisanych przez użytkownika zarządzanego przy użyciu usługi REST
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania, użytkownik przypisany zarządzanych tożsamości w celu wykonywania wywołań interfejsu API REST.
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
ms.openlocfilehash: dc7abd4bdec30ae870ff6add33d4b9b1c08b5bbd
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159643"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzane tożsamości dla zasobów platformy Azure umożliwia usług platformy Azure do usługi uwierzytelniania to uwierzytelnianie pomocy technicznej usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć, listy i Usuń przypisanych przez użytkownika tożsamości zarządzanej przy użyciu programu CURL wykonywania wywołań interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Jeśli używasz Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal.
- Jeśli używasz [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [dystrybucji systemu Linux, OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instalacji lokalnej konsoli wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Jeśli używasz lokalnej konsoli wiersza polecenia platformy Azure, zaloguj się do platformy Azure za pomocą `az login` przy użyciu konta, który jest skojarzony z subskrypcją platformy Azure, o których chcesz wdrożyć lub pobrać informacji o tożsamości zarządzanej przypisanych przez użytkownika.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości zarządzanej przypisanych przez użytkownika (lista).
- Pobieranie elementu nośnego dostęp do tokenu przy użyciu `az account get-access-token` do wykonywania następujących operacji przypisanych przez użytkownika z tożsamości zarządzanej.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika 

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, i `<ACCESS TOKEN>` wartości wybranymi samodzielnie wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

Aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<ACCESS TOKEN>` wartości wybranymi samodzielnie wartościami:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

Aby usunąć tożsamości zarządzanej przypisanych przez użytkownika, użyj następującego żądania programu CURL do interfejsu API usługi Azure Resource Manager. Zastąp `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<ACCESS TOKEN>` wartości parametrów własnymi wartościami:

> [!NOTE]
> Usuwanie tożsamości zarządzanej przypisanych przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, który został przypisany do. Aby usunąć użytkownik przypisany zarządzanych z maszyny Wirtualnej przy użyciu programu CURL, zobacz [usuwanie tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości zarządzanej użytkownik przypisany do usługi Azure VM/VMSS przy użyciu programu CURL, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) i [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu wywołań interfejsu API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity).


