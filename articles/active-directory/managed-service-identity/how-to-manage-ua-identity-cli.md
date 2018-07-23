---
title: Jak zarządzać użytkownik przypisany tożsamość usługi zarządzanej (MSI) przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania użytkownika przypisanego zarządzana usługa tożsamości przy użyciu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 5deaace49bfff994defc06a5f60597add6affc0b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188152"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Utwórz listę lub usunąć użytkownika z przypisaną tożsamości przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć listę i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości przypisanych przez użytkownika (lista).
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. Zaloguj się do platformy Azure za pomocą `az login`, przy użyciu konta, który jest skojarzony z subskrypcją platformy Azure w ramach której chcesz wdrożyć tożsamości przypisanych przez użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika 

Aby utworzyć tożsamości przypisanych przez użytkownika, należy użyć [Utwórz tożsamość az](/cli/azure/identity#az-identity-create) polecenia. `-g` Parametr określa grupę zasobów, gdzie można utworzyć tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista użytkownik tożsamości przypisanych przez

Aby wyświetlić listę tożsamości przypisanych przez użytkownika, użyj [listę tożsamości az](/cli/azure/identity#az-identity-list) polecenia. Zastąp `<RESOURCE GROUP>` swoją własną wartością:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
W odpowiedzi json ma tożsamości użytkowników `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika

Aby usunąć tożsamości przypisanych przez użytkownika, użyj [usuwanie tożsamości az](/cli/azure/identity#az-identity-delete) polecenia.  -N parametr określa jej nazwę, a parametr -g Określa grupę zasobów, w której utworzono tożsamości przypisanych przez użytkownika. Zastąp `<USER ASSIGNED IDENTITY NAME>` i `<RESOURCE GROUP>` wartości parametrów własnymi wartościami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usuwanie tożsamości przypisanych przez użytkownika nie spowoduje usunięcia odwołania, z dowolnego zasobu, który został przypisany do. Usuń te z VM/VMSS za pomocą `az vm/vmss identity remove` polecenia

## <a name="related-content"></a>Powiązana zawartość

Aby uzyskać pełną listę poleceń tożsamość wiersza polecenia platformy Azure, zobacz [tożsamości az](/cli/azure/identity).

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości przypisanych przez użytkownika do maszyny Wirtualnej platformy Azure, zobacz temat [skonfigurować tożsamość usługi zarządzanej (MSI) przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
