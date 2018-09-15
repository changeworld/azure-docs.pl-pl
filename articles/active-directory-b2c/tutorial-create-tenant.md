---
title: Samouczek — tworzenie dzierżawy usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować się do rejestrowania aplikacji, tworząc dzierżawy usługi Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5831d868bec940c4e38f62e70e456ae84903ada
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604914"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C

Zanim Twoje aplikacje mogą wchodzić w interakcje z usługą Azure Active Directory (Azure AD) B2C, musi być zarejestrowany w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. W polu wyszukiwania nad listą zasobów w portalu Azure Marketplace Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
3. Wybierz **utworzyć nową dzierżawę usługi Azure AD B2C**, wprowadź nazwę organizacji i początkowa nazwa domeny, który jest używany w nazwie dzierżawy, wybierz kraj, a następnie kliknij przycisk **Utwórz**. Upewnij się, w kraju dzierżawy, ponieważ nie można zmienić później.

    ![Tworzenie dzierżawy](./media/tutorial-create-tenant/create-tenant.png)

    W tym przykładzie nazwa dzierżawy jest contoso0522Tenant.onmicrosoft.com

Aby rozpocząć zarządzanie wskazywał nową dzierżawę, kliknij słowo **tutaj** gdzie mówi **kliknij tutaj, aby zarządzać nowym katalogiem**. Zostanie wyświetlony **rozwiązywanie** komunikat informujący o tym, musisz połączyć swoją subskrypcję do nowej dzierżawy. 

## <a name="link-your-tenant-to-your-subscription"></a>Łączenie dzierżawy z subskrypcją

W celu uzyskania pełnej funkcjonalności i dokonania opłat za zużycie, należy połączyć dzierżawę usługi Azure AD B2C z subskrypcją platformy Azure. W przypadku dzierżawy nie połączenia ze swoją subskrypcją w aplikacjach nie będzie działać poprawnie.

Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy. 

![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/tutorial-create-tenant/switch-directories.png)

1. Wybierz **Utwórz zasób** w górnym lewym górnym rogu witryny Azure Portal.
2. W polu wyszukiwania nad listą zasobów w portalu Azure Marketplace Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie kliknij przycisk **Utwórz**.
3. Wybierz **dzierżawy moją subskrypcję systemu Azure łączy istniejące usługi Azure AD B2C**, wybierz dzierżawcę, który został utworzony, wybierz swoją subskrypcję, wprowadź *myContosoTenantRG* jako nazwę grupy zasobów, Zaakceptuj Lokalizacja, a następnie kliknij **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją

> [!div class="nextstepaction"]
> [Włącz uwierzytelnianie za pomocą konta w aplikacji sieci web](active-directory-b2c-tutorials-web-app.md)