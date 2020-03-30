---
title: Zarządzanie tożsamością zarządzaną przypisaną przez użytkownika w witrynie Azure portal — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, listy, usuwania i przypisywania roli do tożsamości zarządzanej przypisanej przez użytkownika.
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
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244136"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Tworzenie, lista, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika za pomocą portalu Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure z tożsamością zarządzaną w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć, wyświetlić listę, usunąć lub przypisać rolę do tożsamości zarządzanej przypisanej przez użytkownika za pomocą witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika.
2. W polu wyszukiwania wpisz typ *Tożsamości zarządzane*, a następnie w obszarze **Usługi**kliknij pozycję **Tożsamości zarządzane**.
3. Kliknij **pozycję Dodaj** i wprowadź wartości w następujących polach w obszarze Tworzenie okienka tożsamości **zarządzanej przypisanego użytkownikowi:**
   - **Nazwa zasobu:** Jest to nazwa tożsamości zarządzanej przypisanej przez użytkownika, na przykład UAI1.
   - **Subskrypcja**: Wybierz subskrypcję, aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika w obszarze
   - **Grupa zasobów:** Utwórz nową grupę zasobów zawierającą tożsamość zarządzaną przypisaną przez użytkownika lub wybierz pozycję **Użyj istniejącej,** aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika w istniejącej grupie zasobów.
   - **Lokalizacja:** Wybierz lokalizację, aby wdrożyć tożsamość zarządzaną przypisaną przez użytkownika, na przykład **zachodnie stany USA**.
4. Kliknij przycisk **Utwórz**.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić listę/odczytywanie tożsamości zarządzanej przypisanej przez użytkownika, twoje konto wymaga przypisania roli [Operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz typ *Tożsamości zarządzane*, a następnie w obszarze Usługi kliknij pozycję **Tożsamości zarządzane**.
3. Zwracana jest lista tożsamości zarządzanych przypisanych przez użytkownika dla subskrypcji.  Aby wyświetlić szczegóły tożsamości zarządzanej przypisanej przez użytkownika, kliknij jej nazwę.

![Wyświetlanie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Usunięcie tożsamości przypisanej przez użytkownika nie powoduje usunięcia jej z maszyny Wirtualnej lub zasobu, do który został przypisany.  Aby usunąć przypisaną tożsamość użytkownika z maszyny Wirtualnej zobacz, [Usuń tożsamość zarządzaną przypisaną przez użytkownika z maszyny Wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby usunąć tożsamość zarządzaną przypisaną przez użytkownika.
2. Wybierz tożsamość zarządzaną przypisaną przez użytkownika i kliknij przycisk **Usuń**.
3. W polu potwierdzenia wybierz opcję **Tak**.

![Usuwanie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika 

Aby przypisać rolę do tożsamości zarządzanej przypisanej przez użytkownika, twoje konto wymaga przypisania roli [Administrator dostępu użytkownika.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika.
2. W polu wyszukiwania wpisz typ *Tożsamości zarządzane*, a następnie w obszarze Usługi kliknij pozycję **Tożsamości zarządzane**.
3. Zwracana jest lista tożsamości zarządzanych przypisanych przez użytkownika dla subskrypcji.  Wybierz przypisaną przez użytkownika tożsamość zarządzaną, którą chcesz przypisać rolę.
4. Wybierz **pozycję Kontrola dostępu (IAM),** a następnie wybierz pozycję Dodaj **przypisanie roli**.

   ![Rozpoczęcie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. W bloku Dodawanie przypisania roli skonfiguruj następujące wartości, a następnie kliknij przycisk **Zapisz:**
   - **Rola** - rola do przypisania
   - **Przypisywanie dostępu do** - zasobu w celu przypisania tożsamości zarządzanej przypisanej przez użytkownika
   - **Select** - element członkowski, aby przypisać dostęp
   
   ![Identyfikator tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
