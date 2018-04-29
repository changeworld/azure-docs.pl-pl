---
title: Uaktywnij role zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: a985e67cc566cc45b3ee6b8dc98e91a8f34abd1b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Uaktywnij role zasobów platformy Azure przy użyciu Privileged Identity Management
Zarządzanie tożsamości uprzywilejowanych (PIM) wprowadzono nowe środowisko w aktywowania ról dla zasobów platformy Azure. Członkowie roli kwalifikujących się zaplanować aktywacji dla przyszłych daty i godziny. Można również określić czas trwania aktywacji określonym terminie (skonfigurowanych przez administratorów). Aby uzyskać więcej informacji, zobacz [jak aktywować lub dezaktywować role w programie Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Uaktywnij role
Przejdź do **Moje ról** sekcji w okienku po lewej stronie. Wybierz **Aktywuj** dla roli, która ma być aktywowana.

![Karta "Kwalifikujących się role" w okienku "Mój ról".](media/azure-pim-resource-rbac/rbac-roles.png)

Z **aktywacji** menu, wprowadź datę rozpoczęcia i czas, aby aktywować rolę. Opcjonalnie, Zmniejsz czas trwania aktywacji (długość czasu, przez który rola jest aktywna), a następnie wprowadź uzasadnienie, jeśli to konieczne. Następnie wybierz opcję **Aktywuj**.

Jeśli nie zostaną zmodyfikowane datę i godzinę, w sekundach uaktywnieniu roli. W **Moje ról** okienku komunikat transparentu pokazuje, czy rola jest przechowywane w kolejce w celu aktywacji. Wybierz przycisk Odśwież, aby wyczyścić tę wiadomość.

![Okienko "Mój ról" komunikat transparentu i powiadomienia o oczekuje na zatwierdzenie](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Jeśli aktywacja jest zaplanowane na przyszłe daty i godziny, oczekujące żądania jest wyświetlana na **oczekujących żądań** okienka po lewej stronie. Aktywacja roli nie jest już potrzebne, można anulować żądania, wybierając **anulować** przycisku.

![Lista oczekujących żądań z przyciski "Anuluj"](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Zastosuj tylko tyle praktyki administracyjne

Przy użyciu najlepszych rozwiązań tylko tyle administracyjnej (JEA) z przypisaniami roli zasobu jest proste PIM dla zasobów platformy Azure. Użytkownicy i członkowie grupy z przydziałami w subskrypcji platformy Azure lub grupy zasobów można aktywować ich istniejącym przypisaniu roli na zmniejszenie zakresu. 

Ze strony wyszukiwania Znajdź podrzędnego zasób, który trzeba zarządzać.

![Wybieranie zasobu](media/azure-pim-resource-rbac/azure-resources-02.png)

Wybierz **Moje ról** w okienku po lewej stronie i wybierz odpowiednią rolę, aby aktywować. Typ przydziału jest **dziedziczonych** ponieważ rola została przypisana w ramach subskrypcji, a nie w grupie zasobów.

![Lista przypisania ról kwalifikujących się z wyróżnionym typem przypisania](media/azure-pim-resource-rbac/my-roles-02.png)
