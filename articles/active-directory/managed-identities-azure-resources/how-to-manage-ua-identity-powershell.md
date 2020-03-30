---
title: Tworzenie, wyświetlanie listy & usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu programu Azure PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, listy i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu programu Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547402"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu programu Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure z tożsamością zarządzaną w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć, wyświetlić listę i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu programu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell,](/powershell/azure/install-az-ps) jeśli jeszcze tego nie zrobiłeś.
- Jeśli używasz programu PowerShell lokalnie, wykonaj również te czynności: 
    - Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.
    - Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Uruchom polecenie `Install-Module -Name PowerShellGet -AllowPrerelease`, aby pobrać wersję wstępną modułu `PowerShellGet` (po uruchomieniu tego polecenia może być konieczne uruchomienie polecenia `Exit` umożliwiającego zakończenie bieżącej sesji programu PowerShell w celu zainstalowania modułu `Az.ManagedServiceIdentity`).
    - Uruchom, `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` aby zainstalować wersję wstępną `Az.ManagedServiceIdentity` modułu, aby wykonać operacje tożsamości zarządzanej przypisane przez użytkownika w tym artykule.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby utworzyć tożsamość zarządzaną przypisaną `New-AzUserAssignedIdentity` przez użytkownika, użyj polecenia. Parametr `ResourceGroupName` określa grupę zasobów, w której ma być tworzę tożsamość zarządzaną przypisaną przez użytkownika, a `-Name` parametr określa jego nazwę. Zastąp `<RESOURCE GROUP>` wartości i `<USER ASSIGNED IDENTITY NAME>` parametrów własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić listę/odczytywanie tożsamości zarządzanej przypisanej przez użytkownika, twoje konto wymaga przypisania roli [Operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika, użyj polecenia [Get-AzUserAssigned].  Parametr `-ResourceGroupName` określa grupę zasobów, w której została utworzona tożsamość zarządzana przypisana przez użytkownika. Wymień własną `<RESOURCE GROUP>` wartość:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi tożsamości zarządzane przypisane przez `"Microsoft.ManagedIdentity/userAssignedIdentities"` użytkownika mają `Type`wartość zwróconą dla klucza.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby usunąć tożsamość zarządzaną przypisaną `Remove-AzUserAssignedIdentity` przez użytkownika, użyj tego polecenia.  Parametr `-ResourceGroupName` określa grupę zasobów, w której została utworzona `-Name` tożsamość przypisana przez użytkownika, a parametr określa jego nazwę. `<RESOURCE GROUP>` Zastąp `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, do którego został przypisany. Przypisania tożsamości muszą być usuwane oddzielnie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę i więcej szczegółów dotyczących tożsamości zarządzanych programu Azure PowerShell dla poleceń zasobów platformy Azure, zobacz [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
