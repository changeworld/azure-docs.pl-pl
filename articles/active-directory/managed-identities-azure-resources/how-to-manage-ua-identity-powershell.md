---
title: Sposób tworzenia, wyświetlania i usuwania przypisanych przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania, przypisanych do użytkowników zarządzanych tożsamości przy użyciu programu Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: c7191f60b8780e8ccee9b330aa21d8174f0f0148
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160714"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, listy i usuwać przypisane przez użytkownika tożsamości zarządzanej przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go.
- Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Jeśli używasz programu PowerShell lokalnie, wykonaj również te czynności: 
    - Uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.
    - Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Uruchom polecenie `Install-Module -Name PowerShellGet -AllowPrerelease`, aby pobrać wersję wstępną modułu `PowerShellGet` (po uruchomieniu tego polecenia może być konieczne uruchomienie polecenia `Exit` umożliwiającego zakończenie bieżącej sesji programu PowerShell w celu zainstalowania modułu `AzureRM.ManagedServiceIdentity`).
    - Uruchom `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` do zainstalowania wstępnej wersji `AzureRM.ManagedServiceIdentity` modułu, aby wykonać użytkownik przypisany zarządzanych tożsamości operacje, w tym artykule.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości zarządzanej przypisanych przez użytkownika (lista).

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, użyj [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia. `ResourceGroupName` Parametr określa grupę zasobów, gdzie trzeba utworzyć przypisanych przez użytkownika tożsamości zarządzanej, a `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

Aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika, użyj [Get AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono zarządzanych tożsamości przypisanych przez użytkownika. Zastąp `<RESOURCE GROUP>` swoją własną wartością:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi zarządzanych tożsamości przypisanych przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

Aby usunąć tożsamości zarządzanej przypisanych przez użytkownika, użyj [AzureRmUserAssignedIdentity Usuń](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono tożsamości przypisanych przez użytkownika i `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usuwanie tożsamości zarządzanej przypisanych przez użytkownika nie spowoduje usunięcia odwołania, z dowolnego zasobu, który został przypisany do. Przydziały tożsamości należy usunąć oddzielnie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną listę produktów oraz więcej szczegółów dotyczących programu Azure PowerShell zarządzanych tożsamości dla poleceń zasobów platformy Azure, zobacz [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
