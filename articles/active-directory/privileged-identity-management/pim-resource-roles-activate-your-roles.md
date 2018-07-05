---
title: Uaktywnij role zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Opisuje sposób aktywowania ról w usłudze PIM.
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
ms.openlocfilehash: d2f61f1ebdd473a24115c7774801f5b7694f224f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443209"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Uaktywnij role zasobów platformy Azure przy użyciu Privileged Identity Management
Privileged Identity Management (PIM) wprowadzono nowe środowisko w aktywowania role zasobów platformy Azure. Elementy członkowskie kwalifikowania się do roli można zaplanować aktywacji dla przyszłej daty i godziny. Można również wybrać czas trwania aktywacji określonych w maksymalnym (skonfigurowane przez administratorów). Aby uzyskać więcej informacji, zobacz [jak aktywować lub dezaktywować ról w usłudze Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Uaktywnij role
Przejdź do **Moje role** sekcji w okienku po lewej stronie. Wybierz **Aktywuj** dla roli, która ma zostać uaktywniona.

![Karta "Kwalifikujące się role" w okienku "Moje role".](media/azure-pim-resource-rbac/rbac-roles.png)

Z **aktywacji** menu, wprowadź datę rozpoczęcia i czasu aktywacji roli. Opcjonalnie można zmniejszyć czas trwania aktywacji (długość czasu, przez jaki rola jest aktywna) i podać uzasadnienie, jeśli to konieczne. Następnie wybierz **Aktywuj**.

Jeśli data i godzina rozpoczęcia nie są modyfikowane, rola jest aktywowane w ciągu kilku sekund. W **Moje role** okienku komunikat transparentu pokazuje, że rola znajduje się w kolejce do aktywacji. Wybierz przycisk Odśwież, aby wyczyścić ten komunikat.

![Okienko "Moje role" komunikat transparentu i powiadomienia o oczekujących na zatwierdzenie](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Jeśli aktywacja jest zaplanowana do przyszłej daty i godziny, oczekujące żądanie pojawi się na **oczekujących żądań** kartę okienka po lewej stronie. Jeśli aktywacji roli jest już potrzebne, można anulować żądania, wybierając **anulować** przycisku.

![Lista oczekujących żądań z przyciski "Anuluj"](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Stosowanie praktyk Just Enough Administration

Przy użyciu tylko tyle administracji (JEA) najlepszych rozwiązań za pomocą przypisań ról z zasobu jest prostym, zawierającym PIM dla zasobów platformy Azure. Użytkownicy i członkowie grupy za pomocą przypisań do grup zasobów lub subskrypcji platformy Azure można aktywować istniejące przypisania roli w ograniczonym zakresie. 

Ze strony wyszukiwania Znajdź zasób podrzędny, który trzeba zarządzać.

![Wybierz zasób](media/azure-pim-resource-rbac/azure-resources-02.png)

Wybierz **Moje role** z okienka po lewej stronie i wybierz odpowiednią rolę, aby aktywować. Typ przypisania **dziedziczone** ponieważ rola została przypisana w ramach subskrypcji, a nie w grupie zasobów.

![Listę kwalifikujących się przypisań ról, z wyróżnionym typem przypisania](media/azure-pim-resource-rbac/my-roles-02.png)
