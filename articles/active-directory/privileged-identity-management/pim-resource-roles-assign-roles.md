---
title: Przypisz role zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przypisać role w usłudze PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: da5a0e41c476a75f230e2d2645e7f5befd644a93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441434"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Przypisz role zasobów platformy Azure przy użyciu Privileged Identity Management

## <a name="assign-roles"></a>Przypisz role

Aby przypisać użytkownika lub grupę do roli, podczas wyświetlania **role** okienku, wybierz rolę, a następnie wybierz **Dodaj użytkownika**. 

![Okienko "Role" za pomocą przycisku "Dodaj użytkownika"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Możesz również wybrać **Dodaj użytkownika** z **członków** okienka.

![Okienko "Członkowie" za pomocą przycisku "Dodaj użytkownika"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


W przypadku dodawania użytkownika lub grupy z **członków** okienko, musisz: 

1. Wybierz rolę z **wybierz rolę** okienko, aby można było wybrać użytkownika lub grupy.

   ![Okienko "Wybierz rolę"](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Wybierz użytkownika lub grupy z katalogu.

3. Wybierz typ przypisania odpowiednich z menu rozwijanego: 

   - **Dokładnie na czas**: zapewnia użytkownikowi lub grupie elementów członkowskich kwalifikujących się, ale nie jest trwały dostęp do roli na czas określony lub nieokreślony (jeśli jest skonfigurowane w ustawieniach roli). 
   - **Bezpośrednie**: nie jest wymagane elementy członkowskie użytkownika lub grupy można aktywować przypisania roli (znanych jako trwały dostęp). Zalecamy używanie przypisania bezpośredniego użytku krótkoterminowej, gdzie dostęp nie być wymagane, jeśli zadanie zostało ukończone. Przykładami są zmiany na wywołanie i zależne od czasu działania.

4. Jeśli przypisanie powinno być trwałe (trwałe kwalifikujące się przypisania just in time lub trwale aktywne przypisania bezpośredniego), zaznacz poniższe pole wyboru **typ przypisania** pole.

   ![Okienko "Ustawienia członkostwa" pola "Typ przypisania" i powiązane pole wyboru](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Pole wyboru musi być niemodyfikowalnych, jeśli inny administrator ma określony czas trwania maksymalna przypisania dla każdego typu przydziału w ustawieniach roli.

   Aby określić czas trwania określone przypisanie, wyczyść pole wyboru, a następnie zmodyfikować początkowy lub końcowy pola daty i godziny.

   ![Okienko "Ustawienia członkostwa" za pomocą pola daty rozpoczęcia, godzinę rozpoczęcia, Data zakończenia i czas zakończenia](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Zarządzanie przypisaniami ról

Administratorzy mogą zarządzać przypisaniami ról, wybierając opcję **role** lub **członków** z okienka po lewej stronie. Wybieranie **role** umożliwia administratorom ograniczyć zakres ich zadań zarządzania do określonej roli. Wybieranie **członków** Wyświetla wszystkie przypisania ról użytkowników i grup dla zasobu.

![Okienko "Role"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Okienko "Członkowie"](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
W przypadku roli do czasu aktywacji transparent powiadomienia jest wyświetlany w górnej części okienka po przejrzeniu członkostwa.


## <a name="modify-existing-assignments"></a>Modyfikuj istniejące przypisania

Aby zmodyfikować istniejące przypisania z widoku szczegółów użytkownika/grupy, wybierz **zmiany ustawień** na pasku akcji. Zmień typ przypisania na **tylko w czasie** lub **bezpośredniego**.

![Okienko "Szczegóły użytkownika" za pomocą przycisku "Zmień ustawienia"](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
