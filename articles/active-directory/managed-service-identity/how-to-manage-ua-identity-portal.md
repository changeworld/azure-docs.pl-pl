---
title: Jak zarządzać użytkownik przypisany tożsamość zarządzaną w witrynie Azure portal
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania użytkownika przypisać tożsamość zarządzaną.
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
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42061517"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Utwórz listę lub usunąć użytkownika z przypisaną tożsamości przy użyciu witryny Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć listę i usunąć użytkownik, któremu przypisano tożsamość przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości przypisanych przez użytkownika (lista).

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby utworzyć użytkownika przypisane tożsamość zarządzaną.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a następnie w obszarze **usług**, kliknij przycisk **tożsamości zarządzanych**.
3. Kliknij przycisk **Dodaj** i wprowadź wartości w następujących polach, w obszarze **Utwórz przypisane do użytkowników zarządzanych** okienko tożsamości:
   - **Nazwa zasobu**: jest to nazwa dla przypisanych użytkowników zarządzanych przy użyciu tożsamości, na przykład UAI1.
   - **Subskrypcja**: Wybierz subskrypcję, aby utworzyć użytkownik, któremu przypisano tożsamość zarządzaną w obszarze
   - **Grupa zasobów**: Utwórz nową grupę zasobów, aby zawierał tożsamości przypisanych przez użytkownika, lub wybierz **Użyj istniejącej** do utworzenia użytkownika należy przypisać tożsamość zarządzaną w istniejącej grupie zasobów.
   - **Lokalizacja**: Wybierz lokalizację w której użytkownik, któremu przypisano tożsamość zarządzaną, na przykład wdrożyć **zachodnie stany USA**.
4. Kliknij pozycję **Utwórz**.

![Tworzenie zarządzanych tożsamości przypisanych przez użytkownika](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Lista użytkownik tożsamości przypisanych przez

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę użytkownika przypisane zarządzanych tożsamości.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a w obszarze usługi, kliknij przycisk **tożsamości zarządzanych**.
3. Zwracana jest lista użytkownik, któremu przypisano zarządzanych tożsamości dla Twojej subskrypcji.  Aby wyświetlić szczegóły użytkownika przypisanego możesz kliknąć jego nazwę.

![Zarządzanych tożsamości przypisanych przez użytkownika listy](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby usunąć użytkownika przypisane tożsamość zarządzaną.
2. Wybierz opcję tożsamości przypisanych przez użytkownika, a następnie kliknij przycisk **Usuń**.
3. W obszarze okno dialogowe z potwierdzeniem wybierz **tak**.

![Usuwanie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)