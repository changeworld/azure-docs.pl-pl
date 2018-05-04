---
title: Jak zarządzać użytkownika przypisane zarządzane usługi tożsamości (MSI) przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, listy i Usuń użytkownika przypisanego zarządzane usługi tożsamość przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: ebd250a7006c1235b30eb195f4bf7383b6293022
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Tworzenie listy lub usunąć użytkownika przypisanego tożsamość przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi Azure za pomocą tożsamości zarządzanych w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak utworzyć listę i usunąć tożsamości przypisane przez użytkownika, przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

    - Użyj [powłoki chmury Azure](../../cloud-shell/overview.md) z portalu Azure (zobacz następną sekcję).
    - Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. Zaloguj się do platformy Azure przy użyciu `az login`, przy użyciu konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć tożsamości przypisane przez użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Utwórz użytkownika przypisane tożsamości zarządzanych 

Do utworzenia tożsamości użytkownika przypisane, użyj [utworzenia tożsamości az](/cli/azure/identity#az-identity-create) polecenia. `-g` Parametr określa grupę zasobów, gdzie można utworzyć tożsamości użytkownika z przypisanym i `-n` parametr określa jego nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów z własne wartości:

> [!IMPORTANT]
> Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Przypisać tożsamości użytkownika z listy

Aby wyświetlić listę tożsamości przypisane przez użytkownika, użyj [listy tożsamości az](/cli/azure/identity#az-identity-list) polecenia.  `-g` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości.  Zastąp `<RESOURCE GROUP>` z własną wartość:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
W odpowiedzi json tożsamości użytkownika ma `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Usuń użytkownika przypisanego tożsamości

Aby usunąć tożsamości przypisane przez użytkownika, użyj [usunąć tożsamości az](/cli/azure/identity#az-identity-delete) polecenia.  Parametr - n określa jego nazwę, a parametr -g Określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości.  Zastąp `<USER ASSIGNED IDENTITY NAME>` i `<RESOURCE GROUP>` wartości parametrów z własne wartości:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usunięcie użytkownika przypisane tożsamości nie spowoduje usunięcia odwołanie, z dowolnego zasobu, który został przypisany do. Usuń z maszyny Wirtualnej/VMSS przy użyciu `az vm/vmss identity remove` polecenia

## <a name="related-content"></a>Zawartość pokrewna

Aby uzyskać pełną listę poleceń tożsamości wiersza polecenia platformy Azure, zobacz [tożsamości az](/cli/azure/identity).

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości użytkowników przypisanych do maszyny Wirtualnej platformy Azure, zobacz [skonfigurować zarządzane usługi tożsamości (MSI) przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
