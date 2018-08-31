---
title: Przypisz role zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7019a6f97a9590d3b652584015f3077f4ed075af
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188924"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Przypisz role zasobów platformy Azure w usłudze PIM

Usługa Azure AD PIM można zarządzać ról wbudowanych zasobów platformy Azure, a także niestandardowe role, w tym (między innymi):

- Właściciel
- Administrator dostępu użytkowników
- Współautor
- Administrator zabezpieczeń
- Menedżer zabezpieczeń i nie tylko

>[!NOTE]
Użytkownicy lub członkowie grupy przypisane do roli właściciel lub Administrator dostępu użytkowników i administratorów globalnych, które umożliwiają Zarządzanie subskrypcją w usłudze Azure AD są administratorami zasobów. Administratorzy mogą przypisać role, konfigurowanie ustawień roli i przegląd dostępu za pomocą usługi PIM dla zasobów platformy Azure. Wyświetl listę [wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-roles"></a>Przypisywanie ról

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

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
