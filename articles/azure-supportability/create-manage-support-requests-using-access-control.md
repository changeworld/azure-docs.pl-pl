---
title: Azure opartej na rolach kontroli dostępu (RBAC) na kontrolowanie uprawnień dostępu do tworzenia i obsługi żądań pomocy technicznej | Dokumentacja firmy Microsoft
description: Azure opartej na rolach kontroli dostępu (RBAC) na kontrolowanie uprawnień dostępu do tworzenia i obsługi żądań pomocy technicznej
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure opartej na rolach kontroli dostępu (RBAC) na kontrolowanie uprawnień dostępu do tworzenia i obsługi żądań pomocy technicznej

[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure.
Obsługuje tworzenia żądania w portalu Azure [portal.azure.com](https://portal.azure.com), używa modelu RBAC platformy Azure, aby zdefiniować, kto może tworzyć i zarządzanie żądaniami obsługi.
Dostęp przez przypisanie odpowiednie role RBAC dla użytkowników, grup i aplikacji na niektórych zakresu, który może być subskrypcji, grupy zasobów lub zasobu.

Spójrzmy na przykład: jako właściciel grupy zasobów z uprawnienia do odczytu w zakresie subskrypcji, możesz zarządzać wszystkie zasoby w grupie zasobów, takich jak witryny sieci Web, maszyn wirtualnych i podsieci.
Jednak podczas próby utworzenia żądania obsługi względem zasobu maszyny wirtualnej, wystąpi następujący błąd

![Błąd subskrypcji](./media/create-manage-support-requests-using-access-control/subscription-error.png)

W przystawce Zarządzanie żądania pomocy technicznej należy zapisać uprawnienia lub roli, która ma Microsoft.Support/* akcji pomocy technicznej w zakresie subskrypcji, aby można było utworzyć i zarządzanie żądaniami obsługi.

Artykule wyjaśniono, jak Azure niestandardowej opartej na rolach kontroli dostępu (RBAC) można użyć do tworzenia i obsługi żądań pomocy technicznej w portalu Azure.

## <a name="getting-started"></a>Wprowadzenie

W powyższym przykładzie będzie mieć możliwość utworzenia żądania obsługi dla zasobu, jeśli rola niestandardowa RBAC subskrypcji zostały przypisane przez właściciela subskrypcji.
[Niestandardowe role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) można tworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI) i interfejsu API REST.

Właściwość akcji niestandardowej roli zabezpieczeń określa operacje platformy Azure, do których rola przyznaje dostęp.
Aby utworzyć niestandardową rolę zarządzania żądania pomocy technicznej, rola musi mieć akcji Microsoft.Support/*

Oto przykład niestandardową rolę, który umożliwia tworzenie i zarządzanie żądaniami obsługi.
Firma Microsoft nazwanego tej roli "Współautor żądania pomocy technicznej" i to, jak firma Microsoft można znaleźć tworzona rola niestandardowa w tym artykule.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Wykonaj kroki opisane w temacie [ten film](https://www.youtube.com/watch?v=-PaBaDmfwKI) Aby dowiedzieć się, jak utworzyć niestandardową rolę dla Twojej subskrypcji.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Tworzenie i zarządzanie żądaniami obsługi w portalu Azure

Spójrzmy na przykład — jesteś właścicielem subskrypcji "Programu Visual Studio subskrypcji MSDN."
Jan jest sieci równorzędnej, kto jest właścicielem zasobu do niektórych grup zasobów w ramach tej subskrypcji oraz ma uprawnienia do subskrypcji do odczytu.
Chcesz udzielić dostępu do sieci równorzędnej, Jan, tworzenie i zarządzanie biletami pomocy technicznej dla zasobów w ramach tej subskrypcji.

1. Pierwszym krokiem jest, aby przejść do subskrypcji, a następnie w obszarze "Ustawienia" można wyświetlić listę użytkowników. Kliknij nazwę użytkownika Jan, kto ma dostęp do czytnika w subskrypcji i umożliwia przypisanie mu nowej niestandardowej roli zabezpieczeń.

    ![Dodaj rolę](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Kliknij przycisk "Dodaj" w bloku "Użytkownicy". Wybierz z listy ról niestandardowych roli "Współautor żądania pomocy technicznej"

    ![Dodaj rolę współautora pomocy technicznej](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po wybraniu nazwy roli, kliknij przycisk "Dodaj użytkowników", a następnie wprowadź poświadczenia e-mail Jan. Kliknij przycisk "Wybierz"

    ![Dodawanie użytkowników](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Kliknij przycisk "Ok", aby kontynuować

    ![Dodawanie uprawnień dostępu](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Teraz zostanie wyświetlony użytkownik z nowo dodanego rola niestandardowa "Obsługa współautora żądanie" w ramach subskrypcji, dla którego jesteś właścicielem

    ![Użytkownik dodany](./media/create-manage-support-requests-using-access-control/user-added.png)

    Jan rejestruje się w portalu, widzi on subskrypcji, do którego został dodany.

7. Jan klika pozycję "Nowe żądanie pomocy technicznej" w bloku "Pomoc i obsługa" i może utworzyć żądania pomocy technicznej dla "Programu Visual Studio Ultimate z subskrypcją MSDN"

    ![Nowe żądanie pomocy technicznej](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Klikając przycisk "Obsługuje wszystkie żądania" Jan wyświetlana lista żądań pomocy technicznej utworzonych dla tej subskrypcji ![przypadku widoku szczegółów](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Usuń obsługę żądania dostępu w portalu Azure

Tak samo, jak jest możliwość przyznania dostępu użytkownika do tworzenia i obsługi żądań pomocy technicznej, istnieje możliwość dostępu dla użytkownika, jak również usunąć.
Aby usunąć możliwość tworzenia i zarządzania żądania pomocy technicznej, przejdź do subskrypcji, kliknij pozycję "Ustawienia", a następnie kliknij przycisk użytkownika (w tym przypadku Jan).
Kliknij prawym przyciskiem myszy nazwę roli "Współautor żądania pomocy technicznej" i kliknij przycisk Usuń,

![Usuń obsługę żądania dostępu](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Gdy Jan loguje się do portalu i podejmie próbę utworzenia żądania obsługi, wykryje on następujący błąd

![Subskrypcja błąd-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jan nie widzą żadnych obsługuje żądania, po kliknięciu przycisku "Obsługuje wszystkie żądania"

![Wyświetl szczegóły sprawy-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
