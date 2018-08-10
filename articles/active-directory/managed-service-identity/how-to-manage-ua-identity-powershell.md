---
title: Sposób tworzenia, wyświetlania i usuwania użytkownika przypisanego (MSI) przy użyciu programu Azure PowerShell
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania użytkowników przypisane tożsamości usługi zarządzanej przy użyciu programu Azure PowerShell.
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
ms.openlocfilehash: 31a632138a4946accfcab858b7b61782fb4e7d72
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005375"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Tworzenie listy i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu programu Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć listę i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze go.
- Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości przypisanych przez użytkownika (lista).

> [!NOTE]
> Gdy użytkownik przypisany tożsamości są nadal w wersji zapoznawczej, musisz najpierw ręcznie zainstalować moduł AzureRM.ManagedServiceIdentity, używając następującego polecenia. 
```azurepowershell-interactive
Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease
```

## <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

Aby utworzyć tożsamości przypisanych przez użytkownika, należy użyć [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia. `ResourceGroupName` Parametr określa grupę zasobów, gdzie można utworzyć tożsamości przypisanych przez użytkownika, a `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista użytkownik tożsamości przypisanych przez

Aby wyświetlić listę tożsamości przypisanych przez użytkownika, użyj [Get AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono tożsamości przypisanych przez użytkownika. Zastąp `<RESOURCE GROUP>` swoją własną wartością:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi tożsamości użytkownika ma `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika

Aby usunąć tożsamości użytkownika, użyj [AzureRmUserAssignedIdentity Usuń](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono tożsamości przypisanych przez użytkownika i `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usuwanie tożsamości przypisanych przez użytkownika nie spowoduje usunięcia odwołania, z dowolnego zasobu, który został przypisany do. Przydziały tożsamości należy usunąć oddzielnie.

## <a name="related-content"></a>Powiązana zawartość

Aby uzyskać pełną listę produktów oraz więcej szczegółów dotyczących poleceń programu Azure PowerShell w plik MSI, zobacz [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
