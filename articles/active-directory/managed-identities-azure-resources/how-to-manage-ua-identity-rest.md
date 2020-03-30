---
title: Zarządzanie tożsamościami zarządzanymi przypisanymi przez użytkownika przy użyciu rest - Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, listy i usuwania tożsamości zarządzanej przypisanej przez użytkownika do wywołania interfejsu API REST.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547427"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure możliwość uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć, wyświetlić listę i usunąć przypisaną przez użytkownika tożsamość zarządzaną przy użyciu funkcji CURL do wykonywania wywołań interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Jeśli używasz systemu Windows, zainstaluj [podsystem windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [powłoki chmury Azure](../../cloud-shell/overview.md) w witrynie Azure portal.
- Jeśli używasz [podsystemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub systemu [operacyjnego dystrybucji systemu Linux,](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) [zainstaluj konsolę lokalną interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Jeśli używasz konsoli lokalnej interfejsu wiersza `az login` polecenia platformy Azure, zaloguj się na platformie Azure przy użyciu konta skojarzonego z subskrypcją platformy Azure, którą chcesz wdrożyć lub pobrać informacje o tożsamości zarządzanej przypisane przez użytkownika.
- Pobieranie tokenu dostępu na `az account get-access-token` okaziciela przy użyciu do wykonywania następujących operacji tożsamości zarządzanej przypisane przez użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        |

**Treść żądania**

|Nazwa  |Opis  |
|---------|---------|
|location     | Wymagany. Lokalizacja zasobu.        |

## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić listę/odczytywanie tożsamości zarządzanej przypisanej przez użytkownika, twoje konto wymaga przypisania roli [Operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z żadnego zasobu, do którego został przypisany. Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny Wirtualnej przy użyciu funkcji CURL, zobacz [Usuwanie tożsamości przypisanej przez użytkownika z maszyny Wirtualnej platformy Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure/maszyn wirtualnych przy użyciu funkcji CURL, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) i [konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu wywołań interfejsu API REST.](qs-configure-rest-vmss.md#user-assigned-managed-identity)
