---
title: Samouczek — Tworzenie dzierżawy Azure Active Directory B2C
description: Dowiedz się, jak przygotować się do rejestracji aplikacji, tworząc dzierżawę Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063346"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy Azure Active Directory B2C

Aby aplikacje mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera twoją subskrypcję. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera twoją subskrypcję. Ten katalog różni się od tego, który będzie zawierać dzierżawę Azure AD B2C.

    ![Filtr katalogu i subskrypcji z wybraną dzierżawą subskrypcji](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
4. Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie kliknij pozycję **Utwórz**.
5. Wybierz pozycję **Utwórz nową dzierżawę Azure AD B2C** i wprowadź nazwę organizacji i początkową nazwę domeny. Wybierz kraj/region (nie można go zmienić później), a następnie kliknij przycisk **Utwórz**.

    Początkowa nazwa domeny jest używana jako część nazwy dzierżawy. W tym przykładzie nazwa dzierżawy to *contoso0926Tenant.onmicrosoft.com*:

    ![Strona tworzenia dzierżawy B2C w Azure Portal](./media/tutorial-create-tenant/create-tenant.PNG)

6. Na stronie **Tworzenie nowej dzierżawy B2C lub link do istniejącej dzierżawy** wybierz pozycję **Połącz istniejącą dzierżawę Azure AD B2C z moją subskrypcją platformy Azure**.

    Wybierz utworzoną dzierżawcę i wybierz swoją subskrypcję.

    W obszarze Grupa zasobów wybierz pozycję **Utwórz nową**. Wprowadź nazwę grupy zasobów, która będzie zawierać dzierżawę, wybierz lokalizację, a następnie kliknij przycisk **Utwórz**.
1. Aby rozpocząć korzystanie z nowej dzierżawy, upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog, który go zawiera.

    Jeśli na liście nie zobaczysz nowej dzierżawy usługi Azure B2C, Odśwież okno przeglądarki, a następnie ponownie wybierz filtr **katalog + subskrypcja** w górnym menu.

    ![Filtr katalogu i subskrypcji z wybraną dzierżawą B2C](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

Następnie Dowiedz się, jak zarejestrować aplikację sieci Web w nowej dzierżawie.

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji >](tutorial-register-applications.md)
