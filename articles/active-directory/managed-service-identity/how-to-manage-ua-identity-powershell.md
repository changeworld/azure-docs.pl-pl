---
title: Tworzenie, listy i Usuń użytkownika przypisanego (MSI) przy użyciu programu Azure PowerShell
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, listy i Usuń użytkownika przypisać tożsamość usługi zarządzane przy użyciu programu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: d413e5821aff8cf26dfc9ba03e6ec9d4134af76e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698948"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Tworzenie listy lub usunąć tożsamości przypisane przez użytkownika, przy użyciu programu Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi Azure za pomocą tożsamości zarządzanych w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak utworzyć listę i usunąć tożsamości przypisane przez użytkownika, przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Jeśli jeszcze.
- Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-user-assigned-identity"></a>Utwórz użytkownika przypisane tożsamości

Aby utworzyć tożsamość użytkownika z przypisanym, użyj [AzureRmUserAssignedIdentity nowy](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) polecenia. `ResourceGroupName` Parametr określa grupę zasobów, gdzie można utworzyć tożsamości użytkownika z przypisanym i `-Name` parametr określa jego nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów z własne wartości:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Przypisać tożsamości użytkownika z listy

Aby wyświetlić listę tożsamości przypisane przez użytkownika, użyj [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości.  Zastąp `<RESOURCE GROUP>` z własną wartość:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi tożsamości użytkownika ma `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Usuń użytkownika przypisanego tożsamości

Aby usunąć tożsamości użytkownika, użyj [AzureRmUserAssignedIdentity Usuń](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) polecenia.  `-ResourceGroupName` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości i `-Name` parametr określa jego nazwę.  Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów z własne wartości:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usunięcie użytkownika przypisane tożsamości nie spowoduje usunięcia odwołanie, z dowolnego zasobu, który został przypisany do. Przypisania tożsamości konieczne do usunięcia oddzielnie.

## <a name="related-content"></a>Zawartość pokrewna

Pełną listę i więcej szczegółów dotyczących poleceń Azure PowerShell MSI, zobacz [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
