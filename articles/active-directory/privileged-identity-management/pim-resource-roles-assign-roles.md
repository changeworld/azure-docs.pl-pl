---
title: Przypisz role zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przypisywania ról usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c27f5432facc34384fce72ffff26e414cbeb7d96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699312"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Przypisz role zasobów platformy Azure przy użyciu Privileged Identity Management

## <a name="assign-roles"></a>Przypisz role

Aby przypisać użytkownika lub grupy do roli, podczas wyświetlania **ról** okienku, wybierz rolę, a następnie wybierz **Dodaj użytkownika**. 

![Okienko "Role" przy użyciu przycisku "Dodaj użytkownika"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Możesz też wybrać **Dodaj użytkownika** z **członków** okienka.

![Okienko "Członkowie" przy użyciu przycisku "Dodaj użytkownika"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Jeśli dodajesz użytkownika lub grupy z **członków** okienka, musisz: 

1. Wybierz rolę z **wybierz rolę** okienko, aby można było wybrać użytkownika lub grupę.

   !["Wybierz rolę" okienko](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Wybierz użytkownika lub grupy z katalogu.

3. Wybierz typ odpowiednie przypisanie z menu rozwijanego: 

   - **Tylko w czasie**: zapewnia to członkom użytkownika lub grupy z kwalifikujących się, ale nie trwały dostęp do roli w podanym okresie lub nieskończoność (jeśli została skonfigurowana w ustawieniach roli). 
   - **Bezpośrednie**: nie wymaga użytkownika lub grupę elementów członkowskich aktywować przypisania roli (nazywane trwały dostęp). Zalecamy użycie przypisania bezpośredniego stosowania krótkoterminowej, których dostęp nie będzie wymagany po zakończeniu zadania. Przykłady są zmiany w wywołaniu i zależne od czasu działania.

4. Jeśli ma być przypisania stałego (trwale kwalifikuje się do przypisania w czasie, lub trwale aktywne dla przypisania bezpośredniego), zaznacz pole wyboru poniżej **typ przydziału** pole.

   ![Okienko "Ustawienia członkostwa" pola "Typ przydziału" i powiązane pole wyboru](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Pole wyboru może być unmodifiable, jeśli inny administrator ma określone przypisania maksymalny czas trwania dla każdego typu przydziału w ustawieniach roli.

   Aby określić czas trwania danego przydziału, wyczyść pole wyboru, a następnie zmodyfikować początkowy lub końcowy pola daty i godziny.

   ![Okienko "Ustawienia członkostwa" z polami Data rozpoczęcia, godzinę rozpoczęcia, Data zakończenia i godzina zakończenia](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Zarządzanie przypisaniami ról

Administratorzy mogą zarządzać przypisań ról przez wybranie jednej **ról** lub **członków** w lewym okienku. Wybieranie **ról** umożliwia administratorom zakres ich zadań zarządzania do konkretnej roli. Wybieranie **członków** Wyświetla wszystkie przypisania ról użytkowników i grup dla zasobu.

!["Role"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Okienko "Członkowie"](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Jeśli masz roli do czasu aktywacji transparencie powiadomienia zostanie wyświetlony w górnej części okienka podczas wyświetlania członkostwa.


## <a name="modify-existing-assignments"></a>Zmodyfikuj istniejące przypisania

Aby zmodyfikować istniejące przypisania z widoku szczegółów użytkownika/grupy, wybierz opcję **zmienić ustawienia** na pasku akcji. Zmień typ przypisania do **tylko w czasie** lub **bezpośredniego**.

![Okienko "Szczegóły użytkownika" przycisk "Zmień ustawienia"](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
