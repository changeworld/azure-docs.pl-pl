---
title: Samouczek — tworzenie dzierżawy usługi Azure Active Directory B2C
description: Dowiedz się, jak przygotować się do rejestrowania aplikacji, tworząc dzierżawy usługi Azure Active Directory B2C w witrynie Azure portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056324"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C

Zanim Twoje aplikacje mogą wchodzić w interakcje z usługą Azure Active Directory (Azure AD) B2C, musi być zarejestrowany w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera Twoją subskrypcję. Kliknij przycisk **filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera Twoją subskrypcję. Ten katalog jest inny niż ten, który będzie zawierać dzierżawy usługi Azure AD B2C.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
4. Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
5. Wybierz **utworzyć nową dzierżawę usługi Azure AD B2C** i wprowadź nazwę organizacji i początkowa nazwa domeny. Wybierz kraj/region (nie można zmienić później), a następnie kliknij przycisk **Utwórz**.

    Początkowa nazwa domeny jest używany jako część nazwa dzierżawy. W tym przykładzie nazwa dzierżawy jest *contoso0926Tenant.onmicrosoft.com*:

    ![Tworzenie dzierżawy](./media/tutorial-create-tenant/create-tenant.PNG)

6. Na **Utwórz nową dzierżawę B2C lub linka do istniejącej dzierżawy** wybierz **dzierżawy moją subskrypcję systemu Azure łączy istniejące usługi Azure AD B2C**.

    Wybierz dzierżawcę, który został utworzony, a następnie wybierz swoją subskrypcję.

    Dla grupy zasobów wybierz **Utwórz nową**. Wprowadź nazwę grupy zasobów, która będzie zawierać dzierżawy, wybierz lokalizację, a następnie kliknij **Utwórz**.
1. Aby rozpocząć korzystanie z nowej dzierżawy, upewnij się, korzystania z katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który go zawiera.

    Jeśli nie widzisz wskazywał nową dzierżawę usługi Azure B2C, na liście, na początku, Odśwież okno przeglądarki, a następnie wybierz **filtr katalogów i subskrypcji** ponownie w górnym menu.

    ![Przełącz się do dzierżawy katalogu](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

Dowiedz się, jak zarejestrować aplikacji sieci web w nowej dzierżawy.

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji >](tutorial-register-applications.md)
