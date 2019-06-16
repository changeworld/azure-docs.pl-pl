---
title: Azure opartej na rolach kontrola dostępu (RBAC) na kontrolowanie praw dostępu do tworzenia i zarządzają żądaniami pomocy technicznej | Dokumentacja firmy Microsoft
description: Azure opartej na rolach kontrola dostępu (RBAC) na kontrolowanie praw dostępu do tworzenia i zarządzają żądaniami pomocy technicznej
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809752"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure opartej na rolach kontrola dostępu (RBAC) na kontrolowanie praw dostępu do tworzenia i zarządzają żądaniami pomocy technicznej

[Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure.
Obsługuje żądania tworzenia w witrynie Azure portal [portal.azure.com](https://portal.azure.com), używa modelu RBAC platformy Azure w celu zdefiniowania, kto może tworzyć i zarządzać żądaniami pomocy technicznej.
Dostęp jest udzielany przez przypisywanie odpowiednią rolę RBAC użytkownikom, grupom i aplikacjom, które w określonym zakresie, który może być subskrypcji, grupy zasobów lub zasobu.

Spójrzmy na przykład: Jako właściciel grupy zasobów z uprawnieniami do odczytu w zakresie subskrypcji możesz zarządzać wszystkie zasoby w grupie zasobów, takich jak witryny sieci Web, maszyn wirtualnych i podsieci.
Jednak podczas próby utworzenia żądania obsługi względem zasobu maszyny wirtualnej, wystąpi następujący błąd

![Błąd subskrypcji](./media/create-manage-support-requests-using-access-control/subscription-error.png)

W przystawce Zarządzanie żądania pomocy technicznej należy napisać uprawnienia lub rola przypisana Microsoft.Support/* akcji pomocy technicznej w zakresie subskrypcji, aby móc tworzyć i zarządzać nimi żądania pomocy technicznej.

Następujący artykuł wyjaśnia, jak można użyć platformy Azure niestandardowej opartej na rolach kontrola dostępu (RBAC) do tworzenia i Zarządzaj żądaniami obsługi w witrynie Azure portal.

## <a name="getting-started"></a>Wprowadzenie

Korzystając z powyższego przykładu, będzie można utworzyć żądanie pomocy technicznej dla zasobu, jeśli niestandardową rolę RBAC w ramach subskrypcji zostały przypisane przez właściciela subskrypcji.
[Niestandardowe role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) można utworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI) i interfejsu API REST.

Właściwość akcji niestandardowej roli określa operacji platformy Azure, do których rola przyznaje dostęp.
Aby utworzyć niestandardową rolę zarządzania żądanie pomocy technicznej, do roli musi być akcji Microsoft.Support/*

Oto przykład rolę niestandardową, używanej do tworzenia i zarządzania nimi żądania pomocy technicznej.
Firma Microsoft nazwanego tę rolę "Współautor żądania pomocy technicznej" i dlatego sposób nazywamy rolę niestandardową, w tym artykule.

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

Wykonaj kroki opisane w temacie [ten film wideo](https://www.youtube.com/watch?v=-PaBaDmfwKI) dowiesz się, jak utworzyć rolę niestandardową dla Twojej subskrypcji.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Utwórz i Zarządzaj żądaniami obsługi w witrynie Azure portal

Spójrzmy na przykład — jesteś właścicielem subskrypcji "MSDN subskrypcji programu Visual Studio."
Jan podlega sieci równorzędnej, który jest właścicielem zasobu, do niektórych grup zasobów w ramach tej subskrypcji i mają uprawnienia do subskrypcji do odczytu.
Chcesz udzielić dostępu do sieci równorzędnej, Jan, możliwość tworzenia i zarządzania biletami pomocy technicznej dla zasobów w ramach tej subskrypcji.

1. Pierwszym krokiem jest, aby przejść do subskrypcji, a następnie w obszarze "Settings" zobaczysz listę użytkowników. Kliknij użytkownika, Jan, kto ma dostęp czytnika w ramach subskrypcji i umożliwia przypisanie nowej roli niestandardowej mu.

    ![Dodaj rolę](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Kliknij przycisk "Dodaj", w ramach bloku "Użytkownicy". Wybierz rolę niestandardową "Współautor żądania pomocy technicznej" z listy ról

    ![Dodawanie roli "Współautor" pomocy technicznej](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po wybraniu nazwy roli, kliknij pozycję "Dodaj użytkowników" i wprowadź poświadczenia poczty e-mail przez Jana. Kliknij pozycję „Wybierz”

    ![Dodawanie użytkowników](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Kliknij przycisk "Ok", aby kontynuować

    ![Dodawanie dostępu](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Spowoduje to wyświetlenie użytkownikowi nowo dodanych rola niestandardowa "Obsługa Współautor żądania" w ramach subskrypcji, dla którego jesteś właścicielem

    ![Dodano użytkownika](./media/create-manage-support-requests-using-access-control/user-added.png)

    Po zalogowaniu Jan w portalu, widzi subskrypcji, do którego został dodany.

7. Jan klika pozycję "Nowe żądanie pomocy technicznej" w bloku "Pomocy i pomocy technicznej" i można tworzyć żądania pomocy technicznej dla "Visual Studio Ultimate z subskrypcją MSDN"

    ![Nowe żądanie pomocy technicznej](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Klikając pozycję "Wszystkie żądania obsługi" Jan wyświetlana lista żądań pomocy technicznej utworzonych dla tej subskrypcji ![przypadek widoku szczegółów](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Usuń dostęp do żądania pomocy technicznej w witrynie Azure portal

Tak, jak jest możliwe udzielić dostępu do użytkownika, aby tworzyć i zarządzać nimi żądania pomocy technicznej, jest możliwe usunięcie dostępu dla użytkownika, jak również.
Aby usunąć możliwość tworzenia i zarządzają żądaniami pomocy technicznej, przejdź do subskrypcji, kliknij przycisk "Ustawienia", a następnie kliknij przycisk użytkownika (w tym przypadku Jan).
Kliknij prawym przyciskiem myszy nazwę roli "Współautor żądania pomocy technicznej" i kliknij przycisk "Usuń"

![Usuń dostęp do żądania pomocy technicznej](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Jeśli Jan loguje się do portalu i próbuje utworzyć żądanie pomocy technicznej, wykryje on następujący błąd

![Subskrypcja błąd-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jan nie widać żadnych żądania obsługi, po kliknięciu przycisku "Wszystkie żądania obsługi"

![Wyświetl szczegóły przypadku-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
