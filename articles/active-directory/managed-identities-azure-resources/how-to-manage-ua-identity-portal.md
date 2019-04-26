---
title: Jak zarządzać przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania list, Usuń i przypisać rolę do zarządzanych tożsamości przypisanych przez użytkownika.
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
ms.openlocfilehash: 18a15b8039322fc5e43a2b9dfed8a9bd3fc8b5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441648"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Utwórz listę, usuń lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, listy, usuń lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure można utworzyć zarządzanej tożsamości przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a następnie w obszarze **usług**, kliknij przycisk **tożsamości zarządzanych**.
3. Kliknij przycisk **Dodaj** i wprowadź wartości w następujących polach, w obszarze **Utwórz przypisane do użytkowników zarządzanych** okienko tożsamości:
   - **Nazwa zasobu**: Jest to nazwa dla przypisanych do użytkowników zarządzanych tożsamości użytkownika, na przykład UAI1.
   - **Subskrypcja**: Wybierz subskrypcję, można utworzyć przypisanych przez użytkownika tożsamości zarządzanych w ramach
   - **Grupa zasobów**: Utwórz nową grupę zasobów, aby zawierał zarządzanych tożsamości przypisanych przez użytkownika, lub wybierz **Użyj istniejącej** do tworzenia zarządzanych tożsamości przypisanych przez użytkownika w istniejącej grupie zasobów.
   - **Lokalizacja**: Wybierz lokalizację, aby wdrożyć przypisanych przez użytkownika tożsamości zarządzanej, na przykład **zachodnie stany USA**.
4. Kliknij pozycję **Utwórz**.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

Na liście odczytu przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a w obszarze usługi, kliknij przycisk **tożsamości zarządzanych**.
3. Zwracana jest lista tożsamości przypisanych przez użytkownika zarządzanego dla Twojej subskrypcji.  Aby wyświetlić szczegóły tożsamości zarządzanej przypisanych przez użytkownika, kliknij jego nazwę.

![Lista przypisanych przez użytkownika tożsamości zarządzanej](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

Aby usunąć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Usuwanie tożsamości przypisanych przez użytkownika nie powoduje usunięcia go z maszyny Wirtualnej lub zasób, który został przypisany do.  Aby usunąć tożsamości przypisanych przez użytkownika z maszyny Wirtualnej, zobacz [usuwania przypisanych przez użytkownika tożsamości zarządzanej maszyny Wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) użycia konta skojarzonego z subskrypcją platformy Azure, aby usunąć tożsamości zarządzanej przypisanych przez użytkownika.
2. Wybierz tożsamość zarządzaną przypisanych przez użytkownika, a następnie kliknij przycisk **Usuń**.
3. W obszarze okno dialogowe z potwierdzeniem wybierz **tak**.

![Usuń przypisanych przez użytkownika z tożsamości zarządzanej](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Przypisywanie roli do zarządzanych tożsamości przypisanych przez użytkownika 

Aby przypisać rolę do zarządzanych tożsamości przypisanych przez użytkownika, Twoje konto musi [Administrator dostępu użytkowników](/azure/role-based-access-control/built-in-roles#user-access-administrator) przypisania roli.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzanych*, a w obszarze usługi, kliknij przycisk **tożsamości zarządzanych**.
3. Zwracana jest lista tożsamości przypisanych przez użytkownika zarządzanego dla Twojej subskrypcji.  Wybierz tożsamość zarządzanego przypisanego przez użytkownika, którą chcesz przypisać rolę.
4. Wybierz **kontrola dostępu (IAM)** , a następnie wybierz **Dodaj przypisanie roli**.

   ![Start przypisanych przez użytkownika z tożsamości zarządzanej](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. W bloku Dodawanie przypisania roli skonfiguruj następujące wartości, a następnie kliknij przycisk **Zapisz**:
   - **Rola** -rolę do przypisania
   - **Przypisz dostęp do** — zasobów, aby przypisać użytkownik przypisany tożsamości zarządzanej
   - **Wybierz** — element członkowski, aby przypisać dostęp
   
   ![Użytkownik przypisany tożsamość zarządzaną zarządzania tożsamościami i Dostępem](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
