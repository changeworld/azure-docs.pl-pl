---
title: Access Control oparte na rolach (RBAC) na platformie Azure w celu kontrolowania praw dostępu do tworzenia i zarządzania żądaniami obsługi | Microsoft Docs
description: Access Control oparte na rolach (RBAC) na platformie Azure w celu kontrolowania praw dostępu do tworzenia i zarządzania żądaniami obsługi
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 3cf17f6e391608af9d17591a81c579a1db779a6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967810"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Access Control oparte na rolach (RBAC) na platformie Azure w celu kontrolowania praw dostępu do tworzenia i zarządzania żądaniami obsługi

[Access Control oparte na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umożliwia precyzyjne zarządzanie dostępem na platformie Azure.
Support request tworzenia w Azure Portal, [Portal.Azure.com](https://portal.azure.com)korzysta z modelu RBAC platformy Azure, aby zdefiniować, kto może tworzyć żądania pomocy technicznej i zarządzać nimi.
Dostęp jest udzielany przez przypisanie odpowiedniej roli RBAC użytkownikom, grupom i aplikacjom w określonym zakresie, co może być subskrypcją, grupą zasobów lub zasobem.

Oto przykład: Jako właściciel grupy zasobów z uprawnieniami do odczytu w zakresie subskrypcji można zarządzać wszystkimi zasobami w ramach grupy zasobów, takich jak witryny sieci Web, maszyny wirtualne i podsieci.
Jednak podczas próby utworzenia żądania obsługi dla zasobu maszyny wirtualnej wystąpi następujący błąd

![Błąd subskrypcji](./media/create-manage-support-requests-using-access-control/subscription-error.png)

W programie Zarządzanie żądaniami wymaga uprawnień do zapisu lub roli z akcją pomocy technicznej Microsoft. Support/* w zakresie subskrypcji, aby można było tworzyć żądania pomocy technicznej i zarządzać nimi.

W poniższym artykule wyjaśniono, jak można użyć niestandardowego Access Control opartego na rolach (RBAC) na platformie Azure do tworzenia i zarządzania żądaniami obsługi w Azure Portal.

## <a name="getting-started"></a>Wprowadzenie

Korzystając z powyższego przykładu, można utworzyć żądanie pomocy technicznej dla zasobu, jeśli użytkownik ma przypisaną niestandardową rolę RBAC w ramach subskrypcji przez właściciela subskrypcji.
[Niestandardowe role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) można tworzyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

Właściwość Actions roli niestandardowej określa operacje platformy Azure, do których rola udziela dostępu.
Aby utworzyć rolę niestandardową do zarządzania żądaniami obsługi, rola musi mieć akcję Microsoft. Support/*

Oto przykład roli niestandardowej, za pomocą której można tworzyć żądania pomocy technicznej i zarządzać nimi.
Ta rola została nazywana "współautorem żądania pomocy technicznej" i jest to sposób odwoływania się do roli niestandardowej w tym artykule.

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

Postępuj zgodnie z instrukcjami przedstawionymi w [tym filmie wideo](https://www.youtube.com/watch?v=-PaBaDmfwKI) , aby dowiedzieć się, jak utworzyć rolę niestandardową dla subskrypcji.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Tworzenie i zarządzanie żądaniami pomocy technicznej w Azure Portal

Zróbmy przykład — jesteś właścicielem subskrypcji "subskrypcja usługi Visual Studio MSDN".
Jan to element równorzędny, który jest właścicielem zasobu w niektórych grupach zasobów w ramach tej subskrypcji i ma uprawnienia do odczytu subskrypcji.
Chcesz udzielić dostępu do swojego elementu równorzędnego, Jan, możliwość tworzenia biletów pomocy technicznej i zarządzania nimi dla zasobów w ramach tej subskrypcji.

1. Pierwszym krokiem jest przejście do subskrypcji. W obszarze **Ustawienia**zostanie wyświetlona lista użytkowników. Wybierz użytkownika Jan, który ma dostęp czytelnika do subskrypcji. Przypiszemy nową rolę niestandardową do Jan.

    ![Dodaj rolę](./media/create-manage-support-requests-using-access-control/add-role.png)

2. W bloku "Użytkownicy" kliknij pozycję "Dodaj". Wybierz rolę niestandardową "Współautor żądania pomocy technicznej" z listy ról

    ![Dodaj rolę współautor obsługi](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po wybraniu nazwy roli kliknij pozycję "Dodaj użytkowników" i wprowadź poświadczenia poczty e-mail Jan. Kliknij pozycję „Wybierz”

    ![Dodawanie użytkowników](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Kliknij przycisk OK, aby wykonać operację.

    ![Dodawanie uprawnień dostępu](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Teraz zobaczysz użytkownika z nowo dodaną rolą niestandardową "pracownikiem żądania pomocy technicznej" w ramach subskrypcji, dla której jesteś właścicielem.

    ![Dodano użytkownika](./media/create-manage-support-requests-using-access-control/user-added.png)

    Po zalogowaniu się do portalu Jan widzi subskrypcję, do której został dodany Jan.

7. Jan klika polecenie "New Support request" w bloku "pomoc i obsługa techniczna" i może tworzyć żądania pomocy technicznej dla "Visual Studio Ultimate z subskrypcją MSDN"

    ![Nowe żądanie pomocy technicznej](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kliknięcie pozycji "wszystkie żądania pomocy technicznej" Jan może zobaczyć listę żądań pomocy technicznej utworzonych dla tego ![widoku Szczegóły przypadku subskrypcji](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Usuń dostęp do żądania obsługi w Azure Portal

Podobnie jak można udzielić dostępu użytkownikowi do tworzenia żądań pomocy technicznej i zarządzania nimi, można również usunąć dostęp dla użytkownika.

Aby usunąć możliwość tworzenia i zarządzania żądaniami pomocy technicznej, przejdź do subskrypcji, kliknij pozycję "Ustawienia" i kliknij użytkownika (w tym przypadku Jan). Kliknij prawym przyciskiem myszy nazwę roli, "Współautor żądania pomocy technicznej" i kliknij pozycję "Usuń".

![Usuń dostęp do żądania obsługi](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Gdy Jan loguje się do portalu i próbuje utworzyć żądanie pomocy technicznej, wystąpi następujący błąd:

![Błąd subskrypcji — 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jan nie widzi żadnych żądań pomocy technicznej, gdy Jan wybiera "wszystkie żądania pomocy technicznej"

![Widok szczegółów przypadku — 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
