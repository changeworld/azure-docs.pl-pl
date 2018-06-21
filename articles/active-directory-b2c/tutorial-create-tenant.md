---
title: Samouczek — tworzenie dzierżawy usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować do rejestrowania aplikacji przez tworzenie dzierżawy usługi Azure Active Directory B2C przy użyciu portalu Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 04f3dbbe461bfe0f07b6930a92bdd8a721e55098
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296099"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C

Przed aplikacje mogą współdziałać z usługi Azure Active Directory (Azure AD) B2C, musi być zarejestrowana w dzierżawy, którym zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Link do subskrypcji dzierżawy

Jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Wybierz **Utwórz zasób** w lewym górnym rogu portalu Azure.
2. W polu wyszukiwania powyżej listy zasobów w portalu Azure Marketplace, wyszukaj i wybierz **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
3. Wybierz **utworzyć nową dzierżawę usługi Azure AD B2C**, wprowadź nazwę organizacji i początkową nazwę domeny, który jest używany w nazwie dzierżawy, wybierz kraj, a następnie kliknij **Utwórz**. Upewnij się, kraju dzierżawy, ponieważ nie można zmienić później.

    ![Tworzenie dzierżawy](./media/tutorial-create-tenant/create-tenant.png)

    W tym przykładzie nazwa dzierżawy to contoso0522Tenant.onmicrosoft.com

Aby rozpocząć zarządzanie nowej dzierżawy, kliknij słowo **tutaj** gdzie mówi **kliknij tutaj, aby zarządzać nowego katalogu**. Zobaczysz **rozwiązywanie** komunikat z informacją, należy połączyć subskrypcji z nowym dzierżawcą. 

## <a name="link-your-tenant-to-your-subscription"></a>Link do subskrypcji dzierżawy

Musisz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, aby włączyć wszystkie funkcje i opłacać opłaty za użycie. Jeśli nie możesz połączyć dzierżawy do subskrypcji, aplikacje będą działać poprawnie.

1. Upewnij się, że używasz katalog, który zawiera subskrypcję, którą chcesz kojarzyć z nowym dzierżawcą przełączając katalogu w prawym górnym rogu portalu Azure.

    ![Przełącz katalogi](./media/tutorial-create-tenant/switch-directories.png)

    A następnie wybierając katalog, który zawiera subskrypcję.

    ![Wybieranie katalogu](./media/tutorial-create-tenant/select-directory.png)

2. Wybierz **Utwórz zasób** w lewym górnym górnym rogu portalu Azure.
3. W polu wyszukiwania powyżej listy zasobów w portalu Azure Marketplace, wyszukaj i wybierz **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
4. Wybierz **dzierżawy łącze istniejącej usługi Azure AD B2C do subskrypcji platformy Azure**wybierz dzierżawy, który został utworzony, wybierz subskrypcję, wprowadź *myContosoTenantRG* zaakceptować nazwę grupy zasobów lokalizacji, a następnie kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Link do subskrypcji dzierżawy

> [!div class="nextstepaction"]
> [Włączanie aplikacji sieci web do uwierzytelniania kont](active-directory-b2c-tutorials-web-app.md)