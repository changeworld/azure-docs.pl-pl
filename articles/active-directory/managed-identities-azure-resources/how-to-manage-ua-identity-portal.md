---
title: Jak zarządzać przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania, użytkownik przypisany zarządzanych tożsamości.
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
ms.openlocfilehash: 6729bee9bfebd8e80ae3b791dc2a8a480ac8b525
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158726"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-portal"></a>Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, listy i usuwać przypisane przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli można odczytać właściwości tożsamości zarządzanej przypisanych przez użytkownika (lista).

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure można utworzyć zarządzanej tożsamości przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a następnie w obszarze **usług**, kliknij przycisk **tożsamości zarządzanych**.
3. Kliknij przycisk **Dodaj** i wprowadź wartości w następujących polach, w obszarze **Utwórz przypisane do użytkowników zarządzanych** okienko tożsamości:
   - **Nazwa zasobu**: jest to nazwa dla przypisanych do użytkowników zarządzanych tożsamości użytkownika, na przykład UAI1.
   - **Subskrypcja**: Wybierz subskrypcję, można utworzyć przypisanych przez użytkownika tożsamości zarządzanych w ramach
   - **Grupa zasobów**: Utwórz nową grupę zasobów, aby zawierał zarządzanych tożsamości przypisanych przez użytkownika, lub wybierz **Użyj istniejącej** do tworzenia zarządzanych tożsamości przypisanych przez użytkownika w istniejącej grupie zasobów.
   - **Lokalizacja**: Wybierz lokalizację, aby wdrożyć przypisanych przez użytkownika tożsamości zarządzanej, na przykład **zachodnie stany USA**.
4. Kliknij pozycję **Utwórz**.

![Tworzenie zarządzanych tożsamości przypisanych przez użytkownika](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a w obszarze usługi, kliknij przycisk **tożsamości zarządzanych**.
3. Zwracana jest lista tożsamości przypisanych przez użytkownika zarządzanego dla Twojej subskrypcji.  Aby wyświetlić szczegóły tożsamości zarządzanej przypisanych przez użytkownika, kliknij jego nazwę.

![Lista przypisanych przez użytkownika tożsamości zarządzanej](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) użycia konta skojarzonego z subskrypcją platformy Azure, aby usunąć tożsamości zarządzanej przypisanych przez użytkownika.
2. Wybierz tożsamość zarządzaną przypisanych przez użytkownika, a następnie kliknij przycisk **Usuń**.
3. W obszarze okno dialogowe z potwierdzeniem wybierz **tak**.

![Usuń przypisanych przez użytkownika z tożsamości zarządzanej](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)