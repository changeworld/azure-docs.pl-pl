---
title: Samouczek — tworzenie dzierżawy usługi Azure Active Directory B2C
description: Dowiedz się, jak przygotować się do rejestrowania aplikacji, tworząc dzierżawę B2C usługi Azure Active Directory przy użyciu witryny Azure portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186407"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C

Aby aplikacje mogły wchodzić w interakcje z usługą Azure Active Directory B2C (Azure AD B2C), muszą być zarejestrowane w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Połącz dzierżawę z subskrypcją
> * Przełączanie do katalogu zawierającego dzierżawę usługi Azure AD B2C
> * Dodawanie zasobu usługi Azure AD B2C jako **ulubionego** w witrynie Azure portal

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/). Zaloguj się przy za pomocą konta platformy Azure, które zostało przypisane co najmniej rolę [współautora](../role-based-access-control/built-in-roles.md) w ramach subskrypcji lub grupy zasobów w ramach subskrypcji.

1. Wybierz katalog zawierający subskrypcję.

    Na pasku narzędzi portalu Azure wybierz ikonę **Katalog + Subskrypcja,** a następnie wybierz katalog zawierający subskrypcję. Ten katalog różni się od tego, który będzie zawierał dzierżawę usługi Azure AD B2C.

    ![Dzierżawa subskrypcji, filtr Katalog + Subskrypcja z wybraną dzierżawą subskrypcji](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wyszukaj **usługę Azure Active Directory B2C**, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C**.

    ![Tworzenie nowej dzierżawy usługi Azure AD B2C wybranej w witrynie Azure portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Wprowadź **nazwę organizacji** i **początkową nazwę domeny**. Wybierz **kraj lub region** (nie można go później zmienić), a następnie wybierz pozycję **Utwórz**.

    Nazwa domeny jest używana jako część pełnej nazwy domeny dzierżawy. W tym przykładzie nazwa dzierżawy jest *contosob2c.onmicrosoft.com:*

    ![Tworzenie formularza dzierżawy przy przykładowych wartościach w witrynie Azure portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Po zakończeniu tworzenia dzierżawy wybierz **łącze Utwórz nową dzierżawę B2C lub Łącze do istniejącej dzierżawy** u góry strony tworzenia dzierżawy.

    ![Łącze do strony do strony serwera do pobrania w witrynie Azure portal wyróżnione](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Wybierz **pozycję Połącz istniejącą dzierżawę usługi Azure AD B2C z moją subskrypcją platformy Azure**.

   ![Łączenie istniejącego wyboru subskrypcji w witrynie Azure portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Wybierz utworzoną **dzierżawę usługi Azure AD B2C,** a następnie wybierz **subskrypcję**.

    W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź **nazwę** grupy zasobów, która będzie zawierać dzierżawcę, wybierz **lokalizację grupy zasobów,** a następnie wybierz pozycję **Utwórz**.

    ![Formularz ustawień subskrypcji łączy w witrynie Azure portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Można połączyć wielu dzierżaw usługi Azure AD B2C z jedną subskrypcją platformy Azure na potrzeby rozliczeń.

## <a name="select-your-b2c-tenant-directory"></a>Wybieranie katalogu dzierżawy B2C

Aby rozpocząć korzystanie z nowej dzierżawy usługi Azure AD B2C, należy przełączyć się do katalogu, który zawiera dzierżawy.

Wybierz filtr **subskrypcja Katalog +** w górnym menu witryny Azure Portal, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.

Jeśli na początku nie widzisz nowej dzierżawy usługi Azure B2C na liście, odśwież okno przeglądarki, a następnie ponownie wybierz filtr **subskrypcja Katalog +** w górnym menu.

![Katalog zawierający dzierżawę B2C wybrany w witrynie Azure portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Dodawanie usługi Azure AD B2C jako ulubionej (opcjonalnie)

Ten opcjonalny krok ułatwia wybranie dzierżawy usługi Azure AD B2C w następujących i wszystkich kolejnych samouczkach.

Zamiast wyszukiwania *usługi Azure AD B2C* w wszystkich **usług** za każdym razem, gdy chcesz pracować z dzierżawy, zamiast ulubionych zasobów. Następnie można wybrać go z menu portalu **ulubionych** sekcji, aby szybko przejść do dzierżawy usługi Azure AD B2C.

Tę operację należy wykonać tylko raz. Przed wykonaniem tych kroków upewnij się, że przełączony do katalogu zawierającego dzierżawę usługi Azure AD B2C, zgodnie z opisem w poprzedniej [sekcji, Wybierz katalog dzierżawy B2C.](#select-your-b2c-tenant-directory)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu portalu platformy Azure wybierz polecenie **Wszystkie usługi**.
1. W polu wyszukiwania **Wszystkie usługi** wyszukaj pozycję Usługi Azure **AD B2C**, umieść wskaźnik myszy na wyniku wyszukiwania, a następnie wybierz ikonę gwiazdki w etykietce narzędzia. **Usługa Azure AD B2C** jest teraz wyświetlana w witrynie Azure portal w obszarze **Ulubione**.
1. Jeśli chcesz zmienić położenie nowego ulubionego, przejdź do menu portalu Azure, wybierz pozycję **Azure AD B2C**, a następnie przeciągnij ją w górę lub w dół do żądanej pozycji.

    ![Usługa Azure AD B2C, menu Ulubione, witryna Microsoft Azure portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Połącz dzierżawę z subskrypcją
> * Przełączanie do katalogu zawierającego dzierżawę usługi Azure AD B2C
> * Dodawanie zasobu usługi Azure AD B2C jako **ulubionego** w witrynie Azure portal

Następnie dowiedz się, jak zarejestrować aplikację sieci web w nowej dzierżawie.

> [!div class="nextstepaction"]
> [Zarejestruj swoje aplikacje >](tutorial-register-applications.md)
