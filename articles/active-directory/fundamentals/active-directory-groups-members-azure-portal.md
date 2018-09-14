---
title: Jak dodać lub usunąć członków grupy za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak dodać lub usunąć użytkowników i urządzeń z grupy przy użyciu usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: f9244e1285396a2d5de40b596d47e311efa50b83
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574237"
---
# <a name="how-to-add-or-remove-group-members-using-azure-active-directory"></a>Porady: Dodawanie lub usuwanie członków grupy za pomocą usługi Azure Active Directory
Za pomocą usługi Azure Active Directory, można nadal dodawać i usuwać członków grupy.

## <a name="to-add-group-members"></a>Aby dodać członków grupy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

3. Z **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, którą chcesz dodać członków. W tym przypadku użyj naszego wcześniej utworzoną grupę **zasad zarządzania urządzeniami Przenośnymi - Zachodnia**.

    ![Strony grupy — wszystkie grupy, wyróżnioną nazwą grupy](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Z **zasady zarządzania urządzeniami Przenośnymi — omówienie zachodnie** wybierz opcję **członków** z **Zarządzaj** obszaru.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona przeglądu Zachodnia, z podświetloną opcją elementów członkowskich](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Wybierz **dodawać członków**, a następnie wyszukaj i wybierz poszczególne elementy członkowskie, aby dodać do grupy, a następnie wybierz **wybierz**.

    Zostanie wyświetlony komunikat informujący, że elementy członkowskie zostały pomyślnie dodane.

    ![Dodaj stronę elementów członkowskich z wyszukiwane pokazano elementu członkowskiego](media/active-directory-groups-members-azure-portal/update-members.png)

6. Należy odświeżyć ekran, aby wyświetlić wszystkie nazwy elementów członkowskich, dodane do grupy.

## <a name="to-remove-group-members"></a>Aby usunąć członków grupy

1. Z **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, którą chcesz usunąć członka z. Ponownie użyjemy, **zasad zarządzania urządzeniami Przenośnymi - Zachodnia**.

2. Wybierz **członków** z **Zarządzaj** obszaru, wyszukaj i wybierz nazwę członka do usunięcia, a następnie wybierz pozycję **Usuń**.

    ![Strona z informacjami o elementu członkowskiego, za pomocą opcji Usuń](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Kolejne kroki

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Edytuj ustawienia grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
