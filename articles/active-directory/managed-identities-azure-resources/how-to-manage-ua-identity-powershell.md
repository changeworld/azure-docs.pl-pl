---
title: Sposób tworzenia, wyświetlania i usuwania przypisanych przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania, przypisanych do użytkowników zarządzanych tożsamości przy użyciu programu Azure PowerShell.
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
ms.openlocfilehash: 886b56de194f38fbb4b94f96b92bff11f2288b37
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448606"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, listy i usuwać przypisane przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) Jeśli jeszcze go.
- Jeśli używasz programu PowerShell lokalnie, wykonaj również te czynności: 
    - Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.
    - Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Uruchom polecenie `Install-Module -Name PowerShellGet -AllowPrerelease`, aby pobrać wersję wstępną modułu `PowerShellGet` (po uruchomieniu tego polecenia może być konieczne uruchomienie polecenia `Exit` umożliwiającego zakończenie bieżącej sesji programu PowerShell w celu zainstalowania modułu `Az.ManagedServiceIdentity`).
    - Uruchom `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` do zainstalowania wstępnej wersji `Az.ManagedServiceIdentity` modułu, aby wykonać użytkownik przypisany zarządzanych tożsamości operacje, w tym artykule.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, użyj `New-AzUserAssignedIdentity` polecenia. `ResourceGroupName` Parametr określa grupę zasobów, gdzie trzeba utworzyć przypisanych przez użytkownika tożsamości zarządzanej, a `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

Na liście odczytu przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Do listy przypisanych do użytkowników zarządzanych tożsamości, użyj [Get-AzUserAssigned] polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono zarządzanych tożsamości przypisanych przez użytkownika. Zastąp `<RESOURCE GROUP>` swoją własną wartością:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi zarządzanych tożsamości przypisanych przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

Aby usunąć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Aby usunąć tożsamości zarządzanej przypisanych przez użytkownika, użyj `Remove-AzUserAssignedIdentity` polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono tożsamości przypisanych przez użytkownika i `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usuwanie tożsamości zarządzanej przypisanych przez użytkownika nie spowoduje usunięcia odwołania, z dowolnego zasobu, który został przypisany do. Przydziały tożsamości należy usunąć oddzielnie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną listę produktów oraz więcej szczegółów dotyczących programu Azure PowerShell zarządzanych tożsamości dla poleceń zasobów platformy Azure, zobacz [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
