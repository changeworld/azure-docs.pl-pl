---
title: Samouczek — Tworzenie dzierżawy Azure Active Directory B2C
description: Dowiedz się, jak przygotować się do rejestracji aplikacji, tworząc dzierżawę Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345212"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: Tworzenie dzierżawy Azure Active Directory B2C

Aby aplikacje mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako *ulubiony* w Azure Portal

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera twoją subskrypcję.

    Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera subskrypcję. Ten katalog różni się od tego, który będzie zawierać dzierżawę Azure AD B2C.

    ![Wybrany filtr katalogów i subskrypcji z dzierżawcą subskrypcji](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
1. Wyszukaj i wybierz pozycję **Active Directory B2C**, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C**.

    ![Utwórz nową dzierżawę Azure AD B2C wybraną w Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Wprowadź **nazwę organizacji** i **początkową nazwę domeny**. Wybierz **kraj lub region** (nie można go zmienić później), a następnie wybierz pozycję **Utwórz**.

    Nazwa domeny jest używana jako część pełnej nazwy domeny dzierżawy. W tym przykładzie nazwa dzierżawy to *contosob2c.onmicrosoft.com*:

    ![Utwórz formularz dzierżawy przy użyciu przykładowych wartości w Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Po zakończeniu tworzenia dzierżawy wybierz link **Utwórz nową dzierżawę B2C lub link do istniejącej dzierżawy** w górnej części strony tworzenia dzierżawy.

    ![Łącze do łącza do stron nadrzędnych dzierżawy wyróżnione w Azure Portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Wybierz pozycję **Połącz istniejącą dzierżawę Azure AD B2C z moją subskrypcją platformy Azure**.

   ![Połącz istniejący wybór subskrypcji w Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Wybierz utworzoną **Azure AD B2C dzierżawcę** , a następnie wybierz swoją **subskrypcję**.

    W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź **nazwę** grupy zasobów, która będzie zawierać dzierżawę, wybierz **lokalizację grupy zasobów**, a następnie wybierz pozycję **Utwórz**.

    ![Formularz ustawień subskrypcji linku w Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>Wybierz katalog dzierżawy B2C

Aby rozpocząć korzystanie z nowej dzierżawy Azure AD B2C, musisz przełączyć się do katalogu, który zawiera dzierżawcę.

Wybierz filtr **katalogów i subskrypcji** w górnym menu Azure Portal, a następnie wybierz katalog, który zawiera dzierżawę Azure AD B2C.

Jeśli na liście nie zobaczysz nowej dzierżawy usługi Azure B2C, Odśwież okno przeglądarki, a następnie ponownie wybierz filtr **katalog + subskrypcja** w górnym menu.

![B2C dzierżawców — zawierający katalog wybrany w Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Dodaj Azure AD B2C jako ulubiony (opcjonalnie)

Ten opcjonalny krok ułatwia wybranie dzierżawy Azure AD B2C w następujących i kolejnych samouczkach.

Zamiast wyszukiwania "Azure AD B2C" we **wszystkich usługach** za każdym razem, gdy chcesz korzystać z dzierżawy, możesz zamiast tego przystąpić do ulubionego zasobu. Następnie możesz wybrać ją z menu **Ulubione** po lewej stronie, aby szybko przejść do dzierżawy Azure AD B2C.

Wystarczy wykonać tę operację tylko raz. Przed wykonaniem tych kroków upewnij się, że została przełączona do katalogu zawierającego dzierżawę Azure AD B2C, zgodnie z opisem w poprzedniej sekcji, [Wybierz katalog dzierżawy B2C](#select-your-b2c-tenant-directory).

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie [Azure Portal](https://portal.azure.com)
1. Wprowadź *Azure AD B2C* w polu tekstowym wyszukiwania
1. Wybierz **gwiazdkę** , aby dodać Azure AD B2C do ulubionych
1. *Azure AD B2C* teraz pojawia się w menu po lewej stronie **ulubionych** . Następnie możesz wybrać i przeciągnąć go wyżej na liście, jeśli chcesz, jak pokazano na poniższej ilustracji:

![Kroki umożliwiające dodanie Azure AD B2C jako ulubionego w Azure Portal](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako *ulubiony* w Azure Portal

Następnie Dowiedz się, jak zarejestrować aplikację sieci Web w nowej dzierżawie.

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji >](tutorial-register-applications.md)
