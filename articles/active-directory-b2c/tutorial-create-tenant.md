---
title: Samouczek — tworzenie dzierżawy usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować się do rejestrowania aplikacji, tworząc dzierżawy usługi Azure Active Directory B2C w witrynie Azure portal.
services: B2C
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8878eccb079bf78c45ff9c1e4040659d109b1ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360199"
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
2. Upewnij się, używają katalogu, który zawiera Twoją subskrypcję, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który go zawiera. Ten katalog jest inny niż ten, który będzie zawierać dzierżawy usługi Azure AD B2C.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
4. Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
5. Wybierz **utworzyć nową dzierżawę usługi Azure AD B2C**, wprowadź nazwę organizacji i początkowa nazwa domeny, który jest używany w nazwie dzierżawy, wybierz kraj (nie można zmienić później), a następnie kliknij **Utwórz**.

    ![Tworzenie dzierżawy](./media/tutorial-create-tenant/create-tenant.png)

    W tym przykładzie nazwa dzierżawy jest contoso0926Tenant.onmicrosoft.com

6. Na **Utwórz nową dzierżawę B2C lub łącze do istniejącej dzierżawy** wybierz **dzierżawy moją subskrypcję systemu Azure łączy istniejące usługi Azure AD B2C**, wybierz dzierżawcę, który został utworzony, wybierz subskrypcję, a następnie Kliknij przycisk **Utwórz nową**.
7. Wprowadź nazwę grupy zasobów, która będzie zawierać dzierżawy, wybierz lokalizację, a następnie kliknij **Utwórz**.
8. Aby rozpocząć korzystanie z nowej dzierżawy, upewnij się, korzystania z katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który go zawiera.

    ![Przełącz się do dzierżawy katalogu](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](tutorial-register-applications.md)
