---
title: Privileged Identity Management zasobów Azure - Uaktywnij role | Dokumentacja firmy Microsoft
description: Opisuje sposób aktywują role w ramach usługi PIM.
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Zarządzanie tożsamościami uprzywilejowanymi - role zasobów - aktywacji
Aktywacja ról zasobów Azure wprowadza nową funkcjonalność, która umożliwia członkom roli kwalifikujących się do planowania aktywacji dla przyszłych daty/godziny i wybierz czas trwania aktywacji określonym terminie (skonfigurowanych przez administratorów). Dowiedz się więcej o [Aktywacja usługi Azure AD ról tutaj](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Uaktywnij role
Przejdź do sekcji Moje ról na pasku nawigacyjnym po lewej stronie. Kliknij przycisk "Uaktywnij" dla roli, które chcesz aktywować do.
![](media/azure-pim-resource-rbac/rbac-roles.png)

Z menu aktywacji wprowadź odpowiednią datę i godzinę rozpoczęcia aktywowania roli. Opcjonalnie Zmniejsz czas trwania aktywacji (czas roli jest aktywny), a następnie wprowadź uzasadnienie, jeśli jest to wymagane; Kliknij przycisk Aktywuj.

W przypadku braku modyfikacji datę i godzinę, w ciągu kilku sekund zostanie uaktywniona roli. Pojawi się, że rola w kolejce w celu aktywacji komunikat transparentu na stronie Moja ról. Kliknij przycisk Odśwież, aby wyczyścić tę wiadomość.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Jeśli aktywacja jest zaplanowane na czas datę w przyszłości, oczekujące żądania będą wyświetlane na karcie oczekujących żądań w menu nawigacji po lewej stronie. W przypadku aktywacji ról nie jest już wymagana, użytkownik może anulować żądanie, klikając przycisk Anuluj w prawej części strony.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Wystarczającego administracji

Przy użyciu wystarczającego najlepsze praktyki administracyjne (JEA) z przypisaniami roli zasobu jest proste — PIM zasobów Azure. Użytkownicy i członkowie grupy z przydziałami w subskrypcji platformy Azure lub grupy zasobów można aktywować ich istniejącym przypisaniu roli na zmniejszenie zakresu. 

Ze strony wyszukiwania Znajdź podrzędnego zasób, który trzeba zarządzać.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Wybierz z menu nawigacji po lewej stronie Moje ról, a następnie wybierz odpowiednią rolę, aby aktywować. Powiadomienie typ przydziału jest dziedziczone, ponieważ rola została przypisana w subskrypcji, a nie w grupie zasobów, jak pokazano poniżej.

![](media/azure-pim-resource-rbac/my-roles-02.png)
