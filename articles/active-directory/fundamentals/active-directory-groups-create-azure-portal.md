---
title: Tworzenie grupy dla użytkowników w usłudze Azure AD | Microsoft Docs
description: Jak utworzyć grupę w usłudze Azure Active Directory i dodać członków do grupy
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/04/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82d475e5adadb4e7670f24a6193348c9e1b37a16
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767527"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [Program PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

W tym artykule wyjaśniono, jak utworzyć i wypełnić nową grupę w usłudze Azure Active Directory. Grupa służy do wykonywania zadań zarządzania, takich jak przypisywanie licencji lub uprawnień do kilku użytkowników lub urządzeń jednocześnie.

## <a name="how-do-i-create-a-group"></a>Jak utworzyć grupę?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Wszystkie usługi**, w polu tekstowym wprowadź filtr **Użytkownicy i grupy**, a następnie naciśnij klawisz **Enter**.

   ![Otwieranie okna zarządzania użytkownikami](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. W bloku **Użytkownicy i grupy** wybierz pozycję **Wszystkie grupy**.

   ![Otwieranie bloku grup](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. W bloku **Użytkownicy i grupy — Wszystkie grupy** wybierz polecenie **Dodaj**.

   ![Wybieranie polecenia Dodaj](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. W bloku **Grupa** dodaj nazwę i opis grupy.
6. Aby wybrać członków do dodania do grupy, wybierz pozycję **Przypisane** w polu **Typ członkostwa**, a następnie wybierz pozycję **Członkowie**. Aby uzyskać więcej informacji o sposobie zarządzania członkostwem w grupie dynamicznej, zobacz [Tworzenie zaawansowanych reguł członkostwa w grupie przy użyciu atrybutów](../active-directory-groups-dynamic-membership-azure-portal.md).

   ![Wybieranie członków do dodania](./media/active-directory-groups-create-azure-portal/select-members.png)
7. W bloku **Członkowie** wybierz co najmniej jednego użytkownika lub urządzenie w celu dodania do grupy, a następnie kliknij przycisk **Wybierz** w dolnej części bloku, aby dodać te pozycje do grupy. Pole **Użytkownik** służy do filtrowania wyświetlanych danych na podstawie dopasowania wpisanej wartości do dowolnej części nazwy użytkownika lub urządzenia. W tym polu nie są akceptowane żadne symbole wieloznaczne.
8. Po zakończeniu dodawania członków do grupy wybierz pozycję **Utwórz** w bloku **Grupa**.    

   ![Potwierdzenie utworzenia grupy](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkami grupy](active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../active-directory-groups-dynamic-membership-azure-portal.md)
