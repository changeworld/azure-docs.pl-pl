---
title: Privileged Identity Management zasobów Azure - Przypisz role | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przypisywania ról usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Zarządzanie tożsamościami uprzywilejowanymi - role zasobów - przypisania

## <a name="assign-roles"></a>Przypisz role

Aby przypisać do roli użytkownika lub grupę, wybierz rolę (Jeśli wyświetlanie role), 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

lub kliknij przycisk Dodaj na pasku akcji (jeśli są w widoku elementów członkowskich).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Jeśli Dodawanie użytkownika lub grupy na karcie elementy członkowskie, musisz: 

1. Wybierz z menu Dodaj rolę, zanim będzie można wybrać użytkownika lub grupę.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Wybierz użytkownika lub grupy z katalogu.

3. Wybierz typ odpowiednie przypisanie z menu rozwijanego. 

    - **Tylko w czasie przypisania:** zawiera członków użytkownika lub grupy z kwalifikujących się, ale nie trwały dostęp do roli w określonym przedziale czasu lub nieskończoność (jeśli została skonfigurowana w ustawieniach roli). 
    - **Bezpośrednie przypisywanie:** nie wymaga użytkownika lub grupę elementów członkowskich aktywować przypisania roli (nazywane trwały dostęp). Firma Microsoft zaleca korzystanie z bezpośredniego przypisania krótkoterminowej Użyj takie jak zmiany na wywołanie lub czas działania poufne, których dostęp nie będzie wymagany po zakończeniu zadania.

Pole wyboru poniżej listy rozwijanej Typ przypisania służy do określenia Jeśli przypisanie powinno być stałe (trwale kwalifikuje się do aktywowania tylko w czasie przypisania/trwale active dla przypisania bezpośredniego).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Pole wyboru może być unmodifiable, jeśli inny administrator ma określone przypisania maksymalny czas trwania dla każdego typu przydziału w ustawieniach roli.

 Aby określić czas trwania danego przydziału, usuń zaznaczenie pola wyboru i zmodyfikować uruchomienia i/lub kończyć pól daty i godziny.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Zarządzanie przypisaniami ról

Administratorzy mogą zarządzać przypisań ról, wybierając ról lub elementy członkowskie z lewym pasku nawigacyjnym. Wybieranie ról pozwala administratorom zakres ich zadań zarządzania do konkretnej roli, gdy elementy członkowskie Wyświetla wszystkie przypisania ról użytkowników i grup dla zasobu.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Jeśli masz roli do czasu aktywacji transparencie powiadomienia jest wyświetlane w górnej części strony, gdy wyświetlanie członkostwa.


## <a name="modify-existing-assignments"></a>Zmodyfikuj istniejące przypisania

Aby zmodyfikować istniejące przypisania z widoku szczegółów użytkownika/grupy, wybierz opcję Zmień ustawienia na pasku akcji w górnej części strony. Zmień typ przypisania tylko w przypisanie czasu lub bezpośredniego przypisania.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
