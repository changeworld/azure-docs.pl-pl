---
title: Dodawanie lub usuwanie członków grupy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania lub usuwania elementów członkowskich do grupy przy użyciu usługi Azure Active Directory.
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561970"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Dodawanie lub usuwanie członków grupy za pomocą usługi Azure Active Directory
Za pomocą usługi Azure Active Directory, można nadal dodawać i usuwać członków grupy.

## <a name="to-add-group-members"></a>Aby dodać członków grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

3. Z **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, którą chcesz dodać członków. W tym przypadku użyj naszego wcześniej utworzoną grupę **zasad zarządzania urządzeniami Przenośnymi - Zachodnia**.

    ![Strony grupy — wszystkie grupy, wyróżnioną nazwą grupy](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stronie przeglądu grupy **MDM policy - West** wybierz pozycję **Członkowie** w obszarze **Zarządzanie**.

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

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
