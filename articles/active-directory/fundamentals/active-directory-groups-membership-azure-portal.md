---
title: Jak dodać lub usunąć grupę z innej grupy w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać lub usunąć grupę z innej grupy za pomocą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: c28fe5ef226fac993fde221b16bfa875ba4845ca
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579772"
---
# <a name="how-to-add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Porady: Dodawanie lub usuwanie grupy z innej grupy za pomocą usługi Azure Active Directory
Ten artykuł ułatwia dodawanie i usuwanie grupy z innej grupy za pomocą usługi Azure Active Directory.

>[!Note]
>Jeśli próbujesz usunąć grupę nadrzędną, zobacz [jak zaktualizować lub usunąć grupę i jej elementów członkowskich](active-directory-groups-delete-group.md).

## <a name="add-a-group-as-a-member-to-another-group"></a>Dodaj grupę jako członek do innej grupy
Możesz dodać istniejącą grupę, do innej grupy istniejących, tworzenie członka grupy (podgrupy) i grupy nadrzędnej. Element członkowski grupy dziedziczy atrybuty i właściwości grupy nadrzędnej, zaoszczędzić czas.

### <a name="to-add-a-group-as-a-member-to-another-group"></a>Aby dodać grupę jako członek do innej grupy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

3. Na **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, która jest w stanie się członkiem innej grupy. Na potrzeby tego ćwiczenia używamy **zasad zarządzania urządzeniami Przenośnymi - Zachodnia** grupy.

    >[!Note]
    >Możesz dodać grupy jako członka do tylko jednej grupy naraz. Ponadto **wybierz grupę** okno filtry wyświetlania, w oparciu o dopasowanie zgłoszenia użytkownika do dowolnej części nazwy użytkownika lub urządzenia. Jednak symbole wieloznaczne nie są obsługiwane.

    ![Wybrano grupę zachodnie grupy — wszystkie grupy strona przy użyciu zasad zarządzania urządzeniami Przenośnymi —](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na **członkostwa w grupach - Zachodnia — zasady zarządzania urządzeniami Przenośnymi** wybierz opcję **członkostwa w grupach**, wybierz opcję **Dodaj**, Znajdź grupę Twoja grupa ma być członkiem, a następnie wybierz  **Wybierz**. W tym ćwiczeniu używamy **zasady zarządzania urządzeniami Przenośnymi — wszystkie organizacji** grupy.

    **Zasad zarządzania urządzeniami Przenośnymi - Zachodnia** grupy jest teraz członkiem **zasady zarządzania urządzeniami Przenośnymi — wszystkie organizacji** grupy, dziedziczy wszystkie właściwości i konfiguracji zasad zarządzania urządzeniami Przenośnymi — wszystkie grupy w organizacji.

    ![Utwórz członkostwa w grupie, dodając grupy do innej grupy](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Przegląd **członkostwa w grupach - Zachodnia — zasady zarządzania urządzeniami Przenośnymi** strony, aby zobaczyć relację grupy i elementów członkowskich.

    ![MDM - zachód - zasady członkostwa stronę grupy przedstawiający grupy nadrzędnej](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Aby uzyskać bardziej szczegółowy widok relacji grup i elementów członkowskich, wybierz nazwę grupy (**zasady zarządzania urządzeniami Przenośnymi — wszystkie organizacji**) i przyjrzyj się **zasady zarządzania urządzeniami Przenośnymi — zachodnie stany** strony szczegółów.

    ![Strona członkostwo grupy, zawierający element członkowski i szczegóły grupy](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-member-group-from-another-group"></a>Usuń grupę elementu członkowskiego z innej grupy
Możesz usunąć istniejącą grupę elementu członkowskiego z innej grupy. Jednak usunięcie członkostwa powoduje również usunięcie wszelkich atrybuty dziedziczone i właściwości dla użytkowników.

### <a name="to-remove-a-member-group-from-another-group"></a>Aby usunąć grupę elementu członkowskiego z innej grupy
1. Na **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, która ma zostać usunięta, jest członkiem innej grupy. W tym ćwiczeniu ponownie używamy **zasad zarządzania urządzeniami Przenośnymi - Zachodnia** grupy.

2. Na **zasady zarządzania urządzeniami Przenośnymi — omówienie zachodnie** wybierz opcję **członkostwa w grupach**.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu zachodnie](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Wybierz **zasady zarządzania urządzeniami Przenośnymi — wszystkie organizacji** grupy z **członkostwa w grupach - Zachodnia — zasady zarządzania urządzeniami Przenośnymi** strony, a następnie wybierz pozycję **Usuń** z **zasad zarządzania urządzeniami Przenośnymi - Zachodnia** strony szczegółów.

    ![Strona członkostwo grupy, zawierający element członkowski i szczegóły grupy](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

- [Dodawanie lub usuwanie członków z grupy](active-directory-groups-members-azure-portal.md)

- [Edytuj ustawienia grupy](active-directory-groups-settings-azure-portal.md)

- [Przypisywanie licencji do użytkowników według grupy](../users-groups-roles/licensing-groups-assign.md)
