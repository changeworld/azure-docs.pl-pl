---
title: Dodawanie lub usuwanie członków grupy — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące dodawania lub usuwania członków z grupy przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561970"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Dodawanie lub usuwanie członków grupy przy użyciu usługi Azure Active Directory
Korzystając z usługi Azure Active Directory, można kontynuować dodawanie i usuwanie członków grupy.

## <a name="to-add-group-members"></a>Aby dodać członków grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Grupy**.

3. Na stronie **Grupy — wszystkie grupy** wyszukaj i wybierz grupę, do której chcesz dodać członka. W takim przypadku użyj naszej wcześniej utworzonej grupy, **zasady MDM - Zachód**.

    ![Grupy — strona Wszystkie grupy, wyróżniona nazwa grupy](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stronie przeglądu grupy **MDM policy - West** wybierz pozycję **Członkowie** w obszarze **Zarządzanie**.

    ![Zasady MDM — strona Przegląd zachodni, z wyróżnioną opcją Członkowie](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Wybierz **pozycję Dodaj członków**, a następnie wyszukaj i wybierz każdego z członków, których chcesz dodać do grupy, a następnie wybierz pozycję **Wybierz**.

    Zostanie wyświetlony komunikat informujący, że członkowie zostali pomyślnie dodani.

    ![Dodawanie strony członków z wyświetlonym wyszukiwaniem członków](media/active-directory-groups-members-azure-portal/update-members.png)

6. Odśwież ekran, aby wyświetlić wszystkie nazwy członków dodane do grupy.

## <a name="to-remove-group-members"></a>Aby usunąć członków grupy

1. Na stronie **Grupy — wszystkie grupy** wyszukaj i wybierz grupę, z której chcesz usunąć członka. Ponownie użyjemy **polityki MDM - Zachód**.

2. Wybierz **pozycję Członkowie** z obszaru **Zarządzaj,** wyszukaj i wybierz nazwę członka do usunięcia, a następnie wybierz pozycję **Usuń**.

    ![Strona informacji o człowiku, z opcją Usuń](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
